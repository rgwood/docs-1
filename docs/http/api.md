---
title: 'API Reference'
sidebar_position: 2
---

## OpenAPI 

All RPC requests are POST requests to `https://{app_id}.cloud.ditto.live/api/v2/store`.  

<details>
<summary>OpenAPI Spec (swagger.json)</summary>

  ```json
 {
    "openapi": "3.0.3",
    "info": {
      "title": "Ditto HTTP RPC API",
      "version": "2.0.0"
    },
    "servers": [
      {
        "url": "https://{app_id}.cloud.ditto.live/api/v2/store",
        "description": "The Ditto Big Peer that synchronizes and makes data availabile cloud compute & storage.",
        "variables": {
          "app_id": {
            "default": "REPLACE_WITH_MY_APP_ID",
            "description": "This is your App ID from the Ditto Portal"
          }
        }
      }
    ],
    "paths": {
      "/find": {
        "post": {
          "description": "",
          "operationId": "find_endpoint",
          "requestBody": {
            "description": "Find up to $limit number of records from an $offset in the $collection.  The query is formed with the parameter $query and the parameters $args.  Optionally query details can be turned on with $describe and type annotation can be enabled with $serializeAs",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/FindRequest"
                }
              }
            },
            "required": true
          },
          "responses": {
            "200": {
              "description": "The call was completed successfully, even if not records were found.  Anydocuments found will be returned as an array in the documents parameter.  If no records are found it will be an empty array ",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/FindResponse"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/FindResponse"
                  }
                }
              }
            },
            "400": {
              "description": "Bad Request",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                }
              }
            },
            "401": {
              "description": "Forbidden",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                }
              }
            },
            "500": {
              "description": "Internal Server Error",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                }
              }
            }
          },
          "deprecated": false
        }
      },
      "/findbyid": {
        "post": {
          "description": "",
          "operationId": "find_by_id_endpoint",
          "requestBody": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/FindByIdRequest"
                }
              }
            },
            "required": true
          },
          "responses": {
            "200": {
              "description": "RPC call was successful",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/FindByIdResponse"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/FindByIdResponse"
                  }
                }
              }
            },
            "400": {
              "description": "Bad Request",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                }
              }
            },
            "401": {
              "description": "Forbidden",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                }
              }
            },
            "500": {
              "description": "Internal Server Error",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                }
              }
            }
          },
          "deprecated": false
        }
      },
      "/write": {
        "post": {
          "description": "",
          "operationId": "write_endpoint",
          "parameters": [
            {
              "name": "X-DITTO-CLIENT-ID",
              "in": "header",
              "description": "The client ID to be used for this request.",
              "required": true,
              "deprecated": false,
              "schema": {
                "type": "string"
              },
              "example": "AAAAAAAAAAAAAAAAAAAAew=="
            },
            {
              "name": "X-DITTO-TXN-ID",
              "in": "header",
              "description": "This optional header is the minimum transaction ID to act on.  If big peer isn't on the transaction ID or newer then a 404 will be returned with no changes made.",
              "required": false,
              "deprecated": false,
              "schema": {
                "type": "string"
              }
            }
          ],
          "requestBody": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/WriteRequest"
                }
              }
            },
            "required": true
          },
          "responses": {
            "200": {
              "description": "RPC call was successful",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/WriteResponse"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/WriteResponse"
                  }
                }
              }
            },
            "400": {
              "description": "Bad Request",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                }
              }
            },
            "401": {
              "description": "Forbidden",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                }
              }
            },
            "500": {
              "description": "Internal Server Error",
              "content": {
                "application/json": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                },
                "application/cbor": {
                  "schema": {
                    "$ref": "#/components/schemas/RpcError"
                  }
                }
              }
            }
          },
          "deprecated": false
        }
      }
    },
    "components": {
      "schemas": {
        "FindResponse": {
          "type": "object",
          "required": [
            "documents"
          ],
          "properties": {
            "txnId": {
              "type": "integer",
              "format": "int64"
            },
            "documents": {
              "type": "array",
              "items": {
                "$ref": "#/components/schemas/Document"
              }
            }
          },
          "example": {
            "documents": {
              "id": 1,
              "contents": {
                "name": "Francis",
                "favoriteBook": {
                  "title": "The Great Gatsby",
                  "published": 1925
                }
              }
            },
            "txnId": 9000
          }
        },
        "SerializedAs": {
          "type": "string",
          "default": "latestValues",
          "enum": [
            "latestValues",
            "latestValuesAndTypes"
          ]
        },
        "FindByIdResponse": {
          "type": "object",
          "properties": {
            "txnId": {
              "type": "integer",
              "format": "int64",
              "description": "Transaction ID"
            },
            "document": {
              "$ref": "#/components/schemas/Document"
            }
          }
        },
        "RpcError": {
          "type": "object",
          "required": [
            "message"
          ],
          "properties": {
            "message": {
              "type": "string",
              "description": "A human readable description"
            }
          },
          "description": "Error during request",
          "example": {
            "message": "Some kind of human readable description of the error"
          }
        },
        "Update": {
          "type": "object",
          "required": [
            "collection",
            "query",
            "commands"
          ],
          "properties": {
            "args": {
              "$ref": "#/components/schemas/AnyValue"
            },
            "collection": {
              "type": "string",
              "description": "The Collection type"
            },
            "commands": {
              "type": "array",
              "items": {
                "$ref": "#/components/schemas/UpdateCommand"
              }
            },
            "query": {
              "type": "string",
              "description": "The query"
            }
          },
          "description": "Update an existing value"
        },
        "Remove": {
          "type": "object",
          "required": [
            "collection",
            "query"
          ],
          "properties": {
            "query": {
              "type": "string"
            },
            "collection": {
              "type": "string"
            },
            "args": {
              "$ref": "#/components/schemas/AnyValue"
            }
          },
          "description": "Removing a field"
        },
        "WriteCommand": {
          "oneOf": [
            {
              "type": "object",
              "properties": {
                "update": {
                  "$ref": "#/components/schemas/Update"
                }
              }
            },
            {
              "type": "object",
              "properties": {
                "upsert": {
                  "$ref": "#/components/schemas/Upsert"
                }
              }
            },
            {
              "type": "object",
              "properties": {
                "remove": {
                  "$ref": "#/components/schemas/Remove"
                }
              }
            }
          ],
          "description": "The corresponding database transaction to perform"
        },
        "WriteCommandResult": {
          "oneOf": [
            {
              "type": "object",
              "properties": {
                "update": {
                  "$ref": "#/components/schemas/UpdateResult"
                }
              }
            },
            {
              "type": "object",
              "properties": {
                "upsert": {
                  "$ref": "#/components/schemas/UpsertResult"
                }
              }
            },
            {
              "type": "object",
              "properties": {
                "remove": {
                  "$ref": "#/components/schemas/RemoveResult"
                }
              }
            }
          ]
        },
        "TypeOverride": {
          "type": "string",
          "description": "This is a list of possible value type overrides for an upsert.  We will accept counter or",
          "enum": [
            "counter",
            "register"
          ]
        },
        "UpsertResult": {
          "type": "object",
          "required": [
            "transactionId"
          ],
          "properties": {
            "transactionId": {
              "type": "integer",
              "format": "int64"
            }
          }
        },
        "FindRequest": {
          "type": "object",
          "required": [
            "collection",
            "query",
            "limit",
            "describe",
            "serializedAs"
          ],
          "properties": {
            "limit": {
              "type": "integer",
              "format": "int32",
              "description": "The limit",
              "default": 1000
            },
            "describe": {
              "type": "boolean",
              "default": false
            },
            "query": {
              "type": "string",
              "description": "The parameterized query to run."
            },
            "serializedAs": {
              "$ref": "#/components/schemas/SerializedAs"
            },
            "args": {
              "$ref": "#/components/schemas/AnyValue"
            },
            "collection": {
              "type": "string",
              "description": "The collection to query"
            },
            "offset": {
              "type": "integer",
              "format": "int32",
              "description": "The offset"
            }
          },
          "description": "A data transfer object for a request to the \"Find\" RPC method.  This can either come in directly or nested inside of a [RpcRequest]",
          "example": {
            "collection": "people",
            "query": "favoriteBook.title == 'The Great Gatsby'"
          }
        },
        "UpdateResult": {
          "type": "object",
          "required": [
            "transactionId",
            "updated",
            "error",
            "internalError",
            "permissionDenied"
          ],
          "properties": {
            "transactionId": {
              "type": "integer",
              "format": "int64"
            },
            "error": {
              "type": "integer",
              "format": "int64"
            },
            "internalError": {
              "type": "integer",
              "format": "int64"
            },
            "permissionDenied": {
              "type": "integer",
              "format": "int64"
            },
            "updated": {
              "type": "integer",
              "format": "int64"
            }
          }
        },
        "RemoveResult": {
          "type": "object",
          "required": [
            "transactionId",
            "deleted",
            "permissionDenied",
            "internalError"
          ],
          "properties": {
            "permissionDenied": {
              "type": "integer",
              "format": "int64"
            },
            "deleted": {
              "type": "integer",
              "format": "int64"
            },
            "internalError": {
              "type": "integer",
              "format": "int64"
            },
            "transactionId": {
              "type": "integer",
              "format": "int64"
            }
          }
        },
        "WriteResponse": {
          "type": "object",
          "required": [
            "results"
          ],
          "properties": {
            "results": {
              "type": "array",
              "items": {
                "$ref": "#/components/schemas/WriteCommandResult"
              }
            }
          },
          "description": "Response from a write"
        },
        "FindByIdRequest": {
          "type": "object",
          "required": [
            "collection",
            "id",
            "serializedAs"
          ],
          "properties": {
            "id": {
              "$ref": "#/components/schemas/AnyValue"
            },
            "serializedAs": {
              "$ref": "#/components/schemas/SerializedAs"
            },
            "collection": {
              "type": "string",
              "description": "The name of the ditto collection"
            }
          },
          "description": "Find a document by it's id",
          "example": {
            "collection": "people",
            "id": "abc123"
          }
        },
        "UpdateCommandMethod": {
          "type": "string",
          "description": "Update commands for mutatable fields",
          "enum": [
            "set",
            "increment",
            "replaceWithCounter"
          ]
        },
        "UpdateCommand": {
          "type": "object",
          "required": [
            "method",
            "path",
            "value"
          ],
          "properties": {
            "path": {
              "type": "string"
            },
            "value": {
              "$ref": "#/components/schemas/AnyValue"
            },
            "method": {
              "$ref": "#/components/schemas/UpdateCommandMethod"
            }
          },
          "description": "Update an existing value"
        },
        "Upsert": {
          "type": "object",
          "required": [
            "collection",
            "id",
            "value"
          ],
          "properties": {
            "valueTypeOverrides": {
              "type": "object"
            },
            "value": {
              "$ref": "#/components/schemas/AnyValue"
            },
            "id": {
              "$ref": "#/components/schemas/AnyValue"
            },
            "collection": {
              "type": "string"
            }
          },
          "description": "Upserting a value"
        },
        "Document": {
          "type": "object",
          "required": [
            "id",
            "fields"
          ],
          "properties": {
            "id": {
              "$ref": "#/components/schemas/AnyValue"
            },
            "fields": {
              "$ref": "#/components/schemas/AnyValue"
            }
          },
          "description": "A Document is a Map that can contain other CRDT types"
        },
        "WriteRequest": {
          "type": "object",
          "required": [
            "commands"
          ],
          "properties": {
            "commands": {
              "type": "array",
              "items": {
                "$ref": "#/components/schemas/WriteCommand"
              }
            }
          },
          "description": "Open a write transaction",
          "example": {
            "collection": "people",
            "commands": [
              {
                "method": "upsert",
                "collection": "people",
                "id": 1,
                "value": {
                  "name": "Susan",
                  "age": 31,
                  "favoriteBook": {
                    "published": 1925,
                    "title": "The Great Gatsby"
                  }
                }
              }
            ]
          }
        },
        "AnyValue": {}
      },
      "securitySchemes": {
        "api_jwt_token": {
          "type": "http",
          "scheme": "bearer"
        }
      }
    },
    "externalDocs": {
      "url": "https://docs.ditto.live/http/installation/",
      "description": "For more detailed instructions on how to use this API and the Ditto SDK, please see the documentation."
    }
}
  ```
