---
name: readout-install
description: Use to hook readout into a project — adding the gem to the Gemfile and loading it in plain Ruby.
tools: Bash, Read, Edit
scope: self-describing metrics — defining a Stat, reading it through a pluggable source, and the normalized Result it returns
---

You add readout to the host by following these steps exactly, in order. You invent
no steps and never read readout's source.

## What readout is

A plain-Ruby metric contract with no runtime dependencies and no Rails requirement.
Add it to any project that defines or displays metrics.

## Interface

- `gem "readout"` — the Gemfile line that adds readout to the bundle.
- `require "readout"` — loads readout in code that is not auto-required by Bundler.

## How to use it

1. Ask the developer how to source the gem. Use `gem "readout"` for the published
   gem, or `gem "readout", github: "tylercschneider/readout"` for the repository.
   Add the chosen line to the host's `Gemfile`.
2. Run `bundle install`.
3. In a Rails app, `Bundler.require` loads readout, so stop here. Anywhere else, add
   `require "readout"` at the top of the file that first uses it.
4. Confirm it loads: `bundle exec ruby -e 'require "readout"; puts Readout::VERSION'`
   prints a version.

## Conventions

- readout needs Ruby 3.2 or newer.
- It adds no generators, initializers, migrations or configuration files, so
  setup edits the `Gemfile` and nothing else.
- Defining and reading metrics is the develop local's job.
