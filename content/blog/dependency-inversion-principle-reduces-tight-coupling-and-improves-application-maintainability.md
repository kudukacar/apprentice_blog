---
path: DIP
date: 2020-03-21T14:46:45.456Z
title: Dependency Inversion Principle reduces coupling and improves maintainability
description: A blog about the Dependency Inversion Principle
---
Dependency inversion principle (DIP), the “D” in [SOLID](https://en.wikipedia.org/wiki/SOLID), states:

* High-level modules should not depend on low-level modules. Both should depend on abstractions.
* Abstractions should not depend on details (concrete implementations). Details should depend on abstractions.

The code below shows a dependency on a concrete class. Here, TicTacToe depends specifically on the HumanPlayer class. 

```ruby
class TicTacToe
  def play_turn
    player = HumanPlayer.new("X")
    player.selection
  end
end
```

On the other hand, the code below shows a dependency on an abstract player object. This object could be any class or module, but must respond to a particular message, selection. 

```ruby
class TicTacToe
  def play_turn(player)
    player.selection
  end
end
```

Depending on concrete classes leads to tight coupling. In the first example above, TicTacToe knows the name of the HumanPlayer class, and the arguments to initialize the class. A change in these properties forces a change to TicTacToe. By creating a HumanPlayer instance,  TicTacToe declares it only collaborates with HumanPlayer. The addition of a computer player requires a change to TicTacToe. This design leads to brittle, and inflexible applications.

On the other hand, following DIP by depending on abstractions reduces coupling, and promotes more flexible and maintainable applications. Instead of creating a new instance within a method, inject the dependency as an argument. In this pattern, known as dependency injection, an implementation of DIP, TicTacToe does not need to know about the particular class or the arguments needed to initialize the class.  TicTacToe only needs an object responding to selection. See example below, where TicTacToe initializes with a player object. This design decouples TicTacToe and HumanPlayer. Now, TicTacToe collaborates with any player object implementing selection, and does not depend on a particular concrete class.

```ruby
class TicTacToe
  attr_reader :player
  
  def initialize(player)
    @player = player
  end
  
  def play_turn
    player.selection
  end
end
```

Dependency injection allows for unit testing. When a method creates an instance of a class, the method relies on code in another class, and a test does not ensure the method’s objective. On the other hand, with dependency injection, an alternate object, for example, a fake, playing the same role as the real object, can be injected for testing. This pattern isolates the tested method. As a result, changes to the collaborating object’s class does not break the class’ tests.  See example code below.

```ruby
Rspec.describe TicTacToe do
  class FakePlayer
    def selection
      2
    end
  end

  describe "#play_turn" do
    it "plays the game" do
      player = FakePlayer.new
      
      expect(TicTacToe.new(player).play_turn).to eq(2)
    end
  end
end
```

This result might not be ideal in all situations, because a class’ tests pass even though the application does not work. For example, suppose the HumanPlayer class changes the selection method name to move. The TicTacToe test still passes, because the FakePlayer role did not change accordingly. To resolve this issue, the roles, FakePlayer, and HumanPlayer, rely on an abstraction, Player. Player describes the public methods, which FakePlayer and HumanPlayer reference. Any changes to methods occur in Player, and force changes in FakePlayer and HumanPlayer. Now all roles, FakePlayer, HumanPlayer, and TicTacToe rely on abstractions, as required by DIP.  Elixir protocols implement this abstraction. See example code below.

```ruby
defprotocol Player do
  def selection(player)
end
  
defmodule HumanPlayer do
  defstruct []
  
  defimpl Player do
    def selection(%HumanPlayer{}) do
      IO.gets("Please enter your selection")
    end
  end
end

defmodule TicTacToeTest do
  defmodule FakePlayer do
    defstruct []
    
    defimpl Player do
      def selection(%FakePlayer{}) do
        2
      end
    end
  end
  
  test "plays the game" do
    assert TicTacToe.play_turn(%{player: %FakePlayer{}}) == 2
  end
end
```

DIP prescribes modules depend on abstractions rather than directly on each other. This design reduces coupling between modules, and promotes application flexibility and maintainability.
