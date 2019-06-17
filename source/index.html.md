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

## Update

```http
PUT /users HTTP/1.1
Content-Type: application/json

{
  "email": "new@email.com",
  "username": "newusername"
}

```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "email": "new@email.com",
  "provider": "email",
  "username": "newusername",
  "uid": "new@email.com",
  "allow_password_change": false,
  "created_at": "2019-05-13T10:07:21.228Z",
  "updated_at": "2019-05-13T10:11:38.087Z"
}

```

Update user account.

Parameters can be omitted as needed.

### Parameters

Parameter | Description
----------|------------
email     | Updated mail address
username  | Updated username

## Change Password

```http
PUT /users/password HTTP/1.1
Content-Type: application/json

{
  "current_password": "password"
  "password": "P@ssw0rd"
  "password_confirmation": "P@ssw0rd"
}

```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 23,
  "email": "john@gmail.com",
  "provider": "email",
  "username": "JohnTheBoy",
  "uid": "john@gmail.com",
  "allow_password_change": true,
  "created_at": "2019-06-04T10:08:28.838Z",
  "updated_at": "2019-06-04T10:10:39.027Z"
}

```

### Parameters

Parameter             | Description
----------------------|------------
current_password      | current/old password
password              | The new password
password_confirmation | The new password again to prevent typos

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
  "id": 3,
  "name": "Erics Wonderland",
  "code": "e177f3",
  "public": true,
  "description": "Here we go: Erics third wonderland tournament.",
  "owner_username": "eric_kunze",
  "stages": [
    {
      "id": 7,
      "level": 1,
      "state": "playoff_stage",
      "matches": [
        {
          "id": 15,
          "state": "not_started",
          "position": 0,
          "winner": null,
          "match_scores": [
            {
              "id": 23,
              "points": 0,
              "team": {
                "id": 17,
                "name": "Barambo"
              }
            },
            {
              "id": 24,
              "points": 0,
              "team": {
                "id": 18,
                "name": "Kurumba"
              }
            }
          ]
        },
        {
          "id": 16,
          "state": "not_started",
          "position": 1,
          "winner": null,
          "match_scores": [
            {
              "id": 25,
              "points": 0,
              "team": {
                "id": 19,
                "name": "Gatego"
              }
            },
            {
              "id": 26,
              "points": 0,
              "team": {
                "id": 20,
                "name": "Tentiku"
              }
            }
          ]
        }
      ],
      "groups": []
    },
    {
      "id": 8,
      "level": 0,
      "state": "playoff_stage",
      "matches": [
        {
          "id": 17,
          "state": "not_ready",
          "position": 0,
          "winner": null,
          "match_scores": []
        }
      ],
      "groups": []
    }
  ],
  "teams": [
    {
      "id": 17,
      "name": "Barambo"
    },
    {
      "id": 18,
      "name": "Kurumba"
    },
    {
      "id": 19,
      "name": "Gatego"
    },
    {
      "id": 20,
      "name": "Tentiku"
    }
  ]
}

```

Request a detailed Tournament object.
This will include all available attributes and relations.
Public tournaments can be requested by anyone, private tournaments only by their owner.

## Show tournament statistics

```http
GET /tournaments/1/statistics HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "most_dominant_score": {
    "id": 1,
    "group_points": 1,
    "received_points": 0,
    "scored_points": 100,
    "team": {
      "id": 69,
      "name": "Zoe"
    }
  },
  "least_dominant_score": {
    "id": 2,
    "group_points": 0,
    "received_points": 100,
    "scored_points": 0,
    "team": {
      "id": 76,
      "name": "Jasper"
    }
  },
  "group_scores": [
    {
      "id": 1,
      "group_points": 1,
      "received_points": 0,
      "scored_points": 100,
      "team": {
        "id": 69,
        "name": "Zoe"
      }
    },
    {
      "id": 2,
      "group_points": 0,
      "received_points": 100,
      "scored_points": 0,
      "team": {
        "id": 76,
        "name": "Jasper"
      }
    }
  ]
}
```

Retrieve a list of group scores for tournaments with a group stage.  
Additionally calculate a most (most `scored_points`) and least (most `received_points`) dominant team.

## Create a tournament

```http
POST /tournaments HTTP/1.1
Content-Type: application/json

