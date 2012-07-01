---
layout: post
title: Drupal tiny input formats
permalink: /680
tags: [drupal, filter, html_filter, input_format, upload, wysiwyg]
---

Памятка чтобы не забыть как все это собрать в кучу.


**Используемые модули:**

[http://drupal.org/project/wysiwyg](http://drupal.org/project/wysiwyg)


[http://drupal.org/project/better_formats](http://drupal.org/project/better_fo
rmats)


[http://drupal.org/project/wysiwyg_template](http://drupal.org/project/wysiwyg
_template)


[http://drupal.org/project/wysiwyg_button_order](http://drupal.org/project/wys
iwyg_button_order)


[http://drupal.org/project/imagepicker](http://drupal.org/project/imagepicker)


[http://drupal.org/project/filepicker](http://drupal.org/project/filepicker)


[http://drupal.org/project/imageapi](http://drupal.org/project/imageapi)


[http://drupal.org/project/imagecache](http://drupal.org/project/imagecache)


[http://drupal.org/project/imagecache_actions](http://drupal.org/project/image
cache_actions)


**Форматы ввода**

[http://www.lullabot.com/articles/drupal-input-formats-and-
filters](http://www.lullabot.com/articles/drupal-input-formats-and-filters) -
отличная статья по теме.


Мне необходимы всего два формата ввода: plain, wysiwyg.


[![](http://mac-blog.org.ua/wp-content/uploads/115-300x154.png)](http://mac-
blog.org.ua/wp-content/uploads/115.png)


plain выделен как формат по умолчанию, доступ к формату wysiwyg имеют только
пользователи у которых есть роль editor.


**Формат ввода plain**

Что важно - чтобы реализовать plain - нужен фильтр html - в его настройках, в
разделе "Разрешенные теги" просто не указываем ничего - тем самым заставим
этот фильтр зачищать все теги целиком. Так же важно чтобы фильтр
преобразования перевода строки в соотв. теги - запускался позже фильтра html
иначе последний зачистит все теги которые навставляет первый.


[![](http://mac-blog.org.ua/wp-content/uploads/27-300x267.png)](http://mac-
blog.org.ua/wp-content/uploads/27.png)


[![](http://mac-blog.org.ua/wp-content/uploads/33-300x191.png)](http://mac-
blog.org.ua/wp-content/uploads/33.png)


[![](http://mac-blog.org.ua/wp-content/uploads/42-300x160.png)](http://mac-
blog.org.ua/wp-content/uploads/42.png)


**Формат ввод wysiwyg**

Тут уже не нужен фильтр преобразования новых строк - как как об этом
позаботиться tinymce, так же необходимо подумать какие теги будут
генерироваться редактором tiny и добавить их к списку разрешенных в настройках
фильтра html.


[![](http://mac-blog.org.ua/wp-content/uploads/52-300x262.png)](http://mac-
blog.org.ua/wp-content/uploads/52.png)


[![](http://mac-blog.org.ua/wp-content/uploads/61-300x191.png)](http://mac-
blog.org.ua/wp-content/uploads/61.png)


[![](http://mac-blog.org.ua/wp-content/uploads/72-300x154.png)](http://mac-
blog.org.ua/wp-content/uploads/72.png)


**Better formats**

Этот модуль сделает две главных вещи: спрячет выбор формата ввода от
пользователей и укажет форматы по умолчанию для всевозможных случаев.


Важно перетащить настройки для **editor**'а выше настроек для
авторизированного пользователя.


[![](http://mac-blog.org.ua/wp-content/uploads/81-300x267.png)](http://mac-
blog.org.ua/wp-content/uploads/81.png)


В настройках прав доступа - для авторизированных и неавторизированных
пользователей выставляем галочки возле поля **collapse format fieldset by
default**, а остальные убираем, тем самым мы спрячем от пользователей все что
связано с форматами ввода.


После всего вышеуказанного все должно заработать следующим образом:


В форме добавления отзыва - используется формат ввода plain без исключений.


На формах добавления и редактирования материалов (делать это может только
пользователь с ролью editor) - используется формат ввода wysiwyg отять же без
исключений.


На всех формах, все упоминания о форматах ввода, подсказки и т.п. удалены.


**TinyMCE**

Для подключения tiny, необходим модуль wysiwyg, а так же сам редактор,
разорхивированный в папку /sites/all/libraries/tiny. Что важно - если
необходим язык, отличный от английского - необходимо так же скачать и
разорхивировать его в ту же папку, иначе редактор работать не будет.


[![](http://mac-blog.org.ua/wp-content/uploads/91-135x300.png)](http://mac-
blog.org.ua/wp-content/uploads/91.png)


Настроек миллион, и все зависит от задач которые будут ставиться перед контент
редактором.


Так же в догонку неплохо использовать плагин
http://drupal.org/project/wysiwyg_template - он добавит возможность вставлять
заранее подготовленные шаблоны в контент - что очень поможет контент редактору
быстро создавать качественный материал.


Так же неплохой модуль: [http://drupal.org/project/wysiwyg_button_order](http:
//drupal.org/project/wysiwyg_button_order) - который позволит задать порядок
кнопок для tiny. Не уверен насчет практической необходимости - но вполне
пригодная штука - хотя бы те же разделители раставить между группами кнопок
редактора.


**Вставка рисунков в TinyMCE**

Есть сто тысяч модулей по этой теме, один краше другого.


В начале я собирался использовать стандартный модуль **Upload** - что мне в
нем очень понравилос: он стандартный, можно подгружать как рисунки так и
файлы, после удаления постав - удаляются и его файлы. Ну а с другой стороны -
не возможности интегрироваться в tiny - я себе слабо представляю как можно
было бы обьяснить контент редактору как вставлять рисунки и файлы в пост. Так
же не возможности использовать превьюшки рисунков которые делает
**imagecache**. Собрался написать модуль который бы добавлял к табличке
загруженых файлов ссылку для вставки файла в пост - но как оказалось у wysiwyg
модуля в api нет такой функциональности - а писать под каждый редактор - тоже
не реально - потом просто не уследишь. Остался вариант делать detach редактора
- вставлять в хвост textarea и потом снова attach редактор - но как то это уже
попахивает перебором.


Благо нашелся модуль **imagepicker** - который по своей природе очень
напоминает механизм добавления рисунков в wordpress.


Для того чтобы его можно было использовать необходимо дать роли **editor**
право на **use imagepicker**.


После чего настроить модуль по своему усмотрению и можно пользовать:


[![](http://mac-blog.org.ua/wp-content/uploads/121-102x300.png)](http://mac-
blog.org.ua/wp-content/uploads/121.png)


[![](http://mac-blog.org.ua/wp-content/uploads/131-300x159.png)](http://mac-
blog.org.ua/wp-content/uploads/131.png)


Еще одним доводом выбрать imagepicker - есть модуль filepicker - тоесть мы
дадим редактору, хоть и перегруженый, но все же механизм добавления в
содержимое как рисунков так и файлов.


Тем более что всегда можно затюнить формы этих модулей для их упрощения.


Так же косательно рисунков - никуда не деться от модулей **imageapi**,
**imagecache**, **imagecache_actions** - они позмолят создавать настраиваемые
миниатюры рисунков, вешать на них ватермарки и многое другое. Добавляя новый
preset не забываем выставлять права на его просмотр авторизированным и не
авторизированным пользователям.


И как обычно - epic fail - imagepicker - не умеет работать с модулем
imagecache :(


Всетаки пришлось написать свой модуль upload_insert.


**Редактор кода**

В качестве редактора кода самым оптимальным оказался CodeMirror - патч для
которого можно найти здесь - [http://mac-blog.org.ua/692](http://mac-
blog.org.ua/692) его очень удобно использовать для редактирования php формата
- тоесть там нет никаких редакторов - зато есть подсветка синтаксиса и
нормальная обработка табов.


[![](http://mac-blog.org.ua/wp-content/uploads/116-300x277.png)](http://mac-
blog.org.ua/wp-content/uploads/116.png)


[wysiwyg](http://mac-blog.org.ua/wp-content/uploads/wysiwyg.zip) - в архиве
файлы которые нужно сложить в папку модуля wysiwyg. После чего необходимо
скачать и разорхивировать сам редактор в папку
/sites/all/libraries/codemirror.

