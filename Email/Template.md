Generated on 19 Jan 2016

\Tidal\Email\Template Class
===========================

This email template class is responsible for saving and rendering Twig-based
email templates. This class is used _both_ for one-off emails _and_ stored,
templated emails. In the case of one-off emails, the template record is
never actually saved; we just use the Twig helper methods in this class to
render the one-off email.

Templated emails, on the other hand, should be given a `key` and
`channel_id`, and invoked from other parts of the application. Some examples
of templated emails include Gator's "postpublished" email,
"applicationreceived", campaign workflow emails, badge awarding emails, etc.
These templates _are_ stored in the DB.


Basic Usage
-----------

The most typical use-case is sending an email for a template that already exists:

    $template = \Tidal\Email\Template::whereChannelId($channelId)
        ->whereKey('postpublished')
        ->firstOrFail();
    $template->send([
        // to, from, environment, etc.
    ]);

Saving a new template is straightforward. This is an Eloquent model so you
can use the `create()` constructor or `new Template` and then `save()`

    Template::create([
        'channel_id' => 'whatever',
        'html' => '<p>Hello {{ user.name }},</p><p>I'm sending you an email.</p>',
        'text' => "Hello {{ user.name }},\n\nI'm sending you an email.</p>",
        'subject' => 'Test email',
        'key' => 'testemail'
    ]);

Twig Templates
--------------

Most of the time, you're just going to be interpolating simple variables and
objects through basic Twig usage: {{ user.name }}, {{ post.title }}, etc.

Objects to be interpolated into the templates need to be set in this class
with the `setVariable` method. However, when invoking from Email::send or
Template::send, you simply pass your Twig variables in through the
`environment` parameter, and the `send` wrapper calls `setVariable` for you.
The only time you'd need to call `setVariable` directly is if you're using
this class directly to render Twig templates for some other non-email
purpose.

Fields
------

Field		| Type			| Description
-------------|---------------|------------------------------------------
id			| int			| Primary Key
channel_id	| varchar(24)	| Channel the template belongs to
created_at	| datetime		| Created
updated_at	| datetime		| Updated
html			| text			| HTML of the email
text			| text			| Plaintext version of the email
subject		| varchar(255)	| Subject line
pinned		| boolean		| Pinned/favorite
key			| varchar(64)	| Key for internal use (eg, channel 'postpublished' email)

 