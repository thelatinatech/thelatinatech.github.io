---
title: "Solving a Murder (with SQL)"
layout: post
---
A murder. A handful of clues. A database full of suspects. Follow along as I use SQL to unravel the mystery while exploring the beginner-friendly concepts that make this challenge such a fun way to learn.


One of the most enjoyable ways to learn SQL is by solving real problems instead of memorizing syntax. The **SQL Murder Mystery** does exactly that - it turns a SQL lesson into a detective game where every clue requires another query.

Instead of relying on the provided database diagram, I challenged myself to investigate using only SQL. Whenever I needed to understand the database, I explored it directly through queries.

If you'd like to solve it yourself first, you can find the challenge here:

[**https://mystery.knightlab.com/**](https://mystery.knightlab.com/)

---
## Beginner Takeaways

If you're new to SQL, this challenge is a great reminder that solving problems isn't about memorizing every command, it's about breaking a problem into smaller questions. 

Here are the biggest lessons I took away from this exercise:

- **Start with what you know.** Every clue narrowed the search a little further. Instead of trying to write one giant query, I used each result to guide my next question.
- **Think of SQL as asking questions.** Every `SELECT` statement answers a specific question: _Who lives on this street? Which gym members match these criteria? Who attended this event?_ Solving the mystery became much easier once I focused on asking the right question at each step.
- **Learn a handful of commands well.** This challenge relied mostly on a small set of SQL fundamentals: `SELECT`, `WHERE`, `ORDER BY`, `LIKE`, `JOIN`, and `COUNT()`. You don't need to know every SQL feature to solve meaningful problems.
- **Explore unfamiliar databases.** Before diving into the mystery, I inspected the database to see what tables were available. Taking a few minutes to understand the structure of a database often saves time later and makes it easier to connect the dots.
- **SQL is iterative.** My first query never solved the entire mystery. Each query gave me new information that refined my next search. That's exactly how many real-world data investigations work.

---
## The Case

The only information we're given is:

- Crime: **Murder**
- Date: **January 15, 2018**
- Location: **SQL City**

Our first task is finding the crime scene report.

```
SELECT *
FROM crime_scene_report
WHERE type = 'murder'
  AND city = 'SQL City'
  AND date = 20180115;
```

The report gives us our first two leads:

- One witness lives at the **last house on Northwestern Dr**
- Another witness is named **Annabel** and lives on **Franklin Ave**

Already we're practicing an important SQL skill: filtering data using multiple conditions.

---
## Finding the Witnesses

To locate the first witness, I searched everyone living on Northwestern Drive and ordered the house numbers from highest to lowest.

```
SELECT id, name, address_number
FROM person
WHERE address_street_name = 'Northwestern Dr'
ORDER BY address_number DESC;
```

This identified **Morty Schapiro**.

For the second witness, the report already gave us part of her name.

```
SELECT id, name
FROM person
WHERE address_street_name = 'Franklin Ave'
  AND name LIKE 'Annabel%';
```

This returned **Annabel Miller**.

### SQL Concepts

- `WHERE`
- `ORDER BY`
- `LIKE`

---
## Interviewing the Witnesses

Now that we knew their IDs, we could retrieve both interviews.

```
SELECT *
FROM interview
WHERE person_id IN (14887, 16371);
```

Together, the interviews provided several clues about the suspect:

- Male
- Member of **Get Fit Now Gym**
- Gold membership
- Membership ID begins with **48Z**
- Visited the gym on **January 9**
- License plate contains **H42W**

Each interview narrowed the search, turning thousands of records into just a handful.

---
## Narrowing Down the Suspect

The gym membership clues were the easiest place to start.

```
SELECT *
FROM get_fit_now_member
WHERE id LIKE '48Z%'
  AND membership_status = 'gold';
```

Only two members matched.

To confirm the witness statement, I checked who visited the gym on January 9.

```
SELECT *
FROM get_fit_now_check_in
WHERE check_in_date = 20180109
  AND membership_id LIKE '48Z%';
```

Both suspects had checked in that day, so I needed another clue.

The final clue was the partial license plate.

```
SELECT *
FROM drivers_license
WHERE plate_number LIKE '%H42W%'
  AND gender = 'male';
```

This identified **Jeremy Bowers** as the murderer.

---
## Plot Twist: The Mastermind

After submitting Jeremy as the murderer, the game reveals there's someone else behind the crime.

Jeremy's interview provided a new set of clues:

- Female
- Red hair
- Height between 65–67 inches
- Drives a Tesla Model S
- Wealthy
- Attended the SQL Symphony Concert **three times** in December 2017

This required combining information across multiple tables.

```
SELECT
    person.name,
    annual_income,
    height,
    hair_color,
    car_make,
    car_model
FROM drivers_license
JOIN person
    ON drivers_license.id = person.license_id
JOIN income
    ON person.ssn = income.ssn
WHERE gender = 'female'
  AND hair_color = 'red'
  AND car_make = 'Tesla'
  AND car_model = 'Model S'
  AND height BETWEEN 65 AND 67;
```

This narrowed the suspects considerably.

The final step was checking concert attendance.

```
SELECT
    person.name,
    COUNT(*) AS concerts
FROM facebook_event_checkin
JOIN person
    ON facebook_event_checkin.person_id = person.id
WHERE event_name = 'SQL Symphony Concert'
  AND date LIKE '201712%'
GROUP BY person.name
HAVING COUNT(*) = 3;
```

Only one person matched every clue:

**Miranda Priestly.**

Case closed.

---
## Final Thoughts

As a WhoDunIt fan, the SQL Murder Mystery was a fun way to practice thinking like *both* a detective and a data analyst. Rather than following a tutorial, I challenged myself to explore the database using only SQL, letting each clue guide my next query.

More than anything, this exercise reinforced that SQL isn't just a programming language - it's a tool for investigating data. Whether you're debugging an application, analyzing business metrics, or exploring a new database at work, the process is often the same: gather evidence, test assumptions, and let the data point you toward the answer.

If you're just beginning your SQL journey, I highly recommend giving this challenge a try without looking at a walkthrough first. Even if you don't solve it on your first attempt, you'll come away with a stronger understanding of how to think through problems using SQL—and that's a skill that transfers far beyond this mystery.