</details>


## Usage


The HTTP API is accessible through the `https://{app_id}.cloud.ditto.live/api/v2/store/[method]` endpoint.

### Endpoints

* `/api/v2/store/write`
* `/api/v2/store/findbyid`
* `/api/v2/store/find`


### Write

A full example with `curl` that shows how to use the HTTP API to create explicit
types. In this example, we create a `friends` key with a Register that is an
array, and `orderCount` which is a counter.


```
curl -X POST 'https://{app_id}.cloud.ditto.live/api/v2/store/write' \
  --header 'X-DITTO-CLIENT-ID: AAAAAAAAAAAAAAAAAAAABQ==' \
  --header 'Content-Type: application/json' \
  --data-raw '{
      "commands": [{
        "method": "upsert",
        "collection": "people",
        "id": "123abc",
        "value": {
          "name": "John",
          "friends": ["Susan"],
          "orderCount": 5
        },
        "valueTypeOverrides": {
          "orderCount": "counter"
        }
      }]
  }'
```

To create and modify a mutable type such as Register Map or Counter in the HTTP API
for v2, you can annotate the value with the type you intend to use. In v2, Arrays are registers by default. That means you do not need to add
`register` to the `valueTypeOverrides` payload, but you can if you want to be
explicit.

In this example, We use the `counter` override by adding the following key to the payload:

