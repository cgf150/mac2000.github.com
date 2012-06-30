---
layout: post
title: call drupal 7 api outside drupal
permalink: /308
tags: [api, drupal, drupal7, extend, php]
----

Simle sample showing how to check is user logged

    
    <code><?php
    define('DRUPAL_ROOT', dirname(__FILE__));
    chdir(DRUPAL_ROOT);
    require_once DRUPAL_ROOT . '/includes/bootstrap.inc';
    drupal_bootstrap(DRUPAL_BOOTSTRAP_FULL);
    
    global $user;
    
    if ($user->uid) {
        print "Logged in";
    } else {
       print "Logged out";
    }
    
    echo '<hr />';
    
    $nid = 2;
    $node = node_load($nid);
    echo '<pre><code>'.print_r($node, true).'</code></pre>';
    $node->field_last_check_result['und'][0]['value'] = 20;
    
    node_save($node);</code>


[http://www.group42.ca/creating_and_updating_nodes_programmatically_in_drupal_
7](http://www.group42.ca/creating_and_updating_nodes_programmatically_in_drupa
l_7)

