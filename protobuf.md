# Protocol Buffers (protobuf) Guide

> This guide covers Protocol Buffers version 3 (proto3) and later.

## Table of Contents

- [Overview](#overview)
- [Scalar Types](#scalar-types)
- [Composite Types](#composite-types)
  - [Messages](#messages)
  - [Enums](#enums)
  - [Repeated Fields](#repeated-fields)
  - [Maps](#maps)
  - [Oneof](#oneof)
- [Well-Known Types](#well-known-types)
- [Field Numbers](#field-numbers)
- [Default Values](#default-values)
- [Packages and Imports](#packages-and-imports)
- [Services and RPC](#services-and-rpc)
- [Options](#options)
- [Safe Schema Changes](#safe-schema-changes)
- [Unsafe Schema Changes](#unsafe-schema-changes)
- [Best Practices](#best-practices)
- [Helpful Tips](#helpful-tips)

---

## Overview

Protocol Buffers (protobuf) is a language-neutral, platform-neutral, extensible mechanism for serializing structured data. It is smaller, faster, and simpler than XML or JSON.

A `.proto` file defines the structure of your data. The `protoc` compiler generates source code from `.proto` files for use in your application.

```proto
syntax = "proto3";

package example;

message Person {
  string name = 1;
  int32  age  = 2;
}
```

The `syntax = "proto3";` declaration **must** be the first non-comment, non-empty line of the file.

---

## Scalar Types

| Proto Type   | Notes                                          | Default |
|--------------|------------------------------------------------|---------|
| `double`     | 64-bit floating point                          | `0`     |
| `float`      | 32-bit floating point                          | `0`     |
| `int32`      | Variable-length encoding; inefficient for negatives | `0` |
| `int64`      | Variable-length encoding; inefficient for negatives | `0` |
| `uint32`     | Variable-length encoding, unsigned             | `0`     |
| `uint64`     | Variable-length encoding, unsigned             | `0`     |
| `sint32`     | Variable-length encoding; efficient for negatives (ZigZag) | `0` |
| `sint64`     | Variable-length encoding; efficient for negatives (ZigZag) | `0` |
| `fixed32`    | Always 4 bytes; efficient if values often > 2²⁸ | `0`   |
| `fixed64`    | Always 8 bytes; efficient if values often > 2⁵⁶ | `0`   |
| `sfixed32`   | Always 4 bytes, signed                         | `0`     |
| `sfixed64`   | Always 8 bytes, signed                         | `0`     |
| `bool`       | Boolean value                                  | `false` |
| `string`     | UTF-8 or 7-bit ASCII encoded text              | `""`    |
| `bytes`      | Arbitrary byte sequence                        | `b""`   |

### Choosing the right integer type

- Use `int32`/`int64` for general-purpose integers that are rarely or never negative.
- Use `sint32`/`sint64` when values are frequently negative (ZigZag encoding is more efficient).
- Use `uint32`/`uint64` for values that are always non-negative.
- Use `fixed32`/`fixed64` or `sfixed32`/`sfixed64` when values are almost always large (> 2²⁸ or > 2⁵⁶), as the fixed cost becomes cheaper than varint encoding.

---

## Composite Types

### Messages

Messages are the primary building blocks. Fields are identified by their **field number** (not their name) in the binary encoding.

```proto
message Address {
  string street = 1;
  string city   = 2;
  string zip    = 3;
}

message Person {
  string  name    = 1;
  int32   age     = 2;
  Address address = 3;  // nested message
}
```

### Enums

Enums must have a zero value, conventionally named `<ENUM_NAME>_UNSPECIFIED` or `UNKNOWN`.

```proto
enum Status {
  STATUS_UNSPECIFIED = 0;
  STATUS_ACTIVE      = 1;
  STATUS_INACTIVE    = 2;
}
```

Use the `allow_alias` option when multiple names should map to the same value:

```proto
enum Direction {
  option allow_alias = true;
  DIRECTION_UNSPECIFIED = 0;
  DIRECTION_NORTH       = 1;
  DIRECTION_UP          = 1;  // alias
}
```

### Repeated Fields

Repeated fields represent ordered lists (arrays/slices). In proto3, numeric repeated fields use packed encoding by default.

```proto
message Team {
  repeated string members = 1;
  repeated int32  scores  = 2;
}
```

### Maps

Maps are shorthand for a repeated message of key-value pairs. Keys may be any scalar type except `float`, `double`, or `bytes`. Values may be any type except another map.

```proto
message Config {
  map<string, string> labels   = 1;
  map<int32,  Status> statuses = 2;
}
```

- Map fields cannot be `repeated`.
- Map entries are unordered.
- Duplicate keys in binary format: last value wins.

### Oneof

`oneof` ensures at most one field is set at a time. Setting one field clears the others.

```proto
message SearchRequest {
  oneof query {
    string keyword = 1;
    int64  item_id = 2;
  }
  int32 page = 3;
}
```

- `repeated` fields and `map` fields cannot be inside a `oneof`.
- Fields inside a `oneof` share memory; only one value exists at a time.

---

## Well-Known Types

Google ships a set of pre-defined message types in `google/protobuf/`. Import and use them rather than reinventing common patterns.

| Type                        | Description                              |
|-----------------------------|------------------------------------------|
| `google.protobuf.Timestamp` | Point in time (seconds + nanos since Unix epoch) |
| `google.protobuf.Duration`  | Signed duration (seconds + nanos)        |
| `google.protobuf.Any`       | Arbitrary serialized message with a type URL |
| `google.protobuf.Struct`    | Arbitrary JSON-like structure            |
| `google.protobuf.Value`     | Single JSON value                        |
| `google.protobuf.ListValue` | List of JSON values                      |
| `google.protobuf.FieldMask` | Set of field paths used in partial updates |
| `google.protobuf.Empty`     | Empty message (useful as RPC placeholder)|
| `google.protobuf.BoolValue` | Nullable/optional bool wrapper           |
| `google.protobuf.Int32Value`| Nullable/optional int32 wrapper          |
| `google.protobuf.StringValue`| Nullable/optional string wrapper        |
| *(and other scalar wrappers)*|                                         |

```proto
import "google/protobuf/timestamp.proto";

message Event {
  string                    name       = 1;
  google.protobuf.Timestamp created_at = 2;
}
```

---

## Field Numbers

Field numbers uniquely identify fields in the **binary wire format**. They must be:

- Unique within a message.
- In the range `1` to `536,870,911` (`2²⁹ - 1`).
- **Not** in the reserved range `19000–19999` (used internally by the protobuf runtime).

Field numbers **1–15** are encoded using 1 byte; **16–2047** use 2 bytes. Reserve `1–15` for the most frequently used fields to reduce message size.

```proto
message Example {
  string frequent_field   = 1;   // 1 byte tag overhead
  string occasional_field = 16;  // 2 byte tag overhead
}
```

Once a field number is used and messages are in production, **never reuse** a field number for a different purpose. Use the `reserved` keyword to prevent accidental reuse.

```proto
message OldMessage {
  reserved 2, 5, 9 to 11;           // numbers
  reserved "old_field", "temp";     // names
}
```

---

## Default Values

In proto3, fields not present in the serialized bytes take their type's default value. There is **no way to distinguish** between a field being explicitly set to its default value and the field being absent.

| Type               | Default      |
|--------------------|--------------|
| Numeric types      | `0`          |
| `bool`             | `false`      |
| `string`           | `""`         |
| `bytes`            | empty bytes  |
| Enum               | First value (must be `0`) |
| Message            | `null` / not set |
| `repeated` / `map` | Empty list / empty map |

If distinguishing "not set" from "set to zero/false/empty" matters, use a wrapper type (`google.protobuf.Int32Value`, etc.) or a `oneof` with a single field.

---

## Packages and Imports

Use packages to avoid name collisions and to organise related definitions.

```proto
syntax = "proto3";

package mycompany.myservice.v1;

import "mycompany/common/v1/types.proto";
```

- Use lowercase, dot-separated package names.
- The package name becomes a namespace prefix in most generated languages.
- Prefer **versioned packages** (e.g. `v1`, `v2`) to support multiple API versions side-by-side.

---

## Services and RPC

Services define RPC endpoints, typically used with gRPC.

```proto
service UserService {
  rpc GetUser    (GetUserRequest)     returns (GetUserResponse);
  rpc ListUsers  (ListUsersRequest)   returns (ListUsersResponse);
  rpc CreateUser (CreateUserRequest)  returns (CreateUserResponse);

  // Server-side streaming
  rpc WatchUsers (WatchUsersRequest)  returns (stream UserEvent);

  // Client-side streaming
  rpc BulkCreate (stream CreateUserRequest) returns (BulkCreateResponse);

  // Bidirectional streaming
  rpc Sync       (stream SyncRequest) returns (stream SyncResponse);
}
```

---

## Options

Options customise behaviour at the file, message, field, enum, or service level.

```proto
// File-level
option java_package = "com.example.myservice";
option go_package   = "github.com/example/myservice/v1;myservicev1";

// Message-level
message Foo {
  option deprecated = true;
  string bar = 1 [(validate.rules).string.min_len = 1];  // custom/extension options
}

// Field-level
string email = 2 [deprecated = true];
```

Common built-in options:

| Option                | Level   | Description                                  |
|-----------------------|---------|----------------------------------------------|
| `deprecated`          | Field / Message | Mark as deprecated                   |
| `packed`              | Repeated field  | Control packed encoding (default `true` for scalars in proto3) |
| `java_package`        | File    | Java package name                            |
| `java_outer_classname`| File    | Outer Java class name                        |
| `go_package`          | File    | Go import path and package name              |
| `optimize_for`        | File    | `SPEED` (default), `CODE_SIZE`, `LITE_RUNTIME` |

---

## Safe Schema Changes

These changes are **backward and forward compatible** — old readers can still parse messages written by new writers, and vice versa.

| Change | Notes |
|--------|-------|
| Add a new field | Old parsers ignore unknown fields; new parsers use the default for missing fields. |
| Remove a field | **Mark the number and name as `reserved`** to prevent future reuse. |
| Rename a field | Safe in binary format (uses field number), but breaks JSON/text format and generated accessor names. |
| Change a field's default value | No effect on binary; only affects newly created messages in code. |
| Add a new enum value | Old parsers treat unknown enum values as `0` in proto3. |
| Add a new `oneof` case | Treated as an unknown field by old parsers. |
| Add an RPC method | Old clients/servers ignore methods they don't know. |
| Change `optional` to `repeated` (same field number and type) | Old parsers reading a `repeated` field see only the last element; new parsers reading an `optional` field treat it as a single-element list. |
| Change between `int32`, `uint32`, `int64`, `uint64`, and `bool` | Wire-compatible; values are truncated or zero-extended as needed. |
| Change between `string` and `bytes` | Wire-compatible as long as the bytes are valid UTF-8. |
| Change between `sint32` and `sint64` | Wire-compatible. |
| Change between `fixed32` and `sfixed32`, or `fixed64` and `sfixed64` | Wire-compatible (same wire type). |
| Convert a non-`oneof` field into a `oneof` | Wire-compatible if only one field is ever set at a time. |

---

## Unsafe Schema Changes

These changes **break compatibility** between old and new parsers and should never be made to deployed schemas.

| Change | Why It Breaks |
|--------|---------------|
| Change a field number | The binary format uses field numbers, not names. Old parsers see the field as unknown; new parsers miss the field entirely. |
| Reuse a field number for a different type | Parsers try to decode the old data as the new type, causing data corruption or parse errors. |
| Change between incompatible wire types (e.g., `int32` → `string`, `string` → `int32`) | Different wire types; parsers will fail to decode or silently corrupt data. |
| Change between `bytes` and `string` when data is not valid UTF-8 | `string` fields require valid UTF-8; invalid bytes will cause parse errors in strict runtimes. |
| Remove an enum value without reserving it | Could be reused later with a different meaning, silently corrupting old data. |
| Change a `repeated` field to a non-`repeated` field | Old data may have multiple values; new parser only reads one. |
| Change a `map` to a non-`map` (or vice versa) | Maps are encoded as repeated message entries; changing the encoding type corrupts data. |
| Remove a `reserved` range or name | Allows field numbers or names to be accidentally reused. |
| Change field from `oneof` to non-`oneof` (or vice versa) | Wire-compatible only in some cases; oneof semantics (clearing other fields) will be lost. |
| Rename a package | Breaks the `type_url` in `google.protobuf.Any`, JSON field name lookup, and generated code. |
| Change `sint32`/`sint64` to `int32`/`int64` (or vice versa) | Both use varint wire type but apply ZigZag encoding differently; values are silently corrupted. |

---

## Best Practices

### Schema design

- **Use `reserved` immediately** when removing a field or enum value. Do not wait.
- **Prefer message wrappers over scalar fields** when "not set" is a meaningful state.
- **Use versioned packages** (`v1`, `v2`) rather than modifying deployed schemas in place. When a breaking change is unavoidable, create a new package version.
- **Keep messages small and focused.** Large, monolithic messages are harder to evolve safely.
- **Use `google.protobuf.FieldMask`** for partial update APIs to avoid ambiguity between "field not provided" and "field set to empty/zero."
- **Define enum zero values** as `<ENUM_NAME>_UNSPECIFIED` (e.g. `STATUS_UNSPECIFIED = 0`). Never give the zero value real semantic meaning; it represents the absence of a choice.
- **Avoid using field numbers above 15 for hot fields.** Numbers 1–15 encode in one byte; 16+ require two bytes.

### File organisation

- One package per directory; one or a small number of related `.proto` files per package.
- Name files in `lower_snake_case.proto`.
- Name messages and enums in `UpperCamelCase`; fields and enum values in `lower_snake_case` and `UPPER_SNAKE_CASE` respectively.
- Always include the `go_package`, `java_package`, or language-specific option relevant to your stack.
- Store `.proto` files in source control alongside the code that uses them.

### API design

- Follow the [Google API Design Guide](https://cloud.google.com/apis/design) for RPC and resource-oriented APIs.
- Prefer **request/response message pairs per RPC** rather than reusing the same message for multiple methods. This makes it easier to evolve each endpoint independently.
- Add an `int32 page_size` and `string page_token` / `string next_page_token` pattern for paginated list methods.
- Never use generic types like `google.protobuf.Any` unless truly necessary — they sacrifice type safety and make tooling harder.

### Enumerations

- Always keep `0` as an unspecified/unknown sentinel value.
- Prefix enum value names with the enum name to avoid conflicts (e.g. `COLOR_RED`, not just `RED`).
- Do not rely on the numeric value of enum entries in application logic — use the symbolic names.

---

## Helpful Tips

### Use `protoc-gen-validate` for field validation

The [protoc-gen-validate](https://github.com/bufbuild/protoc-gen-validate) plugin lets you annotate fields with validation rules that are enforced at runtime:

```proto
import "validate/validate.proto";

message CreateUserRequest {
  string email = 1 [(validate.rules).string.email = true];
  int32  age   = 2 [(validate.rules).int32 = {gte: 0, lte: 130}];
}
```

### Use the Buf toolchain

[Buf](https://buf.build) provides linting, breaking-change detection, and a schema registry:

```sh
buf lint                  # lint .proto files
buf breaking --against .git#branch=main  # detect breaking changes vs main
buf generate              # generate code using buf.gen.yaml
```

The `buf breaking` command automates detection of the unsafe changes listed above.

### Inspecting wire format

Use `protoc --decode_raw` to inspect raw binary without a `.proto` file:

```sh
cat message.bin | protoc --decode_raw
```

Or decode with a known type:

```sh
cat message.bin | protoc --decode=mypackage.MyMessage mypackage/my_message.proto
```

### JSON interoperability

Proto3 messages have a canonical JSON mapping. Field names are camelCased in JSON by default (`my_field` → `myField`). Use `json_name` to override:

```proto
string my_field = 1 [json_name = "myField"];
```

Fields set to their default value are **omitted** from JSON output by default. Use `EmitUnpopulated` (Go) / `includeDefaultValueFields` (Java) options to include them.

### Backward-compatible "optional" semantics in proto3

Use a `oneof` with a single field to get true optional semantics (distinguishing "not set" from "set to default"):

```proto
message Foo {
  oneof optional_count {
    int32 count = 1;
  }
}
```

Alternatively, in proto3.15+ (protoc 3.15+), the `optional` keyword is supported:

```proto
message Foo {
  optional int32 count = 1;  // has_count() / clearCount() generated
}
```

### Avoid `google.protobuf.Any` for performance-sensitive paths

`Any` requires serializing the inner message, storing it as bytes, and then deserializing again. Prefer `oneof` when the set of possible types is known at compile time.

### Proto file linting rules to follow

| Rule | Rationale |
|------|-----------|
| All field names in `lower_snake_case` | Consistent cross-language naming |
| Enum values in `UPPER_SNAKE_CASE` | Convention |
| Message/enum/service names in `UpperCamelCase` | Convention |
| Files named `lower_snake_case.proto` | Convention |
| Package name matches directory structure | Predictable imports |
| Always set `go_package` / `java_package` | Avoids compiler warnings; required by many generators |
| No `required` fields (proto2 only, but worth knowing) | `required` is permanently removed in proto3 |

### Proto3 vs proto2 key differences

| Feature | proto2 | proto3 |
|---------|--------|--------|
| `required` fields | Yes (avoid) | Removed |
| `optional` keyword | Explicit | Implicit (all singular fields); explicit `optional` re-added in 3.15 |
| Default values | Per-field custom defaults | Fixed type defaults only |
| Unknown fields | Dropped by default | Preserved (since proto3.5) |
| JSON mapping | Optional | Canonical and built-in |
| Groups | Supported (deprecated) | Removed |
