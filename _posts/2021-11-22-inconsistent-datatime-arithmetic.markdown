---
layout: post
title:  "Inconsistent DateTime arithmetic"
date:   2021-11-22 20:00:00 +0000
categories: [PHP, Datetime]
tags: [php, datetime]
---

There is some inconsistency in date/time operations. Example presented in this post is written in PHP language, but it also exists in Java, C# and probably more languages.

This post was inspired by book [*Software Mistakes and Tradeoffs by Tomasz Lelek and Jon Skeet*](https://www.manning.com/books/software-mistakes-and-tradeoffs).

## Requirements
Let's assume you're building a *voting app*. The requirement is to check if voter is **18 years old**.

## Solution
You can do it in two ways by:*
1. **Subtracting** 18 years from election date
2. **Adding** 18 years to voter's birthday

*\*there are more combinations, but these use cases covers them*

## Implementation

For the blog post purposes we'll see tests which covers these cases.

### Implementation of subtracting

```php
public function test_can_person_vote_by_subtracting_18_years_from_election_date(): void
{
    //Given
    $electionDate = DateTimeImmutable::createFromFormat('Y-m-d', '2022-02-28');
    $personBirthDay = DateTimeImmutable::createFromFormat('Y-m-d', '2004-02-29');
    $eighteenYearsInterval = new DateInterval('P18Y');

    //When
    $result = $electionDate->sub($eighteenYearsInterval);

    //Then
    self::assertTrue($personBirthDay >= $result);
}
```

Test result is positive - it passes. It means that person **can vote** in this case. To visualize it better I prepared a picture with timeline showing this case.

![visualization of first test](/assets/img/posts/2021-11-22/test_can_person_vote_by_subtracting_18_years_from_election_date.png)

### Implementation of adding

```php
public function test_can_person_vote_by_adding_18_years_to_birthday(): void
{
    //Given
    $electionDate = DateTimeImmutable::createFromFormat('Y-m-d', '2022-02-28');
    $personBirthDay = DateTimeImmutable::createFromFormat('Y-m-d', '2004-02-29');
    $eighteenYearsInterval = new DateInterval('P18Y');

    //When
    $result = $personBirthDay->add($eighteenYearsInterval);

    //Then
    self::assertTrue($electionDate >= $result);
}
```

This time test is red - it fails which means person **cannot vote** because will have 18 birthdays on *2022-03-01* and the election date is *2022-02-28*.

![visualization of second test](/assets/img/posts/2021-11-22/test_can_person_vote_by_adding_18_years_to_birthday.png)

## Summary

You need to be careful when you work with dates, because many traps you can be caught. In this particular case I would suggest asking your business, because answer depends on country you are building software for.
