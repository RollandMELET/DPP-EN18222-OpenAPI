# DPP-EN18222-OpenAPI

**Unofficial community proposal.** An OpenAPI 3.1 description of the methods of
EN 18222:2026 (Digital Product Passport - Application Programming Interfaces
(APIs) for the product passport lifecycle management and searchability), plus a
proposal for API discovery. Not endorsed by CEN, CENELEC or CEN/CLC/JTC 24.

## What is here

| File | Content |
|---|---|
| [`openapi.yaml`](openapi.yaml) | OpenAPI 3.1, the 12 methods of EN 18222:2026 (create, read by passport, by product, by products, by date, update, delete, data elements and collections, registration). Each operation names its method in `x-en18222-method`. |
| [`discovery/DISCOVERY.md`](discovery/DISCOVERY.md) | How a client goes from a scanned product URI to the API: `/.well-known/api-catalog` (RFC 9727), a profile identifier per version (RFC 6906), an `api-catalog` link in the GS1 Digital Link linkset. |
| [`discovery/examples/`](discovery/examples/) | Worked example on a construction product demonstrator. |

Identifiers follow EN 18219:2026 (Digital product passport - Unique identifiers)
and are percent-encoded into one path segment. Passport payloads follow
EN 18223:2026 (Digital Product Passport - System interoperability) and are left
open in the schema.

## What is not here

The text of the standards. This repository cites EN numbers, titles and method
names only. Buy or consult the standards through your national standards body.

## Validate

```bash
npx @redocly/cli lint openapi.yaml
```

## Prior art

- [OwnYourData/dpp-service-public](https://github.com/OwnYourData/dpp-service-public)
  (Apache-2.0): an OpenAPI 3.0 of a working DPP service derived from EN 18222:2026.
  Method names here were cross-checked against it. No text was copied.
- [OpenDPP/opendpp-interop](https://github.com/OpenDPP/opendpp-interop): interoperability
  around EN 18223.
- [open-dpp/open-dpp](https://github.com/open-dpp/open-dpp): issues on EN 18222 alignment.

This repository aims to be a neutral reference open to contributions: pull
requests are welcome.

## Status

Version 0.1.0. Method names and status codes are to be checked against the
published standard before 1.0.0. Corrections welcome as issues or pull requests.

## Why

My ambition: to make the digital product passport for construction products (CPR DPP) **SIMPLE, CLEAR AND AFFORDABLE**, even for the smallest craft businesses, because it serves one of the noblest projects of our time: the climate neutrality Europe must reach by 2050.

Rolland Mélet, RoRworld

## License

Apache-2.0, see [LICENSE](LICENSE).
