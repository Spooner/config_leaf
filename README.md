ConfigLeaf
==========

ConfigLeaf allows an object to be configured using a terse syntax
like Object#instance_eval, while not exposing the internals 
(protected/private methods and ivars) of the object!

If the user doesn't like to use this ConfigLeaf wrapping, then they
can just use normal #tap syntax by requesting a block parameter.

Installation
------------

Add this line to your application's Gemfile:

    gem 'config_leaf'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install config_leaf

Usage
-----

ConfigLeaf can be used directly by the end-user after an object has been created,
but it more likely used by those wanting to allow block setting of config after
their object has been instantiated:

    require 'config_leaf'
    
    class Cheese
      attr_accessor :value, :list
      
      def initialize(&block)
        # Set defaults.
        @value = 0
        @list = []

        # Allow the user access via ConfigLeaf syntax.
        ConfigLeaf.wrap object, &block if block_given?
      end

      def reverse!
        @list.reverse!
      end
      
      protected
      def explode!
        DeathStar.instance.boom!
      end
    end

    # User uses the ConfigLeaf block.
    object = Cheese.new do
      list [1, 2, 3]      # Calls object.list = [1, 2, 3]
      list << 4           # Calls object.list << 4
      value 5             # Calls object.value = 5
      value list.size     # Calls object.value = object.list.size
      reverse!            # Calls object.reverse!
      
      # These would be possible if using instance_eval, but not here.
      #explode!           # Not allowed because it is protected (raises an exception).
      #@value = 5         # Would set @value on the wrapper, not in the wrapped object, so has no effect.
    end

    # User chooses to not use ConfigLeaf block syntax by requesting a block parameter.
    object = Cheese.new do |c|
      c.list = [1, 2, 3]
      c.list << 4
      c.value = 5
      c.value = c.list.size
      c.reverse!
    end

Contributing
------------

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request