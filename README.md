# Carrierwave AWS S3 Guide
### A (mostly) pain-free guide to using AWS S3 to store images for your rails app.
Upload your 📷 to the ☁️

This guide will walk you through the process of setting up the [carrierwave gem](https://github.com/carrierwaveuploader/carrierwave) along with [fog](https://github.com/fog/fog) to enable image uploading to Amazon S3 for your Ruby on Rails projects.

This guide assumes knowledge of Ruby on Rails gems, installation and usage. I am using the example of adding images to a room listing for a apartment-sharing service, you should, of course, customise it to your needs.

### Why AWS?

To be written

Add the gems into your Gemfile:

`
gem 'carrierwave'
gem 'fog'
`

then run:

`
bundle install
`

To incorporate carrierwave into your project, generate an uploader:

`
rails generate uploader images
`

and a migration for the model:

`
rails g migration add_images_to_rooms image:string
`

mount the uploader in the model:

```
mount_uploader :image, ImagesUploader
```

Next we'll configure carrierwave, as well as fog.

In the image_uploader.rb file in /app/uploaders add:

```ruby
if Rails.env.production?
  storage :fog
else
  storage :file
end
  
def extension_whitelist
  %w(jpg jpeg gif png)
end
```

if you want to upload to AWS even in deveopment, remove the if statement `if Rails.env.production?`

To add the image upload button, in your form, add:

```ruby
<div class="field">
  <%= f.label :image %>
  <%= f.file_field :image %>
</div>
```

To make the image display on the page, add this to your view:

```ruby
<%= image_tag room.image.url if room.image? %>
```
  
To implement AWS:

First sign up for [Amazon Web Services](https://aws.amazon.com/) - there should be an option for a free trial.

Then, navigate to the [S3 console](https://console.aws.amazon.com/s3), and create a bucket in your region of choice, and use the [security credentials page](https://console.aws.amazon.com/iam/home?#/security_credential) to generate your access keys. Note down both the access key ID and the secret key! Finally, make sure you have "AmazonS3FullAccess" and "AmazonS3ReadOnlyAccess" in your policy permissions.

create a carrier_wave.rb file in config/initializers/carrier_wave.rb

```ruby
if Rails.env.production?
  CarrierWave.configure do |config|
    config.fog_credentials = {
      # Configuration for Amazon S3
      :provider              => 'AWS',
      :aws_access_key_id     => ENV['S3_ACCESS_KEY'],
      :aws_secret_access_key => ENV['S3_SECRET_KEY'],
      :region                => ENV['S3_REGION']
    }
    config.fog_directory     =  ENV['S3_BUCKET']
  end
end
```

Make sure to put in your S3 region:

AWS Regions:

|Code	               |Name                      |
---------------------|:------------------------:|
|us-east-1           |US East (N. Virginia)
|us-east-2           |US East (Ohio)
|us-west-1           |US West (N. California)
|us-west-2           |US West (Oregon)
|ca-central-1        |Canada (Central)
|eu-west-1           |EU (Ireland)
|eu-central-1        |EU (Frankfurt)
|eu-west-2           |EU (London)
|ap-northeast-1      |Asia Pacific (Tokyo)
|ap-northeast-2      |Asia Pacific (Seoul)
|ap-southeast-1      |Asia Pacific (Singapore)
|ap-southeast-2      |Asia Pacific (Sydney)
|ap-south-1          |Asia Pacific (Mumbai)
|sa-east-1           |South America (São Paulo)

If you are deploying to heroku, use these heroku config settings:

```
$ heroku config:set S3_ACCESS_KEY=<access key>
$ heroku config:set S3_SECRET_KEY=<secret key>
$ heroku config:set S3_BUCKET=<bucket name>
$ heroku config:set S3_REGION=<region name>
```
