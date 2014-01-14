---
layout: post
title: Creating a simple REST application with Silex part 2.
category: Coding
tags: Silex REST PHP
image: /images/blog/silex.png
---
In [part 1]({% post_url 2013-12-23-Creating-a-simple-REST-application-with-Silex %}) you installed Silex and setup 2 routes, `/` and `/{stockcode}`.
Now lets expand upon those by adding a POST and a DELETE route. 
<!-- more -->

The 2 routes we created use GET but to make your application truly useful you will want to use at least 1 more type and that is POST.

## Adding a POST route
Let us add a route to allow new products to be added.  To our `index.php` file we will need to add the following somewhere before the `$app->run();` line.

{% highlight php linenos %}
<?php
$app->post('/', function (Silex\Application $app, Symfony\Component\HttpFoundation\Request $request) {

    $name = $request->get('name');
    $quantity = $request->get('quantity');
    $description = $request->get('description');
    $image = $request->get('image');
    
    // Code to add the toy into the toy db
    // and return a toy id
    //$toy_id = create_toy($name, $quantity, $description, $image);
    //$toy = get_toy($toy_id);
    
    // For now lets just assume we have saved it
    $toy = array(
        '00003' => array(
            'name' => $name,
            'quantity' => $quantity,
            'description' => $description,
            'image' => $image,
        )
    );
    
    // Useful to return the newly added details
    return new Symfony\Component\HttpFoundation\Response(json_encode($toy), 201);
});
{% endhighlight %}

## Adding a DELETE
add DELETE /{stockid}

## Other routes
explain other verbs

## The same route but different verbs
You might have noticed that we now have 2 URLs in the application that are the same `/` but actually they are not.  One uses the GET verb and one the POST.
How does it know which to use? Well that depends on the request type it receives and explaining how to send POST, DELETE, PUT etc is a big post in itself. 
For simple testing of routes you can use [RESTClient](http://www.restclient.net) for Firefox or [Postman](http://www.getpostman.com/) for Chrome.  If you want to 
consume a route in your client code you can use plain old php fopen, curl or the nice [Guzzle library](http://guzzlephp.org/). 

## Part 3
Now that we have several routes in `index.php` it is starting to look messy and you should be able to see that adding everything into 1 file will
 cause us headaches down the road in terms of readability and maintainability.  Let us look at moving these into separate libraries.

## Links
Creating a simple REST application with Silex  
[part 1]({% post_url 2013-12-23-Creating-a-simple-REST-application-with-Silex %})


