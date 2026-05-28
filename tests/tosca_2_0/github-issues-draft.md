# GitHub Issue Drafts — Remaining Test Case Problems

Each section below is a draft issue suitable for filing against
`oasis-open/tosca-community-contributions` (or upstream as appropriate).
Grouped first by category, then by file.

---

## Category A — Test case bugs (fixable in this repo)

### 1. `timestamp/timestamp.yaml`: `time4` is not a valid RFC 3339 timestamp

**File:** `tests/tosca_2_0/timestamp/timestamp.yaml`

The test expects the file to parse successfully (`time4: "2000-02-29"`), but
this is a date-only value with no time component or time offset. RFC 3339 (the
spec TOSCA defers to for timestamps) requires both a time-of-day and a `Z` or
`±HH:MM` offset.

**Suggested fix:** change `time4: "2000-02-29"` to a full RFC 3339 timestamp,
e.g. `time4: "2000-02-29T00:00:00Z"`.

---

### 2. `artifact-definition/s121.yaml`: undefined properties

**File:** `tests/tosca_2_0/artifact-definition/s121.yaml`

The template references properties that are never declared on the artifact
type: `name`, `container-format`, `disk-format`, `min-disk`, `size`.

**Suggested fix:** either declare these on the artifact type, or remove them
from the template.

---

### 3. `function-syntax/tutorial-functions.yaml`: undefined `tosca` namespace

**File:** `tests/tosca_2_0/function-syntax/tutorial-functions.yaml`

The file uses a `tosca` namespace prefix that is never defined or imported.

**Suggested fix:** either import a profile that establishes the `tosca`
namespace, or remove the prefix from references in the file.

---

### 4. `capability-allocation/capability-allocation-float.yaml` and
`capability-allocation/s61a.yaml`: allocations reference node attributes,
not capability attributes

**Files:**
- `tests/tosca_2_0/capability-allocation/capability-allocation-float.yaml`
- `tests/tosca_2_0/capability-allocation/s61a.yaml`

Per spec, allocation keywords are meant to deduct from a *capability's*
attributes. These two examples deduct from attributes of the parent Compute
node instead.

**Suggested fix:** move the allocated attributes (or their counterparts) onto
the capability type, and update the allocation references accordingly.

---

### 5. `requirement-assignment-grammar/requirement-assignment-alloc.yaml`:
allocation references target node, not target capability

**File:** `tests/tosca_2_0/requirement-assignment-grammar/requirement-assignment-alloc.yaml`

Same issue as #4 but in the requirement-assignment context: the allocation
points at a property of the target node rather than a property of the target
capability.

**Suggested fix:** update the allocation to reference a capability property.

---

### 6. `requirement-assignment-grammar/requirement-assignment-attribute.yaml`:
`description` not allowed on attribute assignment

**File:** `tests/tosca_2_0/requirement-assignment-grammar/requirement-assignment-attribute.yaml`

Per Section 9.7 (Attribute Assignment) of TOSCA 2.0, attribute assignments do
not support the `description` keyword. This file uses it.

**Suggested fix:** remove the `description` keyword from the attribute
assignment.

---

### 7. `representation-graph-query-functions/s97.yaml`: optional values
assigned to mandatory properties

**File:** `tests/tosca_2_0/representation-graph-query-functions/s97.yaml`

The `vRouter` node type declares mandatory properties (`mgmt-net-name`,
`mgmt-cp-v4-fixed-ip`, etc.) but the templates use `$get_input` to fetch
values from `NetworkInfo`, whose nested fields are declared `required: false`.

**Suggested fix:** either declare the source-side fields as required, or
declare the destination-side properties as optional.

---

### 8. `handling-unbounded-requirement-count-ranges/s150.yaml`:
no matching capability on target

**File:** `tests/tosca_2_0/handling-unbounded-requirement-count-ranges/s150.yaml`

`Client.requirements[ServedBy]` expects a target node with a `Service`
capability where `Client` is a valid source type and `ServedBy` is a valid
relationship type. The `Server` node type in the file has no such capability.

**Suggested fix:** add a `Service` capability (with `valid_source_node_types`
including `Client`, `valid_relationship_types` including `ServedBy`) to
`Server`.

---

### 9. `requirement-count/requirements-and-capabilities.yaml`: 5 distinct issues

**File:** `tests/tosca_2_0/requirement-count/requirements-and-capabilities.yaml`

Multiple problems:
- `light2` socket relationship does not uniquely specify the target capability
- `light3` socket relationship does not uniquely specify the target capability
- `light7` has an invalid node filter
- `light8` comment claims `node_filter` at the node level applies to all
  requirements — no such spec statement exists
- `light9` has an invalid node filter

**Suggested fix:** address each item individually; may warrant splitting into
multiple smaller tests.

---

### 10. `node-filter-definition/node-filter-select.yaml`: confused profile vs.
import

**File:** `tests/tosca_2_0/node-filter-definition/node-filter-select.yaml`

The file defines a profile whose name (`computeInfrastructure.yaml`) collides
with the name of a TOSCA file that appears intended to be imported.

**Suggested fix:** clarify the example — either rename the profile or
restructure so the import resolves correctly.

---

### 11. `node-template/node-template-filter.yaml`: malformed `node_filter`
template

**File:** `tests/tosca_2_0/node-template/node-template-filter.yaml`

A node template literally named `"node_filter"` is defined; if the intent was
to attach a node filter to the previous node template:
- the indentation is wrong
- the `select` directive is missing
- other definitions are not allowed alongside a node filter

**Suggested fix:** restructure to attach a proper node filter under the
preceding template via the `select` directive.

---

### 12. `substitution-mapping/imports/super-load-balancer.yaml`: target
capability type incompatible with relationship type

