---
title: Fixed my first bug this week~
date: 2018-03-17 02:24:31.000000000 -07:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- General
tags:
- CLI
- gerrit
- git
- JIRA
- Python
meta:
  timeline_notification: '1521253473'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '15807345971'
  _wpcom_is_markdown: '1'
permalink: "/2018/03/17/fixed-my-first-bug-this-week/"
---
I fixed my first software-related bug at work this week! Super proud of myself, and this blog post is dedicated to kind of how I did it using what I learned (on my own personal time). I pushed the fix to **gerrit** , a code review repository software that my work uses, and am waiting for feedback. I also wrote a couple more useful Python wrapper scripts that utilize some cool intermediate Python concepts that I recently learned.

Coming from a test engineer role, I typically report bugs and propose new potential features, and file these as **JIRA** tickets. Due to other more important action items in the current sprint,&nbsp; the developers that I work with did not have time to work on this issue; thus I took the initiative to fix this bug. Ironically, I had filed the **JIRA** bug that I implemented the fix for.

I do want to note that this bug was also a blocker to one of the tasks I had to do, but I was also genuinely interested in fixing it and enhancing my programming skillset.

In the fix and in the wrapper Python scripts, I used some cool intermediate Python concepts, including:

- static methods
- generators
- recursion

Also, I familiarized myself with **git** and **gerrit** , which is kind of a code review repository for collaboration among teams. I have a **GitHub** , which is another type of code review repository, so I was semi-familiar with some basic git commands and functionality.

From some research I did this week, **git** is a distributed version control tool for tracking changes in files/code/programs and for coordinating work on these files/code/programs among multiple developers, and **gerrit** is built and integrated on top of git.

For more information about&nbsp; **gerrit** , check out the this&nbsp; **gerrit** documentation:&nbsp;[https://gerrit-review.googlesource.com/Documentation/intro-user.html](https://gerrit-review.googlesource.com/Documentation/intro-user.html)

Some useful **git** commands for me to remember are:

- git log
- git reflog

Looks like the personal time I put in is actually paying off! Hopefully the code review goes well~

**UPDATE (March 30, 2018)**: I received a +2 on the **gerrit**  **code review** , meaning my changes to the API were approved and merged! I also received compliments regarding how "beautiful" and well written the Python code was from the manager and a software engineer working under him. However, one of my other commits (with the wrapper Python scripts) was rejected (review label value of -2), since the manager suggested that I implement these wrapper scripts into the **command line utility** , instead of separate Python wrapper scripts, which I quickly fixed. I implemented a recursion argument into the&nbsp; **command line utility** , and it is now one of the features of the API. That&nbsp; **patch set** with this new feature was also approved and merged into the API.

See here for more details regarding&nbsp; **gerrit code review&nbsp;** and the&nbsp; **review labels** :&nbsp;[https://gerrit-review.googlesource.com/Documentation/config-labels.html](https://gerrit-review.googlesource.com/Documentation/config-labels.html)

&nbsp;
