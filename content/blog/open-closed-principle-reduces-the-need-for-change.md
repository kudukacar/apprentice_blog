---
path: OCP
date: 2020-03-15T21:47:57.941Z
title: Open-Closed Principle reduces the need for change
description: A blog about the Open-Closed Principle
---
Bertrand Meyer described the Open-Closed Principle (OCP), the "O" in SOLID, as “Software entities should be open for extension, but closed for modification”.  He used inheritance, now viewed as a coupled and complex design pattern, to achieve this principle.  Robert C. Martin later defined this principle as, “You should be able to extend the behavior of a system without having to modify that system.”  He used polymorphism, now viewed as a flexible and maintainable design pattern, to achieve this aim.  Polymorphism refers to different objects' ability to respond to the same message.  

In a Tic-Tac-Toe application I recently built, I have two human players, capture input regarding a position on the board from each player, and cycle through players until the game is over.  I initialize the TicTacToe class with the players.  The simplified code looks as follows:

```ruby
class HumanPlayer
  def selection(board)
    loop do
      puts "Please choose a position for your token"
      gets.strip.to_i
    end
  end
end

class TicTacToe
  def initialize(players)
    @players = players
  end
  
  def play_game
    players.cycle do |player|
      player.selection(board)
      break unless in_progress?
    end
  end
end
```

Requirements change, and the client now wants a game involving a human and a beatable computer player.  I add a DumbComputerPlayer class, I modify the play_game method, and add a conditional to check the type of player.  I update the code as follows: 

```ruby
class DumbComputerPlayer
  def random_move(board)
    available_positions = (1..9).to_a.filter { |position| board.is_available?(position) }
    available_positions.sample
  end
end

class TicTacToe
  def initialize(players)
    @players = players
  end
  
  def play_game
    players.cycle do |player|
      if player.class == HumanPlayer
        player.selection(board)
      else
        player.random_move(board)
      end
      break unless in_progress?
    end
  end
end
```

Requirements change again, and the client wants an unbeatable computer player.  Again, I have to modify the conditional statement to account for this change.  

If we design the application following OCP using polymorphism, we don’t need to modify the code every time requirements change.  Rather, we extend the code by adding a new class, and within this class we include a method of the same name as the method in the existing class.  For example, when we add a beatable computer player, we create a DumbComputerPlayer class with a "selection" method to determine the computer player’s random move.  We extend the application by creating a new class, and use polymorphism by adding a method of the same name.  Now, the DumbComputerPlayer and the HumanPlayer respond to the same message, although they have different implementations of this method.  Instead of passing two HumanPlayer instances as arguments to the TicTacToe initialize method, we pass HumanPlayer and DumbComputerPlayer instances.   No need to modify the code.  If requirements change again, and we have to build an unbeatable computer player, we create a SmartComputerPlayer class with a selection method, and pass the SmartComputerPlayer instance into the initialize method.  Polymorphism in action: three different player objects responding to the same message.  See OCP using polymorphism code below. 

```ruby
class HumanPlayer
  def selection(board)
    loop do
      puts "Please choose a position for your token"
      gets.strip.to_i
    end
  end
end

class DumbComputerPlayer
  def selection(board)
    available_positions = (1..9).to_a.filter { |position| board.is_available?(position) }
    available_positions.sample
  end
end

class TicTacToe
  def initialize(players)
    @players = players
  end
  
  def play_game
    players.cycle do |player|
      player.selection(board)
      break unless in_progress?
    end
  end
end
```

OCP using polymorphism reduces the need for code changes, and unexpected behavior regarding these changes.  Interfaces in Java, and protocols in Elixir are mechanisms to implement polymorphism.  Stay tuned for another blog about protocols in Elixir.
