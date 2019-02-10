---
layout: post
title:  "JSON Nesting"
date:   2018-11-27 17:19:19 -0600
categories: json postgres psql upsert document mongo
permalink: /blog/json-nesting
description: "Postgres now supports jsonb! But, it has plenty of complexities under the hood."
---

If you are one of the lucky ducks that uses PostgreSQL, you likely already know that as of <s>9.2</s> <s>err, 9.3</s> 9.4, that Binary JSON is fully supported! And NoSQL... sorta. We're still in Postgres, so maybe NotAllThatMuchSQL? In many applications, you'll find a structure kind of like this:

```SQL
| index | special_identifier | data                       |
|-------|--------------------|----------------------------|
| 1     | S-a-sz-01          | '{"sub-head":"value"}'     |
| 2     | F-2-sz-01          | '{"title":"JSON Article"}' |
```

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

We can even index!

**The SQL They Taught Your Dad**

```SQL
CREATE INDEX idx ON table ((lower(special_identifier)));
```

**Forbidden JSON Techniques The Man Tried To Hide From You**

```SQL
CREATE INDEX jsonidx ON table ((data->>'title'));
```

Okay, from a syntactic point of view, not a lot has changed. Our data is still in a table, after all. So, what's the big deal? Why do we want large, unstructured blocks of data in a paradigm based on structure and relationships? Well, if you've ever read a MongoDB forum, or visited a Slack channel for web developers, or happened to be at a bus stop with a developer who has, at one point, heard of NoSQL, you'll invariably hear one of three answers:

