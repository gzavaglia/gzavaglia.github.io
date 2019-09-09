---
layout: post
title:      "Custom Validations"
date:       2019-09-09 18:58:05 +0000
permalink:  custom_validations
---


Whenever we create an account or log in into a website, we get errors if we don't do what we're supposed to do. For example, if we want to create an account in any website and we select a username that someone else has, the website will throw an error to us to let us know that it couldn't creat the account because the username was taken. Or if we want to log in and we type the wrong password, we would also get an error telling us that we couldn't log in because the password doesn't match. So how does the app knows all these?

ActiveRecord has several solutions to help us *validate* usernames, passwords, to make sure that arguments meet the length requirement, or if something is left blank. The keyword is **validates**. 

We can validate whatever we want! Like: 

```
Class Author < ActiveRecord::Base
            validates :name, presence: true
			validates :phone_number, length: { minimum: 10 }
			validates :name, uniqueness: true
end
			
```

The first line validates that the name is not blank; the second line, takes care of making sure phone numbers have a minimum of 10 characters, to avoid any invalid data in our databases; the las line takes care of the uniqueness of our name, making sure we're not taking any names that are used by other user. 

Not only this, we can make our custom validations for anything we want. In the lab **Rails/ActiveRecord Validations** we're tasked to create our own validator that tells the user if their title is not *clickbait-y* enough. To determine what is and isn't clickbait-material, our app should take the title of a post and look if it contains either "Won't Believe", "Guess", "Top #", or "Secret". If any of those words are missing from a title, an error should be dropped to let the user know. So how do we create a custom validation? 
I created a new helper class within the models folder called `ClickbaitValidator ` which inherites from the ActiveRecord module `EachValidator`

```
class ClickbaitValidator < ActiveModel::EachValidator
  def validate_each(record, attribute, value)
    unless value =~ /(Won't Believe|Secret|Top \d|Guess)/
      record.errors[attribute] << ("Not a clickbait title")
    end
  end
end
```

inside, I overrite the `#validate_each` method: it provides acess to the individual *attribute* being validated as well as the entire *record* and *values* . In the `Post` model we need to provide a flag to expect the new validation class to get to work. 

```
class Post < ActiveRecord::Base
  validates :title, presence: true
  validates :content, length: {minimum: 250}
  validates :summary, length: {maximum: 250}
  validates :category, inclusion: {in: %w(Fiction Non-Fiction)}
  validates :title, clickbait: true
end
```

The last line is what triggers the error if the title doesn't contain the keywords that we told it too. This way, we can create validations for anything we'd like! Crazy right? 
