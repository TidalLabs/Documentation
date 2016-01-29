Generated on 19 Jan 2016

\Tidal\Email\Sender
===================

This is a transport class that manages the actual sending of emails via
SwiftMailer and SendGrid.

Unless you are working on \Tidal\Email's `flushSend` method, or have some
hyper-specific use case (you probably don't), you'll likely never need to
use this class. Nonetheless...

SwiftMailer requires both a "transport" and an instance of the Swift_Mailer
object. We're using the Swift_SmtpTransport transport hooked up to SendGrid
(see `initTransport`). This class is responsible for bootstrapping both the
transport and the Swift_Mailer instance.

The `transformAddresses` method is responsible for transforming email
address from the format that \Tidal\Email likes to the format that
SwiftMailer likes.

The `attachEmail` method attaches an email object to this Sender and
prepares it to be sent; finally the `send` method invokes the mailer's
`send` and gets the email out.

Usage:

    $email = \Tidal\Email::first();
    $sender = new \Tidal\Email\Sender();
    $sender->attachEmail($email);
    $sender->send();

Or:

    $email = \Tidal\Email::first();
    $sender = new \Tidal\Email\Sender($email);
    $sender->send();

 