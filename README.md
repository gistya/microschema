# microschema
a concise notation for data and object modeling (YAML-inspired)

Intro
-----

**microschema** uses YAML’s structure to represent the relationships within the data model. However I am not sure it’s 100% YAML compliant because it uses special characters in weird ways. I may formalize this further in the future.

Also, I don’t expect you to use this, or even to try to understand it, because it’s very peculiar. However if you want to adopt this, I’m down with that.

The main purpose of micro-schema was to have a plain-text representation of a schema in as compact and human-readable a format as possible, so that it could fit into boxes in a flow chart AND be able to be tracked in git. The only downside to this approach is that it makes use of extended characters, as you will see, but I find that makes it more readable, and heck, it’s 2015, not 1975. We CAN use more than just base64 characters now. We have unicode. We have touch-screen keyboards with arbitrary character sets. It’s OK. Really.

*Note: the attached diagram won’t make much sense to you until I explain certain things, and it’s kind of weird, but I’ll be glad to answer any questions you have about it. Please don’t judge until you know my reasons; there are certain aspects of that which were not done how I would have preferred but there were reasons for that :D*

If you’re not familiar with YAML, Google will find JSON to/from YAML converters.

I unabashedly took artistic license choosing which symbols to use for which types, mostly to make **microschema** visually pleasing and as compact as possible. I put as much thought as possible into each choice (see the FAQ) and am prepared to justify and defend my selections. Ultimately it does come down to artistic license, though, so I don't expect you (or even myself) to *agree* with or be happy about these choices. At the end of the day, we must, each of us, make decisions that are not always the ideal; perfection is impossible. However, because I did take artistic license (and, perhaps, poetic license) in creating **microschema**, therefore I release this work under the Artistic License 2.0. 

Example
-------

Here is an example invoice in YAML from www.yaml.org/start.html:

    invoice: 34843
    date   : 2001-01-23
    bill-to: &id001
        given  : Chris
        family : Dumars
        address:
            lines: |
                458 Walkman Dr.
                Suite #292
            city    : Royal Oak
            state   : MI
            postal  : 48046
    ship-to: *id001
    product:
        - sku         : BL394D
          quantity    : 4
          description : Basketball
          price       : 450.00
        - sku         : BL4438H
          quantity    : 1
          description : Super Hoop
          price       : 2392.00
    tax  : 251.42
    total: 4443.52
    comments: >
        Late afternoon is best.
        Backup contact is Nancy
        Billsmer @ 338-4338.

So, in **microschema**, to represent this abstractly we would do:

    invoice: #
    date: ™
    bill-to: ?
        given: ?
        family: ?
        address:
            lines: |
                ?
                ?
        city: ?
        state: ?
        postal: #
    ship-to: ?
    product: &
        - sku: ?
          quantity: #
          price: #.#
          description: ?
     tax: #.#
    total: #.#
    comments: >
        ?

Use Cases
---------

I made **microschema** while refactoring a data model for an e-commerce enterprise application, as I migrated it to using Doctrine MongoDB ODM for PHP (to which I once contributed). 

My team is now using **microschema** to track our data model over time in a project that syncs an Apple iOS/CoreData/SQLite3-based ("ICS" stack [?]) client with a Linux/Apache/MySQL/CakePHP-based ("LAMP" stack) server. 

FAQ
---

Most of these questions are ones that I frequently ask myself about microschema.

   - Q: Why do you call it "YAML-inspired"? 
   - A: Because it's not YAML-compliant. 
  	 
   - Q: Why isn't it YAML-compliant?
   - A: Because YAML won't let it be. YAML has all kinds of not cool restrictions on what characters you can use for what. Put a : or # in the wrong place and you're screwed in YAML. So.. microschema looks like YAML and follows YAML's structuring rules but it's not YAML. MAYAML (microschema ain't YAML).

   - Q: Why use the ? symbol used instead of the $ symbol for strings? 
   - A: To most people, $ implies currency. Also, it's a commonly used symbol at the start of variable names, and I wanted to leave open the option to put in the actual variable names *including the $ sign* so I could distinguish variable name from descriptions. 
   
   - Q: Why use the ™ symbol for date/time? 
   - A: TiMe :D If it's just a float or double in your DB, you could just use #.#, however in Core Data we use NSDate not NSNumber for dates. So it's a different type.
   
   - Q: Why use the & for collections/arrays?
   - A: The & symbol traditionally means "and" in English. Arrays are collections of this and this and this and this and this. And this. Etc. 
   
   - Q: I see you have && representing "hashed by array"... but what does "hashed by array" mean? 
   - A: It means using array as the keys in a hashed array (AKA dictionary)
  
   - Q: Why would anyone ever use arrays as the keys of an array?
   - A: "Theirs not to reason why, Theirs but to do and die." -Tennyson
   
            /* E.g. in Swift: */
            
		        var theChargeOfTheLightBrigade:Dictionary =
	        	[ ["Theirs","not","to"]:"reason why",
	        		["Theirs","but","to"]:"do and die" ]
        		var Tennyson:String! = TheChargeOfTheLightBrigade[["Theirs","but","to"]];
		        print(Tennyson) /* output: do and die */
   
   - Q: Why use the % for boolean values?
   - A: The % symbol is another ligature combining the solidus / symbol, which traditionally meant "or" in English usage, with two zero signs. We derive our meaning from the solidus "or", and we use the two circles as representatives of the two possible states: *TRUE or FALSE*.   
   
   - Q: Is there a way to distinguish floats from doubles?
   - A: You could use #.## if you want.
   
   - Q: Is there a defined way to distinguish 32-bit from 64-bit types?
   - A: No, but you could always make a pull request. I look at this as more of an implementation detail. I suppose you could append -32 or -64 or something like that to a symbol.
   
   - Q: Is there a defined way to represent Endianness?
   - A: No, and I'm open to suggestions and pull requests.
   
   - Q: What does "+name embedded doc" mean?
   - A: By "embedded doc" I refer to a MongoDB embedded document (see official explanation [here](https://docs.mongodb.org/manual/tutorial/model-embedded-one-to-many-relationships-between-documents/)). Obviously we could use this for any type of DB, not just MongoDB, but I mention that one becaues it's a common design pattern for MongoDB. Using the address example from that link, in microschema we would have this:
           `addresses: & +address`, where the property "addresses" is an array of embedded documents that conform to the "address" schema
   
   - Q: What's the point of the • symbol? 
   - A: For hashed arrays/dictionaries, the • symbol is an optional spacer that means "hashed by." It goes in between the types, so, &•? means an array hashed by strings. The • also comes into play later when you want to write a description of the sources of the values using √ (see below).
   
   - Q: What's the point of the √ symbol, "value of"? 
   - A: When describing the contents of a dictionary/hashed array type, it is useful to know not only what type is used for the key, but also what property is the source of that key. For example imagine a dictionary for looking up the names of ranges of car prices (see Swift example below). If we have a *usedCategories* object with a property called *priceRange* and another property called *categoryName*, then we could describe the following dictionary in *microschema* as `carPriceRangeDescriptionLookup: &•& √categoryName•√priceRange`. I chose • so it could never be confused for dot syntax or carrot syntax.
           var carPriceRangeDescriptionLookup:Dictionary = 
           [   [1,499]          "junk"
               [500,4999]     : "hoopty"
               [5000,9999]    : "100k plus club"
               [10000,14999]  : "pretty nice"
               [15000,34999]  : "pre-owned"
               [35000,9999999]: "jay leno"        ]

We could also put regex in brackets after things to signify validation etc.
