Almost every app needs file upload implementation. 

We use [S3](https://aws.amazon.com/s3/) as file storage on servers, and disk storage in development and test environments.

# Shrine
There are several file upload gems out there. We use [Shrine](https://github.com/janko-m/shrine) because of its flexibility. It is the youngest of all the popular file uploaders, but it maintains an issue count of 0 and has fixed all the issues other gems have.

Shrine is a general-purpose file uploader gem. Since it has no direct dependency,
__it can be used in both Rails and non-Rails projects.__ Shrine features a rich
plugin system which makes it incredibly easy to change its behavior to suit
your needs. All this power is packaged in an easy-to-use form factor.

Official docs can be found [here](https://shrinerb.com/)
There is also [a demo application](https://github.com/erikdahlstrand/shrine-rails-example).

## Plugins
As mentioned before, Shrine has a lot of plugins. Make sure you scan the documentation to see which plugins would be useful in your app.

You will most certainly need to [validate](https://shrinerb.com/docs/validation) and [process](https://shrinerb.com/docs/processing) your files so read these parts carefully.

By default, Shrine stores files in flat structure, filenames are random strings. If you prefer folder-like structure, then you'll need to use [pretty_location](https://shrinerb.com/docs/plugins/pretty_location) plugin.


## Direct S3 upload

Mobile or web front ends often upload files through the app server, which means that the file does a double hop: from the frontend to the backend, then from the backend to the cloud storage service.

Direct upload solves this double-hop performance problem by giving one-time credentials to the frontend app to upload files directly to the cloud, and it sends out references to those files to the backend.

This is extremely useful if you want to speed up the uploading process and improve user experience.

To implement direct S3 upload with Shrine, read the instructions [here](https://shrinerb.com/docs/direct-s3).

## Security
Files stored on S3 are private by default. This means file URL's will be signed and they will expire after some specified time.
It's best to explicitly set the expiration time for **each uploader class** using [url_options](https://shrinerb.com/docs/plugins/url_options) plugin.

Some files need be public, i.e. albums' covers. In that case, set the `acl` to `public-read` via [upload_options](https://shrinerb.com/docs/plugins/upload_options) plugin.


## Important things

- Use **jsonb** data type for file columns when possible.

- If you're uploading images, you should process them (compression) and create **multiple derivatives** (versions) of different sizes. Consult with frontend devs on required dimensions.
**Backgrounding** plugin should be combined with the processing for better user experience. 

- Always validate **mime type** for uploaded files, and extension if needed.

- Shrine doesn't automatically delete files from cache storage when moving them to store storage. Tell DevOps to **set a lifecycle policy** with appropriate amount of time **for cache storage** prefix.


## Accessing files on S3
To access files on s3, ask somebody from the DevOps team for S3 console access. 
If you already have bucket credentials, you can also use [Cyberduck](https://cyberduck.io/).

After downloading Cyberduck, follow these steps to connect to your S3 bucket.

1. Click to create a new bookmark.

![New bookmark](/images/cyberduck_bookmark.jpg)

2. Fill in the information from your S3 secrets.

![Bucket info](/images/cyberduck_bucket_info.jpg)

3. Double-click your bookmark and fill in the Secret Access Key.

![Connection](/images/cyberduck_connection.jpg)