1.  Minimize relationship complexity
2.  Maximize schema flexibility
3.  [You turn it on and it scales right up](https://www.youtube.com/watch?v=b2F-DItXtZs "Every MongoDB argument to date")

I lack the emotional fortitude to jump into raw metal v metal performance <s>pissing contests</s> discussions. We can analyze the first two points at a syntactic and ideologic level, without having to worry (too much) about configurations. With everything above in mind, let's talk about `INSERT.`

At some point, your table will need data. Otherwise, what's the point of a database? Additionally, the data you keep on hand has a nasty tendency to grow over time. Let's build a table to have a more concrete reference point for this article. To honor database education traditions, let's call it `users.`

```SQL
| unique_index | name  | age | actions                               |
|--------------|-------|-----|---------------------------------------|
| 1            | Nick  | 27  | NULL                                  |
| 2            | Sasha | 28  | '{"account-activated":"2018-05-20"}'  |
| 3            | Lily  | 27  | '{"account-dectivated":"2017-04-01"}' |
```

Eventually, Trey comes along and wants to sign up for the amazing service we're providing.

```SQL
INSERT INTO users (name, age, actions)
VALUES ('Trey', 30, '{"signup-email":"fake@madeup.org"}'::jsonb);
```

The unique_index automagically increments for us, and Trey is added to our table.

```SQL
| unique_index | name  | age | actions                               |
|--------------|-------|-----|---------------------------------------|
| 1            | Nick  | 27  | NULL                                  |
| 2            | Sasha | 28  | '{"account-activated":"2018-05-20"}'  |
| 3            | Lily  | 27  | '{"account-dectivated":"2017-04-01"}' |
| 4            | Trey  | 30  | '{"signup-email":"fake@madeup.org"}'  |
```

No big deal, right? It's a simple insertion, so nothing complex changes. We've added a lot of flexibility in terms of trackable actions, and, so far, there's no cost.

The next logical step is to tackle `UPDATE`. Trey obviously signed up with a fake email, so let's correct it.

```SQL
UPDATE users SET actions = '{"signup-email":"real@real.gov"}'::jsonb
WHERE unique_index = 4;
```

```SQL
| unique_index | name  | age | actions                               |
|--------------|-------|-----|---------------------------------------|
| 1            | Nick  | 27  | NULL                                  |
| 2            | Sasha | 28  | '{"account-activated":"2018-05-20"}'  |
| 3            | Lily  | 27  | '{"account-dectivated":"2017-04-01"}' |
| 4            | Trey  | 30  | '{"signup-email":"real@real.gov"}'    |
```

This looks right, right? We wanted to replace the email Trey signed up with, so we did. Now, like most websites, we want to send out an activation email and track when he first signed up.

```SQL
UPDATE users SET actions = '{"account-activated":"2018-11-27"}'::jsonb
WHERE unique_index = 4;
```

```SQL
| unique_index | name  | age | actions                               |
|--------------|-------|-----|---------------------------------------|
| 1            | Nick  | 27  | NULL                                  |
| 2            | Sasha | 28  | '{"account-activated":"2018-05-20"}'  |
| 3            | Lily  | 27  | '{"account-dectivated":"2017-04-01"}' |
| 4            | Trey  | 30  | '{"account-activated":"2018-11-27"}'  |
```

So, we just lost his email. This makes sense in Postgres- we're updating the entire column. It doesn't care what the type of that column is as long as the value supplied can land there. I bring this up because it may seem intuitive to treat a new JSON key as a sub-column and to append the value as a default for JSON columns. It's an easy mistake to make, and easy to correct too. Here's the query to append:

```SQL
UPDATE users SET actions = users.actions || '{"account-activated":"2018-11-27"}'::jsonb
WHERE unique_index = 4;
```

For a simple add/replace, the JSON operators are pretty smooth.
They syntactically stand out from the rest of PSQL, but I'm okay with that.
If you're shifting paradigms, it's useful to have a visual indicator of what is what.
The JSON(B) functions are all clearly labeled as such, so you should always know what to expect.

Should.

Let's say our site has grown to include a social aspect.
It's 2018, so it was only inevitable.
Our users now have the ability to list out their interests.
The categories and lists on our front-end changes daily, and we, the lowly back-end developers, decide to throw it in a new document column.

Here's what some sample data could look like:

```SQL
'{"shows":
  '{"comedy":
      '{"Arrested Development":"2015-08-12",
        "scrubs":"2017-11-01"}',
    "news":
      '{"The Daily Show":"2014-03-13"}'
    }',
  '{"movies": ...}'
}'
```

As categories get added, it's simple enough to append them.
We can use the same query as above to list out all of the anime series, video games, and ice cream flavors Trey feels like divulging to our organization.
Fantastic.

Oh no.
Wait a minute.
Humans are fickle beings, and our interests change all of the time.
One morning, Trey decides he has had it up to here with American comedies, and binge watches a ton of Asian Rom-Coms.
He has to tell the world about it, our website included.
So, how do we add a show in the comedy category?
With our large, growing website and the possibility of shared accounts, we want to make sure it's simple, thread-safe, and doesn't rely on _a priori_ information.

So, how do we do it?
Typically, these scenarios call for the Postgres portmanteau [upsert](https://en.wiktionary.org/wiki/upsert).
We've already seen the insert side:

```SQL
INSERT INTO users (name, age, actions, likes)
VALUES ('Trey', 30, '{"signup-email":"fake@madeup.org"}'::jsonb, '{"shows":{"comedy":{"Good Morning Call":"2017-11-27"}}}'::jsonb);
```

Now we just need to determine the conflict conditions and our update statement. For the sake of this article, let's assume, for whatever statistically impossible scenario this would pop up in, that name and age, as a pair, act as a unique index. Yes, I know that is A) never going to be true and B) dumb; however, this isn't an article on key/constraint selection. So, where are we at?

```SQL
INSERT INTO users (name, age, actions, likes)
VALUES ('Trey', 30, '{"signup-email":"fake@madeup.org"}'::jsonb, '{"shows":{"comedy":{"Good Morning Call":"2017-11-27"}}}'::jsonb)
ON CONFLICT (name, age)
DO UPDATE SET likes = users.likes || '{"shows":{"comedy":{"Good Morning Call":"2017-11-27"}}}'::jsonb;
```

This looks right. We want to add Good Morning Call to the comedy category of shows of likes, so, why wouldn't we append it? Because it isn't a deep operation. So, the top-level key `shows` in `users.likes` will be **entirely replaced** by our update. We don't want to rely on the client sending the full blurb, since we'd lose thread safety. Additionally, this operation **is not** safe! If `users.likes` is `NULL`, the columns value will remain `NULL`. So, how do we actually perform a deep insert?

```SQL
INSERT INTO users (name, age, actions, likes)
VALUES ('Trey', 30, '{"signup-email":"fake@madeup.org"}'::jsonb, '{"shows":{"comedy":{"Good Morning Call":"2017-11-27"}}}'::jsonb)
ON CONFLICT (name, age)
DO UPDATE SET likes = jsonb_set(likes, '{shows, comedy, Good Morning Call}', '2017-11-27');
```

Great, `jsonb_set` will drill into our structure with the provided path and upsert the value we gave it. Well, maybe. While upserting, it's important to differentiate between the row we're trying to insert and the row we're conflicting with. In Postgres, this is done with a special keyword `EXCLUDED` or the dot notation seen above `users.likes`, respectively.

Wait.

That didn't work for you?

It didn't.

Know why?

In the `jsonb_set` function, we have to specify the path of the data we're updating. Since we could be referring to two different rows (the one in the table and the one we tried to insert), Postgres will throw its hands up and let you know there's an unresolvable ambiguity. Programming is fun, isn't it?

Don't worry, this line of thought was doomed from the beginning anyway. The function we relied on upon, `jsonb_set` follows a path, then tries to land a value there. Which doesn't work if the path doesn't exist, because you'd be associating data to a null key.

So, now we have to introduce a sub-SELECT clause to handle the ambiguity of the update and we have to build a path traversal clause by hamming together `coalesce` and `jsonb_set`. I'll skip the step-by-step approach and vomit that out here:

```SQL
INSERT INTO users (name, age, actions, likes)
VALUES ('Trey', 30, '{"signup-email":"fake@madeup.org"}'::jsonb, '{"shows":{"comedy":{"Good Morning Call":"2017-11-27"}}}'::jsonb)
ON CONFLICT (name, age)
DO UPDATE SET likes =
    (SELECT coalesce( jsonb_set( likes, '{shows, comedy, Good Morning Call}', '2017-11-27'),
                      jsonb_set( likes, '{shows, comedy}', '{"Good Morning Call":"2017-11-27"}'),
                      jsonb_set( likes, '{shows}', '{"comedy":{"Good Morning Call":"2017-11-27"}}'),
                      '{"shows":{"comedy":{"Good Morning Call":"2017-11-27"}}}'::jsonb)
     FROM users
     WHERE (name = 'Trey' AND age = 30))
```

In short: If you care about the structure of your data, use a structured format. In the name of never doing another join again, we've created an absolute disaster. Is this type of querying worth the flexibility when spinning up resources is so simple?

The multi-paradigm approach is awkward, especially in the case of a true update. As many Java developers can tell you, it _is_ possible for one framework/technology/language to support too many paradigms. In fact, any number greater than one is usually too much. Remember, using the right technology is better than using _the_ technology.