{
  "name": "Tournament 01",
  "description": "An interesting description",
  "public": false,
  "group_stage": false,
  "playoff_teams_amount": 4,
  "teams": [
    {
      "id": 1
    },
    /* or */
    {
      "name": "Name 01"
    },

    /* for "group_stage": true */
    {
      "id": 1,
      "group": 1
    },   
    /* or */
    {
      "name": "Name 01",
      "group": 1
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

For tournaments with a group stage (`"group_stage": true`) an additional `"group"` parameter, specifying the group as an integer, is required for each team.

**Parameters without default value are required!**

#### Parameters

Parameter   | Description | Default
---------   | ----------- | -------
name        | Name of the tournament |
teams       | Teams to create/add to the tournament |
description | Description of the tournament | `''`
public      | Whether the tournament is public (`true` or `false`) | `true`
group_stage  | Whether the tournament will start with a group stage (`true` or `false`) | `false`
playoff_teams_amount | _(only required for tournaments with group stage)_ <br> The amount of teams that play in the first playoff stage after group stage <br> This needs to be a positive power of two (1,2,4,8,16,..) and <= the amount of teams |

## Update a tournament

```http
PATCH /tournaments/3 HTTP/1.1
Content-Type: application/json

{
  "name": "Erics VERY NICE Wonderland",
  "description": "Here we go: Erics third wonderland tournament. Forgot this sentence..",
  "public": false
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 3,
  "name": "Erics VERY NICE Wonderland",
  "code": "e177f3",
  "public": false,
  "description": "Here we go: Erics third wonderland tournament. Forgot this sentence..",
  "owner_username": "eric_kunze",
  "stages": [
    {
      "id": 7,
      "level": 1,
      "state": "playoff_stage"
    },
    {
      "id": 8,
      "level": 0,
      "state": "playoff_stage"
    }
  ],
  "teams": [
    {
      "id": 17,
      "name": "Barambo"
    },
    {
      "id": 18,
      "name": "Kurumba"
    },
    {
      "id": 19,
      "name": "Gatego"
    },
    {
      "id": 20,
      "name": "Tentiku"
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
playoff_teams_amount | _(only relevant for tournaments with group stage)_ <br> The amount of teams that play in the first playoff stage after group stage <br> This needs to be a positive power of two (1,2,4,8,16,..) and <= the amount of teams <br> Updating this will overwrite instant_finalists_amount and intermediate_round_participants_amount
instant_finalists_amount | _(only relevant for tournaments with group stage)_ <br> The amount of teams that advance into playoffs instantly after group stage
intermediate_round_participants_amount | _(only relevant for tournaments with group stage)_ <br> The amount of teams that need to play an intermediate round before advancing to playoffs

The last three need to fit together such that 
playoff_teams_amount = instant_finalists_amount + (intermediate_round_participants_amount / 2)

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

## Start a Match

Starting a Match is only available when the matches current state is 'not_started'.

```http
PATCH /matches/1 HTTP/1.1
Content-Type: application/json

{
  "state": "in_progress",
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "state": "in_progress",
  "position": 2,
  "match_scores": [
    {
      "id": 17,
      "points": 0
    },
    {
      "id": 18,
      "points": 0
    }
  ]
}
```

### Parameters

Parameter   | Value
---------   | -----------
state       | 'in_progress'

## End a Match

Stopping a match is only available if the matches current state is 'in_progress'.
Stopping a match will result in the match below being filled with the winning team / the group points being recalculated.

```http
PATCH /matches/1 HTTP/1.1
Content-Type: application/json

{
  "state": "finished",
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "state": "finished",
  "position": 2,
  "match_scores": [
    {
      "id": 17,
      "points": 0
    },
    {
      "id": 18,
      "points": 0
    }
  ]
}
```

### Parameters

Parameter   | Value
---------   | -----------
state       | 'finished'

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

# Groups

## Show a group

Groups belonging to a private tournament can only be viewed by the owner of that tournament.

```http
GET /groups/7 HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 7,
  "number": 4,
  "matches": [
    {
      "id": 47,
      "state": "not_started",
      "position": 0,
      "winner": null,
      "match_scores": [
        {
          "id": 82,
          "points": 0,
          "team": {
            "id": 37,
            "name": "John"
          }
        },
        {
          "id": 83,
          "points": 0,
          "team": {
            "id": 38,
            "name": "Tom"
          }
        }
      ]
    },
    {
      "id": 48,
      "state": "not_started",
      "position": 1,
      "winner": null,
      "match_scores": [
        {
          "id": 84,
          "points": 0,
          "team": {
            "id": 37,
            "name": "John"
          }
        },
        {
          "id": 85,
          "points": 0,
          "team": {
            "id": 39,
            "name": "Simon"
          }
        }
      ]
    },
    {
      "id": 49,
      "state": "not_started",
      "position": 2,
      "winner": null,
      "match_scores": [
        {
          "id": 86,
          "points": 0,
          "team": {
            "id": 38,
            "name": "Tom"
          }
        },
        {
          "id": 87,
          "points": 0,
          "team": {
            "id": 39,
            "name": "Simon"
          }
        }
      ]
    }
  ],
  "group_scores": [
    {
      "id": 23,
      "group_points": 0,
      "received_points": 0,
      "scored_points": 0,
      "team": {
        "id": 37,
        "name": "John"
      }
    },
    {
      "id": 24,
      "group_points": 0,
      "received_points": 0,
      "scored_points": 0,
      "team": {
        "id": 38,
        "name": "Tom"
      }
    },
    {
      "id": 25,
      "group_points": 0,
      "received_points": 0,
      "scored_points": 0,
      "team": {
        "id": 39,
        "name": "Simon"
      }
    }
  ]
}
```

# Stages

## Show a stage

```http
GET /stages/1 HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "level": -1,
  "state": "in_progress",
  "matches": [],
  "groups": [
    {
      "id": 1,
      "number": null,
      "matches": [
        {
          "id": 1,
          "state": "not_started",
          "position": 0,
          "winner": null,
          "match_scores": [
            {
              "id": 1,
              "points": 0,
              "team": {
                "id": 1,
                "name": "bobo"
              }
            },
            {
              "id": 2,
              "points": 0,
              "team": {
                "id": 2,
                "name": "baba"
              }
            }
          ]
        },
        {
          "id": 2,
          "state": "not_started",
          "position": 1,
          "winner": null,
          "match_scores": [
            {
              "id": 3,
              "points": 0,
              "team": {
                "id": 1,
                "name": "bobo"
              }
            },
            {
              "id": 4,
              "points": 0,
              "team": {
                "id": 3,
                "name": "bubu"
              }
            }
          ]
        },
        {
          "id": 3,
          "state": "not_started",
          "position": 2,
          "winner": null,
          "match_scores": [
            {
              "id": 5,
              "points": 0,
              "team": {
                "id": 1,
                "name": "bobo"
              }
            },
            {
              "id": 6,
              "points": 0,
              "team": {
                "id": 4,
                "name": "bebe"
              }
            }
          ]
        },
        {
          "id": 4,
          "state": "not_started",
          "position": 3,
          "winner": null,
          "match_scores": [
            {
              "id": 7,
              "points": 0,
              "team": {
                "id": 2,
                "name": "baba"
              }
            },
            {
              "id": 8,
              "points": 0,
              "team": {
                "id": 3,
                "name": "bubu"
              }
            }
          ]
        },
        {
          "id": 5,
          "state": "not_started",
          "position": 4,
          "winner": null,
          "match_scores": [
            {
              "id": 9,
              "points": 0,
              "team": {
                "id": 2,
                "name": "baba"
              }
            },
            {
              "id": 10,
              "points": 0,
              "team": {
                "id": 4,
                "name": "bebe"
              }
            }
          ]
        },
        {
          "id": 6,
          "state": "not_started",
          "position": 5,
          "winner": null,
          "match_scores": [
            {
              "id": 11,
              "points": 0,
              "team": {
                "id": 3,
                "name": "bubu"
              }
            },
            {
              "id": 12,
              "points": 0,
              "team": {
                "id": 4,
                "name": "bebe"
              }
            }
          ]
        }
      ],
      "group_scores": [
        {
          "id": 1,
          "group_points": 0,
          "received_points": 0,
          "scored_points": 0,
          "team": {
            "id": 1,
            "name": "bobo"
          }
        },
        {
          "id": 2,
          "group_points": 0,
          "received_points": 0,
          "scored_points": 0,
          "team": {
            "id": 2,
            "name": "baba"
          }
        },
        {
          "id": 3,
          "group_points": 0,
          "received_points": 0,
          "scored_points": 0,
          "team": {
            "id": 3,
            "name": "bubu"
          }
        },
        {
          "id": 4,
          "group_points": 0,
          "received_points": 0,
          "scored_points": 0,
          "team": {
            "id": 4,
            "name": "bebe"
          }
        }
      ]
    },
    {
      "id": 2,
      "number": null,
      "matches": [
        {
          "id": 7,
          "state": "not_started",
          "position": 0,
          "winner": null,
          "match_scores": [
            {
              "id": 13,
              "points": 0,
              "team": {
                "id": 5,
                "name": "lolo"
              }
            },
            {
              "id": 14,
              "points": 0,
              "team": {
                "id": 6,
                "name": "lulu"
              }
            }
          ]
        },
        {
          "id": 8,
          "state": "not_started",
          "position": 1,
          "winner": null,
          "match_scores": [
            {
              "id": 15,
              "points": 0,
              "team": {
                "id": 5,
                "name": "lolo"
              }
            },
            {
              "id": 16,
              "points": 0,
              "team": {
                "id": 7,
                "name": "lala"
              }
            }
          ]
        },
        {
          "id": 9,
          "state": "not_started",
          "position": 2,
          "winner": null,
          "match_scores": [
            {
              "id": 17,
              "points": 0,
              "team": {
                "id": 5,
                "name": "lolo"
              }
            },
            {
              "id": 18,
              "points": 0,
              "team": {
                "id": 8,
                "name": "lele"
              }
            }
          ]
        },
        {
          "id": 10,
          "state": "not_started",
          "position": 3,
          "winner": null,
          "match_scores": [
            {
              "id": 19,
              "points": 0,
              "team": {
                "id": 6,
                "name": "lulu"
              }
            },
            {
              "id": 20,
              "points": 0,
              "team": {
                "id": 7,
                "name": "lala"
              }
            }
          ]
        },
        {
          "id": 11,
          "state": "not_started",
          "position": 4,
          "winner": null,
          "match_scores": [
            {
              "id": 21,
              "points": 0,
              "team": {
                "id": 6,
                "name": "lulu"
              }
            },
            {
              "id": 22,
              "points": 0,
              "team": {
                "id": 8,
                "name": "lele"
              }
            }
          ]
        },
        {
          "id": 12,
          "state": "not_started",
          "position": 5,
          "winner": null,
          "match_scores": [
            {
              "id": 23,
              "points": 0,
              "team": {
                "id": 7,
                "name": "lala"
              }
            },
            {
              "id": 24,
              "points": 0,
              "team": {
                "id": 8,
                "name": "lele"
              }
            }
          ]
        }
      ],
      "group_scores": [
        {
          "id": 5,
          "group_points": 0,
          "received_points": 0,
          "scored_points": 0,
          "team": {
            "id": 5,
            "name": "lolo"
          }
        },
        {
          "id": 6,
          "group_points": 0,
          "received_points": 0,
          "scored_points": 0,
          "team": {
            "id": 6,
            "name": "lulu"
          }
        },
        {
          "id": 7,
          "group_points": 0,
          "received_points": 0,
          "scored_points": 0,
          "team": {
            "id": 7,
            "name": "lala"
          }
        },
        {
          "id": 8,
          "group_points": 0,
          "received_points": 0,
          "scored_points": 0,
          "team": {
            "id": 8,
            "name": "lele"
          }
        }
      ]
    }
  ]
}
```

## Update a stage

```http
PATCH /stages/1 HTTP/1.1
Content-Type: application/json

{
  "state": 'finished'
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "level": -1,
  "state": "in_progress",
  "matches": [],
  "groups": [
    {
      "id": 1,
      "number": null
    },
    {
      "id": 2,
      "number": null
    }
  ]
}
```

### Parameters

Parameter | Description
--------- | -----------
state    | Desired State (only used to stop group stages and trigger playoff generation by doing so)

# Bets

## Create a bet

```http
POST /matches/1/bets HTTP/1.1
Content-Type: application/json

{
  "team": 1
}
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "team": { 
    "id": 1,
    "name": "Bubba"
  }
}
```

### Parameters

Parameter | Description
--------- | -----------
team      | ID of the target team **or `null` to bet on undecided**

## List bets for a match

```http
GET /matches/1/bets HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "team": {
      "id": 1,
      "name": "Bubba"
    },
    "bets": 42
  },
  {
    "team": {
      "id": 2,
      "name": "Rocky"
    },
    "bets": 84
  },
  {
    "team": null, /* undecided */
    "bets": 168
  }
]
```

## List bets for a tournament

```http
GET /tournaments/1/statistics HTTP/1.1
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  ...
  "bets": {
    "current_user": { /* assuming user01 is the current user */
      "correct": 10,
      "incorrect": 0
    },
    "all": [
      {
        "username": "user01",
        "correct": 10,
        "incorrect": 0
      },
      {
        "username": "user02",
        "correct": 0,
        "incorrect": 10
      }
    ] 
  }
}
```

As an addition to statistics specified [here](#show-tournament-statistics) the endpoint also returns information about bets.
