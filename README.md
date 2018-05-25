## Cockpit Single Sign On Demo

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

### Running the demo

Find a place where you feel comfortable running this code as root,
maybe a throw away virtual machine.  You need to have Cockpit, Python,
nginx, and sscg installed.

You need to run three processes simultaneously, which is best done in
three parallel terminals:

 1) ./max-pane
 2) ./max-pane-cockpit
 3) ./max-pane-nginx

"max-pane" takes the place of the management system.  It serves a
cruddy UI, offers a minimalistic API, and does a little OAuth.

"max-pane-cockpit" runs a instance of Cockpit that has been specially
configured to work with max-pane.

"max-pane-nginx" ties the two together and serves them on port 8080.

Thus, be sure to open port 8080 if you want to access these services
from outside of localhost.

Then start with these steps:

 - browse to `https://MASTER:8080`, where `MASTER` is the address of
   the machine that runs max-pane-nginx.  This brings up the login
   screen of max-pane.

 - log in with root / hunter2

 - type the root password of `MASTER` into the corresponding text
   input field and hit "Add".

 - right click on the "Web Console" link that has appeared to open
   Cockpit in a new tab.

 - switch to the new tab and watch the Cockpit UI for `MASTER` appear
   without having to log in again.

 - log out of Cockpit and it the "Login Again" link to seamlessly log
   in again.

Here is a recording of what should happen: https://youtu.be/FSSiL_oHWi0

### Description

Before diving into the code, it might help to have a look at the big
picture.

Implementing a seamless single sign on combines two features of
Cockpit: external authentication helpers, and OAuth.

#### External authentication

Cockpit can be configured to use an external program to perform
authentication.  Such a program usually gets handed the username and
password from the login page and is responsible for establishing the
Cockpit session.

Cockpit itself comes with a few authentication programs, including
`cockpit-session` for PAM and GSSAPI authentication, and `cockpit-ssh`
for remote authentication via SSH.

The `max-pane-cockpit` script configures Cockpit to use the
`cockpit-auth-max-pane` program to perform all remote authentication.

#### OAuth

In addition, Cockpit is configured to exclusively use OAuth for
authentication, instead of the normal user and password prompts.

When OAuth is used, Cockpit initiates a "implicit grant" scheme with
the max-pane server to get a access token.  This token is passed to
`cockpit-auth-max-pane`.

The job of `cockpit-auth-max-pane` is now to verify that the token is
valid, and if it is, spawn Cockpit's own `cockpit-ssh` with the right
parameters and credentials.

It does this job by making a API call to the `max-pane` server on
localhost.  If `max-pane` accepts the token, it will return the
credentials for use with `cockpit-ssh`.

#### Host parameter

One additional detail is that the authentication program can define
what the 'host' parameter actually means.  With `cockpit-ssh` it's the
actual hostname or IP address that you would also use on the
commandline with "ssh".  With our `cockpit-auth-max-pane` here it is
simply the index into the array of hosts.

#### Reverse proxying

This demo uses a reverse proxy in front of the management system and
Cockpit so that both can be served on the same port, with the same TLS
certificate, and without third-party cookies.

Accordingly, Cockpit doesn't use TLS at all and both Cockpit and
max-pane only listen on localhost.

Nginx redirect requests to `/web-console/...` to Cockpit and Cockpit
has been configured to expect the `/web-console/` prefix.

#### Linking

Thus, when taking all this togeher, all `max-pane` needs to do is to
use this simple relative URL for linking to Cockpit:

    /web-console/=INDEX

This can be a regular link, or the source for an iframe.
