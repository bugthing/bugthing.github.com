---
layout: default
title: Derive metadata for rspec test
---

Sometimes I need to add metadata tags to rspec tests, here is a way to derive the metadata so I dont have to add the data to every test

```
  config.define_derived_metadata(
    described_class: ->(c) { c.is_a?(Class) && c.ancestors.include?(StandardError) }
  ) do |metadata|
    metadata[:error] = true
  end
  config.include(ErrorSpecHelper, :error)
```

