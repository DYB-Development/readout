---
name: readout-install
description: Use to hook readout into a project — adding the gem to the Gemfile and loading it with require.
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
- `require "readout"` — loads readout in code that Bundler does not auto-require.

## How to use it

1. Ask the developer how to source the gem. Use `gem "readout"` for the published
   gem, or `gem "readout", github: "tylercschneider/readout"` for the repository.
2. If the host is itself a gem, ask the developer whether readout is a runtime
   dependency of it. If it is, add `spec.add_dependency "readout"` to the host's
   gemspec, and if step 1 chose the repository, also add that line to the host's
   `Gemfile`. If it is not, or the host is an app, add the line from step 1 to the
   host's `Gemfile`.
3. Run `bundle install`.
4. In a Rails app, `Bundler.require` loads readout, so skip this step. Anywhere
   else, add `require "readout"` at the top of the file that first uses it.
5. Confirm it loads: `bundle exec ruby -e 'require "readout"; puts Readout::VERSION'`
   prints a version.

## Conventions

- readout needs Ruby 3.2 or newer.
- It adds no generators, initializers, migrations or configuration files, so
  setup edits the `Gemfile` or gemspec and nothing else.
- After a readout upgrade, run `bundle update readout` and repeat step 5.
- Defining and reading metrics is the develop local's job.
