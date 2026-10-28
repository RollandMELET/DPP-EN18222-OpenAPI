# Discovery proposal: from a data carrier to the EN 18222 API

Status: community proposal, version 0.1. Not part of any standard.

## The gap

EN 18222:2026 (Digital Product Passport - Application Programming Interfaces
(APIs) for the product passport lifecycle management and searchability) fixes
relative paths such as `v1/dpps/{dppId}`. It does not say where the API root is.
A client that scans a QR code (market surveillance, recycler, BIM tool) gets a
product URI, then has no standard way to find the API that serves the passport.

Existing implementations each solve it their own way (a proprietary
`/.well-known/dpp-service` document, for example). This proposal uses only
mechanisms already registered at IANA, so nothing new needs to be invented.

## Three building blocks

| Need | Mechanism | Reference |
|---|---|---|
| Find the APIs of a domain | `/.well-known/api-catalog` and link relation `api-catalog` | RFC 9727 (api-catalog: A Well-Known URI and Link Relation to Help Discovery of APIs) |
| Get the machine description, with its security scheme | link relation `service-desc` to an OpenAPI file | RFC 8631 (Link Relation Types for Web Services) |
| Know that the API implements EN 18222, and which version | link relation `profile` | RFC 6906 (The 'profile' Link Relation Type) |

The catalog is a linkset, RFC 9264 (Linkset: Media Types and a Link Relation
Type for Link Sets), served as `application/linkset+json`.

## 1. Profile identifier per version

One URI per major version of the method set:

```
tag:rorworld.eu,2026:en18222:1
```

Meaning: "this API exposes the methods of EN 18222:2026 as described in
`openapi.yaml` version 0.x or 1.x of this repository".

A tag URI, RFC 4151 (The 'tag' URI Scheme), needs no registry and no server.
It is provisional by design: the right owner of this identifier is CEN/CLC/JTC 24.
If JTC 24 or the Commission publishes an identifier, this one is replaced and
kept only as an alias.

A breaking change of the standard (a new edition with incompatible methods)
gives a new profile, `...:en18222:2`. Additive changes keep `:1`.

## 2. API catalog at the well-known address

`GET https://<api domain>/.well-known/api-catalog`, `Accept: application/linkset+json`

The entry of the EN 18222 API gives:
- `anchor`: the API root, to which the relative paths of EN 18222 are appended;
- `service-desc`: the OpenAPI file, whose `securitySchemes` tells the client how
  to authenticate (EN 18239:2026 fixes no protocol);
- `profile`: the identifier above.

Example: [`examples/soprema-api-catalog-proposed.json`](examples/soprema-api-catalog-proposed.json).

## 3. Link from the product's GS1 Digital Link linkset

The resolver that answers the scanned URI adds one link of type `api-catalog`
to the linkset of the product, pointing to the catalog above. It also exposes
the passport under the IANA relation `dpp` next to `https://ref.gs1.org/voc/dpp`.

A client then follows four steps:

```bash
ID=https://soprema.dpp.360sc.yt/01/03434550314367/10/US03-048838
curl -s -H "Accept: application/linkset+json" "$ID"            | jq '.linkset[0]["api-catalog"][0].href'
curl -s -H "Accept: application/linkset+json" "<catalog href>" | jq '.linkset[] | select(.profile[]?.href=="tag:rorworld.eu,2026:en18222:1") | .anchor'
curl -s "<service-desc href>"                                    # OpenAPI, security scheme
curl -s "<anchor>v1/dppsByProductId/$(jq -rn --arg u "$ID" '$u|@uri')"
```

Link relation names are lowercase registered names (`api-catalog`, `dpp`), which
the GS1 linkset schema accepts next to full URIs.

## Worked example: Soprema demonstrator

- [`examples/soprema-linkset-as-served-2026-10-28.json`](examples/soprema-linkset-as-served-2026-10-28.json):
  linkset served on 28 October 2026 for a batch of a construction product
  (link titles, served in French, translated to English here).
- [`examples/soprema-linkset-proposed.json`](examples/soprema-linkset-proposed.json):
  the same, plus `dpp` and `api-catalog`. Only these two keys are added.
- [`examples/soprema-api-catalog-proposed.json`](examples/soprema-api-catalog-proposed.json):
  the catalog the API domain would serve. Not deployed today (the well-known
  address answers 404).

## Open questions

1. Who issues the profile identifier in the long run: JTC 24, the Commission
   (DPP registry), or a community namespace?
2. Should the EU DPP registry record the catalog URI rather than a bare
   `dppApiEndpoint`?
3. One catalog per operator domain, or one per passport service provider acting
   for several operators?
