# SQL Assignment: Coding Judge Platform Database

Create a relational database for an online coding judge, a website like LeetCode or Codeforces where
users solve programming problems. The goal is to practice creating tables, relationships, primary
keys, foreign keys, a many-to-many link table, constraints and SQL queries with joins, grouping and
calculations.

> **Background.** Users write solutions to programming problems. Some users are also authors who
> publish problems. Every problem has a difficulty, a time limit and some points, and it is labeled
> with topic tags (array, graph, dynamic programming and so on). A user picks a programming language
> and sends a solution. That is a **submission**. The judge runs the submission on several tests and
> stores the result of each test. Slower languages get more time: the effective time limit is the
> problem's time limit multiplied by the language's time multiplier. A submission is accepted only if
> it passes every test.

## 1. Database Tables

**users**

| Column | Meaning |
|---|---|
| user_id | Unique number of the user |
| username | Public nickname of the user |
| first_name, last_name | User's name |
| email | User's email |
| registration_date | When the user signed up |

**languages**

| Column | Meaning |
|---|---|
| language_id | Unique number of the language |
| language_name | Name of the programming language |
| version | Compiler or interpreter version |
| time_multiplier | How many times more time this language gets (C++ = 1, Python = 3) |
| memory_limit_mb | Maximum memory a solution may use, in MB |

**problems**

| Column | Meaning |
|---|---|
| problem_id | Unique number of the problem |
| author_id | The user who published the problem |
| title | Title of the problem |
| difficulty | How hard the problem is |
| time_limit_ms | Time limit per test in milliseconds (for a multiplier of 1) |
| points | Points a user earns for solving the problem |
| created_date | When the problem was published |

**tags**

| Column | Meaning |
|---|---|
| tag_id | Unique number of the tag |
| tag_name | Name of the topic |
| description | What the topic is about |

**problem_tags**

| Column | Meaning |
|---|---|
| problem_id | The problem |
| tag_id | A tag that this problem has |

**submissions**

| Column | Meaning |
|---|---|
| submission_id | Unique number of the submission |
| user_id | The user who sent the solution |
| problem_id | The problem being solved |
| language_id | The language the solution is written in |
| submit_time | When the solution was sent (date and time) |
| judge_time | When the judge finished checking it (date and time) |
| status | Final verdict of the submission |
| code_length | Length of the source code in characters |

**test_results**

| Column | Meaning |
|---|---|
| result_id | Unique number of the test result |
| submission_id | The submission that was tested |
| test_number | Number of the test (1, 2, 3, ...) |
| runtime_ms | How long the test ran, in milliseconds |
| memory_kb | How much memory the test used, in KB |
| verdict | Result of this test |

## 2. Relationships

- A user can author many problems. Each problem has one author.
- A user can make many submissions. Each submission belongs to one user.
- A problem can have many submissions. Each submission is for one problem.
- A language can be used in many submissions. Each submission uses one language.
- A problem can have many tags, and a tag can belong to many problems (many-to-many through `problem_tags`).
- A submission can have many test results. Each test result belongs to one submission.
- The relationships between tables must be implemented using **foreign keys**.

## 3. Required Constraints

- Every table must have a **PRIMARY KEY**. In `problem_tags` the primary key is the pair
  (`problem_id`, `tag_id`), so the same tag cannot be added to the same problem twice.
- All foreign keys must reference the right tables, and foreign key columns must be **NOT NULL**.
- Username and email must be **UNIQUE** and **NOT NULL**.
- Language name must be **UNIQUE** and **NOT NULL**. Version must be **NOT NULL**.
- Problem title and tag name must be **UNIQUE** and **NOT NULL**.
- Submit time must be **NOT NULL**.
- Time multiplier, memory limit, time limit and points must be greater than 0.
- Difficulty must be one of: `'EASY'`, `'MEDIUM'`, `'HARD'`.
- Submission status must be one of: `'PENDING'`, `'ACCEPTED'`, `'WRONG_ANSWER'`, `'TIME_LIMIT'`,
  `'RUNTIME_ERROR'`, `'COMPILE_ERROR'`.
- Code length must be greater than or equal to 0.
- A submission's judge time cannot be earlier than its submit time.
- A submission with status `'PENDING'` must have no judge time. Every other status must have a judge time.
- Test number must be greater than 0. The same submission cannot have two results for the same test
  number (**UNIQUE** pair).
- Runtime and memory must be greater than or equal to 0.
- Test verdict must be one of: `'PASS'`, `'FAIL'`, `'TIMEOUT'`, `'CRASH'`.

## 4. Sample Data

| Table | Rows |
|---|---|
| users | 6 |
| languages | 4 |
| problems | 6 |
| tags | 8 |
| problem_tags | 12 |
| submissions | 13 |
| test_results | 31 |

The data must allow every query below to return a meaningful result. In particular:

- at least one user with no submissions, at least one problem that nobody has attempted and at least
  one tag that is not used by any problem;
- at least one submission of each status;
- at least one test with each verdict;
- one user who solved the same problem twice (so the leaderboard must not count it twice);
- an accepted submission passes all of its tests, and a compile error or pending submission has no tests;
- a test marked `'PASS'` never runs longer than the effective time limit.

## 5. Required SQL Queries

1. Display all users.
2. Display all languages with their version, time multiplier and memory limit.
3. Display all problems with the author's username, easiest first.
4. Display all tags of a selected problem.
5. Display all submissions of a selected user, ordered by submit time.
6. Count how many submissions each problem has (problems with no submissions must show 0).
7. Display all accepted submissions with the username, problem title and language.
8. Display all hard problems.
9. Calculate the effective time limit of each submission in milliseconds:
   `time_limit_ms × time_multiplier`
10. Leaderboard: for each user show the number of different problems solved and the total points,
    highest first (users who solved nothing must show 0, and a problem solved twice counts once).
11. For each problem show the number of submissions, the number of accepted submissions and the
    acceptance rate in percent (problems with no submissions must show 0).
12. For each judged submission show how many tests passed, how many tests ran, the average runtime
    and the peak memory.
13. Display how long the judge took for each judged submission, in seconds, longest first.
14. Display problems that nobody has attempted.
15. Data check: find tests marked `'PASS'` that ran longer than the effective time limit (the result
    should be empty).

## 6. What to Submit

- One `.sql` file that creates the tables, adds the constraints, inserts the sample data and contains
  all the queries. It must run from top to bottom without errors.
- An ER diagram of the database (for example made in drawSQL or dbdiagram.io).