```
"valueTypeOverrides": {
  "orderCount": "counter",
  "friends": "register"
}
```

:::info
**Can I issue a find query inside of a write command?**

No. First, send a `find`, and then after a response, send an `write` request.
::::

### FindByID


To find this document you can use `/api/v2/store/findbyid`:
```
curl --location --request POST 'https://{app_id}.cloud.ditto.live/api/v2/store/findbyid' \
--header 'X-DITTO-CLIENT-ID: AAAAAAAAAAAAAAAAAAAABQ==' \
--header 'Content-Type: application/json' \
--data-raw '{
  "collection": "people",
  "id": "123abc"
}'
```

```
{
  "collection": "people",
  "id": "123abc"
}
```

### Find

When you query for this data using `/api/v2/store/find`, you can use the key `serializedAs: latestValuesAndTypes`  to receive a response with each
type specified:

Request:
```
curl -X POST 'https://{app_id}.cloud.ditto.live/api/v2/store/find' \
  --header 'X-DITTO-CLIENT-ID: AAAAAAAAAAAAAAAAAAAABQ==' \
  --header 'Content-Type: application/json' \
  --data-raw '{
    "collection": "people",
    "query": "name=='John'",
    "limit": 2,
    "serializedAs": "latestValuesAndTypes"
}'
```

