---
path: SRP
date: 2020-03-09T10:26:49.502Z
title: Single responsibility makes change more palatable
description: A blog about the Single Responsibility Principle
---


I have a difficult time following the single responsibility principle (SRP), the “S” in [SOLID](https://en.wikipedia.org/wiki/SOLID) design. I think of classes as large objects, holding many methods, and doing many things. For example, on a recent project building a Tic-Tac-Toe game, I created a Validator class to validate user input. I thought of the Validator as a general validator, validating all types of user input. The class included methods to validate a user entered board position, and game play option. I also needed collaborators for the various methods, and included them in the initializer. If the need for another user input arises, I add another method and collaborators to the Validator. Soon, the class grows large, dependencies become less obvious, and any change requires dedicated effort. As Sandi Metz describes in “Practical Object-Oriented Design In Ruby”, change is unavoidable, and the need for change makes design matter.

The Validator class with too many responsibilities:

```ruby
class Validator
  def validate_board_position(selection, board)
    return "Selection taken" unless board.is_available?(selection)
  end
  
  def validate_game_options(selection, options)
    return "Invalid entry" unless selection.between?(1, options)
  end
end
```

SRP states a class does the smallest possible useful thing, and everything in a class relates to its central purpose. SRP doesn’t require a class to do one very narrow thing, or change for only a single reason. Although simple in concept, the components of a class’ central purpose is subjective. Hidden responsibilities become more obvious as the application grows, and you duplicate responsibilities, and associated code in other classes. For example, in Tic-Tac-Toe, I used a parse_input method within the Player class to parse the user’s board selection. As the application grew, I garnered user input regarding game play options. Again, I needed a parse_input method. I realized this responsibility was hiding among other responsibilities when I duplicated the code. I separated the parse input method into a class of its own, and inserted this class as needed. As Sandi Metz states, easy to change applications consist of easy to reuse classes. Reusable classes are pluggable units of well-defined behavior with few entanglements. An easy to change application is like a box of building blocks. Select the pieces you need, and assemble them in unanticipated ways.

The ParseInput class with no entanglements and well-defined behavior:

```ruby
class ParseInput
  def to_integer(input)
    Integer(input)
  rescue
    0
  end
end
```



SRP also applies to methods. For example, suppose you have a ComputerPlayer selection method determining all available positions on the board, and then taking a sample of the available positions to make a selection. This method could be split into two methods, the selection method to make a selection from available positions, and the available_positions method to determine all available positions. 

ComputerPlayer selection method before and after SRP refactoring:

```ruby
class ComputerPlayer
  def selection
    available_positions = (1..9).to_a.filter { |position| board.is_available?(position) }
    available_positions.sample
  end
end

class ComputerPlayer
  def selection
    available_positions.sample
  end
  
  def available_positions
    available_positions = (1..9).to_a.filter { |position| board.is_available?(position) }
  end
end
```

Methods with a single responsibility have the following benefits:

* A more readable and maintainable class because the class’ methods accurately describe their purpose
* Less or no comments
* Configurable classes where you can move methods in and out more easily



A few tips on determining if a class has a single responsibility:

* Ask each method in the class a question about its purpose, and see if the question makes sense. For example, with a Player class, and a selection method, ask, Please Mr. Player, what is your selection? This question makes sense. To the validation method, please Mr. Player, is your selection valid? This question makes less sense, indicating this method belongs elsewhere.
* Try to describe the class in one sentence. If you use “and” or “or” then you may have more than one responsibility.



A few constraints from Sandi Metz to keep your classes or methods small, and hopefully, single purposed:

* Classes can be no longer than 100 lines of code
* Methods can be no longer than five lines of code
* Pass no more than four parameters into a method



I started the Tic-Tac-Toe project building large, bulky classes, but by the end of the project, I had small classes, many with only one public facing method, easy to plug and play.
