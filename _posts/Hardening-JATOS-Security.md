---
layout: post
title: Hardening JATOS' Security
author: Kristian Lange
email: lange.kristian@gmail.com
---

JATOS 3 is out and it got some nice security improvements that I want to talk about in more detail in this post.

## User session

First and most important is the better user session security. 

_Don't confuse the user session with the study/batch/group session: the first one is responsible for the authentication of a user in JATOS' GUI - the latter [transfers information inbetween components, batches or groups during study runs](http://www.jatos.org/Session-Data-Three-Types.html)._

A user session cares for authentication and because HTTP is stateless, in order to associate a request to any other request, you need a way to store user data between HTTP requests ([http://stackoverflow.com/questions/3804209/what-are-sessions-how-do-they-work]).

JATOS' user session ID is stored in [Play's session cookie](https://www.playframework.com/documentation/2.5.x/JavaSessionFlash). This means it is readable for everyone but can't be manipulated since Play always adds a hash and any tempering in the cookie string would be detected subsequently.

Btw if you access JATOS via HTTPS only one should set `play.http.session.secure=true` in the `production.conf` which prevents Play's session cookie to be written in unencrypted requests.

Since JATOS 3 each user session now gets a new session ID (even for the same user) and during logout the ID gets invalidated. User session IDs are stored on the server side in an cache together with the users IP. Therefore even if someone somehow gets access to Play's session cookie (session hijacking), they can't use it to access JATOS since they have a different IP/address.

Additionally JATOS version 3 now has a session timout: By default the user session is invalidated after an hour of inactivity and is limited to 24 hours overall. Both times are configurable in [JATOS' `production.conf`](http://www.jatos.org/Configure-JATOS-on-a-Server.html).



## Account lockout


- encrypted session cookie
- security headers
- restrict /jatos to intranet
