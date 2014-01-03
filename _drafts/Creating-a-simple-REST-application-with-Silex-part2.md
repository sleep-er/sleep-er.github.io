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
    return json_encode($toy);
});
{% endhighlight %}

You might have noticed that we now have 2 URL in the application that are the same `/` but actually they are not.  One uses the GET verb and one the POST.
As long as you make a POST request it will try to add a new item and if you make a GET request it will display all the items.

## Adding a DELETE
add DELETE /{stockid}

## Other routes
explain other verbs

## Links
Creating a simple REST application with Silex  
[part 1]({% post_url 2013-12-23-Creating-a-simple-REST-application-with-Silex %})


