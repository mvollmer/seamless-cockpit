Cockpit Single Sign On Demo
---------------------------

Cockpit can easily be linked to from other web applications (the web
being what it is), or even be embedded into an iframe.  However, the
user needs to log into the linked to Cockpit just like he/she needs to
log into a standalone Cockpit.

If the linking web application already has all necessary access to the
hosts that Cockpit will be managing, it would be nice to let the user
only log in once, into the web application.  When hopping over to
Cockpit, the user should already be logged in.

A typical web application would be a management system like Foreman,
ManageIQ, or oVirt.  They all manage hosts behind a "single pane of
glass", and on the page for a given host, one might find an iframe or
a link that leads to the Cockpit UI for that host.  The user has
logged into the management system already and nominally has all the
necessary power to do anything to the host, and it is annoying that
Cockpit asks again for credentials for that specific machine.


This repository contains a working sketch of how to set up the
management system together with Cockpit to seamlessly transfer
credentials to Cockpit.

# Running the demo

Find a place where you feel comfortable running this code as root,
maybe a throw away virtual machine.  You need to have Cockpit
installed and Python.

You need to run two processes simultaneously, which is best done in
two parallel terminals:

 1) ./max-pane
 2) ./run-cockpit-ws

"max-pane" takes the place of the management system.  It serves a
cruddy UI on port 8080.

"run-cockpit-ws" runs a specially configured instance of Cockpit on
port 9999.

Thus, be sure to open ports 8080 and 9999 if you want to access these
services from outside of localhost.

Then start with these steps:

 - browse to `http://MASTER:8080`, where `MASTER` is the address of
   the machine that runs max-pane.

 - log in with root / hunter2

 - type the root password of `MASTER` into the corresponding text
   input field and hit "Add".

 - right click on the "Web Console" link that has appeared to open
   Cockpit in a new tab.

 - switch to the new tab and watch the Cockpit UI for `MASTER` appear
   without having to log in again.

 - log out of Cockpit and it the "Log In Again" link to seamlessly log
   in again.

Here is a recording of what should happen:

    XXX

# Detailed description

coming up...
