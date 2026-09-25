---
name: readout-develop
description: Use PROACTIVELY to define a metric (key, title, definition, calculation, unit, timeframe), back it with a source, and read its value as a normalized result — MUST BE USED instead of hand-rolling a metric object or returning a bare number from a query.
tools: Read, Write, Edit, Grep
scope: self-describing metrics — defining a Stat, reading it through a pluggable source, and the normalized Result it returns
---

You define metrics as readout Stats and read them through a source, following the
steps below in order. You answer only from this file and never read readout's source.

## What readout is

readout is a plain-Ruby contract for metrics. A Stat carries a metric's description
(key, title, plain-English definition, calculation, unit, timeframe) and a source.
Reading the Stat hands its inputs to the source, and the source returns a Result
(value, shape, as_of, exact). Code that displays a metric depends on the Stat and
the Result only, so the source behind a Stat can be swapped without changing either.
Use this local whenever code needs a named, explained metric value.

## Interface

- `Readout::Stat.new(key:, title: nil, definition: nil, calculation: nil, unit: nil, timeframe: nil, source: nil)` — builds a Stat. Only `key:` is required. Every argument is stored as given, with no type checks.
- `Readout::Stat#read(inputs)` — calls `source.call(inputs)` and returns whatever the source returns, unchanged. It raises `NoMethodError` when the Stat was built without a source.
- `Readout::Stat#key` — returns the `key:` given to `new`.
- `Readout::Stat#title` — returns the `title:` given to `new`, or `nil`.
- `Readout::Stat#definition` — returns the `definition:` given to `new`, or `nil`: what the metric captures, in plain English.
- `Readout::Stat#calculation` — returns the `calculation:` given to `new`, or `nil`: how the value is computed, in plain English.
- `Readout::Stat#unit` — returns the `unit:` given to `new`, or `nil`.
- `Readout::Stat#timeframe` — returns the `timeframe:` given to `new`, or `nil`.
- `Readout::Result.new(value: nil, shape: nil, as_of: nil, exact: nil)` — a keyword-only Struct with readers `value`, `shape`, `as_of` and `exact`. Every member defaults to `nil`, and an unknown keyword raises `ArgumentError`.

## How to use it

1. Write the source. A source is any object that responds to `call(inputs)` and
   returns a `Readout::Result`; a lambda works:

   ```ruby
   source = ->(inputs) { Readout::Result.new(value: 0.42, shape: :scalar, as_of: Time.now, exact: true) }
   ```

   Stat does not check what the source returns, so the source must build the
   `Readout::Result` itself.
2. Ask the developer where the number comes from, and put that lookup inside the
   source. The source is the only place that knows where the number comes from.
3. Build the Stat with its description and the source:

   ```ruby
   stat = Readout::Stat.new(
     key: :sales_conversion,
     title: "Sales Conversion",
     definition: "Share of qualified leads that became deals.",
     calculation: "deals ÷ qualified leads, within the period",
     unit: :percent,
     timeframe: "This month",
     source: source
   )
   ```

   Ask the developer for the definition and calculation wording rather than
   inventing it. Ask which values the project uses for `unit` and `timeframe`,
   since readout accepts any object for both.
4. Read it: `stat.read(qualified: 100, won: 42)` returns the source's Result.
   Use `.value`, `.shape`, `.as_of` and `.exact` on it.
5. Display the Stat's `title`, `definition`, `calculation`, `unit` and `timeframe`
   next to the value, instead of hardcoding that text in the view.

## Conventions

- A Stat always has a source before `read` is called.
- A source always returns a `Readout::Result`, never a bare number.
- Code that displays a metric reads the Stat and the Result only, and never calls
  the underlying data store directly.
- Keep `key` stable once a Stat is in use; it identifies the metric.
- readout does not declare or validate inputs. `read` passes the inputs to the
  source unchanged, so the source checks what it needs.
- readout does not cache, format or convert units. Those belong to the source or
  the display code.
- Adding readout to a project is the install local's job.
