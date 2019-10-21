---
layout: post
title:      "Rails Project: Make It Or Break It. "
date:       2019-10-21 18:45:36 +0000
permalink:  rails_project_make_it_or_break_it
---

And we're back in the saddle with Rails. One would think that with all the *magic* that Rails provides you with, this project should be a piece of cake, right? **Wrong**. 

To be completely honest, this project, for me, was a struggle. There were a lot of things going on in my life so it was really hard to get a good starting point. I started two projects before selecting the one that felt more insteresting to me. My first project a web app related to my work. I'm an Electrical Engineer, who does electrical design for buildings, so my first thought was to create an app to keep track of the buildings that I work on, through projects. That felt really boring and it didn't really motivated me enough to work on it.

My next idea, and the one that I stuck with, was to create an app to track your favorite workouts. 

### Feel The Burn: Workout Tracker App

The main design of the app is that we have a **User** that logs in and a **Workout** data base with a bunch of workouts. *Through* new **Routines**, a user can add a specific workout to their profile. 

Just by knowing this, I have a huge part of the project done because now, all I need is to generate each resource to accomodate my three main models. Using the Rails Generator for resource will automatically generate for me an ActiveRecord migration with my columns, a model, controller and the views for each one of these items. And let's not forget, it will actually generate a *resouce* on my Rails-routes model. Using the action resource in my routes will basically create all of my RESTful routes for my User, Workout and Routine. This means, taking the Workout model as an example,  I will get my */workouts*, */workouts/:id*, */workouts/new*, */workouts/:id/edit*, *workouts/:id/delete*. Since at this stage, I'm not 100% sure of which routes I will actually need for my resources, I'm just going to keep them all as they are. 

#### Associations & Database

The first step after creating all my resources is to generate the correct associations and to make sure that my database works as I need it to work before stepping into the Front-End of the app.

My associations are as follows: 

* User *has_many* Routines, *has_many* Workouts *through* Routines. 
* Workout *has_many* Routines, *has_many* Users *through* Routines. 
* Routine *belongs_to* User & *belongs_to* Workout.

So basically my Routines table is going to be my Joint table that will create the relations between my User and Workout models. 

What does the *has_many throug* association  gives me? Now, I'll be able to call user.workouts, and see all the workouts associated with this user, and viceversa, I'll be able to call my workout.users to see which user are using which workout. 

Pretty cool so far!

Once I make sure my database is properly working and after creating a couple of workouts and users to try it out, I can move along to the front end of my app. 

#### Front End: Controller and Views.

Workouts:

I started with the Workout controller and views because basically it was the easiest one. It didn't need anything special, just an *index* showing all the available workouts, a path to create a *new* workout and a *show* path to view each workout's details. 

User:

The user part was a little bit more trickier since I needed to be able to create a new *session* to handle the Log In and also a Sign Up page to create a new User.  The sign up part is very standard new/create form, except for the fact that if a user is authorized to sign up, it will create a new session to use while the user is logged in.

There are certain validations that a user needs to have a name, an email and a password, and the email also has to be unique. 

The need to start a new session once the user signs up or logs in tells me that we need to create a Sessions controller, model and views to being able to handle all this. 

Routine:

To start off, this resource should be pretty straight forward as well. However, there's one little thing. I would like my user to be able to go to my nested RESTfull route, to see all the routines that belongs to them. In order to do that, I need to nest the resource inside my users:

`resource :users do `

  `resource :routines`
	
`end`

This will change my routes to, for example, instead of routes_path now is user_routes_path(user). So now there's two things going on for Routines. We need to have a User in order to have a Routine. Also, whenever we want a user to obe able to create a new form, we need to tell our form to choose the current user as the actual user creating the form. 

**Omniauth**

A cool gem introduced in this project is the use of Omniauth to be able to log in using a third party (Facebook, Google, Github, etc). Why is this a good thing? Besides giving the user the option to not have to remember another password, this alternative gives an extra layer of security to our website. Now, our passwords are not our password, since the user is logging in through a third party account, we're basically utilizing that third party security system instead of ours!

In my app, I utilized Facebook. It's a little bit trickier to set up but here we go trying. After we have all the main things set up through facebook and all the environment keys, I created a new method in my Sessions controller called `fb_create`:

` def fb_create

        @user = User.find_or_create_by(id: auth['id']) do |u|
				
            u.name = auth['info']['name']
						
            u.email = auth['info']['email']
					 
            u.password = SecureRandom.urlsafe_base64

        end
				
        session[:user_id] = @user.id
				
        redirect_to user_path(@user)
				
    end
		

`
The password is set by using the urlsafe_base Ruby class. This generates a random URL-safe string to pass our password validation when we create a new user through Facebook. 

## Overview

And finally, once the project takes shape, the end game is that the user will either Log In/Sign Up, be redirected to their account page (show page), from there the user will be able to go to their Routines page, Workouts page, or edit their information. In the Workouts page the user will be able to add workouts if they don't see the ones they want in the index, and add a workout to their favorites. In the routines page, a user will be able to add, edit and delete a routine, changing the workouts, dates or title of the routine. The user can also go ahead and get into each specific workout routine and see each workout's information if they wanted to. 
Once they're done with that, they can just log out, and the session will deleted. 




