---
layout: post
title:  "Authorization"
date:   2019-02-15 07:26:24
categories: Authorization
---

# Basic Authorization
To solve the problem of hijack username and password in common Basic Authorization, I suggest to use MD5 and random ID.
Client Side:

1) Put a random String (Like "uwolafnfslggk") in request header as a value. Define a key in header(e.g: 'x-inner-key') to map to this value.

2) Combine "username, password and the value of 'x-inner-key'" to a new string.

3) MD5 this new string to generate a MD5 value.

4) Put this MD5 value to the header. Define a key in header ('x-inner-token') to map to this MD5 value.

Server Side:
1) Combine "username, password and the value of 'x-inner-key'" to a new string.

2) MD5 this new string to generate a MD5 value.

3) If the MD5 value is equal to header['x-inner-token'], then authorize success.
