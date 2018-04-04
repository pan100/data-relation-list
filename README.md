# data-relation-list
data-relationlist is a concept to move bidirectional linking between two related web sites away from the servers of a social network. There is also an API protocol to request a bidirectional connection. The bidirectional relations lists are stored by both relations and not at a third party site. The servers then control each other. There are also possibilities for feed syndication based on your own relation list if sites contain info about its feed. In this way we make the web more social without the need of a central social network.

A webmaster at url A contains or links to a page with this structure:

<ul data-relation-list data-relation-api="http://www.someserver.com/relations-api">
</ul>

A mechanism for adding and further periodic checks ensures double linking.

Let's say B (http://www.differentserver.com) wants to add A as a relation and link to B and be linked to from A.
Website B sends a GET to http://www.someserver.com/relations-api/ and gets this info:
{
    main: "http://www.someserver.com",
    link-title: "Some Server",
    ul: "http://www.someserver.com/relations/",
    api: "http://www.someserver.com/relations-api/"
}

B is now ready to send a relation request.
First B adds info to its own UL at http://www.differentserver.com/relations:

<ul data-relation-list data-relation-api="http://www.differentserver.com/relations-api">
    <li style="display:none" data-relation-pending><a href="http://www.someserver.com">Some Server</a> - <span data-relationtype>acquaintance</span></li>
</ul>

Then B sends a POST request to A (http://www.someserver.com/relations-api) containing:

{
    type: "add-request",
    main: http://www.differentserver.com,
    link-title: "Different Server",
    ul: "http://www.differentserver.com/relations",
    api: "http://www.differentserver.com/relations-api",
}

A now has received the relation request and awaits manual response. Let's say A wants to confirm.
A checks the ul of B and finds the li and sees that it is correct. Then A adds the relation to its own
ul:

<ul data-relation-list data-relation-api="http://www.someserver.com/relations-api">
    <li><a href="http://www.differentserver.com">Different Server</a> - <span data-relationtype>acquaintance</span></li>
</ul>

It links back but it is visible and does not contain the data-relation-pending attribute.
Then A sends a post to B (http://www.differentserver.com/relations-api):

{
    type: "add-request-approved",
    main: "http://www.someserver.com",
    ul: "http://www.someserver.com/relations/
}

B can now check that the ul at its location is correct and correct its own ul:

<ul data-relation-list data-relation-api="http://www.differentserver.com/relations-api">
    <li><a href="http://www.someserver.com">Some Server</a> - <span data-relationtype>acquaintance</span></li>
</ul>

Now the two sites are linking to each other with the given relation "acquaintance".
To ensure the two way linking the sites can now periodically automatically check each other's ul.
For instance every half hour. What to do if it finds an ul where it has been removed or the response
does not include the ul or if it includes a 404 or server error for instance, is up to the implementor
and the user. For instance, the user can be set to be notified and remove the link by choice or it
can be done automatically.

The task of the open source project is to provide implementations of the concept for various server
side languages and plug-ins to CMS systems. A start can be to create a site that automatically accepts
all and checks and automatically removes broken links.
