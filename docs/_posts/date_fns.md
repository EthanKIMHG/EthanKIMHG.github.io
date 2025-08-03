---
layout: post
title:  "date-fns"
date:   2025-08-03 00:00:00 +0900
categories: jekyll update
---
# date-fns

**What is `date-fns`?**
`date-fns` is a modern JavaScript date utility library that provides a comprehensive, yet lightweight and modular, set of functions for manipulating, formatting, and comparing dates. It's often seen as a more modern and tree-shakable alternative to larger libraries like Moment.js.

## Key Features and Philosophy

1.  **Modular and Tree-Shakable:**
    *   Unlike monolithic libraries, `date-fns` is designed as a collection of individual functions. This means you only import the functions you need, leading to smaller bundle sizes in your applications (thanks to tree-shaking).
    *   **Example:** `import { format, addDays } from 'date-fns';`

2.  **Immutable:**
    *   All `date-fns` functions return a new date instance instead of modifying the original date object. This promotes predictable behavior and avoids unintended side effects.
    *   **Example:** `const newDate = addDays(originalDate, 5); // originalDate remains unchanged`

3.  **Pure Functions:**
    *   Functions are pure, meaning they produce the same output for the same input and have no side effects.

4.  **Native Date Object:**
    *   `date-fns` works directly with JavaScript's native `Date` object. This means you don't need to wrap dates in a special object (like Moment.js does), making it easier to integrate with other libraries and standard JavaScript.

5.  **TypeScript Support:**
    *   It's written in TypeScript, providing excellent type definitions out of the box.

6.  **Localization:**
    *   Supports a wide range of locales, allowing you to format dates according to different language and regional conventions.

## Common Use Cases and Examples

### 1. Installation

```bash
npm install date-fns
# or
yarn add date-fns
```

### 2. Formatting Dates

```javascript
import { format } from 'date-fns';
import { ko } from 'date-fns/locale'; // Import Korean locale

const now = new Date();

// Basic formatting
console.log(format(now, 'yyyy-MM-dd')); // e.g., "2025-08-03"
console.log(format(now, 'MM/dd/yyyy')); // e.g., "08/03/2025"
console.log(format(now, 'HH:mm:ss')); // e.g., "14:30:00"

// More complex formatting
console.log(format(now, 'EEEE, MMMM do, yyyy')); // e.g., "Sunday, August 3rd, 2025"

// Formatting with locale
console.log(format(now, 'yyyy년 MM월 dd일 EEEE', { locale: ko })); // e.g., "2025년 08월 03일 일요일"
console.log(format(now, 'PPP', { locale: ko })); // e.g., "2025년 8월 3일"
```

### 3. Manipulating Dates (Adding/Subtracting)

```javascript
import { addDays, subMonths, startOfWeek, endOfDay } from 'date-fns';

const today = new Date();

// Add 7 days
const nextWeek = addDays(today, 7);
console.log(format(nextWeek, 'yyyy-MM-dd'));

// Subtract 3 months
const threeMonthsAgo = subMonths(today, 3);
console.log(format(threeMonthsAgo, 'yyyy-MM-dd'));

// Get the start of the current week (Sunday by default)
const weekStart = startOfWeek(today);
console.log(format(weekStart, 'yyyy-MM-dd'));

// Get the end of the current day
const dayEnd = endOfDay(today);
console.log(format(dayEnd, 'yyyy-MM-dd HH:mm:ss'));
```

### 4. Comparing Dates

```javascript
import { isBefore, isAfter, isEqual, differenceInDays } from 'date-fns';

const date1 = new Date(2025, 7, 3); // August 3, 2025
const date2 = new Date(2025, 7, 5); // August 5, 2025
const date3 = new Date(2025, 7, 3); // August 3, 2025

console.log(isBefore(date1, date2)); // true
console.log(isAfter(date2, date1));  // true
console.log(isEqual(date1, date3));  // true

// Difference in days
console.log(differenceInDays(date2, date1)); // 2
```

### 5. Parsing Dates

While `date-fns` primarily focuses on formatting and manipulation, it also has parsing capabilities, though often the native `new Date()` constructor is sufficient for ISO 8601 strings.

```javascript
import { parseISO } from 'date-fns';

const isoString = '2025-08-03T10:00:00Z';
const parsedDate = parseISO(isoString);
console.log(format(parsedDate, 'yyyy-MM-dd HH:mm:ss'));
```

## `date-fns` vs. Moment.js

Historically, Moment.js was the go-to date library. However, it has several drawbacks that `date-fns` addresses:

*   **Mutability:** Moment.js objects are mutable, which can lead to unexpected bugs.
*   **Bundle Size:** Moment.js is monolithic and not tree-shakable, leading to larger bundle sizes.
*   **Native Date Object:** Moment.js wraps dates in its own object, which can sometimes complicate interoperability.
*   **Maintenance:** Moment.js is now in maintenance mode, recommending users to migrate to other libraries.

`date-fns` is a highly recommended choice for modern JavaScript projects due to its modularity, immutability, and direct use of the native `Date` object, making it efficient and easy to integrate.