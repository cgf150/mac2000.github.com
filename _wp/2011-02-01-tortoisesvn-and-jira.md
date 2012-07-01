---
layout: post
title: TortoiseSvn and Jira
permalink: /394
tags: [code, jira, scm, svn, tortoisesvn]
---

[https://plugins.atlassian.com/plugin/details/10017](https://plugins.atlassian
.com/plugin/details/10017)


Инсталлируем: [https://github.com/csharptest/JiraSVN/downloads](https://github
.com/csharptest/JiraSVN/downloads)


Идем в свойства TortoiseSVN


[![](http://mac-blog.org.ua/wp-content/uploads/13-300x238.png)](http://mac-
blog.org.ua/wp-content/uploads/13.png)


Далее добавляем Issue Tracker Integration


[![](http://mac-blog.org.ua/wp-content/uploads/2-300x199.png)](http://mac-
blog.org.ua/wp-content/uploads/2.png)


Где:



  * Working Copy Path - путь к снимку репозитория


  * Provide - то что мы установили


  * Parameters - логин, пароль, адресс jira'ы


[http://justaddwater.dk/2010/06/10/windows-svn-pre-commit-
hook/](http://justaddwater.dk/2010/06/10/windows-svn-pre-commit-hook/)


Скрипт который запрещает коммиты без комментария


Создаем файл D:\SVN\Rabota.UA\hooks\pre-commit.bat с таким содержимым:


    REM Subversion pre-commit hook for Windows machine
    REM put this in your SVN repository folder /hooks/pre-commit.bat
    REM we use it with svn version
    REM http://stackoverflow.com/questions/869248/windows-pre-commit-hook-for-comment-length-subversion

    @echo off
    :: Stops commits that have empty log messages.
    @echo off

    setlocal

    rem Subversion sends through the path to the repository and transaction id
    set REPOS=%1
    set TXN=%2

    rem line below ensures at least one character ".", 5 characters require change to "....."
    svnlook log %REPOS% -t %TXN% | findstr . > nul
    if %errorlevel% gtr 0 (goto err) else exit 0

    :err
    echo. 1>&2
    echo ADD JIRA TASK NUMBER BEFORE COMMIT 1>&2
    exit 1


Если нужно сделать client side pre-commit hook


тогда батник будет выглядеть вот так:


    @echo off
    setlocal
    set REPOS=%1
    set TXN=%2
    set COMMENT=%3
    rem check that logmessage contains at least 1 characters
    type "%COMMENT%" | findstr "." > nul
    if %errorlevel% gtr 0 goto err
    exit 0
    :err
    echo Empty log message not allowed. Commit aborted! 1>&2
    exit 1


Суть изменений в следующем: третьим параметром идет путь к файлу в котором
лежит наш коммент, теперь мы проверяем не svnlook'ом, а обычным type'ом,
наличие в этом файле текста


Добавляется это дело в настройках TortoiseSVN\Hook Scripts (рядом с Issue
Tracking Integration)


[http://code.google.com/p/tortoisesvn/source/browse/trunk/contrib/hook-scripts
/client-side/PreCommit.js.tmpl?r=19213](http://code.google.com/p/tortoisesvn/s
ource/browse/trunk/contrib/hook-scripts/client-side/PreCommit.js.tmpl?r=19213)

