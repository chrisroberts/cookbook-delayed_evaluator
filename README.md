# DelayedEvaluator

This is a PoC cookbook to show a working solution for delayed
attribute evaluation within resource blocks. The relevant
Chef ticket is here:

* http://tickets.opscode.com/browse/CHEF-1814

## Example

Below is an example recipe to demonstrate the usage.

```ruby
include_recipe 'delayed_evaluator'

node.default[:testbook][:test_content] = "I'm the compile time value!"

ruby_block 'reset test content' do
  block do
    node.default[:testbook][:test_content] = "I'm the execution time value!"
  end
end

file '/opt/test_no_delay' do
  content node[:testbook][:test_content]
end

file '/opt/test_delay' do
  content delay_eval{ node[:testbook][:test_content] }
end
```

Note that the evaluation block must be provided to the delay_eval
method. It simply generates a Chef::DelayedEvaluator instance to
prevent conflicts where attributes are expecting a block.
