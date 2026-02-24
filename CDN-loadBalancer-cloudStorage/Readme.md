# Cloud CDN with HTTP Load Balancer and Cloud Storage

## Overview

This lab walks you through setting up **Google Cloud CDN** (Content Delivery Network) backed by a **Cloud Storage bucket** and served through an **HTTP Load Balancer**. You will upload a file to Cloud Storage, enable CDN caching through the load balancer, and verify that content is being served from Google's edge network rather than the origin bucket.

## What You Will Do

- Create a multi-regional **Cloud Storage bucket** and make it publicly accessible
- Upload an image file to the bucket using `gsutil`
- Create an **HTTP Load Balancer** with a Cloud Storage backend bucket
- Enable **Cloud CDN** on the backend with custom TTL settings
- Use `curl` to time HTTP requests and observe the speed difference between cache MISS and cache HIT
- Explore **Cloud Logging** to confirm content was served from cache vs. backend

## Outcome

By the end of this lab, you will have:

- A publicly accessible Cloud Storage bucket serving static content
- A global HTTP Load Balancer with Cloud CDN enabled
- Verified proof (via response times and logs) that CDN caching is working
- An understanding of the difference between `response_sent_by_backend` and `response_from_cache`

## Where This Applies

- **Static website hosting** — Serving HTML, CSS, images, and JS files globally with low latency
- **Media delivery** — Distributing images, videos, and downloads to a global audience
- **E-commerce platforms** — Speeding up product image loading for shoppers worldwide
- **SaaS applications** — Caching frontend assets to reduce backend load and improve user experience

## Real-World Use Cases

- A global e-commerce site like Amazon caching product images at edge locations so shoppers in every country see fast load times regardless of where the image files are stored
- A news website caching article images and thumbnails so that viral stories load instantly for millions of simultaneous users without overwhelming the origin server
- A mobile app serving its static assets (icons, fonts, images) through a CDN to reduce app load times for users in regions far from the primary data center
- A SaaS company reducing cloud egress costs by caching frequently accessed files at CDN edge locations instead of repeatedly fetching them from the origin storage bucket