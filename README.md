# phoneblogger

A multi user blog user email messages or SMS. 

# Install 

This will install the blog system. 

This says Ubuntu install (as this is the system I am currently working on), but it should work on any *nix system if you have met the dependencies. 

First clone the base system: 

    git clone git://github.com/diversen/coscms.git yoursite

Enter base system: 

    cd yoursite

Enable apache2 host

    // you will need to be root
    sudo ./coscli.sh apache2 --en yoursite

Ready to install. You will be asked about DB configuration.  Will ask you for version to install. Check out the `latest version` version or try `master` (tagged versions are tested and should work, while master will work 99% of the time work as well). After writing the `config/config.ini` file and install all profile's modules from git repos. At last system will prompt you for a super user. 

    ./coscli.sh prompt-install --install

Set correct perms for public files (e.g. upload folder)

    // you will need to be root user as we change 
    // the perms to be www-data
    sudo ./coscli.sh file --chmod-files

# Enable module emailparse

This module is called emailparse because you can send messages from your phone to
emails - and then a cron job will parse the messages. 

# Requirements

You will need a working IMAP server. With some modifications you would be able 
to create this with e.g. pop3 or any other mail server system, but this uses
IMAP because IMAP has more options than pop3, and at the same time it is 
possible to keep it quite simple. 

# IMAP setup

courier IMAP setup is a long story. All I can say is that the users table is
setup the same way as described in this article: 

http://articles.slicehost.com/2008/9/2/mail-server-adding-domains-and-users-to-mysql

# Database

Normally, if your are hosting multiple emails, you want to keep your users in
a separate database. Create a database called 'mail'

    CREATE DATABASE `mail`;

# Users table

As described in the slicehost article all IMAP users exists in the following
table:

    CREATE TABLE `users` (
    `email` varchar(80) NOT NULL,
    `password` varchar(20) NOT NULL,
    PRIMARY KEY (`email`)
    ) ENGINE=MyISAM DEFAULT CHARSET=latin1

We need some additionel info and create an user_alias table

    CREATE TABLE `users_alias` (
    `server_name` varchar(80) NOT NULL,
    `user_id` varchar(80) NOT NULL,
    `parent_id` int(10) NOT NULL,
    PRIMARY KEY (`email`)
    ) ENGINE=MyISAM DEFAULT CHARSET=latin1

# Edit configuration:

edit emailparse/emailparse.ini, and set:

    ; the email domain you will be using
    emailparse_domain = 'os-cms.dk'
    ; the name of the mail users database
    emailparse_database = 'mail'
    ; the default IMAP password of all users
    ; the same password is used for ALL users. Set a good one
    ; the password is in clear text, and will be encrypted by
    ; mysql
    emailparse_imap_password = 'password'

INSERT INTO `users` (email, password)  values ('mail@phoneblogger.me', encrypt('password') );

