---
path: ISP
date: 2020-03-19T13:06:32.625Z
title: Interface Segregation Principle reduces unnecessary coupling
description: A blog about the Interface Segregation Principle
---
I am building a Tic-Tac-Toe console game in Elixir.  I created a Board protocol consisting of functions associated with managing the Tic-Tac-Toe board.  I also created a Presenter module to display the board.  The Board protocol started with one function, get, used by the Presenter module.  As the application grew, I added other functions to the Board protocol.  Although the Presenter module implements only one Board Protocol function and this function did not change, I needed to update the PresenterTest module with each change to the Board protocol.  I hesitated changing the Board protocol because of all the changes needed in test modules implementing this protocol.  I also felt uncomfortable including unused functions in PresenterTest and other module tests. The code seemed less clear, redundant, and inflated. See code below.

```erlang
defprotocol Board do
  def get(board, position)
  def place_token(board, position, token)
  def available_positions(board)
  def game_over?(board)
end
  
defmodule PresenterTest do
  use ExUnit.Case

  defmodule BoardWithThreeX do
    defstruct [:position_value]

    defimpl Board do
      def get(board, position) do
        position_value = if Enum.member?([1, 2, 3], position), do: "X"
        %{board | position_value: position_value}
      end

      def place_token(_board, _position, _token) do
      end

      def available_positions(_board) do
      end

      def game_over?(_board) do
      end
    end
  end

  test "formats the board" do
    spaces = "   "

    expected_grid = """

     X | X | X 
    ---+---+---
    #{spaces}|#{spaces}|#{spaces}
    ---+---+---
    #{spaces}|#{spaces}|#{spaces}

    """

    assert Presenter.present(%BoardWithThreeX{}) == expected_grid
  end
end
```

At the time, I didn’t know I was violating the Interface Segregation Principle, the “I” in [SOLID](https://en.wikipedia.org/wiki/SOLID), first described by Robert C. Martin (Uncle Bob). See his [article](https://drive.google.com/file/d/0BwhCYaYDn8EgOTViYjJhYzMtMzYxMC00MzFjLWJjMzYtOGJiMDc5N2JkYmJi/view). The gist of the principle states, “Clients should not be forced to depend upon interfaces that they do not use”. In the example with the Presenter, although the PresenterTest uses one Board protocol function, we must change the PresenterTest with changes to any function in the Board protocol.  For example, adding a function to Board protocol necessitates a change in the PresenterTest even though the Presenter does not use this function.  These unnecessary functions in the PresenterTest produce unexpected side-effects and coupling, clouding the Presenter’s behaviors, and reducing the applications flexibility and maintainability. To comply with ISP, I split the Board protocol into targeted protocols. Now, modules depend only on protocols they use. See updated code below.

```erlang
defprotocol BoardUpdate do
  def place_token(board, position, token)
end

defprotocol BoardState do
  def available_positions(board)
end

defprotocol BoardInspect do
  def get(board, position)
end

defprotocol BoardOutcome do
  def game_over?(board)
end
  
defmodule PresenterTest do
  use ExUnit.Case

  defmodule BoardWithThreeX do
    defstruct [:position_value]

    defimpl BoardInspect do
      def get(board, position) do
        position_value = if Enum.member?([1, 2, 3], position), do: "X"
        %{board | position_value: position_value}
      end
    end
  end
end
```

Uncle Bob also references inheritance related ISP. See his [article](https://drive.google.com/file/d/0BwhCYaYDn8EgOTViYjJhYzMtMzYxMC00MzFjLWJjMzYtOGJiMDc5N2JkYmJi/view) for more details. He describes a Door superclass and TimedDoor subclass. The TimedDoor needs access to a TimerClient behavior. As a result, the Door superclass inherits from the TimerClient class. Now, the Door’s subclass, TimedDoor, can implement this behavior. However, all varieties of Door do not need the TimerClient, but must depend on TimerClient and implement its behaviors, else violate the [Liskov Substitution Principle](https://friendly-archimedes-6b3170.netlify.com/blog/liskov-substitution-principle-promotes-more-maintainable-applications/). He refers to this design as interface pollution. The Door superclass interface is polluted and “fat”. To solve this unnecessary coupling, he recommends creating a DoorTimer adapter object that carves out the TimerClient interface needed by TimedDoor, and delegates it to TimedDoor. Now TimedDoor depends directly on the adapter object, the only TimerClient interface it needs, and does not involve Door, reducing unneeded dependencies and coupling. Alternatively, he recommends a more elegant solution involving multiple inheritance. The TimedDoor inherits from both the Door and TimerClient superclasses. This design eliminates the adapter object.

Follow ISP and create well-defined, targeted interfaces. This approach reduces unnecessary coupling and dependencies, unintended side-effects, and makes applications more flexible and maintainable.
