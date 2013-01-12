## Laravel 4 with PSR-0 autoloading.

A lot of people have been asking for a way to avoid doing a

	composer dump-autoload

every time they create a new class.

The reason you have to do this is because Laravel is setup to use class mapping by default for these resource folders, you can see this in the default composer.json :

	"autoload": {
		"classmap": [
			"app/commands",
			"app/controllers",
			"app/models",
			"app/database/migrations",
			"app/tests/TestCase.php"
		]
	},

Each time we run `composer dump-autoload` composer cycles these directories and creates a map of class names to file locations so that it can later load them `on the fly` when needed. So when you create a new controller, you have to `dump-autoload` to add it to the map.

Let's PSR-0 load this instead, so we can have our directory structure represent our namespaced classes :

	"autoload": {
		"psr-0": {
			"Example": "app/src/"
		}
	},

Now `dump-autoload` just once more. Now composer knows that namespaces map up to a directory structure in the source folder, it can 'guess' where the file is supposed to be so there's no need for a map. I have created a new directory structure to handle these changes :

```
/app
	/src
		/Example
			/Command
			/Controller
			/Migration
			/Model
			/Test
```

As you can see, we now have a directory for each of the 'class' type resources in our application. I have moved the files from the default Laravel folders across and deleted the old locations (also removed them from composer.json).

I have also namespaced the files to match the directory structure, for example heres the HomeController :

	<?php namespace Example\Controller;

	use View;

	class HomeController extends BaseController {

		/*
		|--------------------------------------------------------------------------
		| Default Home Controller
		|--------------------------------------------------------------------------
		|
		| You may wish to use controllers instead of, or in addition to, Closure
		| based routes. That's great! Here is an example controller method to
		| get you started. To route to this controller, just add the route:
		|
		|	Route::get('/', 'HomeController@showWelcome');
		|
		*/

		public function showWelcome()
		{
			return View::make('hello');
		}

	}

Several things have changed, first of all the class now has a namespace representing the directory the file is contained in relative to the `src` folder. Secondly I have added the `use View` line because the `View` class is located in the root namespace, and if we try to use it without this line it will try to look in `Example\Controller` instead. This is something to take note of when coding inside a namespace. You can either `use` the resource to bring it into the current namespace, or prefix the class with a `\` when using it, for example :

	return \View::make('hello');

This would also work, however I like to `use` the files to get a clear overview of which resources the class is using.

Routes will also need to take note of the namespacing, for example :

	Route::get('/', 'Example\Controller\HomeController@showWelcome');

Now you may be asking yourself, why doesn't Laravel set this by default?

Well Laravel doesn't know what your project is called, and what the root namespace would be. It is a zero configuration framework, meaning it should 'just work' out of the box. Class mapping is then the most sensible choice.

I highly recommend taking a look at the [composer documentation](http://getcomposer.org/doc/) to take advantage of Laravel 4. It's an extremely clever piece of software and well worth learning.

Enjoy Laravel!

( PS Source included. )

Dayle xx.
