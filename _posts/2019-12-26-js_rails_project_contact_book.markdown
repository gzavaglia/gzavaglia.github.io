---
layout: post
title:      "JS+Rails Project: Contact Book"
date:       2019-12-26 19:38:47 +0000
permalink:  js_rails_project_contact_book
---


## The App
The app that I decided to build is a single page application that compiles all your contacts in a list. Every contact is a JS object with a name, a phone number and an email address. So this is basically a web version of what you would find on your phone or on Outlook when you tap or click on contacts. 
At the top of the app there's a form to enter a new contact, if you double click on the phone or email you'll be able to edit it and there's also a *delete* button under each contact that removes the contact from the list. 

## The Process
### Backend: Contacts-API
The Application Programming Interface (API), is a way for one system to communicate with other external systems. An API abstracts away any of the complicated logic of a system and presents the data in a way that's easier to interact. 
In order to create a new API, I start by creating a new Rails application. The key is to create the new Rails app using the **API Flag** so it doesn't include the views and all other stuff that comes with Rails that we're not going to use for this application. 

Once all my directories and dependecies are created, it's important that before I do anything else, we take care of the Cross-Origin Resources Sharing, since we're going to be making requests to this API from other sites. Now we can start creating the database, controller and model. 

My controllers is going to have all the actions and methods that a regular Rails app: #index, #show, #update and #destroy. The thing we're doing different than a Rails app here is that what we render in all of these cases is a JSON object and the status of 200. After creating the controllers, I go ahead and add my Routes, using #namespace to account for the API-Version 1 I just made. 

Now, I need to create my Model and Database. My Contacts DB is going to consist of three strings: **name, phone, email**. And my Model is just going to adopt (thank you, Rails) from this database all the constructors for these attributes. Special shout-out to *Faker* the gem that I used to seed my database using names of queens from Rupaul's Drag Race, and also phone numbers and email addresses. 

As the database gets seeded and the routes, controller and models are working, it's time to move into the Frontend of our app.

### Frontend: JavaScript

Our Backend and Front end are both going to have separate folders within our contact app. I'm going to use my Frontend to talk to my API and present the information. 

The first stop is our `index.html` file which is the main display of our information. Inside our index, we'll provide the user with a form where they can fill out to create a new contact. I'm also going to create a `contacts-container` which I'll use to push all my new contacts in to display to the user. 

There are several source files that will be called in our `index.html` file, but the first one is our `index.js`. The only responsability this file has is to trigger the whole app. There's not a lot going on in this file but just assign a random variable to be a new instance of an App class. 

Once the base is set with my index.html, index.js, I'm off to create my Adapter: The ContactsAdapter.js is what's responsible to talking to my API sending all the GET, POST, PATCH, DELETE requests. Our only attribute for the ContactsAdapter class is going to be the bases URL which in our case is `http://localhost:3000/api/v1/contacts`. 
Now we need to start break the ice and start the communication with the API. We need to have methods to get our contacts so we can render them (#getContacts), create new contacts (#createContact), create new ones (#createContact), edit and update them (#updateContact) and delete them (#deleteContact). 
The adapter is going to be our middleman between the backend and frontend. 

Our frontend also host a file called App.js, which its job is to create a new instance of the Contacts class to put all the information inside the app. 

The big file in all this is my `contacts.js` file. This file will be the one tasked to do all the heavy lifting. I create a new Contacts class with an attribute of an empty array where all my contacts are going to live; an adapter instance of the ContactsAdapter class that we will use to bring in all the information; and a method of Event Listeners which should be triggered as soon as we open the page and our app is loaded. 

Most of the methods within the Contacs class are going to utilize the adapter to return the promises of JSON objects and display them in any way we want to. Each one of these methods will start with an event listener, this triggers an action from our contacts.js, this action is going to be a method specially built to handle this action. For instance, clicking on "submit" within the new contact form will kick off a fuction where the information will be passed to the server through our adapter, the adapter will make the post request, give back the promise, then the new contact will be added to the contacts array and rendered.  Other events listeners in my app are: double clicking will trigger the edit function and clicking the delete button will result on delete that contact. 

The render function in my app is what creates each contact within the inner HTML of the `contacts-container` . In my app, a user would be able to see the names of the contacts and with a click on the gray area where the name is shown, it will toggle a menu that will open down and show all of that contact's information (phone number and email).  

```
for (i = 0; i < coll.length; i++) {
          coll[i].addEventListener("click", function() {
            this.classList.toggle("active")
            var content = this.nextElementSibling
           if (content.style.display === "block") {
              content.style.display = "none"
            } else {
              content.style.display = "block"
            }
          })
        }
```

Lastly, the `contact.js` file (Contact class), it's what's going to be responsible of creating a singular instance of a Contact class and displaying it as a "Contact" type of object. 

#### Editing a Contact and Patch Requests
There are two event listeners we have to look for to do a successfull patch request. If we break it down, the user will use the first even listener (double-click) to select what they want to edit, when the user does this, the content will turn "editable" and the user will be able to modify the contact information on the screen. The update request, is taken care of by when the user hits the key **ENTER** on their keyboard, by doing this, information should be saved and the update request is successful.

#### Deleting a Contact
While creating my contact list, I added a delete button at the end of each contact. Once the user clicks on this button, the adapter will send a delete request to the server deleting the contact from the database. Meanwhile, in our frontend, the contact will be deleted from the contacts array and the updated list will be rendered. 

## Conclsion 
This was a fun and educational project, it's nice to see different alternatives to render information and make this information render in an easy and fast manner. The thing that I learnt the most while building this app is the way we're communicating with the server and how with its help we can manipulate the information. 



