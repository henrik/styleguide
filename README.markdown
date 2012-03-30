# Styleguides

A work in progress.

The idea is to question coding style by making it explicit, public and versioned.

Obviously much of this is down to taste, but feel free to [tell me on Twitter](http://twitter.com/henrik) when you disagree and why.


# Any language


## Prefer obvious code to comments.

Before writing a comment, see if you can rename or extract so the comment is no longer needed.


## Interface comments go outside; implementation comments go inside.

A comment that helps you use the interface goes outside:

```ruby
# Arguments must be so and so.
def method(x, y)
end
```

A comment that relates to implementation, not interface or usage, goes inside:

```ruby
def method(x, y)
  # We did it this way because of blurk.
  doing_it_this_way
end
```


## Don't check in commented-out code.

Feel free to comment out code in development, but when you push, it's either in or out. You can retrieve it from version control history later if you want it back.

## Also:

* Obvious beats clever.


# Ruby


## Indent `private` like any class method.

Do:

```ruby
class Example

  def public
  end

  private

  def private
  end

end
```

I used to favor this style:

```ruby
class Example

  def public
  end

private

  def private
  end

end
```

It makes the public/private boundary very obvious, and is similar to how `rescue` is indented in a method. But editor autoindentation doesn't tend to use this style, nor the teams I've worked on.


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


## Make option hash arguments explicit at the top of a method.

Do:

```ruby
def foo(opts={})
  x = opts[:x]
  y = opts[:y]
  other_stuff
  x * y
end
```

Don't:

```ruby
def foo(opts={})
  other_stuff
  opts[:x] * opts[:y]
end
```

When you look at the method, it should be immediately obvious what the option hash arguments are.

If they're still not obvious enough, document the options minimally in a comment.


# Ruby on Rails


## Avoid `default_scope` in Active Record.

It tends to cause confusing behavior.
