---
layout: post
title:      "My first CLI Gem"
date:       2019-08-21 18:15:34 +0000
permalink:  my_first_cli_gem
---


Hello, so today I finally finished my first actual running program. I created a Command Line Interface for one of my favorite video games - Dota2. The program gives the user the ability to select a Hero from a list and return a short summary of the Heroes information. The returned information includes -  the heroes name, primary attribute, and the role that the hero is meant to play in the game. 

The purpose of the post is to give you an idea of how I tackled creating this gem.

The first thing I did was pull the data that I needed from the web. I used open-uri and json to scrape the data I needed.

```
require 'open-uri'
require 'json'
class Api 

    def self.get_heroes
        data = open('https://api.opendota.com/api/heroes').read
        JSON.parse(data)
    end
end
```

Next, I created my Hero class and gave it various attributes that I know that I would like to use throughout the program. I created readers/writers for the attributes and also used the initialize method so that each individual instance of Hero will have attributes written into it. Also I utilized a hashed @@all class method because I noticed that in the scraped data, every hero had a unique id. I wanted to use this id when creating my list in the CLI.

```
class Hero
    
    attr_accessor :id, :name, :attack_type, :roles, :primary_attribute
    PRIMARY_ATTRIBUTE_LOOKUP = {
        'agi' => 'Agility',
        'str' => 'Strength',
        'int' => 'Intelligence'
    }.freeze

    @@all = {}
       
    def initialize(details)
        @id = details["id"]
        @name = details["localized_name"]
        @attack_type = details["attack_type"]
        @roles = details["roles"]
        @primary_attribute = details["primary_attr"]
        @@all[@id] = self 
    end

    def roles_string
        @roles.join(", ")
    end 

    def summary
        "#{@name} is an #{PRIMARY_ATTRIBUTE_LOOKUP[@primary_attribute]} hero with a #{@attack_type} attack. This hero is an asset to its team when playing the roles of #{roles_string}."
    end

    def self.all 
        @@all
    end

end 
```

 
At this point my instances of Hero still have not been created. I needed a method to iterate through my data.

```
def info(id)
      Api.get_heroes.each do |hero| 
      Hero.new(hero) 
      end
      Hero.all[id]
    end
```


At this point all of my pieces are exist and it is just a matter of piecing them together. I created my DotaHandler class to handle all the operations and piecing together of my CLI.

```
require "dotacli/version"
require "dotacli/api"
require "dotacli/hero"

module Dota
  class Error < StandardError; end

  class DotaHandler

    def initialize
      line_break
      puts "Welcome to my dotaCLI! In this CLI you will be able to see information on the popular games awesome playable heroes"
      line_break
      puts "Type list to get started!"
    end

    def list
      Api.get_heroes
    end

    def info(id)
      Api.get_heroes.each do |hero| 
      Hero.new(hero) 
      end
      Hero.all[id]
    end


    def start_program
      line_break
        numbered_list
        more_info
        info_again
    end

    def numbered_list
      x = gets.chomp
        if x == "list"
          list.each {|hero| puts "#{hero["id"]} #{hero["localized_name"]}" }
        else
          puts "Im sorry that command is not valid!"
          line_break 
          start_program
          puts "Type list to get started"
        end
    end

    def more_info
      line_break
      puts "To see more information on a particular hero, type in the the number to the left of the heroes name."
      line_break
        r = gets.chomp
        line_break
        x = info(r.to_i)
        if x
          puts info(r.to_i).summary
      line_break   
        else "Sorry that hero doesnt exist"
        end
    end 

    def info_again
      puts "Type y to get info on a different hero.  Type back to see the list again."
      h = gets.chomp 
      if h == "y"
        more_info
        info_again
      elsif h =="back"
        line_break
        puts "Type list to see the list"
        start_program
      else 
        puts "Im sorry that command is not valid!"
        line_break
        info_again
      end 
    end 

    def line_break
      puts ""
    end

    

  end 
end
```

