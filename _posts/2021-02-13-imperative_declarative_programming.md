---
title: 'Imperative and Declarative Programming'
categories: ['编程语言']
tags: ['programming languages', 'programming paradigms']
resource_path: /blog/assets/2021/02/13/loaders_plugins
---

Imperative and Declarative Programming
===

Programming Paradigms
---

Programming paradigms are a way to classify programming languages based on their features. Some languages are designed to support one paradigm, while other programming languages support multiple paradigms(the trend of times).

The classification in different articles is slightly different, I take the one written in [wiki][1]. **Common** (not all) programming paradigms include:

* **imperative** in which the programmer instructs the machine how to change its state
  * **procedural** which groups instructions into procedures
  * **object-oriented** which groups instructions with the part of the state they operate on
* **declarative** in which the programmer merely declares properties of the desired result, but not how to compute it
  * **functional** in which the desired result is declared as the value of a series of function applications
  * **logic** in which the desired result is declared as the answer to a question about a system of facts and rules
  * **mathematical** in which the desired result is declared as the solution of an optimization problem
  * **reactive** in which the desired result is declared with data streams and the propagation of change

Simple Definition
---

* In Imperative Programming, you write down **HOW** to do it, **HOW** it should be done.
* In Declarative Programming, you write down **WHAT** you want, **WHAT** needs to be done.

Examples
---

1. You arrive at a restaurant, approach the front desk and say…

    * An **imperative** approach (HOW): I see that table located under the Gone Fishin’ sign is empty. My husband and I are going to walk over there and sit dow  
    * A **declarative** approach (WHAT): Table for two, please.

2. you asked a question: “I’m by Wal-Mart. How do I get to your house from here?"

    * An **imperative** response: Go out of the north exit of the parking lot and take a left. Get on I-15 south until you get to the Bangerter Highway exit. Take a right off the exit like you’re going to Ikea. Go straight and take a right at the first light. Continue through the next light then take your next left. My house is #298.
    * A **declarative** response: My address is 298 West Immutable Alley, Draper Utah 84020

3. Write a function called double which takes in an array of numbers and returns a new array after doubling every item in that array. *double([1,2,3]) -> [2,4,6]*

    * An **imperative** solution:
        ```js
        function double (arr) {
            let results = []
            for (let i = 0; i < arr.length; i++){
                results.push(arr[i] * 2)
            }
            return results
        }
        ```
    * A **declarative** solution:
        ```js
        function double (arr) {
            return arr.map((item) => item * 2)
        }
        ```
4. Using jQuery (or vanilla JavaScript), add a click event handler to the element which has an id of “btn”. When clicked, toggle (add or remove) the “highlight” class as well as change the text to “Add Highlight” or “Remove Highlight” depending on the current state of the element..

    * An **imperative** solution: jQuery
      ```js
        $("#btn").click(function() {
        $(this).toggleClass("highlight")
        $(this).text() === 'Add Highlight'
            ? $(this).text('Remove Highlight')
            : $(this).text('Add Highlight')
        })
        ```

    * A **declarative** solution: React.js
        ```jsx
        <Btn 
        onToggleHighlight={this.handleToggleHighlight}
        highlight={this.state.highlight}> 
            {this.state.buttonText}
        </Btn>
        ```
5. A more complex example<sup>[\[3\]][3]</sup>:
    * An **imperative** opproach:
        ```js
        const numbers = [1, 2, 3, 4, 5, 6];
        result = 0;
        for (let i = 0; i< numbers.length; i++>) {
            if(numbers[i % 2 === 0]) {
                result += numbers[i] * 2;
            }
        }
        ```
    * A **declarative** approach:
        ```js
        const numbers = [1, 2, 3, 4, 5, 6];
        const result = numbers
            .filter(i => i % 2 === 0)
            .map(i => i * 2)
            .reduce((acc, next) => acc + next);
        ```

    * An even more **declarative** approach:
        ```js
        const numbers = [1, 2, 3, 4, 5, 6];
        const isPair = i => i % 2 === 0;
        const double = i => i * 2;
        const sum = (a, b) => a + b;
        const result = numbers.filter(isPair).map(double).reduce(sum);
        ```

Relations between Imperative and Declarative Programming
---

* **many declarative approaches have some sort of imperative abstraction layer.** Computers don’t understand declarative code, declarative programming still uses imperative programming under the hood. 

  The declarative response to the restaurant employee is assuming that the employee knows all the imperative steps to get us to the table. Knowing the address assumes you have some sort of GPS that knows the imperative steps of how to get to your house. 
* **the line between declarative and non-declarative at whether you can trace the code as it runs.** Regex is 100% declarative, as it’s untraceable while the pattern is being executed.

Features of Imperative Programming
---

* Describing **HOW** to do something. You need to list out the steps.
* **Mutating some piece of state.** *State* is the information about something held in memory, which should sound a lot like variables, such as the ```results``` variable in the third example.
* **Not very readale.** your brain needs to step through the code just as an interpreter would while also taking into account the context in which the code lives.
* Conforming to the **operational model** of the machine.
* **High complexity**
* Can exist only one possible set of statements that can express each specific modular semantic.

Features of Declarative Programming
---

* Describing **WHAT** we want to happen. With declarative programming, you’re describing what you’re trying to achieve, without instructing how to do it. 
* **Context-independent.** This means that because your code is concerned with what the ultimate goal is — rather than the steps it takes to accomplish that goal — the same code can be used in different programs, and work just fine.
* **Readable.** Your code is more predictable. By looking at our Btn component in the fourth example, you are able to easily understand what the UI is going to look like.
* Conforming to the **mental model** of the developer. You constantly have this process of mapping your code to its meaning, in your mind,
* **Low complexity**. Less code, less bug.
* Semantics are inconsistent under composition and/or can be expresssed with variations of sets of statements.
* In computer science, declarative programming is a programming paradigm that expresses the logic of a computation without describing its control flow.


[1]: https://en.wikipedia.org/wiki/Programming_paradigm "programming paradigm - wiki"
[2]: https://medium.com/free-code-camp/imperative-vs-declarative-programming-283e96bf8aea "Imperative vs Declarative Programming" 
[3]: https://medium.com/@Rewieer/imperative-and-declarative-programming-e04b48887ab6 "Imperative and Declarative Programming"
[4]: https://www.geeksforgeeks.org/introduction-of-programming-paradigms/ "Introduction of Programming Paradigms"

References
---
1. [programming paradigm - wiki](https://en.wikipedia.org/wiki/Programming_paradigm)  
2. [Imperative vs Declarative Programming - Tyler McGinnis](https://medium.com/free-code-camp/imperative-vs-declarative-programming-283e96bf8aea)
3. [Imperative and Declarative Programming - Anthony Cyrille](https://medium.com/@Rewieer/imperative-and-declarative-programming-e04b48887ab6)
4. [Introduction of Programming Paradigms - geeksforgeeks](https://www.geeksforgeeks.org/introduction-of-programming-paradigms/)