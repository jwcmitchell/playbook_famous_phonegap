# Media Upload

We currently support Photo Uploads only, and pass them through Transload.it for resizing/cropping, and finally save to an S3 bucket. 

Media upload with the Waiting app requires AWS and Transload.it credentials in `config.json`. 

### Upload Profile Photo 

#### Client-side upload 

Our client can upload images from a device using a native plugin or through the normal file-upload dialog box for mobile web. 

#### Accepting the Upload 

An upload POSTs to the `media/profilephoto` endpoint (`routes/media.js`), where we gather the image, save some details (userid, time, etc.) and send it to Transload.it. 

#### Transload.it (crop, resize, upload to S3) 

The "recipe" that we use for Transload.it specifies the S3 bucket to put our image in, and describes cropping/resizing. 

When Transload.it has completed modifying our image, it POSTs to `media/profilephoto_transloadit` where we update our database entry with the updated URLs for our transformed images. 