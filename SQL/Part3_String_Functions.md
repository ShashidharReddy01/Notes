# SQL Notes – Part 3 (String Functions)

String functions transform or inspect text **in your result set only** — the actual table data stays unchanged unless you pair them with `UPDATE`.

---

# 1. CONCAT — Join Strings Together

Combines two or more strings into one.

```sql
SELECT CONCAT('H','e','l','l','o');          -- Hello

SELECT CONCAT(author_fname, ' ', author_lname) AS author_name FROM books;
```

Without the space `' '` separator, names run together (`JhumpaLahiri`). Always remember to add a delimiter manually.

---

# 2. CONCAT_WS — Concat With Separator

First argument is the separator, applied **between** every remaining argument. Saves you from repeating the delimiter.

```sql
SELECT CONCAT_WS(' - ', title, author_fname, author_lname) FROM books;
-- The Namesake - Jhumpa - Lahiri
```

Use `CONCAT_WS` when joining 3+ values with the same separator. Use `CONCAT` when you need different separators between different parts.

---

# 3. SUBSTRING (SUBSTR) — Extract Part of a String

```sql
SELECT SUBSTRING('Hello World', 1, 4);   -- Hell   (start at 1, take 4 chars)
SELECT SUBSTRING('Hello World', 7);      -- World  (start at 7, go to end)
```

**Indexes start at 1** in SQL, not 0.

Negative index counts from the end:

```sql
SELECT SUBSTRING('Hello World', -5);     -- World
```

Common pattern — truncate long titles:

```sql
SELECT CONCAT(SUBSTRING(title, 1, 10), '...') AS short_title FROM books;
```

Build initials by combining SUBSTRING + CONCAT:

```sql
SELECT CONCAT(SUBSTRING(author_fname, 1, 1), '.', SUBSTRING(author_lname, 1, 1), '.') AS initials
FROM books;
-- J.L.
```

`SUBSTR` is an alias for `SUBSTRING` — they work identically.

---

# 4. REPLACE — Swap Substrings

Replaces **every occurrence** of a substring within a string.

```sql
SELECT REPLACE('hello world', 'hello', '!@#$');          -- !@#$ world
SELECT REPLACE('cheese bread coffee milk', ' ', ' and '); -- cheese and bread and coffee and milk
```

**Case-sensitive** — `'Hello'` ≠ `'hello'`:

```sql
SELECT REPLACE('hello world', 'Hello', '!@#$');   -- hello world  (no match, nothing replaced)
```

Practical use — slugify a title:

```sql
SELECT REPLACE(title, ' ', '-') FROM books;
-- The-Namesake
```

---

# 5. REVERSE — Reverse a String

```sql
SELECT REVERSE('Hello World');       -- dlroW olleH
SELECT REVERSE(author_fname) FROM books;
```

MySQL auto-casts non-string types:

```sql
SELECT REVERSE(83);                  -- 38
```

Nesting example — palindrome-style concat:

```sql
SELECT CONCAT(author_fname, REVERSE(author_fname)) FROM books;
-- JhumpaaphmuhJ
```

---

# 6. CHAR_LENGTH vs LENGTH

| Function        | Counts                  | Use when                          |
| --------------- | ----------------------- | --------------------------------- |
| `CHAR_LENGTH()` | Number of **characters** | You care about display/text length |
| `LENGTH()`      | Number of **bytes**      | You care about storage size        |

For ASCII/English text they return the same number. They differ for multi-byte characters (Chinese, emoji, etc.):

```sql
SELECT CHAR_LENGTH('Hey!');    -- 4
SELECT LENGTH('中为');          -- 6  (3 bytes per character in UTF-8)
SELECT CHAR_LENGTH('中为');     -- 2
```

**Rule of thumb:** Default to `CHAR_LENGTH` unless you specifically need byte count.

---

# 7. UPPER and LOWER — Change Case

```sql
SELECT UPPER(title) FROM books;                -- THE NAMESAKE
SELECT LOWER('I LOVE READING BOOKS !!!');      -- i love reading books !!!
```

Also available as `UCASE()` / `LCASE()` — same behavior, less common.

---

# 8. INSERT — Inject Text at a Position

```sql
SELECT INSERT('Hello Bob', 7, 0, 'There ');
-- Hello There Bob
```

Syntax: `INSERT(original, position, chars_to_delete, new_string)`

- **position** — where to start (1-indexed)
- **chars_to_delete** — how many characters to remove at that position before inserting (0 = pure insert, no removal)

```sql
SELECT INSERT('Hello Bob', 6, 3, 'Everyone');
-- Hello Everyone   (replaced 'Bob' with 'Everyone')
```

---

# 9. LEFT and RIGHT — Grab From Edges

```sql
SELECT LEFT('omghahalol', 3);   -- omg   (first 3 chars)
SELECT RIGHT('omghahalol', 3);  -- lol   (last 3 chars)
```

Handy for extracting prefixes, file extensions, or quick initials:

```sql
SELECT LEFT(author_fname, 1) AS initial FROM books;
```

