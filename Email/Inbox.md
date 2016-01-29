Generated on 19 Jan 2016

\Tidal\Email\Inbox Class
========================

This simple class is invoked by a webhook (at this writing provided by 
sendgrid), and is responsible for creating inbound email records from an 
array or object. 

From your webhook, simply invoke:

    $email = \Tidal\Email\Inbox::parse($_POST);

The `parse` method will return an Email object (already saved). Any fields 
provided in `$_POST` that aren't Email model fields will be saved as 
metadata.
 