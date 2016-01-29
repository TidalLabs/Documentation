Generated on 19 Jan 2016

\Tidal\Email Manager
====================

The Email component manages sending and receiving email.

Inbound mail requires pecl mailparse extension.

Basic Usage
-----------

If all you want to do is send an email, here you go!

    \Tidal\Email::send(array(

        // Subject line is always required.
        'subject' => 'Hello, this is a test email',

        // Emails are sent in both HTML and plaintext. Try to provide both
        // whenever possible, but text is required.
        // Emails also use Twig templates! Variables are passed via `environment` below.
        'html'    => '<p>Hello there, {{ user.name }}!</p>',
        'text'    => 'Hello there, {{ user.name }}!',

        // Only 'email' is required, but please also provide a name.
        'from'    => ['email' => 'test        // You should also associate a Channel ID with the email whenever possible.
        // This needs to be a string.
        'channel_id'   => (string) $channel->_id,

        // Send to a user by passing an object with name, email, and mongo_id properties.
        // Again, mongo_id should be string
        'to' => ['name' => 'Burak Kanber', 'email' => 'burak        // You can send to a list of users.
        // BUT! This is not a bulk send! This will send ONE email thread to
        // the users specified.
        // As if you had put three emails in the 'to' field in your email client.
        // For bulk mail, see below.
        // Note that name and mongo_id are optional. But recommended.
        'to' => [
            ['name' => 'Burak Kanber', 'email' => 'burak            ['name' => 'Matt Myers', 'email' => 'matt            ['email' => 'noemi        ],

        // CC and BCC work the same way.
        // Yes, you _can_ specify email addresses only, but it's probably
        // better to neatly structure stuff into objects.
        'cc' => ['burak        'bcc' => 'noemi        // An array of variables to pass to the twig template. Optional.
        'environment' => [
	           'user' => $user,
            'things' => ['thing1', 'thing2']
        ],

        // Label your email too, if you like. For instance, if you don't
        // want a bulk mailer to clog up the outbox UI, you can set the label
        // to 'finished'
        'label' => 'finished',

        // Don't want to send the email? For some reason?
        'sendable' => false,

        // If for some reason you need to spoof an email being received, you
        // can force it into the inbox.
        // This email will _not_ be sent.
        'inbox' => true,

    ));

Calling `send` queues your email to be sent as soon as the email queue consumer
is available. You should very rarely, if ever at all, send an email without
queueing it.

Use User objects whenever possible (even in the `from` field) so we can better
track threads. And use the `channelId` field whenever a channel context is
available, so we can track emails to channels as well.


Email Templates
---------------

The Email\Template class is responsible for managing email templates. It's
used in two ways:

1) The Template class is used to render Twig templates in the body of one-off emails.
2) The Template class is also used to store saved email templates for things
   like welcome messages, "your post was published" notifications, badge
   awarded notifications, campaign workflow emails, etc.

See Email/Template.php for an in-depth description. However, the short version is this:

    $template = Template::whereChannelId($yourId)->whereKey('postpublished')->first();
    $template->send( /* same options as above, minus subject, text, and html 8? );

Bulk Emails
-----------

Most likely you don't want to actually send an email to more than one person
in the 'to' field. You can instead use the Email\Bulk class to send bulk
emails to thousands of people. The options are exactly the same as
`Email::send()`, but the 'to' field is split up and emails are queued
individually.

The Bulk mailer class also does you one huge favor -- it applies each 'to'
recipient as an individual 'user' environment variable, letting you still
use templates as you normally would. Other environment variables are shared
amongst the whole group.

    \Tidal\Email\Bulk::send([
        'to' => [
            ['email' => 'burak            ['email' => 'matt        ],
        'subject' => "Hello there",
        'text' => 'Hi there, {{ user.name }}. We've got an awesome new campaign called {{ campaign.name }} ready for you.',
        'environment' => [
            'campaign' => ['name' => 'Awesome Campaign']
            // Note that 'user' is supplied automatically, with the data from the 'to' field
        ]
        'from' => ['name' => "Today Parenting", 'email' => "today    ]);

The above will do basically what you think it will do.

Tests on the dev server can queue approximately 10,000 emails in 30 seconds using the Bulk mailer.

Receiving Mail
--------------

See Email\Inbox for a description of how incoming mail works. But the quick
version is as follows:

    $emails = \Tidal\Email::whereInbox(true)->whereChannelId( ... )->take(10)->get() ...

Access the $email->html, ->text, ->subject, etc properties as necessary.

Only the domain tidalmail.com is capable of receiving email for the Email
Manager. Senders can send to any address into our database.

Email Drafts
------------

An email draft is not eligible to be sent, and is indicated as a non-eligible
email by setting `sendable = false`.

Flushing Outbound Queue
-----------------------

You should not need to flush the queue yourself. Gator/controller/crawl.php
has a method that uses scopeQueued below, and calls flushSend() on each
email it finds.

You can see unsent emails by querying inbox=false, sent=false,
sendable=true, (scheduled=false || (scheduled=true && scheduled_for < * now()))

Scheduled Emails
----------------

Forthcoming feature.

Architecture
------------

Seven classes make up the email manager.

- Email: This class; represents an email, lets you query emails, and provides the main interface for interacting with the email manager.
- Email\Template: Creates and stores email templates for re-use in either automated or manual mailings.
- Email\Sender: A helper class that manages the actual transport of email. Wraps SwiftMailer.
- Email\Logger: \Tidal\Logger extension that scopes logs with the Email channel.
- Email\Bulk: Helper class to manage bulk sending emails.
- Email\Inbox: Helper class that manages parsing and saving incoming emails via SendGrid's Parse API.

 