---
layout: post
title: "auto merge pull requests on github except the master branch"
date: 2014-03-25 08:39
comments: true
categories: 
---

* Problem: You want to merge all pull requests from developers who don't have push access (they can't merge the PR themselves).
* Stipulation: We probably shouldn't do this on the master branch. Or staging for that matter. 
* Solution: Use the github api (v3)
  * 1 - with a post-hook
  * 2 - with a cron job

This script is solution 2. Its not as efficient (there is a time delay instead of it being instant), but I'm in an odd situation where I can't publicly expose a page that would receive the post-hook. But using this same idea with a post-hook wouldn't be too hard. 


### The python (2.7) script

I put this in /root/ so that non-sudo users can't see the OAUTH_KEY, but you can place it anywhere. 

The way its configured, it won't merge INTO master, staging, release, or development. Although it could go from development into my-feature-1 or any other branch. 

(Disclaimer: I'm still a Python newbie, so there could be bad practices in here.)
{% codeblock lang:python auto_merge_github.py %}
#!/usr/bin/env python
import json
import requests
import datetime


OAUTH_KEY = "xxxxxxxxxxxx"
repos = ['my_app'] # Add all repo's you want to automerged here
ignore_branches = ['master', 'release', 'staging', 'development'] # Add 'master' here if you don't want to automerge into master


# Print merge/no-merge message to logfile
def print_message(merging):
  if merging == True:
    message = "Merging: "
  else:
    message = "Not merging: "
  print message + str(pr_id) + " - " + user + " wants to merge " + head_ref + " into " + base_ref


# Merge the actual pull request
def merge_pr():
  r = requests.put("https://api.github.com/repos/phx-it-web/%s/pulls/%d/merge"%(repo,pr_id,), 
    data=json.dumps({"commit_message": "Auto_Merge"}), 
    auth=('token', OAUTH_KEY))
  if "merged" in r.json() and r.json()["merged"]==True:
    print "Merged: " + r.json()['sha']
  else:
    print "Failed: " + r.json()['message']


# Main

print datetime.datetime.now()

for repo in repos:
  r = requests.get('https://api.github.com/repos/phx-it-web/%s/pulls'%repo, auth=('token', OAUTH_KEY))
  data = r.json()

  for i in data:
    head_ref=i["head"]["ref"]
    base_ref=i["base"]["ref"]
    user=i["user"]["login"]
    pr_id = i["number"]
    if base_ref in ignore_branches:
      print_message(False)
    else:
      print_message(True)
      merge_pr()

{% endcodeblock %}

And the cron code that runs it every 5 minutes:
{% codeblock lang:bash %}
# Auto merge github PR's
*/5 * * * * /usr/local/bin/python2.7 /root/auto_merge_github.py >> /root/auto_merge_github.log 2>&1
{% endcodeblock %}

