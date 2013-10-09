---
layout: post
title: "Reload page and add param on checkbox click with Rails"
date: 2013-10-09 10:03
comments: true
categories: 
---

{% codeblock lang:ruby %}
= form_tag the_current_path, :method => :get do |f|
  = check_box_tag :my_filter, true, params[:my_filter], :onchange => 'this.form.submit();''
  = label_tag :my_filter, "My Filter"
{% endcodeblock %}