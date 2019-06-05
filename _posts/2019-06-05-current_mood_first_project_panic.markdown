---
layout: post
title:      "Current Mood: First Project PANIC"
date:       2019-06-05 19:32:18 +0000
permalink:  current_mood_first_project_panic
---


Is that time of the year, you get your first project and you stare at the blank page (or maybe black in this case) of your text editor and the panic hits you: WHERE DO I START? 

The project guidelines are *simple*: 

1. Provide a CLI 
2. The CLI application must access to data from a web page.
3. The data provided must go at least one level deep.
4. You should never scrape the same thing twice. 

That's it. Pretty straight forward right? WRONG. 

### What's a good topic to base my project on? 

I wanted to do something fun, something that I can see the use of on a daily basis. I play Pokemon and Pokemon-GO daily, so I thought to myself why don't we make a Pokedex? and eventually, catch pokemons and be able to return them to the player. 

So off I went, with a brain full of motivation and no idea how to implement them. 

###  The perfect scrapee 

I tried to look for the perfect match that would allow me to find a list of Pokemon and their details. After a while, I decided to go with the website https://rankedboost.com/pokemon-go/pokedex/. Their code with simple enough for me to both understand it and be able to do all my scrapping. 

Now is where the coding starts. I figured until I have a hash with each Pokemon and its details I can't really do much else and the #PokemonScraper class was born! I used Open-Uri and Nokogiri gems to scrape the website and parse it. 

I needed a method that was able to locate the name of each Pokemon and the href link that would take me to each Pokemon's detail sheet. 
To be 100% honest, the css selector topic was not settled in my brain before I started this project. It was hard for me to see, so I decided to start doing more research about it and play more with it before working on my scraper for the project. The two links that were the most helpful where https://dev.to/kreopelle/nokogiri-scraping-walkthrough-alk and http://ruby.bastardsbook.com/chapters/html-parsing/. Specially the first one. After reading and playing with different css selectors I was ready to start playing with my project. My selectors were pretty straight forward: 

```
take_num = 10
pokemon_names = parsed_pokemon.css("span.PokemonName").map(&:text)
reduced_pokemon = pokemon_names.take(take_num)
```

The second line of code is what gets all the pokemon names from the website, maps them and saved them into the "pokemon_names" array. The last line of code reduces my pokemon array by taking only the first (10) pokemons from the original array and saving them into the "reduced_pokemon" array. I'm doing this because I'm trying to avoid the program to run super slow by taking all 400+ pokemon. But if I want I can change that number ("take_num") and set it as anything I want, and then I can grab as many pokemon as I want. 

Once I was able to grab all the Pokemon names, I needed to move on and grab the link that will take me to the next level of scraping and reducing it:

```
array_links = []
parsed_pokemon.css("span.pokemontypeImgs a").collect do |pok_link|
  array_links << pok_link.attribute('href').value
end
reduced_link_array = array_links.take(take_num)
```

Now I have what I needed, so I can save all this information inside a hash with :name and :profile_url as attributes. 

For the links I used a different iteration method (longer) because I wanted to grab each pokemon go href attribute and save it in its own array. 

I wanted to see which way would be the better way to scrape the second level. I realized that doing in the same method would be a little messy, so I went on creating a new method within my #PokemonScraper class called #.scrape_link, that would take an argument of a link as a String, and return a hash containing the pokemon information: generation, pokedex number, type and a short description. 


```
def self.scrape_link(pokemon_link)
  poke_profile_html = open(pokemon_link).read
  parsed_poke_profile = Nokogiri::HTML(poke_profile_html)
  pokemon_info = parsed_poke_profile.css('.PokemonIDDiv').text
  pokemon_number = pokemon_info.split(" - ")[1].delete("#").to_i
  pokemon_gen = pokemon_info.split(" - ")[0]
  pokemon_info_type = parsed_poke_profile.css('th.PokemonWeightText').map(&:text)
  pokemon_type = pokemon_info_type[1]
  pokemon_description = parsed_poke_profile.css('td.PokedexEntryText').text
  pokemon_card = {:pokemon_generation => pokemon_gen, :pokedex_number => pokemon_number, :pokemon_type => pokemon_type, :description => pokemon_description}
end
```

pokemon_info would return "Gen 1 - #1" because that's the way thats set on the website so I had to basically split it to utilize the two pieces of information as pokemon_gen and pokemon_number. Something similar happened with the pokemon type. The string was "contaminated" with other pieces of information that was not relevant to us so I had to clean it up. 

### Pokemon Class: the factory

Now that we have the information settled in hashes, I can finally begin coding the pokemon class. This class is responsible of creating Pokemon objects, saving them and adding attributes to it. 

