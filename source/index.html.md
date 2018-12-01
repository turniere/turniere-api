---
title: turniere.re API

language_tabs: # must be one of https://git.io/vQNgJ
  - http

toc_footers:
  - <a href='https://github.com/turniere/turniere-api'>Edit on GitHub</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Kittn API! You can use our API to access Kittn API endpoints, which can get information on various cats, kittens, and breeds in our database.

We have language bindings in Shell, Ruby, Python, and JavaScript! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

Request bodies and responses are formatted according to the [JSON:API](https://jsonapi.org/) specification.

# Authentication

```http
GET /PATH HTTP/1.1
access-token: →ozpq-AC9P3iXS_HfgsfyAg
client: qy4HyWhq-FCod5qtzaitqw
expiry: 1544795677
uid: mail@example.com
```

```http
HTTP/1.1 200 OK
access-token: new-access-token
client: qy4HyWhq-FCod5qtzaitqw
expiry: 1544795676
uid: mail@example.com
```

The API uses [devise-token-auth](https://devise-token-auth.gitbook.io/devise-token-auth/) for authentication.

Therefore after signing in and receiving an `access-token` and `client` token in the header those values have to be included in each request.

The returned `access-token` is valid for one request and a new one will be returned on each request.

<aside class="notice">
Authentication is required for all update methods<br>
Their headers are omitted from all example requests on this page for brevitiy, though.
</aside>

# Users

## Sign in

```http
POST /users/sign_in HTTP/1.1
Content-Type: application/json

{
  "email": "mail@example.com",
  "password": "p4ssw0rd"
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json
access-token: →ozpq-AC9P3iXS_HfgsfyAg
client: qy4HyWhq-FCod5qtzaitqw
expiry: 1544795676
uid: mail@example.com

{
  "data": {
    "id": 1,
    "email": "mail@example.com",
    "provider": "email",
    "username": "user",
    "uid": "mail@example.com",
    "allow_password_change": true,
    "type": "user"
  }
}
```

Sign a user in to obtain an access token.

### Parameters

Parameter | Description
--------- | -----------
email     | E-Mail of the user
password  | Password of the user

## Sign out

```http
DELETE /users/sign_out HTTP/1.1
access-token: →ozpq-AC9P3iXS_HfgsfyAg
client: qy4HyWhq-FCod5qtzaitqw
uid: mail@example.com
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "success": true
}
```

Sign out a user and invalidate the access token.

# Tournaments

## List tournaments

```http
GET /tournaments HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "data": [
    {
      "id": "1",
      "type": "tournaments",
      "attributes": {
        "name": "Abby",
        "code": "9f94ae",
        "description": "Lorem ipsum dolor sit",
        "public": true
      }
    }
  ]
}
```

List all existing tournaments.

Unauthenticated users can only list public tournaments.

### URL Parameters

Parameter | Default | Description
--------- | ------- | -----------
type      | public  | Type of tournaments to include in the list (either `public` or `private`)

## Get a tournament

```http
GET /tournaments/1 HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "data": {
    "id": "1",
    "type": "tournaments",
    "attributes": {
      "name": "Abby",
      "code": "9f94ae",
      "description": "Lorem ipsum dolor sit",
      "public": true
    },
    "relationships": {
      "teams": {
        "data": [
          {
            "id": "1",
            "type": "teams"
          },
          {
            "id": "2",
            "type": "teams"
          }
        ]
      },
      "stages": {
        "data": [
          {
            "id": "1",
            "type": "stages"
          }
        ]
      }
    }
  }
}
```

Request a detailed Tournament object.
This will include all available attributes and relations.
Public tournaments can be requested by anyone, private tournaments only by their owner.

## Create a tournament

```http
POST /tournaments HTTP/1.1
Content-Type: application/json

{
  "data": {
    "attributes": {
      "name": "Tournament 01",
      "description": "An interesting description",
      "public": false
    },
    "relationships": {
      "teams": {
        "data": [
          {
            "type": "teams",
            "id": "1"
          }
        ]
      }
    }
  }
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "data": {
    "id": "1",
    "type": "tournaments",
    "attributes": {
      "name": "Tournament 01",
      "code": "91d623",
      "description": "An interesting description",
      "public": false
    },
    "relationships": {
      "teams": {
        "data": [
          {
            "id": "1",
            "type": "teams"
          }
        ]
      },
      "stages": {
        "data": []
      }
    }
  }
}
```

### Parameters

All parameters are optional, therefore one request can be used to update either all/some or no parameters.

Parameter   | Description
---------   | -----------
name        | Name of the tournament
description | Description of the tournament
public      | Whether the tournament is public (`true` or `false`)


## Update a tournament

```http
PATCH /tournaments/1 HTTP/1.1
Content-Type: application/json

{
  "data": {
    "attributes": {
      "name": "New name",
      "description": "New description",
      "public": false
    },
    "relationships": {
      "teams": {
        "data": [
          {
            "id": "1",
            "type": "teams"
          }
        ]
      }
    }
  }
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "data": {
    "id": "1",
    "type": "tournaments",
    "attributes": {
      "name": "New name",
      "code": "9f94ae",
      "description": "New description",
      "public": false
    },
    "relationships": {
      "teams": {
        "data": [
          {
            "id": "1",
            "type": "teams"
          }
        ]
      },
      "stages": {
        "data": [
          {
            "id": "1",
            "type": "stages"
          }
        ]
      }
    }
  }
}
```

Update tournament attributes and/or relationships.

Updating relationships will replace them completely.

### Parameters

All parameters are optional, therefore one request can be used to update either all/some or no parameters.

Parameter   | Description
---------   | -----------
name        | Name of the tournament
description | Description of the tournament
public      | Whether the tournament is public (`true` or `false`)

## Delete a tournament

```http
DELETE /tournaments/1 HTTP/1.1
```

```http
HTTP/1.1 204 No Content
```

# Teams

## Show a team

```http
GET /teams/1 HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "data": {
    "attributes": {
      "name": "Murphy"
    },
    "id": "1",
    "type": "teams"
  }
}
```

## Update a team

```http
PATCH /teams/1 HTTP/1.1
Content-Type: application/json

{
  "data": {
    "attributes": {
      "name": "Name01"
    }
  }
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "data": {
    "id": "1",
    "type": "teams",
    "attributes": {
      "name": "Name01"
    }
  }
}
```

### Parameters

Parameter   | Description
---------   | -----------
name        | Name of the team

