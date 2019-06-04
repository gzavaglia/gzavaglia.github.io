---
layout: post
title:      "Music for Coding"
date:       2019-06-04 22:02:10 +0000
permalink:  music_for_coding
---

The Music Library CLI program was one of the hardest I've encountered so far. And it makes sense, we're finally putting together all the pieces that we've being given in order to create something that is familiar to us. However, when I started coding this program, it didn't make much sense what I was doing exactly so I thought of breaking it down here to give myself a better understanding of it.

The basic concept for this program was to create a Music Library, like an super early version of an iPod, where you'd be able to create songs and group them by artists and genres and even display and play songs! So how do this make it work? As one of my mentors always told me break each step into smaller steps and then put everything together...

How do we eat an elephant? ONE BITE AT A TIME. 

So I hope you're hungry, because this is going to be a big elephant we need to digest!

Let's start with something easy, setting up our classes: #Song, #Artist and #Genre. Let's give them attributes and basic methods such as `self.all` and `self.destroy_all`. 
That was not that bad, wasn't it? Good! 

However, even in this trivial start we ran into our first issue: the custom setter methods.

> ... when we call our custom Song#artist= method, it sets the song's @artist property and adds the song to the artist's collection of songs. When you reach these tests, make sure those setter methods are only invoked if Song#initialize is called with artist and/or genre arguments. Otherwise, the @artist and/or @genre properties will be initialized as nil, and you'll have some unexpected consequences in both your code and the test suite.
>

so for this we have to make a custom method to set our #artists and #genres: 
```
def initialize(name, artist = nil, genre = nil)
    @name = name
    if artist != nil 
      self.artist= artist
    end
    if genre != nil
      self.genre= genre
    end
```


Oof! OK. Now let's move on to bigger things. 

Now we need to implement two methods within the Song class: #find_by_name and #find_or_create_by_name. This is actually not that bad. This methods should be familiar to us from previous lessons. I would like to give a shout out to Micah for this one, since he taught me a cool, sweet way to use "Short Circuiting" in order to get the second method. 

```
def self.find_or_create_by_name(name)
    self.find_by_name(name) || self.create(name)
end

```

Fancy huh? 

Now this is when the elephant starts to feel heavier and heavier. Although this part is here to train us to get things done in an easier, faster way, is still new so of course we're going to feel nervous about tackling this part: the use of modules. The idea behind a module is that there's no need to type and re-type the same methods over and over for each of our classes that are going to be using it. Instead let's just type it once and then call it from our classes! This way we can relate classes that are not 100% correlated. We can do this by creating the Module and then extending it or including it in our code, like I did on the Genre and Artist classes. This way they are actually acting in the same way as the #find methods explicitely written for the Song class!

```
extend Concerns::Findable
```

```
module Concerns
  module Findable
    def find_by_name(name)
        self.all.find{|something|something.name == name}
   
    end
  
    def find_or_create_by_name(name)
        self.find_by_name(name) || self.create(name)
    end
  end #end Findable
end #end Concerns 
```

As we see here, I would like to note that the `self` keyword in this case will act as the once we extend it self will be either Genre or Artist class. 

The next step is to create a MusicImporter class. This class will (1) go inside a file (#files) and (2) create a new songs from the file name (#import). 

Now for the grand finale: the actual CLI class - the MusicController 

This is the class that we're going to run to communicate our program to the user. 

First we need to welcome them! Of course, no one likes a rude program! And then we need to ask the user what would they like to do with it. We have options to list all songs, artists, genres, list songs by artist and genres, and also they can ask the program to play the song! But the most important thing, we need an exit. 

after we prompt all the options to the user, we would like our program to recognize the input and provide the user with the desired action. I used the "case" iteration for this and for each string that our user is typing we have a statement in that will call a method to do an specific action. 

And that's pretty much it! We kinda finish our elephant. Probably not in the fastest way, but we'll get better and eventually we can start digesting whales. 

