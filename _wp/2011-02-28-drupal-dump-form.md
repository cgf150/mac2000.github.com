---
layout: post
title: Drupal dump form
permalink: /465
tags: [alter, drupal, flash, form, message]
---

[http://drupal.org/node/651106](http://drupal.org/node/651106)


    <?php
    /**
     * Implementation of HOOK_form_alter()
     */
    function mymodule_form_alter(&$form, $form_state, $form_id) {
      drupal_set_message("Form ID is : " . $form_id);
      drupal_set_message(print_R($form, TRUE));
    }
    ?>

