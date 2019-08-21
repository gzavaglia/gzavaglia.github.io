---
layout: post
title:      ".select method"
date:       2019-08-21 14:54:47 +0000
permalink:  select_method
---


The idea behind this post is to explain the importance of knowing your shortcuts. If you know this, it'll save a lot of time and words typed. 

This started when I was asked to refactor my app's code to be able to return all the users that like "x" tv show. So I have my users and my tv shows. So first thing I started was doing my .each method. The each method it's find but doing a little more research, my code could be much more efficient. 

I ran into the `#.select` method. This methods will take either a hash or an array and (litteraly) select the items part of your array that meet the condition you give it. If you have an array of numbers from 1..10, and you'd like to get the even numbers, you could just say:

`all_numbers.select |number| do 
number.even?
end` 

What this does is returns an array with ONLY the even numbers. 

Now how to apply this in my case?


    users = User.all 
		show = Show.find(params[:id])
    @user_shows = users.select do |user|
     user.shows.any?{|show1| show1.title == show.title }
    end
    erb :'shows/users'

### Breakdown:

The first line gives me the array of all my users, while the second line, finds a specific TV show, based on it's ID number. Next the iteration starts. What I'm basically saying in plain english: "find all the shows from all the existing users, if any of those shows include the show's name that we're looking at, then return true."  Using this, the select method will select those users that return true during the test and add them to an array. 

The last part of the challenge is to grab this array that got returned by the select method and pass it onto the view created to displays all users. For that I set a @user_shows variable equal to the return array. 

Moving to the view 'shows/users.erb', we need to iterate through this array of objects and chose to only display the username of the users that like the show. We can use . each for this and displaying each user object it's user name.
