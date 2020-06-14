---
layout: single
title: "Clean Code: A Handbook of Agile Software Craftsmanship"
author: "Danni"
toc: true
toc_sticky: true
tags:
  - Language
---

Finally had the time to ~~almost~~ finish this book ;-)

# Naming

- Use descriptive and unambiguous names; 
- Avoid misunderstanding; (e.g. Use `accountList` for a list of accounts unless it is the real list data type, otherwise `accounts` or `AccountGroup` would be better);
- Use meaningful distinction; (e.g. Usually do not use `a` or `the` for variable prefix since it is hard to distinguish what it actually means)
- Use names that can be pronounced;
- Use searchable names => easier to adjust during debugging & cr stage;
- Be consistent;
- Avoid encodings:
    - do not append prefix/postfix like `strings` or `str`, the compiler can distinguish them itself;
- Replace [magic numbers](https://en.wikipedia.org/wiki/Magic_number_(programming)) with named constants;

# Function

- **small**;
- Do **ONE** thing;
- Use **descriptive** name;
- Arguments:
    - have fewer arguments => functions & arguments are on different abstract levels;
    - avoid passing `Boolean` as input;
    - If a function has arguments but no output, it should be an event, otherwise must have return;
- No side effects;
- Use exception instead of error;
- Goal: Eliminate duplicate functions;

# Comments

- **NOTE**: some comments might be outdated, or just simply wrong;
- Do not comment on ill-formatted function, re-construct functions instead;
- Dos:
    - Alert
    - Use `// TODO` if necessary;
    - ALways try to explain the code;
- Don'ts:
    - Don't be redundant.
    - Don't add obvious noise.
    - Don't use closing brace comments.
    - Don't comment out code;

# Source code structure
- Shorter file is easier to understand;
- Use identation, even when the function only has one-line statement/empty;
- Declare variables near their usage;
- Vertically distance:
    - Do not place similar concepts in different folder unless for a very good reason;
    - the control variable in the loop should always be decalred within the statement;
    - Keep functions with similar usage close;
    - The function that is calling should always be placed on top of the called function;
- Source code should be clear, and well-structured; Its name shows it's in correct module; At the beginning of the file, it should display the high-level concept & algorithm, then details in following sections; 
- Follow the team rule;
- Boundaries:
    - Hide third-party APIs;
        - once third-party package changes, easier to change our own codebase;
        - consistant code style, easier to read;
    - Write tests for their-party APIs:
        - Learning test: Faster to understadn its usage;
        - Efficient way to know if the API function changes;

# Object & Data Structures

- Avoid hybrid structures => half object half data;
- Only do **one** thing;
- Do not put public accesser & function with change operations together;
- If we want to frequently add functions instead of new objects, we should use **Procedure oriented** programming method => only adds in one place;
- If we want to frequently add data objects, use **OOP** => does not change other data code;
- Hide internal implementation/structures;
- Prefer non-static methods;
- Better to implement many functions than passing many arguments into one function to select a behaviour;

# Error handeling

- Use exceptions instead of code;
- Use **unchecked exceptions** => does not require try/catch or throw to compile => simplify the codes;
- Add exception statement => Why this failed? Because the default exception only provides stack trace; If the system has log system, note it;
- Do not return `NULL` => If returned, we need to constantly check the `NULL` value, thus prone to `NullPointerException`;
- Do not pass `NULL`;
- Define **Special Case Pattern**;

# Tests

- Keep the testing code clean like the production code; But different standard => Usually production code aims for performance but the testing code does not;
- aim for higher test coverage;
- Readability is important;
- **TDD**: First create test data, then test the ata, then verify the result;
- **FIRST** rule: F(fast), I(Independent), R(Repeatable), S(Self-sufficient), T(Timely);

# Code smells

> When to change the code? When the code has bad smeels;

Here's a list:

## Comment

- Unwanted information => e.g. change history;
- Commented out code; => Just delete it;
- Comment that is too obvious => comment should have information the code does not offer;
- Outdated comment;

## Environment

- How many steps to finish the structure => should be seperated into single oprations; 
- How many steps to complete the tests;

## Functions

- Dead function => never called;
- Over-complicated;
- Too many arguments;
- Return arguments;
- Identification parameters;

## Parameters

- Does not follow standard naming principles;
- Use all kinds of prefix/postfix;
- Does not explain what it is used for;
- ...

## Testing

- Not enough coverage;
- No coverage tools;
- Neglect small tests;
- Too slow;

## General

1. Rogidity: difficult to change. A small change causes a cascade of subsequent changes;
2. Fragility: breaks in many places due to a single change;
3. Immobility: cannot reuse parts of the code in other projects because of involved risks and high effort;
4. Needless Complexity;
5. Needless Repetition;
6. Opacity: hard to understand the code;