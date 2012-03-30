# Styleguides

A work in progress.

The idea is to question coding style by making it explicit, public and versioned.

Obviously much of this is down to taste, but feel free to [tell me on Twitter](http://twitter.com/henrik) when I'm wrong.


# Ruby

## Don't align a block of code after assignment.

Extract to a method or indent once on a new line.

So don't:

```ruby
foo = case x
      when 1: y
      when 2: z
      end
```

Do:

```ruby
foo =
  case x
  when 1: y
  when 2: z
  end
```

Or:

```ruby
foo = value_for(x)
```

# Ruby on Rails

## Avoid `default_scope` in Active Record.

It tends to cause confusing behavior.