---

# 10. TRIM — Remove Unwanted Characters

Removes **leading and trailing** whitespace by default:

```sql
SELECT TRIM('    boston    ');     -- 'boston'
```

Targeted trimming with `LEADING`, `TRAILING`, or `BOTH`:

```sql
SELECT TRIM(LEADING '.' FROM '...boston...');    -- 'boston...'
SELECT TRIM(TRAILING '.' FROM '...boston...');   -- '...boston'
SELECT TRIM(BOTH '.' FROM '...boston...');       -- 'boston'
```

You can nest TRIM calls to strip different characters:

```sql
SELECT TRIM(LEADING '.' FROM TRIM('.........boston     '));
-- 'boston     ' → dots removed, then inner TRIM already handled trailing spaces first
```

**Gotcha:** TRIM only removes the **exact character** you specify. To strip multiple different characters, nest calls or use `REPLACE`.

---

# 11. Nesting String Functions

The real power comes from combining these functions. MySQL evaluates **inside out**, just like math.

Build a formatted summary row:

```sql
SELECT
    CONCAT(SUBSTR(title, 1, 10), '...') AS short_title,
    CONCAT(author_lname, ', ', author_fname) AS author,
    CONCAT(stock_quantity, ' in stock') AS quantity
FROM books;
```

Reverse an uppercased string:

```sql
SELECT REVERSE(UPPER('Why does my cat look at me with such hatred?'));
```

Full name in caps:

```sql
SELECT UPPER(CONCAT(author_fname, ' ', author_lname)) AS 'full name in caps' FROM books;
```

Arrow-separated title:

```sql
SELECT REPLACE(title, ' ', '->') AS title FROM books;
```

Blurb builder:

```sql
SELECT CONCAT(title, ' was released in ', released_year) AS blurb FROM books;
```

---

# 12. Common Patterns & Variations

| Task | Pattern |
| ---- | ------- |
| Truncate with ellipsis | `CONCAT(SUBSTRING(col, 1, N), '...')` |
| Build initials | `CONCAT(LEFT(fname, 1), '.', LEFT(lname, 1), '.')` |
| Slugify text | `LOWER(REPLACE(col, ' ', '-'))` |
| Pad display | `LPAD(col, width, '0')` / `RPAD(col, width, ' ')` |
| Find position of substring | `LOCATE('needle', col)` — returns 0 if not found |
| Repeat a string | `REPEAT('*', 5)` → `*****` |
| Get part between delimiters | `SUBSTRING_INDEX(email, '@', -1)` → domain |

---

# 13. Important Gotchas

1. **REPLACE is case-sensitive.** `REPLACE('Hello', 'hello', 'x')` does nothing. If you need case-insensitive replace, convert both sides with `LOWER` first or use a regex-based approach.

2. **SUBSTRING is 1-indexed.** Position 0 behaves like position 1 in MySQL — don't rely on it; always start from 1.

3. **NULL propagation.** Any string function with a NULL argument returns NULL:
   ```sql
   SELECT CONCAT('hi', NULL);   -- NULL (not 'hi')
   ```
   Use `IFNULL(col, '')` or `COALESCE(col, '')` to guard against this.

4. **These don't modify the table.** Every function shown here only affects the SELECT output. To persist changes, pair with `UPDATE`:
   ```sql
   UPDATE books SET title = UPPER(title);
   ```

5. **CHAR_LENGTH vs LENGTH matters for non-ASCII.** If your data has unicode characters, always use `CHAR_LENGTH` for character count.

6. **TRIM only strips one character type at a time.** `TRIM('.-' FROM col)` doesn't work — it's not a character class.

---

# 14. Quick Reference

| Function | Purpose | Example |
| -------- | ------- | ------- |
| `CONCAT(a, b, ...)` | Join strings | `CONCAT('A', 'B')` → `AB` |
| `CONCAT_WS(sep, a, b, ...)` | Join with separator | `CONCAT_WS('-', 'A', 'B')` → `A-B` |
| `SUBSTRING(str, pos, len)` | Extract portion | `SUBSTRING('Hello', 1, 3)` → `Hel` |
| `REPLACE(str, from, to)` | Swap all occurrences | `REPLACE('aa', 'a', 'b')` → `bb` |
| `REVERSE(str)` | Reverse characters | `REVERSE('abc')` → `cba` |
| `CHAR_LENGTH(str)` | Character count | `CHAR_LENGTH('Hey')` → `3` |
| `LENGTH(str)` | Byte count | `LENGTH('中')` → `3` |
| `UPPER(str)` / `LOWER(str)` | Change case | `UPPER('hi')` → `HI` |
| `INSERT(str, pos, len, new)` | Inject/replace at position | `INSERT('AB', 2, 0, 'x')` → `AxB` |
| `LEFT(str, n)` / `RIGHT(str, n)` | First/last n chars | `LEFT('Hello', 2)` → `He` |
| `TRIM(str)` | Strip whitespace/chars | `TRIM('  hi  ')` → `hi` |

---
