# generate-tpt

Research and tooling for publishing teaching resources on Teachers Pay Teachers (TPT).

- [`docs/tpt-teardown.md`](docs/tpt-teardown.md): reverse-engineering teardown of TPT, covering its business model, the October 2026 seller-tier changes, the AI-content ban, URL and listing structure, search, and tech stack.
- [`schema/tpt-listing.schema.json`](schema/tpt-listing.schema.json): JSON Schema for a TPT listing, with a provenance block to check uploads against TPT's AI policy.

TPT announced a ban on AI-generated resources, covers, previews, and listings on 2026-10-08. Read Section 8 of the teardown before you build any generation features aimed at TPT.
