# Style guide

The idea of this repo is to make my coding style an explicit, considered and versioned thing.

I interpret "style" loosely to include a bit of everything from syntax to architecture and process.

It's public in case it interests or helps someone else, and so that points can be discussed. Obviously much of this is down to taste, but feel free to [tell me](http://twitter.com/henrik) when you disagree and why. Or fork it.

I'm trying to avoid repeating the established stuff from other style guides (two spaces for indentation, ASCII or UTF-8 etc) to instead focus on the less obvious or more contentious.

## Other style guides
* [bbatsov's "Ruby Style Guide"](https://github.com/bbatsov/ruby-style-guide)
* [GitHub's version](https://github.com/styleguide/ruby)


# Design

There are of course a ton of principles, but these are some I feel I've learned something from and try to apply in my day to day work.

* DRY: don't repeat yourself. Each piece of knowledge in the system should be in one place and not duplicated. This can be data or domain logic.

* The Law of Demeter. Don't tie one object to the innards of another. Have them interact through public interfaces that are as shallow and as stable as possible.


# HTML

* Always use `alt` attributes for images, but set an empty value (`<img src="foo.jpg" alt="">`) if an image-less version of the site would get by fine without that image.

  So a decorational image should have an empty value. If you have both an image logo and the company name in text, the image logo should probably have an empty value.


# CSS

* Attempt to capture the intent of a style in the selector. Prefer e.g. `.user_name` to `.user > p > span`.

  Makes things more maintainable and readable.

  Further reading: ["Shoot to kill; CSS selector intent" by Harry Roberts (@csswizardry)](http://csswizardry.com/2012/07/shoot-to-kill-css-selector-intent/)


# JavaScript

* Prefer CoffeeScript. It's much more fun.

* End statements with semicolons. It's idiomatic and causes less surprises.

*   Separate `var` statements are easier to maintain. Do this:

    ```js
    var one = 1;
    var two = 2;
    ```

    Don't do some version of:

    ```js
    var one = 1,
        two = 2;
    ```

    Further reading: [Blog post by Ben Alman](http://benalman.com/news/2012/05/multiple-var-statements-javascript/)


# Ruby


## Whitespace

*   This is how I like my empty lines at the class/module level:

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

    Avoid empty lines when the indentation level changes (e.g. between the end of the last function and the end of the class). Use empty lines to separate (groups of) things at the same level of indentation.

    This strikes a good balance between compact and readable.


*   Indent `private` like any class method:

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

*   Don't skip indent levels for alignment. Do any of these:

    ```ruby
    foo(bar, one: 1, two: 2)

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

*   Also, do any of these:

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

*   Strip trailing whitespace, including on blank lines. Many programmers will have their editor display invisible characters so they can complain about trailing whitespace.


## Comments

*   Avoid them. Before writing a comment, see if you can rename or extract so the comment is no longer needed.

*   Comments should usually end with punctuation.

*   Use `# NOTE: Foo.` for especially important comments as Vim (and others?) will highlight it.

*   Two spaces before comments at the end of a line to make them more visually separate:

    ```ruby
    some_code  # Some comment.
    ```

*   A comment that helps you use the interface goes outside:

    ```ruby
    # Arguments must be so and so.
    def method(x, y)
    end
    ```

*   A comment that relates to implementation, not interface or usage, goes inside:

    ```ruby
    def method(x, y)
      # We did it this way because of blurk.
      doing_it_this_way
    end
    ```

*   Don't check in commented-out code. It tends to become dead, forgotten code. Feel free to comment out code in development, but when you push, it's either in or out. You can retrieve it from version control history later if you want it back.

*   Similarly, avoid checking in `TODO` comments unless you're likely to address them soon, or they are likely to become dead and forgotten. I think `FIXME` comments are more likely to stay relevant, assuming they document known issues or shortcuts taken.


## READMEs

*   When possible, do [README driven development](http://tom.preston-werner.com/2010/08/23/readme-driven-development.html). It's like test-first but even higher-level, and forces you to think about your API as a whole before putting cursor to buffer.

*   I like this order:

      1. Short summary
      2. Usage
      3. Installation
      4. Gotchas, todos etc
      5. License


## Testing

*   Work test-first. Don't write a line of code without having a failing test. This is what tests that your test works, and ensures all your code is tested.

    In practice, I do this less for simple view logic and super-simple methods. Though when I do write tests for super-simple methods, it's not uncommon that they catch a super-simple mistake.

*   Test outside-in. E.g. start with one specification in a request test. Create a route, controller, action and model class only as the test calls for them. When it calls for a model method, create a model unit test and add that method test-first, then go back to the request test when it passes.

*   Write request tests instead of controller and view tests.

*   Do write controller tests if there's enough controller-level code to test that it's worth it.

*   Prefer unit testing some helper, model or presenter to testing variations of a view in a request test.

*   Stubbing and mocking is more acceptable the smaller, more reliable and more shallow the interface is.

    When you test class X, it's fine to stub a few methods on class Y, but make it a small number of stable methods that are not intimately tied to the Y internals. Instead of stubbing `Y#first_name` and `Y#last_name`, perhaps you should have the interface `Y#name` or even `Y#name?`.

    And definitely avoid stubbing something like `y.name.first_name`. [The Law of Demeter](http://en.wikipedia.org/wiki/Law_of_Demeter) is very applicable to mocking and stubbing.

    An interface that makes for less fragile tests is also likely to make for less fragile code.

*   Whenever you stub and mock, make sure there is some other, probably higher-level test that will break if the mocked-out interface changes.


## Miscellaneous

* Assignment in a condition is fine without parentheses:

    ```ruby
    if foo = "bar"
    ```

    It's a convenient pattern, not hard to tell from an equality check, and your tests will catch it if you mix the two up.

* Make option hash arguments explicit at the top of a method:

    ```ruby
    def foo(opts={})
      x = opts[:x]
      y = opts[:y]
      other_stuff
      x * y
    end
    ```

    Don't do:

    ```ruby
    def foo(opts={})
      other_stuff
      opts[:x] * opts[:y]
    end
    ```

    When you look at the method, it should be immediately obvious what the option hash arguments are.

    If they're still not obvious enough, document the options in a comment.

* Explicit `nil` branches can be fine.

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

*   For multiline blocks, use `{}` if you use the return value and `do`/`end` otherwise:

    ```ruby
    foos.map { |x|
      x.name
    }

    foos.each do |x|
      puts x.name
    end
    ```

    For one-liners, brackets are always fine: `foos.each { |x| x.do_it }`.

*   Double-quote strings unless the string contains double quotes.

    Single-quoted strings [aren't faster](http://stackoverflow.com/questions/1836467/is-there-a-performance-gain-in-using-single-quotes-vs-double-quotes-in-ruby). Double quotes means you don't need to change them if you add interpolation.

    But don't escape quotes inside a string if you don't need to. Change the quote style instead: `'like "this"'` or `%{'like' "this"}`.

    `%{this}` is theoretically a better default, since you can include pretty much anything and never need to change the quote type, but it's more work to type and to read.

*   Use `%r{}` for regular expressions containing slashes, so you don't have to escape them.

*   In Ruby 1.9 hashes, prefer `{ json: :style }` to `{ :hash => :rockets }` when possible.

*   In Ruby 1.9, prefer `-> { }` stabby lambdas to `lambda {}` or `proc {}`.

*   Don't use

    ```ruby
    class << self
      def foo
      end
    end
    ```

    to define class methods when

    ```ruby
    def self.foo
    end
    ```

    will do. It makes the method definitions harder to search for.

*   Try to keep lines of code to a maximum of around 80 characters for nicer editing in a split editor (and nicer diffs). Lines of text in a README, say, can be longer since your editor should soft-wrap it. I tend to hard-wrap comments, though, because they feel like code.

*   Don't use `begin`/`end` with `rescue` when it spans an entire method. It's more clutter and the cleaner syntax is well-established:

    ```ruby
    def foo
      do_stuff
    rescue
      bar
    end
    ```


## Ruby on Rails


### Controllers

*   Don't cargo cult flash messages. If it's obvious what just happened, don't restate it.

*   Feel free to use the flash for more than copy, such as analytics events and names of partials for complex messages.


### Models/Active Record

*   Avoid `default_scope`. It tends to cause confusing behavior.

*   Don't use SQL outside models. Encapsulate it. Don't do `Item.where(published: true)`; do `Item.published`.

*   This is how I like to order things in an Active Record model:

    ```ruby
    class Model < ActiveRecord::Base
      include Foo
      extend Bar

      # Start out with anything that changes what it is.
      acts_as_baz

      # Associations early since you might want to validate them.
      belongs_to :bar
      has_one :foo

      # Validations. I think of these as especially common lifecycle callbacks.
      validates :name, presence: true

      # Other lifecycle. In the order that they would run.
      before_save :this
      before_destroy :that

      # Scopes are class method macros, so put them before class methods.
      scope :slippery, where(slippery: true)

      # Class methods before instance methods.
      def self.class_method
      end

      def instance_method
      end
    end
    ```


### Helpers

*   Use `config.action_controller.include_all_helpers = false` so helper methods don't leak all over the place.

    When you do need to share helpers, put them in the helper of a shared superclass controller, or `include` the file in the helper for your current controller.


### Mailers

*   Feel free to use one mailer class per mail action if they're not trivial. This way, you can treat them in an OOP fashion with something like an initializer and private methods.

    ```ruby
    class OrderCreatedMailer < ApplicationMailer
      def build(order)
        # …
      end

      private

      def stuff
        # …
      end
    end

    OrderCreatedMailer.build(order).deliver
    ```

### I18n

*   I use single-quoted symbol keys: `t(:'foo.bar')`. Symbols seem suitable as we symbolize a lookup key. The quotes are needed if the symbol contains a period. Single quotes look more lightweight than double.
