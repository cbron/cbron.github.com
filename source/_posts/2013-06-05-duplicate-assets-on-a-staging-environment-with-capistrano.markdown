---
layout: post
title: "Dealing with duplicate assets in a staging environment when deploying with capistrano"
date: 2013-06-04 10:13
comments: true
categories: capistrano, asset-pipeline, staging
---

When you use something like `$('#change_email_form').toggle(300);` and it gets included twice on the same page, everything doesn't seem to work as expected...

I ran into this problem when setting up a staging environment using capistrano. My capfile had the usual `load 'deploy/assets'` which precompiles assets on both production and staging. And everything in 
production worked fine, but staging not so much. After a few failed attemps I found a fix for the problem. 

{% codeblock lang:ruby %}
config.assets.compile = false
{% endcodeblock %}

Ruby guides has this to say about assets.compile:

*In some circumstances you may wish to use live compilation. In this mode all requests for assets in the pipeline are handled by Sprockets directly.
On the first request the assets are compiled and cached as outlined in development above, and the manifest names used in the helpers are altered to include the MD5 hash.*

So basically capistrano was compiling the assets and throwing them in `public/assets` and at the same time Sprockets was trying to handle the requests by using the `app/assets` directory and all the javascript was included twice. In this case it was both in our `subscribe.js` and our `application.js`. 

By setting `config.assets.compile = false` you tell Sprockets to stop attempting to serve your `app/assets` and solely use your public assets. 

This is my final configuration for assets in config/environments/staging.rb

{% codeblock lang:ruby %}
# Disable Rails's static asset server (Apache or nginx will already do this)
config.serve_static_assets = false
# Don't fallback to assets pipeline if a precompiled asset is missed
config.assets.compile = false
# Generate digests for assets URLs
config.assets.digest = true
# Expands the lines which load the assets
config.assets.debug = true
{% endcodeblock %}