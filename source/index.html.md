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
Welcome to the Turnie.re API documentation!
This document will describe all available endpoints and possible responses.

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

The response of a successful [sign in request](#sign-in) will return an initial `access-token` and `client` header.
Those and the `uid` header (set to the email of the logged in user) have to be included in every request to an authenticated path.

The `access-token` is valid until `expiry` (unix timestamp) or **one** request.
Every request will contain a new `access-token` for the next request.

For implementation details and more examples check out [devise-token-auth](https://devise-token-auth.gitbook.io/devise-token-auth/).

<aside class="notice">
Authentication is required for all update and create methods.<br>
Even though the authentication headers are omitted from all examples, they are still required.
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
  "id": 1,
  "email": "mail@example.com",
  "provider": "email",
  "username": "user",
  "uid": "mail@example.com",
  "allow_password_change": true,
  "type": "user"
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

## Register

```http
POST /users HTTP/1.1
Content-Type: application/json

{
  "email": "jonny3210@example.com",
  "username": "jonny3210",
  "password": "123456"
}

```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "email": "mail@example.com",
  "provider": "email",
  "username": "user",
  "uid": "mail@example.com",
  "allow_password_change": false,
  "type": "user"
}

```

Register a user.

<aside class="notice">
The response headers will include authentication headers as well.
</aside>

### Parameters

Parameter | Description
--------- | -----------
email     | E-Mail of the new user
username  | Username of the new user
password  | Password of the new user

# Tournaments

## List tournaments

```http
GET /tournaments HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "code": "184c4a",
    "id": 1,
    "name": "Shadow",
    "public": true
  }
]

```

List all existing tournaments.

Unauthenticated users can only list public tournaments.

### URL Parameters

Parameter | Default | Description
--------- | ------- | -----------
type      | public  | Type of tournaments to include in the list (either `public` or `private`)

## Show a tournament

```http
GET /tournaments/1 HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "code": "184c4a",
  "description": null,
  "id": 1,
  "name": "Shadow",
  "owner_username": "eric_kunze",
  "public": true,
  "stages": [
    {
      "groups": [],
      "id": 1,
      "level": null,
      "matches": [
        {
          "id": 1,
          "match_scores": [
            {
              "id": 1,
              "points": 0,
              "team": {
                "id": 1,
                "name": "Gracie"
              }
            },
            {
              "id": 2,
              "points": 1,
              "team": {
                "id": 2,
                "name": "Misty"
              }
            }
          ],
          "position": null,
          "state": "single_team"
        }
      ]
    },
    {
      "groups": [
        {
          "group_scores": [
            {
              "group_points": 0,
              "id": 1,
              "received_points": 0,
              "scored_points": 0,
              "team": {
                "id": 1,
                "name": "Gracie"
              }
            },
            {
              "group_points": 0,
              "id": 2,
              "received_points": 0,
              "scored_points": 0,
              "team": {
                "id": 2,
                "name": "Misty"
              }
            }
          ],
          "id": 1,
          "matches": [
            {
              "id": 2,
              "match_scores": [
                {
                  "id": 3,
                  "points": 0,
                  "team": {
                    "id": 1,
                    "name": "Gracie"
                  }
                },
                {
                  "id": 4,
                  "points": 1,
                  "team": {
                    "id": 2,
                    "name": "Misty"
                  }
                }
              ],
              "position": null,
              "state": "single_team"
            }
          ],
          "number": null
        }
      ],
      "id": 2,
      "level": null,
      "matches": []
    }
  ],
  "teams": [
    {
      "id": 1,
      "name": "Gracie"
    },
    {
      "id": 2,
      "name": "Misty"
    }
  ]
}

```

Request a detailed Tournament object.
This will include all available attributes and relations.
Public tournaments can be requested by anyone, private tournaments only by their owner.

## Create a tournament
### Without group stage

```http
POST /tournaments HTTP/1.1
Content-Type: application/json

{
  "name": "Tournament 01",
  "description": "An interesting description",
  "public": false,
  "teams": [
    {
      "id": 1
    },
    {
      "id": 2
    }
  ]
}
```

```http
POST /tournaments HTTP/1.1
Content-Type: application/json

