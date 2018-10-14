---
title: Tips for Image Migration to AWS S3
date: 2015-05-25 20:17:43
tags:
---
## Objectives

When migrating to S3, there comes a time that you finished uploading your server images, but users continue to upload afterwards.

This is an issue for us because we don't want to use [Asset Sync](https://github.com/AssetSync/asset_sync) all the time since it might slow down the server power because it replaces them with all the files.

A rake script might come to our rescue while we only need to run a check for server files creation timestamp and then just upload the file we need to reduce the overall syncing process of amazon command line tool.

``` ruby
require "pry"

namespace :s3 do
  desc "Upload recent local images to S3 (A rake helper while migrating to S3, no use after S3 completed migration)"
  task upload: :environment do
    local_path = "/home/apps/my-project/shared/public/system"
    s3_path = Rails.env.staging? ? "/my-project-staging/system" : "/my-project/system"

    image_folder_ids = Dir::glob("#{local_path}/images/*/").find_all { |f| File.mtime(f) >= 5.days.ago }
    excerpt_image_folder_ids = Dir::glob("#{local_path}/excerpt_images/*/").find_all { |f| File.mtime(f) >= 5.days.ago }
    headline_image_folder_ids = Dir::glob("#{local_path}/headline_images/*/").find_all { |f| File.mtime(f) >= 5.days.ago }

    if image_folder_ids.present?
      puts "Image IDs Updated 5 days ago"
      puts "-----------------------------"
      puts image_folder_ids
    end

    if excerpt_image_folder_ids.present?
      puts "Excerpt Image IDs Updated 5 days ago"
      puts "-----------------------------"
      puts excerpt_image_folder_ids
    end

    if headline_image_folder_ids.present?
      puts "Headline Image IDs Updated 5 days ago"
      puts "-----------------------------"
      puts headline_image_folder_ids
    end

    puts "\nUploading...\n"

    image_folder_ids.each do |folder_path|
      id = File.split(folder_path).last
      cmd = "s3cmd put -P #{folder_path} s3:/#{s3_path}/images/#{id}/ --recursive"
      puts cmd
      %x(#{cmd})
    end

    excerpt_image_folder_ids.each do |folder_path|
      id = File.split(folder_path).last
      cmd = "s3cmd put -P #{folder_path} s3:/#{s3_path}/excerpt_images/#{id}/ --recursive"
      puts cmd
      %x(#{cmd})
    end

    headline_image_folder_ids.each do |folder_path|
      id = File.split(folder_path).last
      cmd = "s3cmd put -P #{folder_path} s3:/#{s3_path}/headline_images/#{id}/ --recursive"
      puts cmd
      %x(#{cmd})
    end

    puts "Finished!"
  end
end
```
