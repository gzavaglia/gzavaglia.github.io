---
layout: post
title:      "Project #2: Let Sinatra Sing!"
date:       2019-08-16 12:49:51 -0400
permalink:  project_2_let_sinatra_sing
---


The first thing we need to do when starting a (/any) new projects is to sit down, and read carefully what it's asked of us. We need to know the requirements in order to know where to start, how to start and what we need to do before we can focus on the aesthetics of it. 

For our **Sinatra Portfolio Project** we had our list of requirements:

1. Build an [MVC] Sinatra application.
2. Use [ActiveRecord] with Sinatra.
3. Use multiple models.
4. Use at least one `has_many` relationship on a User model and one `belongs_to` relationship on another model.
5. Must have user accounts - users must be able to sign up, sign in, and sign out.
6. Validate uniqueness of user login attribute (username or email).
7. Once logged in, a user must have the ability to create, read, update and destroy the resource that `belongs_to` user.
8. Ensure that users can edit and delete only their own resources - not resources created by other users.
9. Validate user input so bad data cannot be persisted to the database.
10. BONUS: Display validation failures to user with error [messages]. 

It seems like a handfull at first but the key is to break everything into smaller steps. 

The first thing we notice is the fact that we need to create an MVC application. This is like the most general thing that we've being doing for all our Sinatra Labs, we not be (that) scared of it. 

MVC stands for: Model, View, Controller. Easy enogh. This is just the way of thinking the structure of our app. This is when the **Separation of Concerns** principle comes along. Each one of the MVC items is going to take care of something specific for our program. If we wanted to, we could write our entire code in just one file! But we're nice, we don't want to do that! That would make our program very difficult to read, or troubleshoot/debugging. So instead, we split things up. 

We have our Models: this is the logic behind our applications; Views: this is the facade of our program, this is what the user sees and interacts with; Controllers: is the relationship between the Models and the Views. This way of structuring our program is very helpful: faster developement process, we can provide multiple views, support of different langunages (HTML, CSS, Ruby, ERB, SQL), easy to read and debug. 

Here we have our first step self explained. After creating our github, we needed to set up our MVC folders! Moving to Item #5 of the project requirements, we can get our first set of MVC!. We know we're going to need users, so we need to create a model that creates our users. We first need to create a table through rake with the properties that we want our users to have. In my case, I want users to tell me their *names*, pick a *username* and *password*. Once we create the migration through rake and we implement it TADAH! We have our table. By inheritance we can set up our mode to get all these properties from our table! My User model is going to be able to have a User.name, User.username and User.password. *NOTE: Because of security we needed to implement a way to encrypt the user's passwords. We use bcrypt for this, and instead of creating  a table with a "password" property, we name it "password_digest" Now adding the method `has_secure_password` to the User model, we ensure that any user creates is going to be safe in our hands.*

After we have all these set up our welcome index page where we ask the user to either Sign Up (re route user to a /signup page) or Log In (re route user to a /login page).

For Loging in, I set up a new form that accepts Username and a Password. The app would them authenticate this two to make sure it matches and if it does the website will redirect you to the home page. To make sure we're logging in correctly I've created the class variable @failed, if the variable is false it means we logged in successful if it turns to true, it means there was an error. 
To authenticate and to be logged in, I used helper methods `logged_in?` and `authenticate`. The first one will locate a user and assign the session to them, while the second one makes sure that if the user didn't log in succesfully it will redirect him back to the login page to try again. 

For Signing Up, I've created a new form asking for users to enter their name, username and password. Through some of the methods facilitated by ActiveRecord, I was able to set up simple statements to get all the user's information validated before letting them create an account! Through the validates methods, I'm making sure the username is actually giving a name, a username and a password and also that their username is unique. 

Once the user signs up, they'll be saved into the database and redirected to their home page. 

Here's where the second part beggins. Once the user was abble to log in/ sign up successfully we need to establish the functionality of our program; A.K.A. *what do I want my program to keep track of?* A.KA. my **resource**: in my case I created an app that will keep track of your favorite TV shows. So I created a new model more my shows called *Show*, the properties that a show has are: title, channel, number of seasons. So I created a new table to store all the content! But wait, I also need to add a user_id column to keep track of which user created a show. After I made my migrations I'm ready to make sure my two models relate between each other; this is when `has_many` and `belongs_to` comes into play. A user *has many* shows, and each show will *belong to* a user so I can show each user a the shows they've been creating since they first signed up. After creating the relationships, I utilized the `rake console` to be able to see how to request the information that I want to show my user. The helper method `current_user` is really a life saver for this project! Through this method, I'm able to find a user an return the session so all the shows that the same use created they're now returned to the user even if they log out. 

