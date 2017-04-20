# carrierwave-aws-guide
Upload your 📷 to the ☁️

Generate an uploader, then migration for the model & mount the uploader in the model:

`rails generate uploader images
rails g migration add_images_to_rooms image:string
mount_uploader :image, ImagesUploader`

install all the carrier wave stuff, include fog

stick this in the uploader.rb file

`include CarrierWave::MiniMagick
process resize_to_limit: [400, 400]

if Rails.env.production?
  storage :fog
else
  storage :file
end
  
def extension_whitelist
  %w(jpg jpeg gif png)
end`

In the form:
`
  <div class="field">
    <%= f.label :image %>
    <%= f.file_field :image %>
  </div>`
  
To make it display:
  `<%= image_tag room.image.url if room.image? %>`
  
To implement AWS:
  
create a carrier_wave.rb file in config/initializers/carrier_wave.rb

`if Rails.env.production?
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
end`
  
AWS Regions:
  
Code	               Name
us-east-1            US East (N. Virginia)
us-east-2            US East (Ohio)
us-west-1            US West (N. California)
us-west-2            US West (Oregon)
ca-central-1         Canada (Central)
eu-west-1            EU (Ireland)
eu-central-1         EU (Frankfurt)
eu-west-2            EU (London)
ap-northeast-1       Asia Pacific (Tokyo)
ap-northeast-2       Asia Pacific (Seoul)
ap-southeast-1       Asia Pacific (Singapore)
ap-southeast-2       Asia Pacific (Sydney)
ap-south-1           Asia Pacific (Mumbai)
sa-east-1            South America (São Paulo)

Heroku settings:

$ heroku config:set S3_ACCESS_KEY=<access key>
$ heroku config:set S3_SECRET_KEY=<secret key>
$ heroku config:set S3_BUCKET=<bucket name>
$ heroku config:set S3_REGION=<region name>
