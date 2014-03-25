---
layout: post
title: Creating a simple REST application with Silex part 3.
category: Coding
tags: Silex REST PHP
image: /images/blog/silex.png
---
In [part 1]({% post_url 2013-12-23-Creating-a-simple-REST-application-with-Silex %}) you installed Silex and setup 2 routes, `/` and `/{stockcode}`.
In [part 2]({% post_url 2014-01-22-Creating-a-simple-REST-application-with-Silex-part2 %}) you added a POST and a DELETE route.  In part 3 we are going to
assume a few months have passed and you now have tens, maybe hundreds, of routes.

The problem with having many routes is that we end up with a really big and unwieldy `index.php` file.  Finding the correct route to fix can take a while.
I will be showing you two possible way to manage your routes.
<!-- more -->

After adding our new routes into `index.php` we end up with a file somewhat like this.
{% highlight php linenos %}
<?php

$app->get('/', function() use ($toys) {
    // body here
});

$app->get('/{stockcode}', function (Silex\Application $app, $stockcode) use ($toys) {
    // body here
});

$app->post('/', function (Silex\Application $app, Symfony\Component\HttpFoundation\Request $request) {
    // body here
});

$app->delete('/{stockcode}', function (Silex\Application $app, Symfony\Component\HttpFoundation\Request $request, $stockcode) {
    // body here
});

$app->match('/{stockcode}/image', function (Silex\Application $app, $stockcode) use ($toys) {     
    // body here
})->method('GET|POST');

$app->get('/giftcodes', function (Silex\Application $app, $stockcode) use ($toys) {     
    // body here
});

$app->get('/giftcodes/{giftcode}', function (Silex\Application $app, $stockcode) use ($toys) {     
    // body here
});

$app->post('/giftcodes', function (Silex\Application $app, $stockcode) use ($toys) {     
    // body here
});

$app->get('/stats', function (Silex\Application $app, $stockcode) use ($toys) {     
    // body here
});

$app->get('/stats/{stockcode}', function (Silex\Application $app, $stockcode) use ($toys) {     
    // body here
});

//...
//... Other made up routes here
//...
{% endhighlight %}

You can see from this small example that finding a particular route is not easy.  Imagine the example with the body code for the routes as well. 
Currently no IDEs can cope with routes and you will not get a nice list of route names like you do for functions. 

## Solution 1 - Group and separate

The easiest solution is to group up your routes and move them into separate files.  

Lets split our code into 3 files, `stockcode.php`, `giftcodes.php` and `stats.php`.  Into `stockcode.php` we will move the routes

{% highlight bash %}
    GET /
    GET /{stockcode}
    POST /
    DELETE /
    GE|POST /{stockcode}/image
{% endhighlight %}

`giftcodes.php` will have

{% highlight bash %}
    GET /giftcodes
    GET /giftcodes/{giftcode}
    POST /giftcodes
{% endhighlight %}

`stats.php` will have

{% highlight bash %}
    GET /stats
    GET /stats/{stockcode}
{% endhighlight %}

We can now simplify our `index.php` down to 

{% highlight php %}
<?php
require_once __DIR__.'/vendor/autoload.php';

$filename = __DIR__.preg_replace('#(\?.*)$#', '', $_SERVER['REQUEST_URI']);
if (php_sapi_name() === 'cli-server' && is_file($filename)) {
    return false;
}

$app = new Silex\Application();
// Please set to false in a production environment
$app['debug'] = true;

$toys = array(
    // ...
);

require(__DIR__ . 'stockcode.php');
require(__DIR__ . 'giftcodes.php');
require(__DIR__ . 'stats.php');

$app->run();
{% endhighlight %}

Now when we want to update a stockcode route we know to look in `stockcode.php`.  Much simpler and hopefully easier to maintain.

## Solution 2 - use Providers

As with solution 1 we are going to separate our code into 3 files, broken out along the same lines. For this we need to do more work
than just a simple copy and paste.  I will show you how to do this for the `stockcode` routes.

### Toyshop\StockcodeController.php
{% highlight php linenos %}
<?php
namespace Toyshop;

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\HttpFoundation\JsonResponse;
use Silex\Application;
use Silex\ControllerProviderInterface;

/**
 * The routes used for stockcode.
 *
 * @package toyshop
 */
class StockcodeController implements ControllerProviderInterface
{
    private $toys = array(
        '00001'=> array(
            'name' => 'Racing Car',
            'quantity' => '53',
            'description' => '...',
            'image' => 'racing_car.jpg',
        ),
        '00002' => array(
            'name' => 'Raspberry Pi',
            'quantity' => '13',
            'description' => '...',
            'image' => 'raspberry_pi.jpg',
        ),
    );    
    