**CRUD** is the base for this project. Creat, Read, Update, Delete. This is what the app does. Litterally. Now that my user it's in their home page, they're going to be able to chose if they want to see their shows, create (add) a new show to their list, edit their shows or even delete it! To create we set up again a new form. Asking for a user to give their new show a Title, the number of Seasons it currently has and the channel its in. My user will `get` the form asking for this information and `post` it into the *shows* database. If they want to edit their information, the user would be able to click on the hyperlink of each show in the shows/index page, see the information an pick what they want to change. Say your show change networks like Brooklyn 99, or a new season just popped up: Just click the show, go into edit and you can change the information through patching. The navigation should be easy since on your browser you can actually *see* what your doing: if you want to get into a specific show without clicking, just go into shows/*id* and you'll see your options. This app should be RESTful, meaning when your application receives an HTTP request, it goes into that request and identifies the HTTP method and URL, connects that with a corresponding controller action that has that method and URL, executes the code in that action, and determines which response gets sent back to the client. In other words, a RESTful app is the one that breaks down every method: we use GET to retrieve a resource;  PATCH/PUT to change the state of or update a resource; POST to create that resource ; and DELETE to remove it. Why do we care about this? REST logic leverages less bandwidth, making it more suitable for internet usage; also brings up the idea of the *black box* where regarless of what's in it, you can ask for it resources. 

### How does Sinatra uses CRUD? 

We're going to start off from our *index*  which is where a user will be able to see all the objects (`Show.all`) that were created by them. On my website once you're logged in, you'll get redirected to your home page where you'll be welcomed and you'll be able to select what would you like to do. Your options? View all your shows, create new or log out. If you'll hit to view your shows you'll be redirected to this index we were talking about an it will give you a list of your shows and you'll be able to select any of them to view it's details, edit or delete.  

**Create**: We can click on the link to create a new show which will generate a `get` request and present the shows/new form. The user will now complete the form entering a title, seasons and channel and hit the Submit button. When they do that, the user is sending a post request to add an item into our data base. If everything is correct, the user is redirected to the /shows page where they'll be able to see the previous shows they've entered and the recently created show. To access the parameters to set our Show object's information, we use `params[:something]` where something is any of the params used for our shows (title, channel, seasons).

**Read**: When the user is in the shows page they'll be able to click on any of their shows to view its information. This is what we call "read". Clicking on any of the shows, the user will be redirected to each specific show thru the RESTful route '/shows/:id' where the :id is each individual show's id number on the database. To display this information embedded Ruby (erb) becomes very handy. On our show.erb page, we're going to utilize a mixture of Ruby and HTML to be able to display to the user the information saved into that specific show. 

**Update**: Once we're on an individual show page, we're going to be given the option of edit this item. If we click on "edit", we'll send a `get` request to the route  '/shows/:id/edit' and we'll be presented with the edit form. That's the first action. The second action is through Submit, sending a `patch` request to the '/shows/:id' route. The action ends with a redirect to the specific show show page. 
*Note about Put vs Patch. In this project, I used patch. What's the difference? The kind of similar but one is newer than the other. When we use PUT, we're basically creating a new object and swapping this new object with the old's ID number. When we use patch we're actually modifying the original object. *

**Delete**: Another option the user sees when they go into our '/shows/:id' route is the `DELETE` option. The form is submitted via a DELETE request to the route /articles/:id. This action finds the article in the database based on the ID in the url parameters, and deletes it. It then redirects to the index page /shows.


To log out,  once you click on the hyperlink, it will `get` the /logout request, clear your session and redirect you to the index page. 

This project was eye opening in many ways. I have to admit before starting to work on it, I didn't have a clear grasp of everything that was going on. It was very confusing at time having to switch back and forth between views, models and controllers in order to be able to complete something. Multiple times, the debugging techniques consisted on going to one of the MVC items because I forgot to change a post request or a method's URL in order for it to work. But thanks to all this struggle I think now I feel way more confident and I see the importance of MVC, CRUD and REST on an application. 

*Disclaimer: I wanted to add the flowchart that I made while figuring out all the different routes... However, I still can't figure it out how to add images to this thing! Hopefully I can figure it out soon* 









