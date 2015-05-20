A JSON object **MUST** be at the root of every JSON API response containing
data. This object defines a document's "top level".

    {

The document **MAY** be extended to include
meta-information as `"meta"` members in several locations: at the top-level,
within resource objects, within relationship objects, and within [resource
identifier objects].

All `"meta"` members **MUST** have an object as a value, the contents of which
can be used for custom extensions.

Any members **MAY** be specified within `meta` objects.

      "meta": {
        "copyright": "Copyright 2015 Example Corp.",
        "authors": [
          "Yehuda Katz",
          "Steve Klabnik",
          "Dan Gebhardt"
        ]
      },

The document **MAY** be extended to include relevant URLs
within `"links"` members at several locations: at the top-level, within resource
objects, and within relationship objects.

      "links": {

The URL that generated the current response document.

        "self": "http://example.com/posts",

Pagination links for the primary data.

        "next": "http://example.com/posts?page[offset]=2",
        "last": "http://example.com/posts?page[offset]=10"
      },

The document's "primary data" is a representation of the resource or collection
of resources targeted by a request.

Primary data **MUST** be either:

* a single resource object, a single [resource identifier object], or `null`,
  for requests that target single resources
* an array of resource objects, an array of [resource identifier objects], or
  an empty array ([]), for requests that target resource collections

A logical collection of resources (e.g. the target of a to-many relationship)
**MUST** be represented as an array, even if it only contains one item or is
empty.

      "data": [

"Resource objects" appear in a JSON API document to represent resources.

Every resource object is uniquely identified by the combination of its `"type"`
and `"id"` members.

        {

Each resource object **MUST** contain a `"type"` member, whose value **MUST**
be a string. The `"type"` is used to describe resource objects that share
common attributes and relationships.

          "type": "posts",

Each resource object **MUST** contain an `"id"` member, whose value **MUST**
be a string.

Exception: The `id` member is not required when the resource object originates at
the client and represents a new resource to be created on the server.

          "id": "1",

The value of the `"attributes"` key is a JSON object (an "attributes object")
that represents information about the resource object it is contained within.

The top level of this object shares a namespace with the members of `relationships`
and **MUST NOT** contain `id` or `type` members. Apart from these restrictions,
this object can contain members keyed by any string valid for this specification.

All members which appear in an "attributes object" are considered attributes and
may contain any valid JSON value.

Although has-one foreign keys (e.g. `author_id`) are often stored internally
alongside other information to be represented in a resource object, these keys
**SHOULD NOT** appear as attributes. If relations are provided, they **MUST**
be represented under the "links object".

          "attributes": {
            "title": "JSON API paints my bikeshed!"
          },

The value of the `"relationships"` key is a JSON object (a "relationships object")
that represents references from the resource in whose resource object it's defined
to other resources ("relationships"). These relationships share a namespace with
[attributes]; that is, relationships of a given resource object **MUST** be named
differently than its [attributes].

Relationships may be to-one or to-many. Relationships can be specified by
including a member in a resource's relationship's object. The name of the
relationship is its key in the relationship object.

The value of a relationship **MUST** be an object.

        "relationships": {
          "author": {
            "links": {

A URL for the relationship itself (a
"relationship URL"). This URL allows the client to directly manipulate the
relationship. For example, it would allow a client to remove an `author`
from an `article` without deleting the `people` resource itself.

              "self": "http://example.com/posts/1/relationships/author",

A related resource URL, as defined above.

              "related": "http://example.com/posts/1/author"
            },

A `"data"` member represents "resource linkage".

Resource linkage **MUST** be represented as one of the following:

* `null` for empty to-one relationships.
* a [resource identifier object] for non-empty to-one relationships.
* an empty array (`[]`) for empty to-many relationships.
* an array of [resource identifier objects] for non-empty to-many relationships.

If present, a *related resource URL* **MUST** be a valid URL, even if the
relationship isn't currently associated with any target resources.

A "resource identifier object" is an object that identifies an individual
resource.

It **MUST** contain `"type"` and `"id"` members.

            "data": { "type": "people", "id": "9" }
          },
          "comments": {
            "links": {
              "self": "http://example.com/posts/1/relationships/comments",
              "related": "http://example.com/posts/1/comments"
            },
            "data": [
              { "type": "comments", "id": "5" },
              { "type": "comments", "id": "12" }
            ]
          }
        },

Analogous to the `"links"` member at the document's top level, the optional
`"links"` member within each resource object contains URLs related to the
resource.

If present, this object **MAY** contain a URL keyed by `"self"`, that identifies
the resource represented by the resource object.

A server **MUST** respond to a `GET` request to the specified URL with a
response that includes the resource as the primary data.

        "links": {
          "self": "http://example.com/posts/1"
        }
      }],

To reduce the number of HTTP requests, servers **MAY** allow responses that
include related resources along with the requested primary resources. Such
responses are called "compound documents".

In a compound document, all included resources **MUST** be represented as an
array of resource objects in a top-level `"included"` member.

Compound documents require "full linkage", meaning that every included
resource **MUST** be identified by at least one [resource identifier object]
in the same document. These resource identifier objects could either be
primary data or represent resource linkage contained within primary or
included resources.

      "included": [{
        "type": "people",
        "id": "9",
        "attributes": {
          "first-name": "Dan",
          "last-name": "Gebhardt",
          "twitter": "dgeb"
        },
        "links": {
          "self": "http://example.com/people/9"
        }
      }, {
        "type": "comments",
        "id": "5",
        "attributes": {
          "body": "First!"
        },
        "links": {
          "self": "http://example.com/comments/5"
        }
      }, {
        "type": "comments",
        "id": "12",
        "attributes": {
          "body": "I like XML better"
        },
        "links": {
          "self": "http://example.com/comments/12"
        }
      }]
    }