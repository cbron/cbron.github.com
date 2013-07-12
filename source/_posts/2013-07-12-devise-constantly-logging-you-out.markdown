---
layout: post
title: "Devise constantly logging you out ?"
date: 2013-07-12 10:47
comments: true
categories: 
---
##The problem

I had a jquery mobile app living inside a rails app. I would login and move around the site a few times and suddenly I would be logged out. I was massively confused for a solid hour.

##What was happening
I went digging around in Warden which Devise uses to handle sessions, then I remembered seeing this in my server logs:

    WARNING: Can't verify CSRF token authenticity

I did a little research and realized that my app was making multiple ajax requests while I was moving around. When rails can't verify the CSRF token the first thing it does is kill all sessions, including Devise user sessions. 



##How I fixed it

[I found this rails post](http://weblog.rubyonrails.org/2011/2/8/csrf-protection-bypass-in-ruby-on-rails/), 
[and this blog post](http://excid3.com/blog/rails-tip-2-include-csrf-token-with-every-ajax-request/#.UeA9lPZNYVd)


[This stack overflow question also goes into greater detail](http://stackoverflow.com/questions/7203304/warning-cant-verify-csrf-token-authenticity-rails)


In short, add this to your js somewhere in the header: 

{% codeblock lang:javascript %}
$(document).ajaxSend(function(e, xhr, options) {
  var token = $("meta[name='csrf-token']").attr("content");
  xhr.setRequestHeader("X-CSRF-Token", token);
});
{% endcodeblock %}

That will attach the correct csrf token along with your ajax requests. You should now no longer see the CSRF warning in your server logs.

