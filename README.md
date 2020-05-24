# task-yaml

This defines a standard for a concise task list for priorities and estimates, where users can:
- create and edit with easy-to-use tools (ie. a text editor)
- sort and do basic calculations manually or with programs
- represent basic dependency concepts -- namely, children/subtasks and blocking tasks
- share lists
- migrate to a more feature-rich or UI-rich tool when maturity and workflows require it

Specifications

- Format in YAML.
- For each task:
  - First is a priority number.  The higher the number the higher the priority.
  - Second is an estimate of effort.  We recommend using a power of 2 for number of hours, ie. 0 = 1 hour and 1 = 2 hours and 2 = 4 hours; note that 9 is basically 3 full-time months, too large to get a good estimate and should be broken down before being worked on.
  - Then comes the description.
  - That's basically enough to get started (and it covers the majority of my use cases).  How's that for a simple intro?
  - A nested list for tasks that are part of the current task.
    - Verbosely, the `!hasparts` tag marks tasks may be nested to show sub-tasks (which all must be done before the parent is considered finished).
      - The `!ispartof` tag is the reverse of `!hasparts` relationship
  - The `!blocks` tag marks tasks that cannot begin until that task is finished.
    - The `!awaits` tag is the reverse of `!blocks` relationship
  - If sharing, a name can show the person who is interested or assigned, and we like `@` for this.  (That conflicts with the todotxt.org convention for a context so we'll have to see how hard we want to fight that battle.)
  - Add any other feature from todotxt.org.
    - `+` can mark a project, eg. `+home` or `+yard` or `+family-reunion`.
    - `@` can mark a context though we like `#`, eg. `#home` or `#in-the-city` or `#family-reunion-2020`.  (todotxt.org standard is `@`.)
    - `x` can mark it done, if you want to keep it in the list.  (todotxt.org puts it on the front, which is fine if you're comfortable with alignment being off.  Use it, or move your task to the bottom, or delete it... whatever looks good to you).
    - `<key>:<value` adds other data that may help, eg. `created:2020-05-24`, `due:2020-05-31`, `repeats:monthly`
- For sharing or juggling multiple task lists, start the file with URIs.
  - This might not be a URL, and in such a case would need an external source to map to a location(s).

Example 1

```
---
- 98 1 install helmet
- &instruction
  75 1 add install instructions to README.md
- 90 0 install SSL:
  - 77 0 save to publicly hosted git
  - *instruction 
- 85 3 convert to UUIDs
- 80 1 create a link to the specific claim
```

Explanation of Example 1

- Line 1 shows a task of priority 98 and estimation of 2 hours and 1 hour.
- Lines 2 & 3 show a named tasks of priority 90 and estimation of 1 hour.
- The "install SSL" task has other tasks as part of it.  Note that this requires a ":" (colon) after the task description.
  - Inside that task a reference to the one with ID "instruction" (found earlier as `&instruction`).

Example 2, adding globally unique references

```
%TAG ! tag:taskyaml.org/schema,2020:
%TAG !group! tag:9d9bc93b-01f3-4efd-b003-36ec53f33d3b.prosperity-group,2020:tasks
--- !<tag:ee461452-d91c-42ff-9651-19a35e385037.trent.prosperity-todo,2020:tasks>
- 98 1 install helmet
- &instruction
  75 1 add install instructions to README.md
- 90 0 install SSL:
  - !blocks
    - 77 0 save to publicly hosted git
  - *instruction
  - !hasparts
    80 2 install haproxy
- 85 3 convert to UUIDs
- 80 1 create a link to the specific claim
- !group!#publicize
  70 2 reserve a domain
```

Explanation of Example 2

- The first line declares a `!` tag for the taskyaml.org schema.
- The second line declares a tag `group` that refers to the globally unique URI for another set of tasks which will be referenced, specifically for the "prosperity group".  See another example [here](https://yaml.org/spec/1.2/spec.html#id2782457).
- The third line starts this document and declares a default tag for it which will serve as a globally unique identifier.
See another example [here](https://yaml.org/spec/1.2/spec.html#id2761803).
- The "install SSL" tells that the second task "blocks" (and therefore must be done before) the tasks inside it (ie. in the sublist).
- The last task is marked with `!group` to say that it's part of the set of tasks from the first line, and it's found with an ID of "publicize" in that set.



Related Projects

http://todotxt.org/ (... [introducted by the founder of LifeHacker in 2006](https://lifehacker.com/geek-to-live-list-your-life-in-txt-166299) and [defended](https://lifehacker.com/why-i-get-more-done-with-a-plain-text-to-do-list-5743081) and [summarized simply](https://www.howtogeek.com/355890/every-to-do-list-app-sucks-switch-to-todo.txt-instead/) by others since then... yep, there's quite the rabbit-hole here)
- While this is great for personal lists, I want a couple more features to enable sharing with others and do some advanced calculations such as aggregate estimations; task-yaml is more project-centric from the start.
- One thing todotxt.org got wrong is putting too much variability on the front, so it's tougher to quickly parse out the description.  They also don't put the size of the task front-and-center, so it's harder to see relative sizes of tasks (though they've got a good point that the next item on each project should always be a small task).

Notes

- I'd actually prefer to have the references (eg. "instruction" in the example) NOT at the beginning of the node because they clutter the UI, but the YAML anchors really seem to be a good fit for this.  (An alternative is a custom key-value, eg `id:stuff` in the task.  Both are ugly.)

Remaining Task List for This Project
```
--- !<tag:taskyaml.org/tasks,2020>
- 70 2 parse & model in JSON
  - !blocks
    - 50 4 show graphical dependencies
- &calc-time
  - 60 4 calculate time before finishing, a la https://github.com/trentlarson/Schedule-Forecast
- 60 2 add teams (related to *calc-time )
```
