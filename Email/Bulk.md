Generated on 19 Jan 2016

\Tidal\Email\Bulk mailer class
==============================

Specifying multiple 'to' recipients in `\Tidal\Email::send()` will NOT send
a bulk mail, but rather send one email to multiple people in the 'to' field.
Generally this is not the behavior you want, but it makes sense
semantically.

When sending a bulk mail, use this class. The `send` method here takes all
the same arguments that `\Tidal\Email::send()` does, so for usage please
read \Tidal\Email's docblock.

Use as follows:

    $successful = \Tidal\Email\Bulk::send([
        'to' => [
            $recipient1, $recipient2, ... $recipient10000
        ],
        'from' ...
        'subject' ...
        ... etc
    ]);

The return value of the `send` method is the number of messages successfully
queued. This method will also take each recipient and set them as the 'user'
property of the message's 'environment'.
 