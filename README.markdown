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


# Ruby


## Empty lines on the class/module level.

I like to use empty lines like so:

```ruby
module Mod
  class Example
    include Foo
    extend Bar

    acts_as_baz

    def public
    end

    def another
    end

    private

    def private
    end

  end
end
```

Empty lines around each method so they stand apart. No empty lines between nested modules/classes. No empty line before `include`/`extend` as they are in a sense part of the class definition, much like `< SuperClass` when you inherit.


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

It makes the public/private boundary very obvious, and is similar to how `rescue` is indented in a method. But editor autoindentation doesn't tend to support this style, nor the teams I've worked on.


## Don't skip indent levels for alignment.

Do any of these:

```ruby
foo(
  bar,
  one: 1,
  two: 2
)

foo(
  bar,
  {
    one: 1,
    two: 2
  }
)
```

But don't do this:

```ruby
foo(bar, one: 1,
         two: 2)
```

Also, do any of these:

```ruby
foo =
  case x
  when 1: y
  when 2: z
  end

foo = value_for(x)
```

But don't do:

```ruby
foo = case x
      when 1: y
      when 2: z
      end
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

If they're still not obvious enough, document the options in a comment.


## Explicit `nil` branches can be fine.

This is controversial in my team. If you explicitly want a `nil` return value in some situation, I like this:

```ruby
def example_reader
  if item
    item.do_foo
    item.bar
  else
    nil
  end
end
```

Instead of this:

```ruby
def example_reader
  if item
    item.do_foo
    item.bar
  end
end
```

While it has the same effect, it suggests to me that we don't care about the return value without an item, and that the method just happens to return `nil` as a side effect.


## `do`/`end` vs. `{}`.

I prefer a mix of the two common styles.

I like the Weirich take if it's more than one line: if you use the return value, use braces; otherwise, use `do`/`end`.

But for a one-liner where I don't use the return value, like `foos.each { |x| x.do_it }`, I would use braces.


## Double-quote strings unless the string contains double quotes.

Single-quoted strings [aren't faster](http://stackoverflow.com/questions/1836467/is-there-a-performance-gain-in-using-single-quotes-vs-double-quotes-in-ruby). Double quotes means you don't need to change them if you add interpolation.

But don't escape inside a string (or regexp) if you don't need to. Change the quote style instead: `'like "this"'` or `%{'like' "this"}`.

`%{this}` is theoretically a better default, since you can include pretty much anything and never need to change the quote type, but it's more work to type and to read.


## Also:

In Ruby 1.9 hashes, prefer JSON style to hash rockets when possible.

Prefer `-> { }` stabby lambdas to `lambda {}` or `proc {}`.


# Ruby on Rails


## Avoid `default_scope` in Active Record.

It tends to cause confusing behavior.


## Order of things in an Active Record model.

I like things in this order:

```ruby
class Model < ActiveRecord::Base
  include Foo
  extend Bar

  # Start out with anything that changes what it is.
  acts_as_baz

  # Associations early since they define methods.
  belongs_to :bar
  has_one :foo

  # Validations. I think of these as especially common lifecycle callbacks.
  validates :name, presence: true

  # Other lifecycle. In the order that they would run.
  before_save :this
  before_destroy :that

  # Class methods before instance methods.
  def self.class_method
  end

  def instance_method
  end

end
```

## Rails i18n.

I use single-quoted symbol keys: `t(:'foo.bar')`. Symbols seem suitable as we symbolize a lookup key. The quotes are needed if the symbol contains a period. Single quotes look more lightweight than double.

Use full keys whenever possible, for easier search: `t(:'foo.bar.baz')` even if lazy lookup would let you do `t(:'.baz')`.

Avoid translated views/files as they're painful to send to translators. You can use translation keys inside views. If you have long texts, try combining `simple_format`, [Markdown](http://daringfireball.net/projects/markdown/) or some custom formatting with block literals:

```yaml
long_text: |
  Lorem ipsum dolor sit amet.

  Consectetur adipisicing elit.
```

Don't forget the `|` or YAML will fold your newlines.

If parts of your app don't need translation, or not to as many languages, just use multiple `.yml` files. You can send some to translators and run tests against them to ensure they match up, while other parts can be treated differently.

Don't include markup in the translations. Instead, group translation parts under one key to help the translator:

```yaml
log_in_or_sign_up:
  text: "%{log_in} or %{sign_up} to do stuff."
  log_in: "Log in"
  sign_up: "Sign up"
```

```ruby
t(
  :'log_in_or_sign_up.text',
  log_in: link_to(t(:'log_in_or_sign_up.log_in'), login_path),
  sign_up: link_to(t(:'log_in_or_sign_up.sign_up'), signup_path)
)
```

I organize keys something like this and try to be consistent about things like `controller.action.title`.

```yaml
# FoosController
foos:
  # action
  show:
    title: "Foos"
  create:
    success: "Foo created."
    failure: "Foo could not be created."
mailers:
  # FooMailer
  foo:
    # action
    arrived:
      subject: "Your foo just arrived"
      body: |
        Come get your foo!
```

Beware of Finnish and other highly inflected languages. You can usually make small tweaks to avoid inflection, e.g. "From: Stockholm" instead of "From Stockholm" to avoid the ablative case.
