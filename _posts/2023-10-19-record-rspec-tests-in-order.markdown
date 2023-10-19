---
layout: default
title: Record Rspec tests in order
---
Recently I had a flakey test that only failed when certains where run. So wanted to record which tests run in which order.
Fist I tried just adding to `spec_helper.rb`

```
  config.example_status_persistence_file_path = "tmp/last_run_examples.txt"
```

this was easy but did not record the order inwhich the specs ran, so I ended up doing this

```
$files_executed = []
RSpec.configure do |config|
  ...
  config.before(:each) do |example|
    $files_executed.add(example.location)
  end
  config.after(:suite) do
    File.write("./tmp/last_specs_run.txt", $files_executed.join(' '))
  end
end
```