    /**
     * Connect function is used by Silex to mount the controller to the application.
     *
     * Please list all routes inside here.
     *
     * @param Application $app Silex Application Object.
     *
     * @return Response Silex Response Object.
     */
    public function connect(Application $app)
    {
        /**
         * @var \Silex\ControllerCollection $factory
         */
        $factory = $app['controllers_factory'];

        $factory->get(
            '/',
            'Toyshop\StockcodeController::getAll'
        );     
        
        $factory->get(
            '/{stockcode}',
            'Toyshop\StockcodeController::getStockcode'
        );        

        $factory->delete(
            '/',
            'Toyshop\StockcodeController::deleteStockcode'
        );        

        // ... Other routes     

        return $factory;
    }

    /**
     * Get all the stockcodes.
     *
     * @param Application $app       The silex app.
     *
     * @return string
     */
    public function getAll(Application $app)
    {
        return json_encode($this->toys);
    }

    /**
     * Get a stockcode.
     *
     * @param Application $app       The silex app.
     * @param string      $stockcode The stockcode.
     *
     * @return string
     */
    public function getStockcode(Application $app, $stockcode)
    {
        if (!isset($this->toys[$stockcode])) {
            $app->abort(404, "Stockcode {$stockcode} does not exist.");
        }
        return json_encode($this->toys[$stockcode]);
    }


    /**
     * Delete a stockcode.
     *
     * @param Application $app       The silex app.
     * @param string      $stockcode The stockcode.
     *
     * @return string
     */
    public function deleteStockcode(Application $app, $stockcode)
    {
        if (delete_toy($stockcode)) {
            // The delete went ok and we can now return a no content value
            // HTTP_NO_CONTENT = 204
            $responseCode = Response::HTTP_NO_CONTENT;
        } else {
            // Something went wrong
            $response_code = Response::HTTP_INTERNAL_SERVER_ERROR;
        }
    
        return new Response('', $responseCode);
    }    
    
    // Other functions here
    // ...
    // ...
}
{% endhighlight %}

The important function in `StockcodeController.php` is `connect()` as this is now responsible for the routing. We are making use of a controller_factory
 which you must return from `connect()`.

If you look at the entry for the route GET `/{stockcode}` you will notice that I have changed it from an anonymous function into a call to a function 
inside the class.
{% highlight php %}
<?php
        $factory->get(
            '/{stockcode}',
            'Toyshop\StockcodeController::getStockcode'
        );  
{% endhighlight %}

You could keep all the routs as anonymous but moving them into their own functions helps you maintain your ever growing number of routes.  A bonus
from this is that your IDE will now show then as functions and if you name them in helpful ways you will easily be able to figure out which route
they are associated with.

In the `index.php` file we will use the `mount()` command instead of `require()`. 
{% highlight php %}
<?php
require_once __DIR__.'/vendor/autoload.php';

$filename = __DIR__.preg_replace('#(\?.*)$#', '', $_SERVER['REQUEST_URI']);
if (php_sapi_name() === 'cli-server' && is_file($filename)) {
    return false;
}

$app = new Silex\Application();
// Please set to false in a production environment
$app['debug'] = true;

$toys = array(
    // ...
);

$app->mount('/stockcode', new Toyshop\StockcodeController());
$app->mount('/giftcodes', new Toyshop\GiftcodeController());
$app->mount('/stats', new Toyshop\StatsController());

$app->run();
{% endhighlight %}

## Which is best?
Solution 1 is only a short term fix and only really moves the problem into other files whereas solution 2 fixes it in a nice way and allows you 
to share your providers with other applications. Go for solution 2. It might be more work for you to start with but it really does help you out a 
lot more.

## Further reading
Hopefully I have helped you to think about how to arrange you routes but please take the time the read the 
[Organizing Controllers](http://silex.sensiolabs.org/doc/organizing_controllers.html) documentation on the Silex webpage for more information 
 about how this works.

## Links
[part 1]({% post_url 2013-12-23-Creating-a-simple-REST-application-with-Silex %})  
[part 2]({% post_url 2014-01-22-Creating-a-simple-REST-application-with-Silex-part2 %})
  
[Organizing Controllers - Silex Documentation](http://silex.sensiolabs.org/doc/organizing_controllers.html)
[Creating a provider - Silex Documentation](http://silex.sensiolabs.org/doc/providers.html#controller-providers)
