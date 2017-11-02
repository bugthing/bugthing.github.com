---
layout: default
title: Rspec matcher truncated too much
---

I was in a situation where I needed to get rspec failure output **NOT** to shorten the output
of a failed match.. apparently there is option in works but right now I could use this.

    RSpec::Support::ObjectFormatter.default_instance.max_formatted_output_length = 100_000_000

