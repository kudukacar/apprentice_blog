---
title: Test-Driven Development works!
date: "2020-02-27T23:46:37.121Z"
---

I previously thought of the Test-Driven Development (TDD) process as counterintuitive. After all, how could you write tests before writing code? The tests verify the behavior of your code, so naturally the code comes first. I didn’t have much experience writing tests, but in the past, I wrote entire applications or features before writing the first test. Sometimes, I had difficulty writing the tests, and redesigned the code to make it testable. Hence, the benefit of TDD.

TDD prescribes the Red, Green, Refactor process:

- Red: write only enough of a unit test to fail
- Green: write only enough production code to make the failing unit test pass
- Refactor: remove duplication and make the code more readable

I practiced this process repeatedly with the [Bowling Game Kata](http://butunclebob.com/ArticleS.UncleBob.TheBowlingGameKata). Targeting one scenario at a time, I wrote each test, then wrote the corresponding code to satisfy the test. I wrote only enough code to make each test pass, and incrementally modified the design. Instead of a one off design, the result was a testable, simple, and working design refined through scenario testing.

I followed this process when I built a console Tic-Tac-Toe game. In the past when I built apps, I produced a lump of code, and then spent the bulk of my time debugging. This time, I wrote the tests, and associated code incrementally, and debugged targeted portions quickly. Also, I thought through the design first before writing a test, and if I had difficulty writing a test, I redesigned until I could write the test. With this early preparation and thought, I added subsequent features with minimal side effects, and changes to the existing tests and code.

TDD forces you to prepare, think ahead, and incrementally cover all scenarios. I equate coding without TDD to painting a room without preparing. You start painting quickly, but have greater clean up time, and in the long run, a deficient product, as the paint peels earlier. On the other hand, preparing the room by cleaning, taping, and priming produces a better product, and saves time in the long run. TDD, although requiring some upfront effort, produces a better designed product, and saves time in the long run. I am a new proponent and fan.
