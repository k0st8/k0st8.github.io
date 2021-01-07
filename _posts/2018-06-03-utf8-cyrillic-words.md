---
layout: post
title:  "Utf8 Cyrillic words json_encode"
date:   2018-06-03 1:46:30
categories: Tech
---

# Posible solutions: utf8 cyrillic words json_encode

{% highlight php startinline=true %}
// Demo version
$js = '{
	"cities": [{
		"id": 1,
		"region": "Алтайский край"
	}, {
		"id": 2,
		"region": "Калининградская область"
	}, {
		"id": 3,
		"region": "Брянская область"
	}]
}';
// #1 ------------------------------------------
$ecnd = utf8_encode($js); // Put it through utf8_encode
$decnd = utf8_decode($ecnd);  // Getting data utf8_decode
{% endhighlight %}

----------


{% highlight php startinline=true %}
    // #2 -------------------------------------------
    header('Content-type: application/json;charset=utf-8'); // Add HEADER
    echo json_encode($js); 
    // #3 Option adding json_encode as second argument
    // ,JSON_UNESCAPED_UNICODE|JSON_UNESCAPED_SLASHES 
{% endhighlight %}

----------

[Doc 1][1] 

[Doc 2][2]


  [1]: http://php.net/manual/ru/function.json-encode.php
  [2]: http://php.net/manual/ru/function.utf8-decode.php