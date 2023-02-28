# Learning Hour: Introduction to Approval Testing (Legacy code context)

_Reminder: This learning hour is screen recording friendly. If one of the team members can't join,
you can record it, and they'll be able to watch later._

When you're dealing with legacy code, it may be daunting to get it under the test. And as we know,
the code without appropriate test suite is very difficult to refactor, improve or change.

Approval testing comes in to rescue the day!

Reading: (10 min) _(& asking any questions or clarification)_

## How it works?

1. You create a Printer that prints all the possible outputs of the code under the test.
   This can be simply taking a bunch of objects and producing a somewhat structured text output, or it can
   be as complex as reading the database state (many tables) and outputting this information in CSV or JSON
   format.
2. Use approval testing framework (or you can build your own with simple assertions comparing output with
   the prepared expected output file). Approval frameworks make it easier to work with.
3. Write a new test that sets up the system under the test (Arrange), then exercises certain code path (Act),
   and then finally produces the output with your printer and call approval framework to verify this output
   against the approved version.
4. Initially, there would be no approved version, so this test will always fail.
5. When you're happy with the output produced, you can instruct the approval framework to record this output
   as approved.
6. Next time you make any changes that change the output, the approval framework will show you the difference
   between the current version and the approved one. If you're happy with the changes, then you approve them.
7. You add more approval tests like this for the system under the test until you're happy with the test
   coverage.

## Obstacles

The main major obstacle is that the code should be designed with approval testing in mind. For example, if
your output changes order every time you run it, the approval framework will show failures all the time.

Additionally, if there are any "random" or "auto-generated" pieces (like random values, dates & times, and
incremental or generated IDs) then this would also pose issues for the approval testing.

The solution is to change your software design and test setup to get these concepts under the control and
ensure that every test run the same values will be produced in predictable fashion. For this, you may
consider the following solutions:

- All sources of current date & time should be refactored to special Service/Gateway class or module, that 
  is then provided as dependency for the modules that need to generate these. The test suite should provide 
  a stub for these.
- All sources of random or generated values should be refactored in the same way.
- All sources of cryptography should be refactored in the same way.
- If you have to integrate with the database in your tests, and you delegate the generation of IDs to the
  database, then you can consider the following options:
  - Move the generation responsibility to your domain code instead of the database (if possible or feasible).
  - Truncate the tables and reset the ID sequences, so that they start from the fixed number again.
- If you have to integrate with the DB, and you delegate the generation of timestamps (createdAt/updatedAt):
  - Consider moving the responsibility of generating these to your Repository/Collection/ORM layer instead,
    so that you can provide a dependency for it, or power-mock it in your tests.

If you applied all possible solutions above, and you still have non-deterministic values that you can't take
control of, then you can exclude them from the approval testing using the following approaches:

- Just before verifying that the output is as approved, you can apply a regex replacement to mask problematic
  values.
- In the printer itself, you can have it configured to ignore certain fields that are not predictable.
- Additionally, before you give values and objects to the printer, you can remove or replace problematic fields.

## Coding Kata (Ensemble)

(45 min)

[SupermarketReceipt Refactoring Kata](https://github.com/emilybache/SupermarketReceipt-Refactoring-Kata/tree/main/typescript)

- Spend a few minutes reading the [description of the problem](https://github.com/emilybache/SupermarketReceipt-Refactoring-Kata#the-supermarket-receipt-refactoring-kata)
- Practice creating the approval tests in the ensemble

Use the code sharing tool of your choice (e.g., JB Code-With-Me or VS Code LiveShare).

Ensemble rules:

1. Driver rotates every 5 min 
2. Ensemble serves as a collective navigator 
3. Coach serves as a facilitator, keeping the ensemble productive

## Reflection

(5 min)

1. What did you learn today?
2. What would you do differently starting tomorrow?
3. What else do you want to learn deeper now (that you weren't aware before)?
