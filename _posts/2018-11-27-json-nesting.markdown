---
layout: post
title:  "JSON Nesting"
date:   2018-11-27 17:19:19 -0600
categories: json postgres psql upsert document mongo
---

If you are one of the lucky ducks that uses PostgreSQL, you likely already know that as of <s>9.2</s> <s>err, 9.3</s> 9.4, that Binary JSON is fully supported! And NoSQL... sorta. We're still in Postgres, so maybe NotAllThatMuchSQL? In many applications, you'll find a structure kind of like this:

| index | special_identifier | data                      |
|-------|--------------------|---------------------------|
| 1     | 1-a-sz-01          | `{"sub-head":"value"}     |
| 2     | F-2-sz-01          | `{"title":"JSON Article"} |

And so on and so forth until we've had our fill of data.

Thanks to the recent updates, we can efficiently query through exciting JSON much like our plain, boring columns.

**SQL**
```SQL
SELECT * FROM table WHERE index = 1;
```

**PostgreSQL JSON**
```SQL
SELECT * FROM table WHERE data->>'title' = 'JSON Article';
```

We can aggregate!

**Boring SQL**
```SQL
SELECT count(*) FROM table WHERE index > 10;
```

**Radical NoSQL**
```SQL
SELECT count(*) FROM table WHERE data ? 'title';
```

We can index!

**The SQL They Taught Your Dad**
```SQL
CREATE INDEX idx ON table ((lower(special_identifier)));
```

**Forbidden JSON Techniques The Man Tried To Hide From You**
```SQL
CREATE INDEX jsonidx ON table ((data->>'title'));
```

Okay, from a syntactic point of view, not a lot has changed. Our data is still in a table, after all. So, what's the big deal? Why do we want large, unstructured blocks of data in a paradigm based on structure and relationships? Well, if you've ever read a MongoDB forum, or visited a Slack channel for web developers, or happened to be at a bus stop with a developer who has, at one point, heard of NoSQL, you'll invariably hear one of three answers:
1. Minimize relationship complexity
2. Maximize schema flexibility
3. [It scales right up](https://www.youtube.com/watch?v=b2F-DItXtZs "Every MongoDB argument to date")

I lack the emotional fortitude to jump into raw metal v metal performance <s>pissing contests</s> discussions. We can analyze the first two points at a syntactic and ideologic level, without having to worry (too much) about configurations. With everything above in mind, lets talk about `INSERT.`

At some point, your table will need data. Otherwise, what's the point of a database? Additionally, the data you keep on hand has a nasty tendency to grow over time. Let's build a table to have a more concrete reference point for this article. To honor database education traditions, let's call it `users.`

| unique_index | name  | age | actions                               |
|--------------|-------|-----|---------------------------------------|
| 1            | Nick  | 27  | NULL                                  |
| 2            | Sasha | 28  | '{"account-activated":"2018-05-20"}'  |
| 3            | Lily  | 27  | '{"account-dectivated":"2017-04-01"}' |

Eventually, Trey comes along and wants to sign up for the amazing service we're providing.

```SQL
INSERT INTO users (name, age, actions)
VALUES ('Trey', 30, '{"signup-email":"fake@madeup.org"}'::jsonb);
```

The unique_index automagically increments for us, and Trey is added to our table.
```
| unique_index | name  | age | actions                               |
|--------------|-------|-----|---------------------------------------|
| 1            | Nick  | 27  | NULL                                  |
| 2            | Sasha | 28  | '{"account-activated":"2018-05-20"}'  |
| 3            | Lily  | 27  | '{"account-dectivated":"2017-04-01"}' |
| 4            | Trey  | 30  | '{"signup-email":"fake@madeup.org"}'  |
```
