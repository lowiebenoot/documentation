# Javascript

Just as there is a certain way of structuring .php files, we use a way to keep our Javascript clean. As we told before, when you name your Javascript files like module.js or action.js and place them in the js folder in your module folder, the files will be autoloaded.

> **jQuery**
> When you take a look at the Fork CMS javascript code you'll see we use jQuery a lot. Fork CMS simply includes it on every page so you don't have to worry about that.
> **Including other Javascript**
> Should you want to include some extra javascript, f.e. a library to do this or that, you can add the extra javascript by adding the following line to your code.
  $this->tpl->addJavascript(location of the javascript file);
> This way, it will also be loaded in the head of your page.

## frontend.js

Besides the javascript files per module/action, we have a central piece of javascript code, called frontend.js. Let's take a look to the first lines of code. As we'll see later on, we will use this way of writing javascript in other files too.

First we declare, (if it's not already declared) the object `jsFrontend`. In this object we will store all methods and variables.

```
jsFrontend = 
{
	debug: false,

	init: function() 
	{
		jsFrontend.initAjax();
		jsFrontend.controls.init();
		jsFrontend.forms.init();
		jsFrontend.gravatar.init();
		jsFrontend.search.init();
	},

	initAjax: function()
	{
		// set defaults for AJAX
		$.ajaxSetup(
		{
			url: '/frontend/ajax.php',
			cache: false,
			type: 'POST',
			dataType: 'json',
			timeout: 10000,
			data: { fork: { module: null, action: null, language: jsFrontend.current.language } }
		});
	}
}

... 

$(jsFrontend.init);
```

At the end of the file, we tell jQuery to call the init function we declared in jsFrontend. This function will then call all the other functions that need to be executed.

> **backend.js**
> There is equivalent for the frontend.js in the backend. You can find it in /default_www/backend/core/js/backend.js.

## module.js and action.js

As been told before, you can put a js- file with the module or action name in the js file and it will be autoloaded. For our example we've written an example that handles the click on the "this is awesome" buttons. Because these buttons exists on the detail and the index page, we name our file mini_blog.js so it's loaded on every page of the module.

> **Performance**
> In case of javascript it's often better to write al your javascript in one module file instead of one file per action. It's faster to load one big file, then multiple smaller files.

Beneath you'll see the complete mini_blog.js file. It does one thing only, handle clicks on the *this is awesome* buttons. As you can see, if jsFrontend is not declared yet, we declare it ourself as an object. Just as in frontend.js we define an init-function which we call when the page is loaded. The rest of the code is jQuery.

```
jsFrontend.miniBlog = 
{
	// init, something like a constructor
	init: function() 
	{		
		//init ajax action (this_is_awesome)
		jsFrontend.miniBlog.ajax();
	},
	ajax: function()
	{
		// for all buttons with the awesome class (on the detailpage,
		// just one, but on the index page, many
		$('.awesome a.add').click(function(e)
		{
			e.preventDefault();
			
			// split the url in pieces, so we can easily get the working language
			var chunks = document.location.pathname.split('/');
			
			// we added a rel-attribute to our
			// HTML-tag which contains the id of the blogpost
			var arg = $(this).attr('rel');
			
			$.ajax(
			{ 
				
				data: 
				{ 
					fork: { module: 'mini_blog', action: 'this_is_awesome' }, 
					post_id: arg 
				},
				success: function(data, textStatus) 
				{	
					// alert the user of we're in debug mode and 
					if(data.code != 200 && jsFrontend.debug)
					{
						alert(data.message);
					}	
					// hide the button on which we clicked
					$('#awesome' + arg + ' a.add').hide();
					$('#awesome' + arg + ' .added').css({'opacity':0.4});
					$('#awesome' + arg + ' .added').show();
												
					//increment the item's awesomeness counter
					$('#awesome' + arg + ' .counter').html('<strong>' +
 							(parseInt($('#awesome' + arg + ' .counter').html())
							+ 1) + '</strong>');
				}
			}
			);		
		});
	},
	// define the end of the object
	eoo: true
}
$(jsFrontend.miniBlog.init());
```