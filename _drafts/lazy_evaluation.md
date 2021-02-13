Lazy Evaluation
===

Defination
---

The style of only evaluating what's needed is called lazy evaluation, while the opposite (evaluating immediately) is called strict evaluation.

Benifits
---

1. Avoid evaluating the expressions which are not worth evaluating.
2. Can make smart choice, optimize the calculation process.
3. Take advantage of short circuiting,, makes the actual compiler simpler and easier to understand. With lazy compilers, ifs are not primitives (built into the compiler) but instead a part of the standard library of the language.
4. Can work with infinite lists. Only use parts of an infinite list will not crash the program.  
    ```python
    def addOne(n) = [n] + addOne(n + 1)
    list = addOne(1) # [1, 2, 3, 4, 5, 6, ...]

    oneToThree = list.takeFirst(3)
    print(oneToThree) // [1, 2, 3]
    ```

Notes
---
Lazy evaluation is performant (and correct) only when correctly mixed with *referential transparency* (referentially transparent function is a function that whenever it be called, it will return the same value). If the state of your variables is constantly changing, you can forget about memoization, and suddenly lazy evaluation becomes slower than doing it eagerly.

Implementation
---

1. It needs to use memoization. In other words, it needs to keep a dictionary where the key is the name of the variable, and the value is the result of the evaluation. When it encounters an already evaluated variable, it can simply look up the value in the dictionary.

Examples
---
1. lodash.js
2. Haskell(lazy by default)
3. Swift(list)
4. Perl(list)


参考资料
---
* [What is Lazy Evaluation? — Programming Word of the Day](https://medium.com/background-thread/what-is-lazy-evaluation-programming-word-of-the-day-8a6f4410053f)
* [Referential Transparency](https://programmingwords.com/home/referential-transparency)