**File:** `tests/tosca_2_0/substitution-mapping/imports/super-load-balancer.yaml`

The substitution mapping declares a target capability type that is not
compatible with the relationship type used to bind to it.

**Suggested fix:** align the capability type and relationship type, or use a
compatible target.

---

### 13. `substitution-mapping/substitution-mapping-client.yaml`: 3 issues

**File:** `tests/tosca_2_0/substitution-mapping/substitution-mapping-client.yaml`

- `substitutable` is not a valid directive
- capabilities and relationships do not match
- the `tosca` namespace is not defined

**Suggested fix:** remove or correct each issue individually.

---

### 14. `substitution-mapping/substitution-mapping.yaml`: 4 issues

**File:** `tests/tosca_2_0/substitution-mapping/substitution-mapping.yaml`

- incorrect interface mappings
- incompatible capabilities and requirements
- invalid inputs
- invalid outputs

**Suggested fix:** rewrite to reflect a valid substitution mapping example.

---

### 15. `tosca-path/source-and-target.yaml`: v1.3 syntax and undefined properties

**File:** `tests/tosca_2_0/tosca-path/source-and-target.yaml`

The file mixes TOSCA 1.3 function syntax with TOSCA 2.0, and references
properties that are not defined.

**Suggested fix:** update to TOSCA 2.0 function syntax and either declare or
remove the undefined property references.

---

### 16. `workflows/workflows.yaml`: 4 issues

**File:** `tests/tosca_2_0/workflows/workflows.yaml`

- `tosca` namespace not defined
- incorrect filter syntax (should use boolean expressions; this may also be
  a spec oversight worth raising upstream)
- `activities` are mandatory and missing
- `WebServer` does not define `foo` or `bar` properties

**Suggested fix:** address each item; consider whether filter syntax is a
spec issue.

---

### 17. `time/s70.yaml`: invalid `Bitrate` type — prefixes require single
unit entry with multiplier 1

**File:** `tests/tosca_2_0/time/s70.yaml`

Per spec, when `prefixes` is used the `units` map MUST contain only one entry,
and that entry MUST have a multiplier of 1. The `Bitrate` type in this file
defines both `bps: 1` and `Bps: 8`.

**Suggested fix:** split into two types (one per unit), or drop `Bps`.

---

## Category B — Missing external profiles

These tests reference profiles that are not present in the test repo. They
would all pass if the referenced profiles were added.

### 18. `examples/s24.yaml`: relies on undefined `org.oasis-open.tosca.simple:2.0`

### 19. `namespaces/s35.yaml`: relies on undefined `io.kubernetes:1.30`

### 20. `namespaces/s36.yaml`: `namespaces/namespaces-k8s.yaml` does not
define a `Pod` type

### 21. `namespaces/namespaces.yaml`: top-level `namespace` keyword is used
but not allowed per spec

### 22. `profile-versions/s21.yaml`: undefined profile imported

### 23. `profiles/profiles-profile-tree.yaml`: undefined profile imported

### 24. `profiles/profiles-profile2.yaml`: undefined profile imported

**Suggested fix:** publish the referenced profiles in the repo (under
`profiles/`), or change the tests to import profiles that already exist.

---

## Category C — Spec disagreements (likely upstream OASIS issues)

These are not bugs in the test cases per se — they're places where the
test/spec relationship is ambiguous or contested. They may be better filed
against the spec rather than this repo.

### 25. `boolean/boolean-false-inv.yaml`: YAML allows both `False` and
`false`; should TOSCA distinguish?

### 26. `examples/import-examples-file-schema-missing-inv.yaml`: URLs without
a schema are allowed per Section 6.8.2.2

### 27. `metadata/invalid_metadata_non_string_values.yaml`: per Section
5.3.1, all value types are allowed in metadata

### 28. `metadata/invalid_metadata_missing_value.yaml`: technically a None
value is allowed but not useful; should we flag it?

### 29. `schema-definition/schema-definition-map-bad-entry-schema-inv.yaml`:
test seems valid; key_schema must derive from string is itself overly
restrictive

### 30. `schema-definition/schema-definition-map-keys-mixed-inv.yaml`:
arguably invalid since integer map keys are not allowed; needs spec
clarification

### 31. `service-template-grammar/service-template-missing-node-templates-inv.yaml`:
`node_templates` is required, but the spec allows an empty map — what's
the practical difference?

### 32. `timestamp/timestamp-inv.yaml`: per RFC 3339 §5.6, space is allowed
as a date/time separator. The real reason this file should be rejected is
the missing time offset.

### 33. `artifact-types/artifacts.yaml` and
`repository-definitions/repositories-valid-definition.yaml`: `credential`
is used but the spec does not list it as a supported keyword on these
elements

### 34. `requirement-assignment-grammar/requirement-assignment-count.yaml`:
should `count` values on fixed requirements be allowed?

### 35. `requirement-assignment-grammar/requirement-assignment-filter.yaml`
and `requirement-assignment-full.yaml`: should node filters on fixed
requirements be allowed? What is the use case?

### 36. `interface-types/interfaces.yaml`: per Section 9.8, operation output
parameters do not allow the `value` keyword — but this template uses it

### 37. `property-definition/property-definition-refine-value-equal.yaml`:
per the letter of the spec this refinement should not be allowed; also
implementing a general-purpose equality check for arbitrary data types is
difficult

### 38. `available_allocation/s103a.yaml`: select nodes are useless without
incoming relationships — should the spec flag this?

### 39. `call-operation-activity-definition/call-operation-undefined-workflow-input.yaml`:
the workflow's `location` input is referenced but not defined — should be
caught by validation

### 40. `scalar/scalar-derive-units.yaml`: spec says `units` is mandatory,
but `Mass_larger` does not define units — is the spec correct?
