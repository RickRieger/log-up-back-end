# Log Up — Back End: Sign-Up / Log-In Logic

[![Tests](https://github.com/RickRieger/log-up-back-end/actions/workflows/tests.yml/badge.svg)](https://github.com/RickRieger/log-up-back-end/actions/workflows/tests.yml)

A JavaScript exercise from the **Code Immersives** web development bootcamp in New York City (February 2021). It's the "back end" half of a two-part project: four functions that decide whether an email and password are valid, whether the email belongs to a registered user, and whether the password matches. A pretend "database" of three users holds the data. The front end ([log-up-front-end](https://github.com/RickRieger/log-up-front-end)) builds the screen that uses these functions. [Jest](https://jestjs.io/) tests check each one. This repo is my fork of the assignment. My solution is in [`main.js`](main.js).

**Skills practiced:** string methods (`.endsWith()`, `.toUpperCase()`, `.toLowerCase()`, `.length`), combining conditions with `&&` and `||`, and returning a boolean expression directly.

**Built with:** JavaScript (Node.js) and Jest 26. There's no web page; the badge above shows whether the tests currently pass on GitHub.

> The "database" holds plain-text passwords in variables because this is a beginner exercise. Real apps store passwords hashed (e.g. with bcrypt) on a server, never in front-end code.

---

## Contents

- [My solution](#my-solution)
- [How I got there](#how-i-got-there)
- [Run the tests](#run-the-tests)
- [How it's verified](#how-its-verified)
- [Cleanup log (November 2026)](#cleanup-log-november-2026)
- [Original assignment (from the instructor)](#original-assignment-from-the-instructor)

---

## My solution

Written on **2021-02-24** (commits *"Man this was a beast"*, *"slack is a privelage"* and *"Slack is definatly a privelage"*). All 7 of the assignment's tests passed, and they still do. The solution had no bugs; the 2026 cleanup made it easier to read. *(Shown as it is after the cleanup.)*

```js
const DOMAIN = '@codeimmersives.com';

// Ends with the domain AND has at least one character before the "@"
function isValidEmail(email) {
  return email.endsWith(DOMAIN) && email.length > DOMAIN.length;
}

// At least 8 characters, with at least one lowercase and one uppercase letter.
// If upper-casing the whole string changes it, it had a lowercase letter (and vice versa).
function isValidPassword(password) {
  return (
    password.length >= 8 &&
    password.toUpperCase() !== password &&
    password.toLowerCase() !== password
  );
}

function isRegisteredUser(email) {
  return email === user1 || email === user2 || email === user3;
}

// The email must belong to a user AND the password must be THAT user's password
function passwordMatches(email, password) {
  return (
    (email === user1 && password === password1) ||
    (email === user2 && password === password2) ||
    (email === user3 && password === password3)
  );
}
```

| Function | Key idea |
|---|---|
| `isValidEmail` | `.endsWith()` checks the domain. The length check rejects `'@codeimmersives.com'` with nothing before the `@`. |
| `isValidPassword` | The upper/lowercase trick from the assignment's hint, with no regular expressions: `'Hello'.toUpperCase()` is `'HELLO'`, which is different, so `'Hello'` has a lowercase letter. A password of only numbers (`'12345678'`) is unchanged either way, so it fails both checks. |
| `isRegisteredUser` | Three comparisons joined with `\|\|` (or): true if *any* one matches. The hint says it shouldn't need `&&`. |
| `passwordMatches` | Each user's email **and** password must match *together*: `(email AND password)` for user 1, **or** for user 2, **or** for user 3. So user 1's email with user 2's password is `false`. |

**Each function returns the condition itself.** `return a && b;` gives back `true` or `false` directly, with no need for `if (...) { return true; } else { return false; }`.

**Checked beyond the tests:** a fake domain like `x@codeimmersives.com.evil.com` is rejected, `x@notcodeimmersives.com` is rejected, and an unregistered email can't log in with a real user's password. Emails are matched exactly, so `x@CodeImmersives.com` (capital letters) isn't accepted. The assignment asks for exactly `@codeimmersives.com`.

## How I got there

My commit was titled *"Man this was a beast"*, and the file still held my earlier attempts as comments (removed in the cleanup, but kept in git history at commit `5b96cb1`). They show how the email check evolved:

```js
// Attempt 1: includes() + a typeof trick to make sure there are characters before the domain
if (email.includes('@codeimmersives.com') && typeof email[email.length - 20] === 'string') { ... }

// Attempt 2: endsWith(), but no check for a name before the @
if (email.endsWith('@codeimmersives.com')) return true; else { return false; }

// Attempt 3: endsWith() + length, still wrapped in if/else
if (email.endsWith('@codeimmersives.com') && email.length >= 20) return true; else { return false; }

// Final: return the condition directly
return email.endsWith('@codeimmersives.com') && email.length >= 20;
```

Each step fixed something:
- `includes()` became `endsWith()`, so the domain must be at the *end*.
- A length check was added, so there has to be a name before the `@`.
- The `if`/`else` that returned `true`/`false` became returning the condition itself.

## Run the tests

You need [Node.js](https://nodejs.org/).

```bash
git clone https://github.com/RickRieger/log-up-back-end.git
cd log-up-back-end
npm ci
npm run test:ci   # runs all tests once and exits
npm test          # watch mode, as in the original assignment: re-runs on every save
```

Expected result: `Tests: 7 passed, 7 total`.

## How it's verified

[`.github/workflows/tests.yml`](.github/workflows/tests.yml) runs `npm run test:ci` on GitHub Actions after every push, and the badge at the top shows the result. This repo has no web page, so instead of a live site, a passing test run is how it shows it works.

---

## Cleanup log (November 2026)

All changes below were made on **2026-11-24** on the branch `cleanup-and-ci`, checked by running the tests and extra inputs, and merged into `master`. Each is its own commit. Nothing in the assignment's "Our code below. Do not touch!" section was edited, and the tests are unchanged.

**No bugs were found.** These changes are for readability.

### 1. Removed the commented-out draft attempts

**Why:** four earlier attempts sat above the real code as comments, so a reader had to skip past them to find the working functions. They're described in [How I got there](#how-i-got-there) and preserved in git history.

```js
// Before: 26 lines of commented-out drafts, then the real functions
// const isValidEmail = (email) => { ... }
// function isValidPassword(password){ ... }
// function isValidEmail(email){ ... }
// function isValidEmailAlt(email){ ... }

function isValidEmail(email){ ... }

// After: just the real functions
```

### 2. Explained the "20", and tidied the formatting

**Why:** `email.length >= 20` worked, but it's a *magic number*. A reader can't tell where 20 comes from: `'@codeimmersives.com'` is 19 characters, plus at least one for the name. Naming the domain once makes the rule readable, and keeps it correct if the domain ever changes. Several lines were also not indented, `passwordMatches` had blank lines inside its `return`, and there was a block of empty lines at the end.

```js
// Before
function isValidEmail(email){
  return email.endsWith('@codeimmersives.com') && email.length >= 20;
}

function isRegisteredUser(email){
return email === user1 || email === user2 || email === user3;
}

// After
const DOMAIN = '@codeimmersives.com';

// Ends with the domain AND has at least one character before the "@"
function isValidEmail(email) {
  return email.endsWith(DOMAIN) && email.length > DOMAIN.length;
}

function isRegisteredUser(email) {
  return email === user1 || email === user2 || email === user3;
}
```

Each function also got a one-line comment saying what it checks. I confirmed identical results on the original tests and on the extra inputs listed above.

### 3. Automatic test runs on GitHub

**Why:** `npm test` runs Jest in *watch mode*, which never exits, so an automated check couldn't use it. I added a run-once command and a GitHub Actions workflow.

```jsonc
// Before (package.json)
"scripts": { "test": "jest --watch-all" }

// After
"scripts": {
  "test": "jest --watch-all",   // unchanged: watch mode while working
  "test:ci": "jest --ci"        // new: run once and exit
}
```

### 4. Rewrote this README section

Added everything above. The instructor's original instructions are below, unchanged.

---

## Original assignment (from the instructor)

*The text below is the assignment README as written by the Code Immersives instructor, unchanged.*

### Log Up - Back End

Today we'll be making the back end of our app.

### Introduction

Some apps have a simplified signup/login process, where if you enter an email and password and your email is NOT in their system, they'll register you with that email and password, and if you enter an email that IS in their system, they'll attempt to log you in with your email/password.

This app is even simpler than that, in that our "system" is just variables with the email and password for three users.

But the functions you write here will do the logical work for the interface we'll write for our next part of this project!

### Setup

- Clone and fork this repo.
- Code in `main.js` until you've passed the tests.
- Push to GitHub.
- Turn that link in as your assignment.

### Hints

- There are many hints below, but don't forget to read the tests in `main.test.js`!

### Tasks

- `isValidEmail` - returns `true` if the given string ends with `@codeimmersives.com` and has at least one character before that, and `false` otherwise.
- `isValidPassword` - returns true if the given string is at least 8 characters with a lower case and upper case letter, and false otherwise. Do not use Regular Expressions (regex).
  - HINT: you can COMPARE an upper-cased version of your string to the current string. If they're different, then it has at least one lower-cased letter. You can also do the same to check for an upper-cased letter.
- `isRegisteredUser` - returns true if the given string is identical to ONE of the strings held in the variables `user1`, `user2`, and `user3`. Returns false otherwise.
  - HINT: your solution should NOT need `&&`.
- `passwordMatches` - returns true if the first string given is one of the strings in the user variables AND the second string is the matching string in the password variables. Returns false otherwise.