{
  "name": "Tournament 01",
  "description": "An interesting description",
  "public": false,
  "teams": [
    {
      "name": "Name 01"
    },
    {
      "name": "Name 02"
    },
    {
      "name": "Name 03"
    },
    {
      "name": "Name 04"
    }
  ]
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

TBD
```

Create a new tournament.

This will associate the given teams to a new tournament object or create them and generate a playoff stage.

You can either reuse teams from an existing tournament (first example) or create new teams (second example).

#### Parameters

Parameter   | Description | Default
---------   | ----------- | -------
name        | Name of the tournament |
teams       | Teams to create/add to the tournament (In case of groupstage add a group identifier to each team, if there is none, everyone will be in one big group) |
description | Description of the tournament | `''`
public      | Whether the tournament is public (`true` or `false`) | `true`
groupstage  | Whether the tournament will start with a group stage (`true` or `false`) | `false`

### With group stage

```http
POST /tournaments HTTP/1.1
Content-Type: application/json

{
  "name": "Tournament 01",
  "description": "An interesting description",
  "public": false,
  "groupstage": true,
  "teams": [
    {
      "id": "1",
      "group": 0,
    },
    {
      "id": "2",
      "group": 0,
    },
    {
      "id": "3",
      "group": 1,
    },
    {
      "id": "4",
      "group": 1,
    },
  ]
}
```

```http
POST /tournaments HTTP/1.1
Content-Type: application/json

{
  "name": "Tournament 01",
  "description": "An interesting description",
  "public": false,
  "groupstage": true,
  "teams": [
    {
      "id": "1",
      "group": 0,
    },
    {
      "id": "2",
      "group": 0,
    },
    {
      "id": "3",
      "group": 1,
    },
    {
      "id": "4",
      "group": 1,
    },
  ]
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

TBD
```

Create a new tournament.

This will associate the given teams to a new tournament object or create them and generate a playoff stage.

You can either reuse teams from an existing tournament (first example) or create new teams (second example).

#### Parameters

Parameter   | Description | Default
---------   | ----------- | -------
name        | Name of the tournament |
teams       | Teams to create/add to the tournament (In case of groupstage add a group identifier to each team, if there is none, everyone will be in one big group) |
description | Description of the tournament | `''`
public      | Whether the tournament is public (`true` or `false`) | `true`
groupstage  | Whether the tournament will start with a group stage (`true` or `false`) | `false`


## Update a tournament

```http
PATCH /tournaments/1 HTTP/1.1
Content-Type: application/json

{
  "name": "New name",
  "description": "New description",
  "public": false
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "name": "Abby222",
  "code": "184c4a",
  "public": false,
  "description": "Lorem ipsum dolor sit222",
  "owner_username": "jonny321",
  "stages": [
    {
      "id": 1,
      "level": null
    },
    {
      "id": 2,
      "level": null
    }
  ],
  "teams": [
    {
      "id": 1,
      "name": "Gracie"
    },
    {
      "id": 2,
      "name": "Misty"
    }
  ]
}
```

Update tournament attributes.

### Parameters

All parameters are optional, therefore one request can be used to update either all, some or no parameters.

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
  "id": 1,
  "name": "Gracie"
}
```

## Update a team

```http
PATCH /teams/1 HTTP/1.1
Content-Type: application/json

{
  "name": "New name"
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "name": "New name"
}
```

### Parameters

Parameter   | Description
---------   | -----------
name        | Name of the team

# Matches

## Show a match

```http
GET /matches/1 HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "match_scores": [
    {
      "id": 1,
      "points": 0,
      "team": {
        "id": 1,
        "name": "Bubba"
      }
    },
    {
      "id": 2,
      "points": 1,
      "team": {
        "id": 2,
        "name": "Misty"
      }
    }
  ],
  "position": 0,
  "state": "not_started"
}
```

Matches belonging to a private tournament can only be viewed by the owner of that tournament.

# Match Scores

## Show a match score

```http
GET /match_scores/1 HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "id": 1,
    "points": 0,
    "team": {
        "id": 1,
        "name": "Bubba"
    }
}
```

## Update a match score

```http
PATCH /match_scores/1 HTTP/1.1
Content-Type: application/json

{
  "points": 100,
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "points": 100,
  "team": {
    "id": 1,
    "name": "Bubba"
  }
}
```

### Parameters

Parameter | Description
--------- | -----------
points    | Updated points

# Stages

<aside class="warning">
This resource is currently not implemented and the documentation below refers to an outdated version.
</aside>

## Show a playoff stage

```http
GET /stages/1 HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "data": {
    "attributes": {
      "level": null
    },
    "id": "1",
    "relationships": {
      "groups": {
        "data": []
      },
      "matches": {
        "data": [
          {
            "id": "1",
            "type": "matches"
          }
        ]
      }
    },
    "type": "stages"
  },
  "included": [
    {
      "attributes": {
        "state": "single_team"
      },
      "id": "1",
      "relationships": {
        "match-scores": {
          "data": [
            {
              "id": "1",
              "type": "match-scores"
            },
            {
              "id": "2",
              "type": "match-scores"
            }
          ]
        }
      },
      "type": "matches"
    },
    {
      "attributes": {
        "points": 0
      },
      "id": "1",
      "relationships": {
        "team": {
          "data": {
            "id": "1",
            "type": "teams"
          }
        }
      },
      "type": "match-scores"
    },
    {
      "attributes": {
        "points": 1
      },
      "id": "2",
      "relationships": {
        "team": {
          "data": {
            "id": "2",
            "type": "teams"
          }
        }
      },
      "type": "match-scores"
    },
    {
      "attributes": {
        "name": "Rosie"
      },
      "id": "1",
      "type": "teams"
    },
    {
      "attributes": {
        "name": "Ginger"
      },
      "id": "2",
      "type": "teams"
    }
  ]
}
```

The response will include all matches, match scores and teams required for displaying this stage.

## Show a group stage

```http
GET /stages/1 HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "data": {
    "attributes": {
      "level": null
    },
    "id": "1",
    "relationships": {
      "groups": {
        "data": [
          {
            "id": "1",
            "type": "groups"
          }
        ]
      },
      "matches": {
        "data": []
      }
    },
    "type": "stages"
  },
  "included": [
    {
      "attributes": {
        "number": null
      },
      "id": "1",
      "relationships": {
        "group-scores": {
          "data": [
            {
              "id": "1",
              "type": "group-scores"
            },
            {
              "id": "2",
              "type": "group-scores"
            }
          ]
        },
        "matches": {
          "data": [
            {
              "id": "1",
              "type": "matches"
            }
          ]
        }
      },
      "type": "groups"
    },
    {
      "attributes": {
        "state": "single_team"
      },
      "id": "1",
      "relationships": {
        "match-scores": {
          "data": [
            {
              "id": "1",
              "type": "match-scores"
            },
            {
              "id": "2",
              "type": "match-scores"
            }
          ]
        }
      },
      "type": "matches"
    },
    {
      "attributes": {
        "points": 0
      },
      "id": "1",
      "relationships": {
        "team": {
          "data": {
            "id": "1",
            "type": "teams"
          }
        }
      },
      "type": "match-scores"
    },
    {
      "attributes": {
        "points": 1
      },
      "id": "2",
      "relationships": {
        "team": {
          "data": {
            "id": "2",
            "type": "teams"
          }
        }
      },
      "type": "match-scores"
    },
    {
      "attributes": {
        "name": "Gracie"
      },
      "id": "1",
      "type": "teams"
    },
    {
      "attributes": {
        "name": "Misty"
      },
      "id": "2",
      "type": "teams"
    },
    {
      "attributes": {
        "group-points": 0,
        "received-points": 0,
        "scored-points": 0
      },
      "id": "1",
      "relationships": {
        "team": {
          "data": {
            "id": "1",
            "type": "teams"
          }
        }
      },
      "type": "group-scores"
    },
    {
      "attributes": {
        "group-points": 0,
        "received-points": 0,
        "scored-points": 0
      },
      "id": "2",
      "relationships": {
        "team": {
          "data": {
            "id": "2",
            "type": "teams"
          }
        }
      },
      "type": "group-scores"
    }
  ]
}
```

The response will include all groups, matches, match scores and teams required for displaying this stage.
