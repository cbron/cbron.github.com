---
layout: post
title: "How to tell if today is the first of the Week/Month/Year with ruby"
date: 2013-02-20
comments: true
categories: 
---

I had a project recently where a cron would fire early in the morning and generate stats for the previous day/week/month/year. I needed to know when it fired if today was the beginning of a new month so I could generate last month's stats. 

{% codeblock lang:ruby %}
#First hour of the day:
Time.now.hour == 0

#Beginnning of the week
Time.now.beginning_of_week == Time.now.beginning_of_hour

#First day of the month 
Time.now.day == 1 && Time.now.hour == 0

#First day of the year
Time.now.beginning_of_year == Time.now.beginning_of_hour
{% endcodeblock %}