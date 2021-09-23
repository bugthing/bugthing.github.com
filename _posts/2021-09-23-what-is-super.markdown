---
layout: default
title: what-is-super
---
In ruby I sometimes encounter methods like this:

```
controller do
  def scope
    super.where(location: 'uk')
  end
end
```

its tricky to see what `super` is, so I sometimes apply this little bit of debugging code:

```
controller do
  def scope
    self.class.ancestors.each { |a| puts "#{a} has the method" if a.instance_methods(false).include?(__method__)}
    super.where(location: 'uk')
  end
end
```

it runs through the self.class ancestors and prints any that have an instance method with the same method name.... it been useful a few times
