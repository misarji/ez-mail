EzMail
=======

Created by Keshav Mishra

> Note that API calls are not currently unit-tested (although we have properly test them manually). Therefore, you
are encouraged to test your API usage before going into production.

Introduction
------------

EzMail is a module that integrates with various third-parties API to send mails. Integration is provided with the
API of those services. It does not handle SMTP.

Please note that EzMail only supports Transactional services. Services for campaign marketing emails (like MailChimp
or MailJet) are out-of-the scope of this module.

Here are the currently supported services:

* [Amazon SES](http://aws.amazon.com/ses) (nearly complete)
* [Elastic Email](http://elasticemail.com) (complete)
* [Mailgun](http://www.mailgun.com) (complete)
* [Mandrill](http://mandrill.com) (complete)
* [Postmark](https://postmarkapp.com) (complete)
* [Postage](http://postageapp.com) (complete)
* [Send Grid](http://sendgrid.com) (nearly complete)

Requirements
------------

* PHP 5.3
* [Zend Framework 2](https://github.com/zendframework/zf2)
* [Amazon AWS ZF 2 Module](https://github.com/aws/aws-sdk-php-zf2): only if you plan to use Amazon SES service

Installation
------------

Add `"misarji/ez-mail"` to your `composer.json` file and update your dependencies. Enable `EzMail` in your
`application.config.php`. To use one of the transport layers, see the documentation in the [docs](https://github.com/misarji/EzMail/tree/master/docs) folder.

If you do not have a `composer.json` file in the root of your project, copy the contents below and put that into a
file called `composer.json` and save it in the root of your project:

```
{
    "require": {
        "misarji/ez-mail": "~1.5"
    }
}
```

Then execute the following commands in a CLI:

```
curl -s http://getcomposer.org/installer | php
php composer.phar install
```

Now you should have a `vendor` directory, including a `misarji/ez-mail`. In your bootstrap code, make sure
you include the `vendor/autoload.php` file to properly load the EzMail module.

### Amazon SES

If you want to use Amazon SES, you need to install the official AWS ZF 2 module. Please refer to the documentation
of Amazon SES in the [docs](https://github.com/misarji/EzMail/tree/master/docs) folder.

Documentation
-------------

Documentation for EzMail is splitted for each provider:

* [Amazon SES](/docs/Ses.md)
* [Elastic Email](/docs/ElasticEmail.md)
* [Mailgun](/docs/Mailgun.md)
* [Mandrill](/docs/Mandrill.md)
* [Postage](/docs/Postage.md)
* [Postmark](/docs/Postmark.md)
* [SendGrid](/docs/SendGrid.md)

Cook-book
---------

### How to send an HTML email ?

Every email providers used in EzMail allow to send HTML emails. However, by default, if you set the mail's content
using the `setBody` content, this content will be considered as the plain text version as shown below:

```php
$message = new \Zend\Mail\Message();

// This will be considered as plain text message, even if the string is valid HTML code
$message->setBody('Hello world');
```

To send a HTML version, you must specify the body as a MimeMessage, and add the HTML version as a MIME part, as
shown below:

```php
$message = new \Zend\Mail\Message();

$htmlPart = new \Zend\Mime\Part('<html><body><h1>Hello world</h1></body></html>');
$htmlPart->type = "text/html";

$textPart = new \Zend\Mime\Part('Hello world');
$textPart->type = "text/plain";

$body = new \Zend\Mime\Message();
$body->setParts(array($textPart, $htmlPart));

$message->setBody($body);
```

> For accessibility purposes, you should *always* provide both a text and HTML version of your mails.

### How to configure HttpClient with http_options and http_adapter

By defaut the adapter is Zend\Http\Client\Adapter\Socket but you can override it with other adapter like this in your ez_mail.*.local.php

```php
'ez_mail' => array(
        // Here your email service provider options

        'http_adapter' => 'Zend\Http\Client\Adapter\Proxy' // for example
    )
```

If you want to change some options of your adapter please refer to you adapter class in var $config [here](https://github.com/zendframework/zf2/tree/master/library/Zend/Http/Client/Adapter) and override these in your ez_mail.*.local.php like this :

```php
'ez_mail' => array(
        // Here your email service provider options

        // example for Socket adapter
        'http_options' => array(
            'sslverifypeer' => false,
            'persistent' => true,
        ),
    )
```

### Which provider should I choose?

We won't answer you :-)! Each provider have their own set of features. You should carefully read each website
to discover which one suit your needs the most.

However, for convenience purpose, we [have wrapped a pricing table](/docs/Pricing.md) for each email provider!
