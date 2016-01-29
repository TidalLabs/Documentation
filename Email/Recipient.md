Generated on 19 Jan 2016

\Tidal\Email\Recipient
======================

This class is a simple relational table that relates users to Email records. 
The purpose of this table is to make Email queries against users easier, as 
currently the Email model stores json serialized data for sender, recipient, 
cc, and bcc.

If you want to find emails to or from a specific user, query against this 
table and JOIN on the Email table. 
 