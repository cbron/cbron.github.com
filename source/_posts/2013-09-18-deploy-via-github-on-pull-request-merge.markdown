---
layout: post
title: "deploy via github on pull request merge"
date: 2013-09-18 16:50
comments: true
categories: 
---

*(This is only a concept, but I proved out all the parts seperately so they should work together)*

The downside of github hooks is that right now the only one available through the web is when the repo receives a push. We wanted to have the repo update anytime anyone merges a pull request into the master branch. 

I found this walkthrough which sets a repo up to pull from a normal github hook: <a href="https://gist.github.com/oodavid/1809044" target="_blank">https://gist.github.com/oodavid/1809044</a>

So if you follow that entire guide but then switch out the "Set up service hook" piece for what I have below it should work.


##Setting up a hook on a pull request

First go into github and click on account settings, then Applications and generate a personal access token. 

Then using a rest client POST the following, you'll need to setup a [http://requestb.in/](request bin) to view the payload:

**URL**:  https://api.github.com/repos/username/example_repo/hooks

**Header**:
Authorization
token personal_access_token_XXXXXXXXXXXXXXXXXXXX

**Payload**:
{% codeblock lang:json %}
{
  "name": "web",
  "active": true,
  "events": ["pull_request"],
  "config": {
    "url": "http://requestb.in/XXXXX"
  }
}
{% endcodeblock %}

Using the "Advanced REST Client" extension in chrome it looks something like this: 

<img src="{{ root_url }}/images/rest_client.png" />

Send that and you should get a success message.

Now do a pull request / merge inside github and check your request bin and you should have a huge payload in there. 

{% codeblock lang:json %}
#This is just a piece of the payload:
payload: 
{
 "action":"closed",
 "number":2,
 "pull_request":
  {
   "url":"https://api.github.com/repos/cbron/example_repo/pulls/2",
   "id":8431554,
   "html_url":"https://github.com/cbron/example_repo/pull/2",
   "diff_url":"https://github.com/cbron/example_repo/pull/2.diff",
   "patch_url":"https://github.com/cbron/example_repo/pull/2.patch",
   "issue_url":"https://github.com/cbron/example_repo/pull/2",
   "number":2,
   "state":"closed",
   "title":"testing merge hooks"
  }
}
{% endcodeblock %}

So now going back to that walkthrough, inside your deploy.php page just check the payload for a state of "closed" and you can do a git pull. If the pull request was rejected the state will also be closed and this will still do a pull, but the app will simply be up to date so everythings kosher. You should also be able to check for which branch the pull request was on, but once again, pulling everytime a pull request is closed shouldn't be a big deal.

This hook will now show up in Github under Settings -> Service Hooks -> WebHook URLS

Good luck!

### Resources

* How to setup pull_request hook
   * [https://gist.github.com/bjhess/2726012](https://gist.github.com/bjhess/2726012)
* Receive POST payload
   * [http://requestb.in/](http://requestb.in/)
* Github API 
   * [http://developer.github.com/guides/getting-started/](http://developer.github.com/guides/getting-started/)
* Github Hooks Api
   * [http://developer.github.com/v3/repos/hooks/](http://developer.github.com/v3/repos/hooks/)
* Deploy via hooks
   * [https://github.com/markomarkovic/simple-php-git-deploy](https://github.com/markomarkovic/simple-php-git-deploy)