We initialize our class using *Metaprogramming* so it'll be easier to create all the attributes that we need. We also have our accessor methods for @name, @profile_url, @pokemon_generation, @pokedex_number, @pokemon_type & @description. 

To create new objects I initialize using a pokemon Hash, and iterating through it to create each attribute and also saving each object into its all. 
```
def initialize(pokemon_hash)
    pokemon_hash.each {|key, value| self.send(("#{key}="), value)}
    @@all << self
  end
```

I also need a class method that would creat a pokemon from whatever I have on the pokemon array and another class method that would add the details of the 2nd level of scraping to each pokemon. (plus a #.all method to get 'em all!)

```
  def self.create_from_pokedex(pokemon_array)
    pokemon_array.each do |pokemon|
      self.new(pokemon)
    end
  end

  def add_poke_card(poke_card_hash)
     poke_card_hash.each do |key, value|
      self.send(("#{key}="), value)
     end 
  end #end pokecard
```

### Gotta Catch'em All!

Now it's time to start defining our CLI class. 

We need class that would make the Pokemon objects and another one that would add the details to each pokemon. For these classes, we're going to be calling both PokeScraper and Pokemon classes. 

```
@@pokedex = []
BASE_PATH = "https://rankedboost.com"
  def make_pokedex
    pokemon_array = PokeScraper.scrape_pokemon
    Pokemon.create_from_pokedex(pokemon_array)
  end

  def add_details
    Pokemon.all.each do |pokemon|
      deets = PokeScraper.scrape_link(BASE_PATH + pokemon.profile_url)
      pokemon.add_poke_card(deets)
    end
```

To make_pokedex we're going to be calling our scrape_pokemon class, where we get our info from the website and we return an array of Hashes with the pokemon links and information. After that we pass this array of hashes into our Pokemon class method #.create_from_pokedex. 
To add details, we need to find all the pokemon that we just created, iterate over the @@all array, and add one by one its details. 

Now we can start creating the behavior of our program. 

1. I want the program to welcome the user.
2. The user should be able to catch a random pokemon and save it into it's pokedex... But wait, there's more! We would also like the user to be able to read the pokemon that they just caught information if they want to.
2. The user can see all the pokemon that are available to catch.
3. Also they should see their pokedex with all the pokemon they caught so far.
4. Exit entering the command 'exit'. 

For all these possibilities I used the "case" staments. 

```
def run
    make_pokedex
    add_details
    puts "Hello! Welcome to Pokemon-NO-GO"
    puts "What would you like to do?"
    puts "'catch': to catch a new pokemon"
    puts "'see all': to see all catchable pokemons to catch"
    puts "'pokedex': to see your current pokedex"
    puts "'exit': to quit"
    user_input = gets.chomp

    case user_input
      when 'catch'
        catch_em
        run_again
      when 'see all'
        show_all
        run_again
      when 'pokedex'
        show_pokedex
        run_again
      when 'exit'
        return
      else
        run_again
    end
  end
```

For the #catch_em method I wanted the user to be able to catch a random pokemon from the whole list of pokemon that we have and save that pokemon in a new array called @@pokedex. Also I wanted to prompt the user if they wanted to see the details of the pokemon they just got and how their pokedex looks right now. 

```
  def catch_em
    rando_poke = Pokemon.all.sample
    puts "Nice throw! You caught #{rando_poke.name}!"
    @@pokedex << rando_poke
    puts "Would you like to know more about #{rando_poke.name}? y/n"
    user_input = gets.chomp
    if user_input == "y"
      puts " - Belongs to: #{rando_poke.pokemon_generation}"
      puts " - Pokedex # #{rando_poke.pokedex_number}"
      puts " - Type: #{rando_poke.pokemon_type}"
      puts " - Details: #{rando_poke.description}"
    end
    puts "Would you like to see your pokedex? y/n"
    user_input2 = gets.chomp
    if user_input2 == "y"
      show_pokedex
    end
    
    end
```

The rest of my methods are just to be able to show all and also to show the current state of the pokedex:

```
def show_all
    Pokemon.all.each do |pokemon|
      puts "#{pokemon.name.upcase}"
      puts " - Belongs to: #{pokemon.pokemon_generation}"
      puts " - Pokedex # #{pokemon.pokedex_number}"
      puts " - Type: #{pokemon.pokemon_type}"
      puts " - Details: #{pokemon.description}"
    end
  end

  def show_pokedex
    @@pokedex.each do |pokemon|
      puts "# #{pokemon.pokedex_number} - #{pokemon.name}"
    end
    #run
  end
```

And that, so far, it's my first project! Now I need to clean it up, and make sure it looks presentable. Fingers crossed!!

# #end 




