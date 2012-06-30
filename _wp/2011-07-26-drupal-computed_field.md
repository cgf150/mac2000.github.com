---
layout: post
title: Drupal computed_field
permalink: /740
tags: [cck, computed_field, drupal]
----

Note about using compute_field.


**Modules:**

[http://drupal.org/project/cck](http://drupal.org/project/cck) - with
multigroup (at moment in dev)


[http://drupal.org/project/computed_field](http://drupal.org/project/computed_
field)


**Node types:**

**Product**

Product have one additional field _Price_ that is required and can be only one
per product.


[](http://mac-blog.org.ua/wp-content/uploads/118.png)[![](http://mac-
blog.org.ua/wp-content/uploads/119-300x203.png)](http://mac-blog.org.ua/wp-
content/uploads/119.png)


[![](http://mac-blog.org.ua/wp-content/uploads/29-300x300.png)](http://mac-
blog.org.ua/wp-content/uploads/29.png)


Order


Order have noderefference Product field and its amount in Multigroup and
computed field Total price.


[![](http://mac-blog.org.ua/wp-content/uploads/34-300x203.png)](http://mac-
blog.org.ua/wp-content/uploads/34.png)


[![](http://mac-blog.org.ua/wp-content/uploads/43-300x229.png)](http://mac-
blog.org.ua/wp-content/uploads/43.png)


[![](http://mac-blog.org.ua/wp-content/uploads/53-300x284.png)](http://mac-
blog.org.ua/wp-content/uploads/53.png)


[![](http://mac-blog.org.ua/wp-content/uploads/62-173x300.png)](http://mac-
blog.org.ua/wp-content/uploads/62.png)


**Computed field snippet:**

    
    <code>$total_price = 0;
    foreach($node->field_order_product as $k => $v) {
      $product = node_load($node->field_order_product[$k]['nid']);
      $product_price = $product->field_product_price[0]['value'];
      $amount = $node->field_order_product_amount[$k]['value'];
    
      $total_price += $product_price * $amount;
    }
    
    $node_field[0]['value'] = $total_price;</code>




More snippet examples:
[http://drupal.org/node/149228](http://drupal.org/node/149228)


More complex example, added field _Discount_ and computed field _Price_ to
_Multigroup_, computed field _Price_ will be calculated foreach selected
product. Computed field _Total price_ also changed to retrive price with
discount.


[![](http://mac-blog.org.ua/wp-content/uploads/82-300x221.png)](http://mac-
blog.org.ua/wp-content/uploads/82.png)


[![](http://mac-blog.org.ua/wp-content/uploads/92-180x300.png)](http://mac-
blog.org.ua/wp-content/uploads/92.png)


[![](http://mac-blog.org.ua/wp-content/uploads/93-173x300.png)](http://mac-
blog.org.ua/wp-content/uploads/93.png)


**Code snippet for Price:**

    
    <code>foreach (array_keys($node->field_order_product) as $delta) {
      $product = node_load($node->field_order_product[$delta]['nid']);
      $product_price = $product->field_product_price[0]['value'];
      $amount = $node->field_order_product_amount[$delta]['value'];
      $discount = $node->field_discount[$delta]['value']; 
      $node_field[$delta]['value'] = ($product_price * $amount) * ((100 - $discount) / 100);
    }</code>




**Code snippet for Total Price:**

    
    <code>$total_price = 0;
    foreach($node->field_order_product_price as $order_product_price) {
      $total_price += $order_product_price['value'];
    }
    
    $node_field[0]['value'] = $total_price;</code>