Response: 

```json
{
  "value": {
    "_id": "123abc",
    "fields": {
      "name": {
          "register": { "value": "John" },
      },
      "friends": {
          "register": { "value": ["Susan"] },
      },
      "orderCount": {
          "counter": { "value": 5 }
      },
    }
  }
}
```

For more examples, see the corresponding sections in the Concepts section for [querying](./common/concepts/querying), [update](./common/concepts/update), and [remove](./common/concepts/remove).

## Distinct Values

`POST https://{app_id}.cloud.ditto.live/api/v1/collections/<collection_name>/distinct_values`

Query for the distinct values in a collection. Returns a single document containing the paths and their distinct values.  Paths are specified as json arrays of strings. This query expects a body of json in the following format:

  ```
  {
    "filter": "true"
    "paths": [
      "widgets",
      "nested.device_id",
      "nested.tags[*]",
      "nested",
      "nested.tags"
    ]
  }
  ```
  
  
Parameters

  - filter - The query used to select documents.
  - paths (list) - A list of DittoQl paths to get distinct values for. See Supported Paths section below.
  - timeout_millis (number, optional) - the timeout, in milliseconds

Supported paths

  - Field access. Eg. `fieldA`, `fieldA.fieldB`.
  - Array projection. Eg. `fieldA.arrayB[*]`, `fieldA.arrayB[*].fieldC`.
  - No other access methods are supported.

Response

  The response will contain an object where the keys are the requested paths (same format as the request) and their values are the unique values at the respective paths.
  Note that only primitive values are returned distinctly. Any arrays or objects present at the specified path will appear in the result as an empty array `{}` or object `{}` respectively.

  ```
  HTTP/1.1 200 OK
  Content-Type: application/json
  X-DITTO-TXN-ID: 7
  {
    "item": {
      "widgets": [1, 2, 3],
      "nested.device_id": ["device1", "device2"],
      "nested.tags[*]": ["tag1", "tag2"],
      "nested": [{}],
      "nested.tags": [[]],
    }
  }

  ```
