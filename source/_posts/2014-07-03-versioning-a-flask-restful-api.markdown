---
layout: post
title: "Versioning a Flask-Restful api"
date: 2014-07-03 09:04
comments: true
categories: 
---

*Disclaimer*: This is not necessarily the *best* way to version an api through flask-restful, it is simply *one* way.

There doesn't seem to be a standard way to do this in flask-restful. I would recommend watching this video to see how its done in ruby on rails, as you can almost re-create this in python: [http://railscasts.com/episodes/350-rest-api-versioning](http://railscasts.com/episodes/350-rest-api-versioning)


Project Structure
    repo
      |--app
      	__init__.py
      	urls.py
    	|--common
            authentication.py
            utils.py
        |--models
        	user.py
        	article.py
        |--views
            |--v1
               users.py
               articles.py
            |--v2
               users.py


urls.py

	#V1

	from views.v1.articles import Articles
	from views.v1.users import UsersV1

	api.add_resource(Articles, '/<string:version>/articles')
	api.add_resource(UsersV1, '/v1/users')


	# V2

	from views.v2.users import UsersV2
	api.add_resource(UsersV2, '/v2/users')


The benefit you get from doing it this way is that now you can hit `/v5/articles` and it will still go to the original articles.py view file. This means you don't have to copy over articles.py to the v2 folder once you create it, you only have to create files for things that will change. 

The downside is that you may have a `/v2` folder with updates to users, a `/v3` folder with updates to just articles and a `/v4` folder with just updates to a yet-to-be-created view. But thats what command+T is for in Sublime :) You'll also have to rename your classes from `Users` to `UsersV1/UsersV2` everytime you switch from a single file to a versioned file. 


*P.S. For brevity I left most of the files out of the project structure, you will still need a `__init__.py` file in your views/, v1/, v2/, models/, etc.. folders*
