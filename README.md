Cockpit Single Sign On  Demo
-----------------------------

Cockpit can easily be linked to from other web applications, or even
be embedded into an iframe.  However, the user needs to log into the
linked to Cockpit just like he/she needs to log into a standalone
Cockpit.

If the linking web application already has all necessary access to the
hosts that Cockpit will be managing, it would be nice to let the user
only log in once, into the web application.  When hopping over to
Cockpit, the user should already be logged in.

A typical web application would be management systems like Foreman,
ManageIQ, or oVirt.  They all manage hosts behind a "single pane of
glass", and on the page for a given host, one might find an iframe or
a link that leads to the Cockpit UI for that host.  The user has
logged into the management system already and nominally has all the
necessary power to do anything to the host, and it is annoying that
Cockpit asks again for credentials for that specific machine.


This repository contains a working sketch of how to set up the
management system together with Cockpit to seamlessly transfer
credentials to Cockpit.


Mock:

 - login creds are root / hunter2

 - creates random number, sets as cookie, uses for auth

 - shows dashboard, can add hosts and give user / passwd / private
   key, stored in memory.

 - iframe with Cockpit

 - access_token is random number

 - cockpit ssh program uses access_token to query mock 'rest' api.

 - mock api looks up based on access_token
