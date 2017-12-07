# Example Hyper-Schema Guide

This includes two Hyper-Schema files, one of which is done with purely standard keywords, and the other of which adds a keyword specifically to capture state transtions involving data updates of a single issue.

In both cases, assume that we somehow accessed the schema and know that `"#/definitions/issue"` and `"#/definitions/issueCollection"` are the hyper-schemas of interest.  We do NOT need to parse those names to determine which is the item and which is the collection.

Per [section 6.2.3](https://tools.ietf.org/html/draft-handrews-json-schema-hyperschema-00#section-6.2.3) of the spec, the fact that `"#/definitions/issue` has a link with

```JSON
{
  "rel": "collection",
  "taregetSchema": {"$ref": "#/definitions/issueCollection"}
}
```

at its top level, with while `"#definitions/issueCollection"` has a link with

```JSON
{
  "rel": "item",
  "anchorPointer": "",
  "targetSchema": "#/definitions/issue"
}
```

attached to its array elements but with the whole collection as context (that's what `"anchorPointer": ""` does), tells you that these schemas are a collection-and-item pair.

Since there aren't any URI Template variables that are required to be resolved from the instance in the collection link (which has `"href": "issue{?searchtext}"` and an `hrefSchema` for `searchtext`.

## Standard Draft-07 Hyper-Schema

The first file, [issue.json](issue.json), uses only standard draft-07 hyper-schema keywords.

I think that the basics of the diagram are covered, but there are some parts that don't hold up as well for some of the operations.

### Collection

"Issues" is the collection, and it contains many single item "Issue" entities.

It has a `"submissionSchema"` which, per [§8.3 HTTP POST and the "submission*" Keywords](https://tools.ietf.org/html/draft-handrews-json-schema-hyperschema-00#section-8.3) maps to HTTP POST, which per [§8.6 Creating Resources Through Collections](https://tools.ietf.org/html/draft-handrews-json-schema-hyperschema-00#section-8.6) is recognizable as a "create" operation.

The "search" operation is a little less clear.  I ended up giving the "searchtext" URL query parameter a title of "search" but that's not standard, wouldn't work as well if the operation involed multiple parameters, and would obviously be a problem if the "title" field was needed for something else.

### Item

The schema for an individual "Issue" has title and status as required, and description as optional.  It allows both GET and PUT, and I think that allowing PUT is sufficient to document a general "update" operation.  As far as I can tell, all updates on the issue should be idempotent, so I'd make that a (ui) rather than a (un).

The challenge is in identifying the "open" and "close" operations.  With standard Hyper-Schema, I would fall back to documenting API conventions as with the "searchtext" query parameter for the "search" operation.

In this case, you have an enumerated field with two options, so it would not be hard to infer two matching operations; one to set one enum value, the other to set the other.

The challenge would be whether you want state transitions for all possible enumerated fields, or whether only some are signficant.  For instance, if you had a priority field enumerated with "high", "medium", or "low", changing priority might not be considered as a state operation in the same way as open vs closed.

## Draft-07 Hyper-Schema with Extensions

The [issue-with-transitions.json](issue-with-transitions.json) is the same, except for the `x-dataTransitions` keyword that I made up more or less on the spot.

The idea here would be to extend the hyper-schema with an indication of how the data needs to be change to accomplish a state transition.

The transitions are named as they are in your diagram, and provide a bit of JSON in a patch media type (in this case [JSON Merge Patch](https://tools.ietf.org/html/rfc7396) which usually looks a lot like the regular JSON data structure) that can be applied to execute the transition.

I've still written this up with PUT, so I would expect a client to apply the patch locally and then PUT the result.  You could write this with a PATCH and an Accept-Patch  header using the same media type, and then you could PATCH those patches directly.

It's an interesting idea, although I don't know if it's really  good match for a hypermedia approach or not.  Also, this is simple b/c it's a patch of constant data, but if you wanted to paramterize the patch things get complicated very quickly.

