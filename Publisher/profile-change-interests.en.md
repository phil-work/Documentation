This method is an alternative to the [profile interests
method](./profile-interests.en.md). It is a POST only method which allows you to
add or change interests without having to send the all interests in the
database.

POST request
------------

With the POST request you can edit certain interests. The interests can
be called by their name (as shown below) or by their ID, that can be
retrieved with [/database/\$databaseID/interests](./database-interests.en.md).

### Example POST request

This will be the payload for a POST request, which sets interest
'songbird' to false and interest 'beer' to true. All other interests
will not be changed.

~~~~ {.language-javascript}
{"songbird": false,"beer": true}
~~~~

The payload above can be send to the following url:

~~~~ {.language-javascript}
https://api.copernica.com/profile/$profileID/changeinterests?access_token=...
~~~~