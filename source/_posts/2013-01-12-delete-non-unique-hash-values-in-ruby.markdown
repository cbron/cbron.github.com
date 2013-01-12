---
layout: post
title: "Delete non-unique hash values in ruby"
date: 2013-01-12 14:32
comments: true
categories: 
---

{% codeblock lang:ruby %}
h = {:cat => "cat", :dog => "dog", :fish => "dog"}
arr = Array.new
h.each{|key, val| arr.include?(val) ? h.delete(key) : arr « val }
=> {:cat=>"cat", :dog=>"dog"} 
{% endcodeblock %}