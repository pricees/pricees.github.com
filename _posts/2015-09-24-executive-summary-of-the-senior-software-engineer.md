---
layout: post
title: "Executive Summary of The Senior Software Engineer"
description: ""
category: 
tags: []
---
{% include JB/setup %}

What follows is a long set of notes and highlights from the book:

[The Senior Software Engineer](http://www.amazon.com/The-Senior-Software-Engineer-Practices/dp/0990702804) by David Copeland. This book is amazing. I finished the book feeling the same way that I did after reading [The Pragmatic Programmer](http://www.amazon.com/The-Pragmatic-Programmer-Journeyman-Master/dp/020161622X). 

99.999% of this is copy-pasta'd from the book. The only words that are mine are fat-fingering or when I got bored typing my notes and changed the words for brevity 

This amounts to about 30 pages of reading. Enjoy.

### 3. Focus on Delivery Results ###

- Everything you do should be focused on __delivering results__
- When someone sends you a problem, your reply should be a solution
  - Do not respond with an  email saying *"I'll look into it"*
  - Respond when you have __a solution__
- Result-oriented Approach to Work
  - Don't make __promises__ or __publicize your plans__
  - Delivery results and only results.

- Deliverables are not results
- Working, shipped software is what pays the bills and solves users' problems
- A result may not be working software but a __definitive answer to a question__

A __result__ is an artifact of direct business value: working code, documentation, definitive statements. Anything else _must be_ understood to be fundamentally different.

A __deliverable__ is a _product status report_ or _project schedule_. It shows progress but has zero business value.


Focus is "the act of concentrating interest or activity on something." Focusing on results means that you apply this focus to every aspect of your job.

Responding to an email with "I'll check on it" is a distraction from focusing on delivering results to the task at hand.

Focus involves keeping _promises_ to a minimum. Promising something now will eventually put you in a bind later.

This doesn't mean promises are the devil. Your company should know what you are working on and when it will be available but you can do this by __delivering small results, faster.__

Not reporting progress between deliveries will cause it to lack visibility.

Reimagine your project as a series of small results-driven deliverables. Reduce the scope of the project to the bare minimum needed to solve the business problem. Deliver that as soon as possible.

Turn a boring progress report into working, usable software

Promises of smaller value over shorter time are easier to keep.

**Story cards, sprints, and stand-up meetings on not ends unto themselves. If they are roadblocks to delivering results, they must go.**

### 4. Fix Bugs Efficiently and Cleanly ###

Even though bug tasks are small, they are important.

First, it's important to understand why you even _need_ a defined process. You want your head in the _problem you're solving_.

The Process:

1. Understand the problem.
2. Write test that fail (because the problem has yet to be solved).
3. Solve the problem as quickly as you can, using your tests to know when you've solved it.
4. Modify your solution for readability, conciseness, and safety by using your tests to make sure you haven't broken anything.
5. Commit your changes.

Understand the Problem:

- Avoid the "just start coding" mentality
- Think before coding
- Separate "getting it to work" from "doing it right." It's hard to do both at the same time.
- To understand the bug, reproduce it to see it in action.
- Take a quick trip through the source to see where the bug may live.
- __Maintain Focus__: Resist the urge to fix everything.
- If you cannot find a stakeholder or use production to replicate the problem, find the closest person to the project stakeholder that you can to walk you through the issue.
- If you see a fundamental, major problem with the software, first create a fix that will __"stop the bleeding"__. Then go back to the first step and fix the overarching issue.

Write the test that fails:

- Write an automated test, not a fix, that intentionally fails but will succeed when you've solved the problem.
- If you write the tests last, you won't truly know if you fixed the problem or wrote a broken test.
- Write the test _at the highest level possible_, this might be a UAT test, even when you know it's a _simple model-centric issue_.
- Treat your bug as _a bug in your application_ not a bug in a particular unit of code.

Solve the problem quickly:

- Write software to fix the problem as quickly as possible
- Focus on the problem and the solution
- Documentation, readability, etc are distractions, at this point.

Modify for readability:

- Look at the diffs of your solution and the code
- You should have made the __smallest possible change__ that fixes the problem.
- __Smallest possible change__ doesn't mean less lines of code, necessarily, but rather, the introducing the smallest number of new concepts
- Also remember:
  - No copy and paste of code (DRY it up)
  - Descriptive variable names
  - Coding style should match the code in the file (not the way you think the code should be written).
- Comments:
  - Comments should explain "why" the code is the way it is.
  - Look for eliminating the need for comments through cleaner code.
- Don't over-engineer and know when to quit:
  - Tempted to refactor other code in the file? _Dont!_
  - __Refactoring__ is restructuring an existing body of code, altering its internal structure without changing its external behavior. External behavior should be at the "lowest level" possible.
  
Commit your changes:

- Your commit message is a special kind of code commit. It's a snapshot of what you did in this code change that should serve as a marker for what happened and why
- The first line should be what the code is: "Fixed Bug in Menu," "Update Config in Carrierwave." Brief, but specific.
- Link the commit to the ticket in your project management system if possible.
- Detail any conversations: "Jim said it should work this way..."
- Explain your solution
- Don't explain your code

### 5. Add Features with Ease ###

The process:

1. Understand the problem
2. Understand the system
3. Create acceptance-level tests for the feature
4. Plan your implementation
5. Repeat the cycle from the "bug fix" process until all your acceptance-level tests pass
6. Get a code review
7. Commit your changes

Understand the problem:

- You should push hard to meet with a user or project stakeholder to talk through the feature
- Become intimately familiar with the documentation you've been fiven
- If given a user interface (UI) mockup, familiarize yourself with that
- Meet with stakeholder. Any new information should be documented and emailed to stakeholder as a "checksum" of what you both took away from the meeting.

Understand the system:

Developing a system view is part of _systems thinking_, which Wikipedia defines as:

>...an approach to problem solving, by viewing "problems" as part of an overall system, rather than reacting to specific parts, outcomes, or events and potentially contributing to further development of unintended consequences.

Ask questions:  

- What domain objects will this feature need to interact with?
- What business rules exist around those objects?
- What structures are in place to manage those objects?
- What new objects will you need to create?
- What is the test coverage like? Will you need to add test coverage anywhere before making changes?
- Are there any offline tasks related to these objects?
- Is there special reporting or auditing on the changes related to these objects, and should your new objects b a part of this?
- Are there non-functional requirements to consider, such as performance or security?

Create acceptance-level tests:

- Before you start work, you need to create an acceptance-level test of the feature you are building
- An _acceptance test_ simulates a user using the feature as closely as possible (i.e. the user is "accepting" that the feature works as desired)
- When you start coding, your focus will change from the high-level view you have now to a lower-level view of the code
- You don't need to cover every esoteric edgecase (although you should certainly exercise some), but you want all the "happy paths" to be covered end-to-end



Plan your implementation:

You aren't looking to generate some detailed publishable documentation. In fact, you "plan" might be developed entirely in your head. The point is to think a bit about how you're going to get this task done and create a mental roadmap of how it will work.

The best approach to getting a solid plan in place is to actually do that: discuss it with another developer. Find your nearest co-worker (or technical lead) and talk them through your approach.

_Resist the urge to publicize your plan_ for two reasons:  

- These tasks aren't the same amount of effort - it could be that most of your time is taken up by one or two of these tasks
- As you work, you will learn more about the system and the change you are making, which may change these plans. You may not need or want to update your todo list instead "riding the wave" of changes you encounter. Being beholden to others to follow your to-do list (or keep it up to date) is a distraction that takes your focus away from delivering results

Repeat the Test/Fix/Refactor cycle:

- Work in small steps
- Work clean (refactor)

Clean up as you go. This is what you want to strive for when you code. Once you have passing tests, you should stop and refactor. This might be as simple as renaming a variable to make it more clear, extracting complex logic into a private method, or even creating entirely new classes that simplify what you've done.

It's easier to write new code in a clean codebase.

Get a code review

Your request for a code review should have two parts:

- guidance from you
- diff of your changes

Type of feedback in a code review:

- Code is wrong 
  - contains bug
  - goes against the applications architecture
  - incorrect code conventions
  - implemented incorrectly due to ignorance of codebase
  - If code is right, but your review mis-interpreted it, then your "code is confusing"
- Code is confusing
  - No matter how clear it is, if it isn't clear to reviewer, it's essentially unclear.
  - Review may have suggestion but feel free to ignore. That the code is confusing is more important that the reviewings suggestion to fix it
  - Treat as a refactoring
- Style/aesthetic issue
  - If your team or company as a "house style" use it
  - If you think you should change the style, take it up with the team, don't go rogue!
  - If there is no set style, the style should be done in favor of whoever is going to be maintaining the code.
  - If it's a new concept, you decide the style, but don't go crazy

Commit your changes:

- Once your code is review, you'll want to prepare your commit.

Reporting progress:

 - Steps 1-4, your status is "understanding the problem"
 - Step 5, your status is "coding" or "in progress"
 - Steps 6-7, your status is "in review"

These three "statuses" should give enough visibility to how far along you are without giving any false impressions or hard-to-keep promises.


### 6. Deal With Technical Debt and Slop ###

Despite your best efforts, code can begin to _rot_ and become "crufty"

The reality is that sometimes sloppy code is just sloppy and could've been easily avoided. I call this _slop_, and this chapter is about understanding the difference.

### Slop: Code to fix __now__.###

You had to copy and paste a lot of business logic, your variable names aren't very good, and you've created a few new public APIs that are poorly documented and counter-intuitive. Should you ship this code? __Hell, no!__

The reason is that you can fix these problems before the code has a chance to infect the rest of the system.

Slop is also indicated by poor test coverage, missing error handling, inconsistent use of logging, or a lack of validation of assumptions.

### Technical Dept: Code to Fix Later (or Never) ###

_Technical Debt_ is a wonderful term, coined by Ward Cunningham, used to explain compromises in implementation that save cost now, at a larger future cost (just like real debt). Unlike slop, which is bad code we can fix immediately and quickly, technical debt is code written under certain assumptions that no longer hold.

As opposed to YAGNI, technical debt assumes we _are_ "gonna need it", but we're choosing to ignore that fact to meet a deadline.

Technical debt can  be a good thing.

Slop vs Technical debt is a clear distinction between "poorly written code" and "code that makes  a trade off" Technical debt is a way to delivery results when they're needed, making tradeoffs in a controlled way.

Use comments like "# TECHDEBT:` so you can run static analysis tools on the codebase and see what technical debt you've incurred.

We want developers reading this code to immediately see that, not only is this code technical debt, but_why_ the debt had to be incurred.

### 7. Play Well With Others ###

The company's Chief Technology Officer (CTO) will be incredibly knowledgeable about technology, but will lack the context to understand the issues at hand (this is not part of their job).

The first step is to empathize with your audience. If you can feel what it's like to have to make a decision without detailed information, it can go a long way to communicating such information yourself. Next, you'll need to distill what you know in a way your audience can understand

This is the position your managers and other company decision-makers are in. Not only do they not know how to do what you do, but they often don't even have the vocabulary to know what to ask for.

When communicating:

- Avoid technical jargon of your own. At best, they don't understand. At worst, they think they understand and you miscommunicate
- Listen carefully to the words people use and ask questions if you aren't 100% sure what they mean. You may feel silly, but you will feel sillier delivering a shit product
- Don't "talk down." Ever. Period.
- Don't be afraid to use longer descriptive phrases in place of acronyms and jargon.

Abstracting concepts to simplify them:

When explaining a new concept, you want to keep things simple and straightforward. Start out slowly, painting broad strokes. __Avoid edge cases, at least, initially, as they can create confusion and distraction__. Often the other party will ask about edge cases, this is good, it means they are understanding you.

Tips on abstractions:

- Avoid technical details
- Explain things using analogies; don't worry about precision.
- Use diagrams, visual aids, or demonstrations where possible
- Always offer to provide more details
- If a question has taken you off course, spend a few seconds re-establishing the context of your discussion: "OK, so we were talking about foo..."
- Be prepared to "justify" your position if challenged. Do this by gradually increasing the level of detail. This is especially important if you are delivering "bad news."

Communication isn't easy. Don't be afraid to stop and regroup. It's better than solving the wrong problem.

### 8. Make Technical Decisions ###

Identify Facts:

_Facts_ (more formally, "propositions") are pieces of information about the topic of discussion that can be verified:

- "Java is a compiled language" is a fact
- "Compiled languages slow down the development process" is not a fact

Identify Priorities:

_Priorities_ are a list of what's important to someone, either in general, or with respect to the decision at hand.

In a sense, priorities themselves are facts and all we're doing is using inferences to draw conclusions, however I find the distinction here useful. Facts, in the way I'm describing them here, are about _things_ whereas priorities are about _people_.

Weigh your priorities and others priorities appropriately. You're priority is to make the system fast, your bosses priority is to move people quickly between projects. You give your boss a fact: "Go performs better than Rails in key performance metrics." You boss gives you the fact: "most of our projects are written in Rails."

Draw Conclusions:

Hopefully, you'll have identified facts that speak to everyone's priorities and lead everyone to the same conclusion.

Fallacies:

- Hasty Generalization: Arguing from special case to general rule. "I worked in Clojure once, it was awful, therefore Clojure is awful."
- Strawman: Misrepresenting facts and then arguing against them
- No True Scotsman: "No sane programmer uses threads"
- Composition: Assuming that the something true about the parts will apply to the whole
- Correlation does not imply causation
- False equivalence: concluding two statements are true because they share similar aspects
- Appeal to Authority: "Linus Torvalds says C++ is horrible, thus.."

Document the Decision-Making Process:

Once the decision is made, and you are moving forward, I find it useful to jot down _why_ the decision was made. Use a wiki, perhaps. Nothing grandiose or formalized, just _facts_ and _priorities_. 

This document is useful for two reasons:

- It can prevent someone else from going through the trouble that you've just gone through.
- It keeps you from having to remember the details when, six months from now, a new team member asks why things are done a certain way


### 9. Bootstrap a Greenfield System ###

When given a greenfield project, where there is literally no source code, you have two main goals:

- Make sure everyone involved in the process feels good about the initial direction and decisions being made
- Establish a location for developers to start contributing code

To meet the first goal, apply the powers of _working with others_ and _making technical decisions_.

Regarding the 2nd goal, call it _Iteration Zero_. Iteration because you will work in small increments; zero because it has no business value

At the end of _Iteration Zero_, you will have a deployable application.

The process:

1. Understand the problem
2. Understand the system's place in the existing technical architecture
3. Choose the technology
4. Outline application architecture
5. Create a deployment checklist
6. Build and deploy a minimum deployable system
7. Add features, deploying after each one is complete

Understand the problem:

To understand why the application should be build, find answers to these questions:

- What business problems will this application solve?
- Why is the proposed application the best solution to those problems?
- What other solutions were considered?

Understand the system's place in the technical architecture:

Unless this application is the first one ever being built at your company, you will need to account for existing applications and existing infrastructure, otherwise known as the _technical architecture_.

Your first step is to get a systems view of the technical architecture.

Hopefully, there's documentation around that explains this. Otherwise, __for every system or application__ write out:

- What is it called?
- What is its purpose?
- What technologies does it use?
- Who is the point of contact for it?
- Which development team is responsible for it?
- What other applications does it depend on?

With this list, you should now be able to see where your new application fits.

- Will these systems need to change to accommodate yours?
- If so, how will these changes be made? Do you need to coordinate with the other team, or can you simply make the needed changes yourself?
- Will you need to run these systems in development to run your application or is there a shared development instance you can use?

Most companies has a __"blessed stack"__ of technologies that are used for most applications. 
A set of tools and technologies may see bureaucratic but there are good reasons for this: 

- developers know how to use it
- operations team knows how to deploy and monitor it
- entire team has sense of what does/not work

_Using a blessed stack opens you up to the widest possible array of expertise: your co-workers._

If the blessed stack won't meet your needs, identify what will. You will need to answer the question: __"Why _shouldn't_ I use the blessed stack?"__

How to evaluate technology:
 
- Fitness for purpose: Is this technology a better fit than the blessed stack?
- Performance: Does the blessed stack meet performance needs?
- Developer Productivity: After initial pain of setup and deployment, will productivity gains outweigh up-front costs
- Developer Happiness/Staff Retention: Will our team love it? Will it turn boring tasks into fun adventures?!

Using new technology comes down to two concerns:

- The programming language
- The deployment model

The best way to introduce new technology into your repertoire is to find a low-risk project to use as a trial.

Introducing new technology?

-Often, what the conventions are or how to decide to do something don't matter nearly as much as having _decided_ on them. Here are some common examples:

- Where source code files should live
- The build tool
- Testing framework
- The HTML templating language
- Naming conventions:
  - Primary keys in a db
  - Class names
  - CSS classes 
  - URLs 
  - Support files and directories

It's also worth investigating tools that can "enforce" these conventions.

Establish a "culture of consistency." Each developer should be encouraged to establish a convention when they first face the need to have one.

Create a deployment checklist:

All the stuff orthogonal to the code that go along with it:

- Access to production database
- Logging and log aggregation
- Authentication/Authorization
- URL or other locator for your app in production
- Analytics
- Crontabs or offline scheduled tasks
- Health check monitoring
- Security scans
- Versions - operating system, programming language, system software

Once you have the checklist that addresses everything, deploy a basic application: _the minimum deployable system_.

The list? The only way to truly address them is to actually deploy your application to production.

By "deploy" I don't mean" make available to users." Password-protect or secure this. To "launch" is to make available to users.

Make the deploying of the minimum deployable system __fully automated__.

### 10. Learn to Write ###

Your writing is:

- a permanent expression of your intent and ideas
- a reflection of you as a developer

Three steps to being a better writer:

You can improve your writing immensely by breaking any writing tasks (even emails) into three simple steps:

1. Get it down
2. Revise it
3. Polish it

Analogous to the red/green/refactor code cycle.

Write it down:

Don't worry about spelling, grammar, formatting, or anything else. Just get it down.

The point of "getting it down" is to reduce all barriers to starting to write. You need words flowing out of your hands as quickly as possible.

Revise it:

Revising is the act of reading what you've written and rewriting some (or all) of it to make it easier to read.

Think of this as refactoring your writing.

Beyond correcting grammar and spelling mistakes, you want to aim for brevity and specificity. Your writing must focus on delivery results, so the quicker you get to the point, the easier it will be for readers to get the answers they seek.

Suggestions:

- Instead of using "this" "that" "it" or "they", use specific names
- Name objects, concepts and procedures specifically. "Thing" is a last resort.
- Avoid acronyms, shorthand and jargon.
- Organize thoughts into paragraphs.
- Write as if the readers are getting more and more rushed for time as they read.
- Organize "calls to action" up front to ensure that the busiest reader will get the point of what you're doing.

Polish It:

the art of typography is a deep and mystical one, but its purpose is to serve the written word, making it easier to read without altering its meaning.

For our purpose, this means appropriate use of fonts, boldface, headings and bullet lists.

- Code should be fixed width
- Use lists liberally
- Use headings if document is long
- Make hyperlinks clickable

### Write an Email ###

- An email typically results in the recipient having to do work they otherwise wouldn't have had to do
- Email notifications pop up when email arrives, interrupting other work
- Few people take the time to craft clear and direct messages, making emails awful
- Most people want to stop reading email as soon as they can. Help them! By getting to the point.

Additional Tactics:

- Ask for __only__ one thing.
- Whatever it is you want, write that as the subject in the form of a question
- If you need a decision between options, give them short easy names like "Option A" or "Option B"
- If you plan to take an action and which to inform others, or get an "OK", _word your email as something you plan to do unless there are objects_.
- Consider a basic email signature, unless your company has some action-packed mandated template.
- __When fording a long email chain__, don't just write "Thoughts?", __summarize__ what's being discussed and what you'd like from them.
- For a point-by-point response to a long email or chain, __color and bold__ your response in-line with the original. It may seem garis, but it';; be a lot easier to recipients to read.

### Write System / Application Docs ###

Developer documentation for systems, libraries, or applications serves several purposes:

- It instructs developers on how to setup and use code
- More details on getting startup
- Pointers to API docs
- Troubleshooting

The __README__ is a time-honored place to put documentation.

Structure your __README__ like so:

1. Intro - summarizes the software for quick reference
2. Overview - detailed explanation of what the code does and why
3. Setup instructions
4. Other references

Intro:

- The first line sums up what the app or system does
- Next, how to install it
- Get the devs understanding and running the system ASAP!!

Overview:

Here you provide more details about what the software does and why it needs to exist. You want this to focus on _why the software exists at all_. Even it it's just one paragraph of explanation, it can have a huge effect on how your software is perceived and used.

Setup Instructions:

Next, you should provide a _detailed_ list of setup instructions.
If the setup instructions are long and detailed, try to break them down into sections. Mindless execution of instructions can lead to distractions. Adding sections can give a high-level idea about what subsequent tasks with do.

> Setup for App Foo:
>
> 1. Get access to version control and other systems
>
> 2. Check out a copy of the code
>
> 3. Compile, test, and run the code
>
> 4. Validate access to other systems

Other references:

Finally, you want to provide links to other information, such as:

- API Documentation
- Contact info for questions/support/issues
- More detailed documentation if it's available
- Documentation for developing the software, as opposed to using it

If you cannot put your documentation into the README file or part of the app or system, create a space to do it. A doc's folder, wiki, or some other organized space.

### Write API Documentation ###

API documentation is documentation for how to use the publicly exposed bits of code that other developers might need.

The way I like to approach this is to assume that the reader won't have easy access to the source code.

An API can usually be documented at two levels: the module and the function.
Modules group functions together, and allow the user to go from generic to specific on their quest for the function they need to solve their problem at hand.

A module's documentation needs to do three things:

- Easily explain what types of functions it contains
- Provide pointers to commonly used functions or some other starting point
- Show example or idiomatic usage of the module's common functions

After that, fill in the documentation as needed to describe common usage patterns, along with links to functions and other relevant documentation.

A function's documentation needs the following:

- The first line should summarize the purpose of the function
- If required, expand on the function's general behavior and reason for being
- If the function takes arguments, document each one in the same manner
  - Type
  - Purpose
- Document the return value, type, null if possible
- If the function is complex, document the different ways it may be called


### Practice Writing ###

- Start using Twitter - It's short length requirements force you to be brief
- Add documentation to an open source project, many are sorely lacking
- Write a blog - write short blog entries about your experiences at work or other thoughts on technology.

### 11. Interview Potential Co-Workers ###

> Your job is to identify developers who can work 
> well with you and your team. You need developers 
> who can deliver results, just like 
> you do. And it's difficult.

Ideal Tech Interview:

1. Informal technical discussion to meet and get to know the candidate
2. Have the candidate do a "homework assignment"
3. Conduct a technical phone screen
4. Pair program with the candidate in person

Create a grid with two-axis
Along one axis is the candidate - how do you know them? The second axis is the organization's need - the role to be filled. 
How much impact will this person have?

Informal Tech Discussion:

Informal geek out about anything, but cover:

- What your company does and why anyone would work there
- What problems the company has that the candidate might solve
- What the candidate might expect to work on
- The candidates thoughts on technology in general, but also related to what your team uses and what the candidate is familiar with
- Interesting problems the candidate has solved and what excites them about the position
- Any questions the candidate has about the company

For candidates  you know, this is crucial. It's also a good chance to "sell" the candidate on your company without starting a formal interview process.

Homework Assignment:

Be specific about anything you want the candidate to do, but keep in mind that every omitted detail is a chance to learn something about how the candidate thinks.

I would recommend a tech phone screen, as well.

Have a rubric for evaluating the work. A rubric makes sure you check everything and don't over or underweight aspects of the solution.

Candidates know that home assigns are for vetting and "weeding out." Give one to someone you know has the chops is a waste of time. And could be insulting.

A technical phone screen ensures that you keep expenses to a minimum. Weed out the bad before sinking time and money into an in person interview.

A candidate you know, and has the chops, should be fast-tracked.

The interview:
- Technical questions
- Discussion of the homework
- Questions the candidate has for you

Tech Questions:

Basic knowledge a programmer should have:

- Write some code for a simple problem
- Talk about software design
- Demonstrate knowledge of scripting languages
- Demonstrate knowledge of data structures
- Demonstrate basic understanding of bit manipulation

As you talk, write down the candidates answers, not just "got it", "missed it."

Discussing Homework: 

When discussing the home, make sure to praise anything you liked.
Keep it casual and avoid any divisive arguments. Your goal is to understand how the candidate thinks when solving a problem, not to prove that your particular solution is the best.

Questions for the company:

If they did exceptionally well, let them know. If not, don't say anything.


In office, pair programming:

In a startup, spend over an hour on a real problem. For larger teams, a simple 30 minute problem may do.

Its __real world__, baby! allow them to ask questions, search the googles, etc.

Making the decision:

Let everyone get comfortable talking about the candidate and weighing in on how they performed.

Next, call for a vote. Everyone should vote either"hire" or "no-hire" and everyone should vote at the same time.

Any "no-hires" should voice concerns. Any "hires" should be given the time to persuade the "no-hires." It should be unanimous to hire someone.

__Read the book on more types of interview questions and ish!__

### 12. Be Responsive and Productive ###

You aren't the only person who has work to do, and delivering working software isn't the only result you're expect to deliver.

You have to be _responsive_, without sacrificing your productivity.

By eliminating all "distractions" and just focusing on coding, you will not build trust or engender confidence. You are a senior engineer, you are not allowed to go into a nerd hole and not pop out. Sorry bub. Not happening.

As a technical leader you must be available to others.
Divide responsibilities. "Shield" other programmers _on the ground_ from the stuff that's flying up above. My first boss use to call himself a "shit umbrella." When he was let go, the shit hit the team and one by one we all left.

The key to making this work is to invert your relationship with your "distractions" like email and instant messenger. Instead of letting them interrupt you, pro-actively schedule time to check them, instead.

__Interestingly__, researches found that if a programmer can plan for an interruption, that programmer will resume their task much more quickly after the interruption is complete. Find a "good breaking point."

Stop _polling_ your inbox. Check your email once an hour or some kind of pomodoro time stoppage.

You'll note that during times where we're thinking and writing code, either writing a test or making a test pass, we can't afford an interruption. But , once we've gotten to a known state, either a failing test, a passing test, or a successful refactor, we have enough context in our tests and code that we can stop and check email or instant messenger.

Instead of discussing an SLA in an abstract sense, I'll outline my personal SLA to give you a sense of how it might work. You can adjust this as needed. Note that I don't explicitly advertise this SLA to others.


Manage email effectively:

- Separate reading and responding
- When you've responded to an email, move it to another folder.
- All email read once per hour during normal business hours. _Read_: not responded to.
- Urgent emails (by my assessment of urgency) get a response within an hour, typically during the hourly audit.
- All emails requiring a response get a response by the next business day.
- If I cannot provide the requested information in one business day, I'll respond with an estimate of when I can, and then schedule time to find the information.
- All information requested is provided within five business days.

Managing Interruptions:

Be it instant message or a tap on the shoulder, an interruption is going to be disruptive.

Dealing with this:

- first send reliable signals as to your availability
- shunt these interruptions through email whenever possible

For physical contact, you want to send a signal that are you in the middle of working. Put on headphones (even if you aren't listening to music), it's a subtle signal. Just be sure to take your headphones off or open your door when you aren't deep in the code. A pattern of both availability and unavailability will make each a more powerful signal.

Ask "is this urgent?" Take their word for it, whatever the response. If their request isn't urgent, politely ask if they can send the request in email.

### 13. Lead a Team ###

Eventually, you will be less responsible for these sorts of tasks and more responsible for getting a team of developers focused on a common goal. This is not middle-management, this is a technical lead.

the technical lead is exactly what it sounds like: a leader in the realm of technology.

The difference between general managers and tech leads is that tech leads _write code_.

Never forget your goal is to __delivery results__ _yourself_.

Being the team lead means understanding that your participation in non-coding activities is of greater value to the team than your ability to add new features or fix bugs.

Your involvement in a project could be any one of the following phases:

1. Starting the project
2. Delivering the project
3. Enhancing the project and fixing bugs
4. Leaving the project

Starting the project:

To get the project started out on the right foot, you'll need to consider the following things:

- The business problem being solved
- The team members 
- Delivery schedule
- Delivery process
- Bootstrapping the application

### The business problem being solved ###

- The better you understand the business problem, the more impact you can have.
- A good proxy for being an actual expert is to identify measurable aspects of the business that this project will affect.

### The team members ###

The size and scope of your project may make this untenable, but I would still recommend a small team initialize: no more than three developers.

The following factors are good indicators of productive team members:

- Recently shipped a successful project
- Writes code driven by tests
- Excited or knowledgeable about the technology
- Has significant experience

### Delivery Schedule ###

- First, it's unrealistic to have a definition of what the project should do at any level of specificity: you have no definition of "done" 
- Second, without a team or technology choice, even if you _did_ know what "done" means, you don't know what you have to work with to get the project delivered
- Identify any hard deadlines: a common example is the timing of a feature or produce release with a marketing campaign

What's important to understand is that an arbitrary date chosen by an executive or project manager is _not_ a hard deadline (although you should consider the fact that said executive is privy to information you are not; try to find out before dismissing arbitrary dates)

Prioritize list of features. Features should be hard-ordered such that there is exactly one feature with highest priority, one with the second-highest and so on.

Every feature required by a particular date should be prioritized higher than those not required by that date.

Promise to re-evaluate the schedule on a regular basis, based on work completed, and the stakeholders promise not to hold you to original dates if reality gets in the way.

"Nine women cannot make a baby in one month.:

### Delivery Process ###

- Rather than reacting to a stakeholder's need for status, you must be proactive in showing them, what you and your team have accomplished.
- Recommend a regular demonstration of what the team has accomplished. It should be weekly or more frequent.

Deliver the project:

You'll need to shepherd the team through the development process, ensure that the conventions and policies the team has established are being followed.

You can accomplish much of this via a short, weekly meeting with the team and stakeholders

Weekly Team Meetings:

- Have a regular weekly team meeting. It should be no more than 60.

Monday morning:

1. Review/demonstrate features delivered since the last meeting. __You should do all the demoing yourself__, even for features you didn't build. Keep the demos extremely short and very tight.
2. Each developers spends 30 seconds explaining what they are working on explicitly acknowledging that if are (or are not) blocked on completion. _Do not discuss specifics during the meeting_.
3. Update the delivery schedule
4. List the next several features in the priority list that will be worked on. Confirm priority with stakeholders
5. Finish with any general announcements or other team-wide info.

Any detailed discussions should be taken "offline"
In particular, if certain features won't be available by the hard deadlines in the schedule, you need to have that discussion outside this meeting.

If additional team members can help, say so, but if that cannot, make that clear.

Do not promise to "try"  to meet the deadline anyway.

Remind the stakeholders that they agreed to consider the original estimate inaccurate in exchange for regular, honest feedback.

### Code Reviews ###

- Create a pull request off the feature branch
- Reviews are notified and examine the code
- Code author should respond to issues and discussion to make changes if needed
- Merge after everyone's happy.

As a review your goals are:

- Identify bugs
- Identify obvious security and performance issues
- Code cleanliness
- Verify adherence to, or establishment of, consistent style guidelines

Code reviews aren't just meant to increase the quality of the code, they are also a great learning experience for everyone.

Read the entire changeset before commenting on it:

- Often, some issues are not as serious
- If developers overall approach is flawed, don't waste time writing line-level comments
- It's crucial that you are constructive and instructive
- Do _not_ do this as a "command" but rather a suggestion
- Make the comment about the code, not the coder
- __If you don't understand something, ask__.
- If the code author writes some code that adheres to the guide, but just "isn't how you'd do it", resist the urge to comment
- If there is a disagreement about style, you should err on the side of the developer who will be maintaining the code
- __Don't be afraid to have a real-time conversation__
- If someone is writing code review comments that are too person or adversarial, it needs to stop immediately

Working with QA:

- QA should not be finding bugs! TDD FTW!
- They should use the entire system as a user would
- They should find bugs you and your team didn't anticipate
- QA, as a gatekeeper, should not hold up a release for _every_ bug
- Unless your team produces low-quality work, it's far more efficient to release the code as-is

Security:

Basic security should be considered in everything you do.

Done:

When the project is "done" it enters maintenance mode.

### Enhance the project and fix bugs ###

Sometimes, the application is considered "feature complete" and a small team is assigned to fix bugs and perform security updates.

After the application launches, get together with the project stakeholders and the QA lead assigned to your team.

Push for no less than a weekly release and agree on an "emergency push" procedure to allow for immediate release of important bug fixes.

You will also need to decide if QA i will act as a gatekeeper for new features and fixes.

Eventually, either to work on a new project or for a new company, you'll leave the project.

### Leave the Project ###

Your last duty as team lead is to leave an ongoing project in capable hands.

Your job is to pass that information onto a new lead and publicly acknowledge the change in leadership.

Schedule a one-on-one with the new lead to go over what they'll need to know. Ask them to review all available documentation beforehand,such as the coding style guide, delivery process, and other system documentation. Answer any questions they have about this information.

Next, you want the new lead to be aware of any current or potential issues with the team or application.

### 14. Conclusion ###

Much like the conventions we establish when creating a new application, how you approach being a senior software engineer isn't nearly as important as that you __do__ approach it.

### 15. Engineer as Product Manager ###

They know what to build and they know how to build it.

Knowing what software to build requires specialized knowledge beyond software development.

On one side, you have developers who are great at building software. Project managers identify the right problems to solve and developers write software to solve them.

Whatever decisions they make, it's the _developers_ who will deal with the consequences of those decisions.

They tend to propose solutions that:

- are easy to build and maintain, even if they don't fully solve the problem
- are more interesting to work on than a more optimal solution
- use new technology, putting the solution at risk, also known as "resume-driven development"

I've done all of these things myself and seen talented programmers do the same.

Developers proposing solutions without a solid understanding of the problem being solved are not acting like partners; they are acting like ticket-takers.

A "developer-as-product-manager", even when working with a dedicated product manager, will be "building the right things".

Instead of being pressured to deliver something quickly, a _trusted_ team can have honest conversations about delivery schedules.

On team purpose: Connect what your team does directly to the goals of the organization.

On people: find out about what you're doing and why. Talk to people involved about how they work and how they make decisions.

Whatever you do, start asking yourself (and others) _why_ you are building what you are building. What problem does it solve? What other solutions were evaluated?

### 16. Production is All That Matters ###

You software running in a production environment is the literal results we've talked the entire book about how to deliver. How you software runs in production is almost __all that matters.__

Much like the divide between product manager and programmer, there is inherent inefficiency in having one person write software for another to support in production.

Devops:

The operations team provides a reliable environment in which the app can run, but it's the programmers that must support the applications _behavior_.

To get there requires a simple four-step process, based around _production errors_. A production error is anything unexpected that happens in your application.

The four steps are:

1. Understand why handling production errors are important
2. Detect production errors
3. Fix production errors
4. Avoid production errors

Understanding errors:

You may think that a few missing users won't matter in the grand scheme of things. Aside from being bad customer service and a poor corporate policy, it's also foolish and lazy thinking.

You must begin to make judgment calls. Which production errors _matter_? How many failures are "too many"? How much loss of revenue/increase in cost is OK for the engineering team to create before something needs to be done?

_All_ failures matter, _one_ is too many, and the engineering team should not tolerate loss of revenue or increased costs due to software issues. Engineers are paid a lot of money to solve problems and should treat losses in revenue caused by their solutions seriously.

A culture of fault-tolerance means thinking a bit harder about what you're doing than maybe you would otherwise.

Detecting production errors:

Log every request and what the response was, along with anything else that's relevant to what went on during the request. Log edge cases in your logic, warnings and other events that are outside the "apy path". You might also log the results of complex calculations whose results are important.

Alerting:

__An alert is a push notification that an event requiring action has occurred.__

- _a push notification_ - you get an email, sms, page, something you can't ignore.
- _an event has occurred_ - something has happened that shouldn't have
- _requires some action_ - because of this event, you must take action or loss of revenue/increase in cost will occur. This implies that non-actionable events should not generate alerts.

For proper alerting, you need three things:

- A system to manage sending and cataloging alerts
- A way in your application code to trigger an alert
- A way to trigger alerts from outside your application code

__Do not ignore alerts__ If you are filtering out or ignoring alerts, you are not doing your job.

Fixing Production Errors:

Production errors must be addressed immediately.

- It eliminates any judgment from your remediation procedure
- By allowing production errors to interrupt your workflow, you become motivated to write more fault-tolerant code
- If the error is continuously happening drop what you are doing and fix it.
- If the error is a "one-off" and has a clear remediation procedure (rerunning the task), perform the procedure immediately.
- If the error is transient:
  - Is the state of the system invalid? (bad data?)
  - What's the user's experience?
  - Can the code that broke be enhanced to avoid this problem? If so, do it.

The theme here: restore service by putting the system back into a valid state. 

Avoiding Production Errors:

Networks are flaky. Third-party APIs have bugs. Eventually, you'll come to realize that flakiness and intermittent are normal.


The key to fault-tolerant code is to change your definition of "normal" Many developers incorrectly differentiate the so-called "happy path" from error flows.

There are three possible results from your code and they are all "normal":

- The user did what was expected
- The use did something unexpected
- An external system did something unexpected

It's the last result that you have to now consider normal. What happens when you lose network connectivity? What if the database goes down? What if the file system fills up?

It's somewhat of an art form to determine how much "paranoia" to apply to your code.

Here are rules to take extra care:

- Network connections to 3rd-parties should be assumed to be flaky 
- Responses from 3rd-parties should be assumed to be garbled, unparseable, or invalid at least 10% of the time
- Never ignore return values or status codes - log them
- Design your internal APIs to make fault-tolerance easy to add later. "Success: true" in a json response is almost always wrong
- When you log a warning or an error, it should include the context

Fault-tolerance is about _balance_. Don't go crazy. Think. Observe.Know your system and strike a balanced approach.

## 17. Responsible Refactoring ###

Emboldened by tests, with the words ":ruthless refactoring:" in my head, I used to "improve" the codebase I was maintaining at a previous job. One day, my "cleanup" caused production to break.

I was being irresponsible

Refactoring code that's in development and not currently running on production is something you must absolutely do.

A __pure refactoring__ breaks this rule completely-a refactoring adds no direct business value.

You can't predict the future. Suppose that for the next six months, no requirement surfaces, nor bug is found, that requires modifying the code you refactored for nothing. Was lack of cleanliness _really_ a problem?

The cleanliness of a piece of code is only relevant when we need to change it.

Clean when you need to change:

Part of this plan involved examining the code we need to change. Such a plan could take one of two forms:

- Plow through, making the change as expediently as possible, changing only what's needed to ship.
- Clean up or improve the code in a way that makes the change easier, then make the change.

And __this__ is how you improve your codebase. If cleaning up the code _enables you to work faster_ implementing a feature you aren't dreaming up but _actually_ have at had_, refactoring shipped code is the way to go.

Know when refactoring will help:

- Changes to public APIs - function, method, module, or class names - are almost never worth it.
- If you have to change an existing test, it's probably not worth it
- If you'll be deleting code, it probably __is__ worth it.
- If you are unfamiliar with the code, be wary of "cleaning up the code."


