---
layout: post
date: 2021-11-18
title: "How To Be A Kind Engineer"
permalink: how-to-be-a-kind-engineer
categories: engineering
---

As I've worked in tech, I've come to appreciate when engineers are considerate of other engineers.
(If you're a junior engineer reading this, I implore you to do all this and you will stand out)

Here are a few things that in my mind, make an engineer "considerate":

## Meaningful Git Messages

To me a meaningful git message is something that has 2 (possibly 3 things).
Below is an example:

`[BET-335] Implement caching on expensive user operations`

Here are two things:
1. The message has the ticket id (`[BET-335]`) which you can refer to for more details on the work.
   This can be for example the unique jira slug of the ticket. **Always include it**

2. A clear concise message that says what this commit contains and the purpose of the changes

3. Not shown here, but in commit messages you are allowed to add extra text to describe more in detail
   about the changes made.

For better guidance, read this which was my starting point: https://chris.beams.io/posts/git-commit/

## To The Point Messages

One thing that I greatly dislike is when someone sends me a message via slack with nothing relevant or missing information.
eg,
`hey!` (followed by more lines entered one by one)
`page is not working` (very vague, which page?)

I'm pretty sure everyone has seen the examples above from someone in their company.
Not pretty nice, and greatly distracting if I'm in deep thought.

Here's how I approach (still figuring out what I like best but here's what I got so far):

```
Hey PERSON, I'm working on JIRA-123 ticket and I've ran into some roadblocks with the database.

Problem:
DB is not populating from fixtures

What I've tried:
- Restarting DB
- Walking in circles
- Make a sacrafice to the gods

Ask:
Can we pair up once you are free or can you refer me to someone who might know the fix? thx!
```

This is generally the format I go with, give everything in one go, with different sections and info.
I find it easier to read and would love for someone to send me messages like this. (No one has -_-)
Also the jira ticket is usually linked to the actual ticket for easy access.

For some guidance, ideas on messaging etiquette read this: https://slack.com/intl/en-gb/blog/collaboration/etiquette-tips-in-slack

## Always lint/format before push

I'm surprised when I see commits/PRs failing because they didn't pass the linter/formatter check.
This can be easily avoided if we all ran our linters before commiting (git hooks anyone?)
These tools are here to make it more readable/easier to digest when reviewing.

Also, if you have something like github that comments wherever linter/formatter violations occurred in your PR,
then it gets really hard to review with a wall of linter errors in the way

## Document your own PR

When creating a PR, please:

- Properly describe what your changes are doing (Write as much as you need)
- Link to the Bug tracker issue
- How to test your changes (if applicable)

For your PR code, please take your time to review and:
- Ask any questions you have, don't wait for someone to point them out or hope they don't catch it

  If you were unsure on the changes you made (eg not sure if calling expensive func or something is a good idea)
  then please comment about asking for feedback. Don't expect someone to catch and start a conversation, or even worse
  your changes go out to production and break something, all because you didn't take the initiative.

- Highlight any relevant parts of a file

  Sometimes we have 'noise' in PRs such as indentation changes which mask the real purpose of the changes.
  When this happens I usually comment in the top of the file `A lot of noise due to indentation changes, please see comments inside file for relevant changes`
  Then I go to each of my actual changes and say `relevant bit, doing x,y z here`

## Document your (and someone else's) code

When a codebase grows to a certain size, its hard to maintain, especially with all the due dates and what nots.
Cause of that, I've come to love/hate a few things but for now I'll mainly talk about comments.

*Love them when they are right.*

**Hate them when they are missing, or even worse, when they have the wrong information.**


Thanks to this, I always try to make sure I document anything I'm working on that isn't obvious at a first glance.
**And to update wrong or missing comments.**
I've been bitten before from being to trusting at what a piece of code does from just reading the comments,
and then I'm stuck wondering why its not working. Please comment your code, and update any outdated ones that you see in the wild.
Other devs and I will thank you in spirit.

## Be considerate
Everyone should strive to be kind.
