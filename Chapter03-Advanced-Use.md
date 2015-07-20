#Advanced Use
##Filters
　　Filters let you use other languages within a jade template. They take a block of plain text as an input.  
　　All JSTransformers can be used as jade filters. Popular filters include `:coffee-script`, `:babel`, `:uglify-js`, `:less`, and `:markdown-it`.
###Template engines
* atpl: Compatible with twig templates
* ejs: Embedded JavaScript templates

###Stylesheet languages
* less: This extends CSS with dynamic behaviors such as variables, mixins, operations, and functions

###Minifiers
* uglify-js: No need to install anything, just minifies/beautifies JavaScript

###Others
* cdata: With cdata we don't need to install anything, it just wraps input as `<![CDATA[${INPUT_STRING]]>` with the standard escape for `]]>(]]]]><![CDATA[>)`

　　You need to install the individual language
>Filters are compile time. This makes them fast but means they cannot support dynamic content.

>Built in filters are not available in the browser as they would not all work. Providing you compile your templates server side, they will still work fine though.

	:markdown
	  # Markdown

	  I often like including markdown documents.
	script
	  :coffee-script
	    console.log 'This is coffee script'
-->

	<h1>Markdown</h1>
	<p>I often like including markdown documents.</p>
	<script>console.log('This is coffee script')</script>

##Blocks
Blocks function like small containers for Jade. Their content can be appended to, prepended to, or replaced entirely. To define a block, simply use the `block` keyword, and then the name of the block, as shown:

	block scripts
-->  `<script src="jquery.js"></script>`

By default, a block will just output the nested content.

	layout.jade:
-->

	<!DOCTYPE html>
###Blocks don't provide encapsulation
Variables defined in blocks can be accessed outside of blocks.

	block example
--> `<p>I was defined inside a block</p>`

##Inheritance
Jade supports template inheritance via the `block` and `extends` keywords. A block is simply a "block" of Jade that may be replaced within a child template. This process is recursive.

###Append
We could simplify this example by using the `append` keyword. You could decide to write `block append` rather than just `append`.

	layout.jade:

	page2.jade (in the same directory as layout.jade)
	
	extends layout
-->

	<!DOCTYPE html>
	
###Prepend
The `prepend` keyword does the exact opposite of the `append` keyword, and also has a longer variant: `block prepend`. It is useful when you want to add something to the beginning of a block.

	page3.jade (in the same directory as layout.jade)
	
	extends layout
-->

	<!DOCTYPE html>

##Extends
The `extends` keyword allows a template to extend a layout or parent template. It can then override certain pre-defined blocks of content.

	//- layout.jade
	doctype html
	html
	  head
	    block title
	      title Default title
	  body
	    block content
	    
	//- index.jade
	extends ./layout.jade

	block title
	  title Article Title

	block content
	  h1 My Article
-->

	<!doctype html>
	<html>
	  <head>
	    <title>Article Title</title>
	  </head>
	  <body>
	    <h1>My Article</h1>
	  </body>
	</html>
>You can have multiple levels of inheritance, allowing you to create powerful hierarchies of templates.

###Incompatibility
It is worth noting that blocks are evaluated during compilation, so they will not work with render-time logic such as `if/else` statements.


##Includes
Includes allow you to insert the contents of one jade file into another.

	//- index.jade
	doctype html
	html
	  include ./includes/head.jade
	  body
	    h1 My Site
	    p Welcome to my super lame site.
	    include ./includes/foot.jade

	//- includes/head.jade
	head
	  title My Site
	  script(src='/javascripts/jquery.js')
	  script(src='/javascripts/app.js')
	  
	//- includes/foot.jade
	#footer
	  p Copyright (c) foobar
-->

	<!doctype html>
	<html>
	  <head>
	    <title>My Site</title>
	    <script src='/javascripts/jquery.js'></script>
	    <script src='/javascripts/app.js'></script>
	  </head>
	  <body>
	    <h1>My Site</h1>
	    <p>Welcome to my super lame site.</p>
	    <div id="footer">
	      <p>Copyright (c) foobar</p>
	    </div>
	  </body>
	</html>
###Including Plain Text
Including files that are not jade just includes the raw text.

	//- index.jade
	doctype html
	html
	  head
	    style
	      include style.css
	  body
	    h1 My Site
	    p Welcome to my super lame site.
	    script
	      include script.js

	/* style.css */
	h1 { color: red; }
	
	// script.js
	console.log('You are awesome');
-->

	<!doctype html>
	<html>
	  <head>
	    <style>
	      /* style.css */
	      h1 { color: red; }
	    </style>
	  </head>
	  <body>
	    <h1>My Site</h1>
	    <p>Welcome to my super lame site.</p>
	    <script>
	      // script.js
	      console.log('You are awesome');
	    </script>
	  </body>
	</html>

###Including Filtered Text
You can combine filters with includes to filter things as you include them.

	//- index.jade
	doctype html
	html
	  head
	    title An Article
	  body
	    include:markdown article.md

	//- article.md
	# article.md
	This is an article written in markdown.
-->

	<!doctype html>
	<html>
	  <head>
	    <title>An Article</title>
	  </head>
	  <body>
	    <h1>article.md</h1>
	    <p>This is an article written in markdown.</p>
	  </body>
	</html>

##Mixins
Mixins are small, encapsulated pieces of code that are reusable throughout the template.  
###Defining mixins
Mixin definitions don't output any HTML and are defined using the following syntax.  

	mixin book(name, price)

In the preceding code snippet, `book` is the name of the mixin, and `name` and `price` are both named arguments. The indented block of Jade gets executed in its own scope, where the variables `name` and `price` are passed. So basically, it works just like you would expect a function to work.

###Calling mixins
The syntax for calling mixins is also similar to that of function calls, except we prefix the function name with a + symbol to say that it isn't a tag (which can look quite similar).

-->



--> `<p>(&#596;) - Sean Lang - 2013</p>`
###Passing blocks
Besides just being able to pass arguments, you can also pass entire blocks to a mixin, as shown in the following code snippet:

	mixin input(name)
			
-->

	<form>
The block that will be passed to the mixin is whatever indented block comes after the mixin call. It is just like the way in which whatever indented block comes after a tag is nested in that tag, except here they are passed to the mixin. In this case, `input('type'='text')` and `textarea Type your comment here.` are the passed blocks. Inside the mixin, the `block` keyword tells Jade where to put the contents of
>that interpolation doesn't work in the arguments used to call a mixin.
###The arguments object
Just as the `arguments` object is a local variable available in JavaScript objects, it is available in Jade mixins. In fact, it is used frequently in Jade to make mixins that accept a variable number of args.

	mixin list()
-->

	<ul>