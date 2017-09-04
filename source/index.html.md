---
title: Vendr API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - curl
  - http
  - python

toc_footers:
  - <a href='#'>Vendr github</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the **Vendr API**!

The source code for the API can be found [here](https://github.com/tallosan/vendr-api/tree/master/vendr)

**Introducing SuperDev:**

An example is often worth a thousand words. As such, these docs will attempt to provide
developers with as many as is necessary. To further illustrate our examples, we'll be
using a test devloper named Super Dev to demonstrate API calls & handling.

# Authentication

```python
import requests

base_url = 'http://api.vendr.xyz/o/token/'
api = requests.get(base_url)
```

```shell
# To get an `access_token` via Curl, we would do:
curl -X POST -d "grant_type=password&username=<user_name>
&password=<password>" -u"CLIENT_ID:CLIENT_SECRET" 
http://api.vendr.xyz/o/token/

# Using the syntax above, Super Dev can obtain an access token as follows:
curl -X POST -d "grant_type=password&username=superdev@vendr.xyz
&password=superpwd" -u"foOWPCONHldZXPnkZ1wVUEBemyVZp3dcARY4p7Lb:dGoXvLV0
vU3EikGHUcBrRXhxLJyyLxW4jlReSmJC2UQbo8EYta5W1MWZsLfgVgHh5k5zkhuSprIIVL6T
Z8x4qPplqieeO9C4gQqN8VMXvnFx64PlmLdZ7tpbRdapychk" 
http://api.vendr.xyz/o/token/
```

> Don't forget the OAuth Key.

`http://api.vendr.xyz/o/token/`

Vendr uses the **OAuth 2 Protocol** for authentication. As such, authentication is all
token based. Each User has two tokens:

`access_token`, `refresh_token`

All authentication is done using a user's `access_token`. To obtain one, we'll need
to use the Vendr OAuth Application's client ID and secret, along with the user's
own username and password.


**OAuth Keys**:

`client_id`: foOWPCONHldZXPnkZ1wVUEBemyVZp3dcARY4p7Lb

`client_secret`: dGoXvLV0vU3EikGHUcBrRXhxLJyyLxW4jlReSmJC2UQbo8EYta5W1MWZsLf
gVgHh5k5zkhuSprIIVL6TZ8x4qPplqieeO9C4gQqN8VMXvnFx64PlmLdZ7tpb
Rdapychk

<aside class="notice">
Note, authentication must be done via a <code>user's</code> own credentials.
</aside>

# Users

This is an object representing a Vendr user

## The User object.

> Example Response

```json
[
  {
      "email": "tallosan@vendr.xyz", 
      "id": 1, 
      "join_date": "2017-08-04T00:45:02.454263Z", 
      "password": "bcrypt_sha256$$2b$12$fVAYDPR3fs71sqDxYKg.fO.UMgMI/kUloiZboyxj04lLc0F6lLsNe", 
      "quick_profile": {
          "first_name": "Andrew", 
          "last_name": "T", 
          "prof_pic": "https://s3.ca-central-1.amazonaws.com/media.vendr/users/prof_pics/56c37365-9bfd-4169-b6ea-79cede5fa0b8"
      }
  },
  { ... }
]
```

User objects contain sensitive / personal information. As anyone can view a User
object, it makes sense for us to hide this info from anyone but the actualy user
who owns the object. Thus, some fields are protected and will **not** be included
in the responses unless the correct authentication is provided.

Attribute | Type | Description
--------- | ------- | -----------
id | integer | The user's ID.
email | string | The user's email address.
password | string | <span style="color:##c1ef65">`protected`</span> The user's password. Note, this will be encrypted if the user accessing the user object does not have the correct auth.
quick_profile | profile | `read only` A quickview of the user's profile.
join_date | date | The date the user joined.


### HTTP Requests

List all users.

<span style="color:#b93d6a">`GET`</span> `http://api.vendr.xyz/v1/users/`


## Create a User

> HTTP Request

```shell
POST http://api.vendr.xyz/v1/users/
```

> Example Request

```shell
curl "http://api.vendr.xyz/v1/users/"
     -X POST
     -d {
	    "email": "superdev@vendr.xyz",
   	    "password": "superpwd",
 	    "profile": {
	      "first_name": Super,
	      "last_name": Dev
	    }
      }
```

> Example Response

```json
HTTP 201 Created

curl "http://api.vendr.xyz/v1/users/"
     -X POST
     -d {
	    "email": "superdev@vendr.xyz",
   	    "password": <encrypted_password>,
 	    "quick_profile": {
	      "first_name": Super,
	      "last_name": Dev,
	      "prof_pic": "https://s3.ca-central-1.amazonaws.com/media.vendr/users/defaults/default_user_prof.jpg"
	    }
      }
```

To create a user, we need to at least provide an email and a password.

Note, we can set a user's profile attributes (e.g. <span style="color:#0099e5">first_name</span>) in the same call, and that this profile creation / update is only partial. I.e. we only have to pass as many fields as we have / want to. In the example case, notice how SuperDev only passes his first name and last.

### Arguments

Parameter | Type | Description
--------- | ------- | -----------
email | string | <span style="color:#fe5a1d">`required`</span> The user's email.
password | string | <span style="color:#fe5a1d">`required`</span> The user's password.
first_name | string | `optional` The user's first name.
last_name | string | `optional` The user's last name.
bio | string | `optional` The user's bio.
location | string | `optional` The user's location.
prof_pic | image | `optional` The user's profile pic.

### Returns

Returns the User object, with the Profile in quick view mode.


## Retrieve a specific User

> HTTP Request

```shell
GET http://api.vendr.xyz/v1/users/<user_pk>/
```

> Example Request

```shell
curl "http://api.vendr.xyz/v1/users/1/"
     -X GET
     -H "Authorization:Bearer <user_access_token>"
```

> Example Response

```json
HTTP 200 OK

{
    "email": "tallosan@vendr.xyz", 
    "id": 1, 
    "join_date": "2017-08-04T00:45:02.454263Z", 
    "password": "bcrypt_sha256$$2b$12$fVAYDPR3fs71sqDxYKg.fO.UMgMI/kUloiZboyxj04lLc0F6lLsNe", 
    "quick_profile": {
        "first_name": "Andrew", 
        "last_name": "T", 
        "prof_pic": "https://s3.ca-central-1.amazonaws.com/media.vendr/users/prof_pics/56c37365-9bfd-4169-b6ea-79cede5fa0b8"
    },
}
```

Retrieve a specific user, based on the given user ID.

### URL Parameters

Parameter | Description
--------- | -----------
user_pk | The ID of the user to retrieve

### Returns

The User with the given ID.


## Update a specific User

> HTTP Request

```shell
PUT http://api.vendr.xyz/v1/users/<user_pk>/
```

> Super Dev updates their email to "superdev_v2@vendr.xyz".

> Example Request

```shell
curl -H "Content-Type: application/json"
     -X PUT -d '{"email: "superdev_v2@vendr.xyz"}'
     "http://api.vendr.xyz/v1/users/<user_pk>/"
     -H "Authorization:Bearer <user_access_token>"
```

> Example Response

```json
HTTP 200 OK

{
    "email": "superdev_v2@vendr.xyz", 
    "id": 1, 
    "join_date": "2017-08-04T00:45:02.454263Z", 
    "password": "bcrypt_sha256$$2b$12$fVAYDPR3fs71sqDxYKg.fO.UMgMI/kUloiZboyxj04lLc0F6lLsNe", 
    "quick_profile": {
        "first_name": "Andrew", 
        "last_name": "T", 
        "prof_pic": "https://s3.ca-central-1.amazonaws.com/media.vendr/users/prof_pics/56c37365-9bfd-4169-b6ea-79cede5fa0b8"
    }
}
```

Update a specific user, based on the given user ID.

Note, we cannot update <span style="color:#0099e5">`quick_profile`</span>, as it's read only. All other fields, however,
are valid.

### URL Parameters

Parameter | Description
--------- | -----------
user_pk | The ID of the user to retrieve

### Returns

The updated User object.


## Delete a specific User

> HTTP Request

```shell
DELETE http://api.vendr.xyz/v1/users/<user_pk>/
```

> Example Request

```shell
curl "http://api.vendr.xyz/v1/users/`user_pk`/
     -X DELETE
     -H "Authorization:Bearer <user_access_token>"
```

> Example Response

```json
HTTP 204 Deleted

[]
```

Delete a specific user, based on the given user ID.


### URL Parameters

Parameter | Description
--------- | -----------
user_pk | The ID of the user to delete

### Returns

An empty JSON body, with HTTP response code 204.



# user.Profile

A Profile object.

Created when a User object is created.

## The Profile object.

> Example Response

```json
{
    "bio": This is my Bio.
    "first_name": Super,
    "last_name": Dev,
    "location": "Toronto",
    "prof_pic": "https://s3.ca-central-1.amazonaws.com/media.vendr/users/prof_pics/15034150-79cb-4291-a465-cd6f7d79344f"
}
```

The Profile object contains any personal information about a user.


Attribute | Type | Description
--------- | ------- | -----------
first_name | string | The user's first name.
last_name | string | The user's last name.
bio | string | The user's bio.
location | string | The user's location.
prof_pic | image | The user's profile pic.

## Retrieve a user's Profile

> HTTP Request

```shell
GET http://api.vendr.xyz/v1/users/<user_pk>/profile/
```

> Example Request

```shell
curl "http://api.vendr.xyz/v1/users/`user_pk`/profile/
     -X GET
     -H "Authorization:Bearer <user_access_token>"
```

> Example Response

```json
HTTP 200 OK

{
    "bio": This is my Bio.
    "first_name": Super,
    "last_name": Dev,
    "location": "Toronto",
    "prof_pic": "https://s3.ca-central-1.amazonaws.com/media.vendr/users/prof_pics/15034150-79cb-4291-a465-cd6f7d79344f"
}
```

Retrieve the given User's full profile.

### URL Parameters

Parameter | Description
--------- | -----------
user_pk | The ID of the user to delete


## Update a user's Profile

> HTTP Request

```shell
PUT http://api.vendr.xyz/v1/users/<user_pk>/profile/
```

> Example Request

```shell
curl "http://api.vendr.xyz/v1/users/`user_pk`/profile/
     -X PUT
     -d '{"bio": "My new bio.", "last_name": "New Last Name"}'
     -H "Authorization:Bearer <user_access_token>"
```

> Example Response

```json
HTTP 200 OK

{
    "bio": "My new bio."
    "first_name": "Super",
    "last_name": "New Last Name",
    "location": "Toronto",
    "prof_pic": "https://s3.ca-central-1.amazonaws.com/media.vendr/users/prof_pics/15034150-79cb-4291-a465-cd6f7d79344f"
}
```

Update the given User's profile.

### URL Parameters

Parameter | Description
--------- | -----------
user_pk | The ID of the user to delete

### Returns

The updated Profile object.


## Invalid Methods

<span style="color:#b93d6a">POST</span> `http://api.vendr.xyz/v1/users/<user_pk>/profile`

<span style="color:#b93d6a">DELETE</span> `http://api.vendr.xyz/v1/users/<user_pk>/profile/`


# user.Notification

A Notification object.

Notifications are created behind the scenes in response to certain actions
performed by users. We have a number of different kinds of notifications,
however all have the same core API.

## The Notification object.

```json
{
    "contract": "4aa22aab-b829-4bfc-a85d-e9883276eb6e", 
    "description": "Jana has sent you a new contract on your property 3000 Victoria Park Ave.", 
    "id": "e7d57c2e-fe95-4d95-8a60-a0448fa14aae", 
    "is_viewed": true, 
    "recipient": 1, 
    "timestamp": "2017-08-13T04:28:02.638879Z", 
    "transaction": "f5740fc7-993f-434e-8e47-b96a5bfd50a7"
}
```

Notifications contain information about the resources that they belong to
and the action that triggered them. They also contain useful info about their
relationship with the users they're involved with.

Attribute | Type | Description
--------- | ------- | -----------
pk | uuid | The notification ID.
description | string | Describes the notification's purpose. This is meant for users to see.
`offer`,`contract` | uuid | This is the ID of the resource that prompted the notification's creation. Note, the field is named according to the resource that created it, ie if an Offer triggered this notification, the field will be named "offer".
is_viewed | bool | Indicates whether or not e notification recipient has viewed it. Note, the recipient is responsible for changing this value.
timestamp | date | The date this notification was created.
transaction | uuid | `optional` The transaction that this notification belongs to. Only valid on transaction notifications.

## Retrieve a user's Profile

> HTTP Request

```shell
GET http://api.vendr.xyz/v1/users/<user_pk>/profile/
```

> Example Request

```shell
curl "http://api.vendr.xyz/v1/users/`user_pk`/profile/
     -X GET
     -H "Authorization:Bearer <user_access_token>"
```

> Example Response

```json
HTTP 200 OK

{
    "bio": This is my Bio.
    "first_name": Super,
    "last_name": Dev,
    "location": "Toronto",
    "prof_pic": "https://s3.ca-central-1.amazonaws.com/media.vendr/users/prof_pics/15034150-79cb-4291-a465-cd6f7d79344f"
}
```

Retrieve the given User's full profile.

### URL Parameters

Parameter | Description
--------- | -----------
user_pk | The ID of the user to delete


## Update a user's Profile

> HTTP Request

```shell
PUT http://api.vendr.xyz/v1/users/<user_pk>/profile/
```

> Example Request

```shell
curl "http://api.vendr.xyz/v1/users/`user_pk`/profile/
     -X PUT
     -d '{"bio": "My new bio.", "last_name": "New Last Name"}'
     -H "Authorization:Bearer <user_access_token>"
```

> Example Response

```json
HTTP 200 OK

{
    "bio": "My new bio."
    "first_name": "Super",
    "last_name": "New Last Name",
    "location": "Toronto",
    "prof_pic": "https://s3.ca-central-1.amazonaws.com/media.vendr/users/prof_pics/15034150-79cb-4291-a465-cd6f7d79344f"
}
```

Update the given User's profile.

### URL Parameters

Parameter | Description
--------- | -----------
user_pk | The ID of the user to delete

### Returns

The updated Profile object.


## Invalid Methods

<span style="color:#b93d6a">POST</span> `http://api.vendr.xyz/v1/users/<user_pk>/profile`

<span style="color:#b93d6a">DELETE</span> `http://api.vendr.xyz/v1/users/<user_pk>/profile/`


