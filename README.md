# task-yaml

This defines a standard for a concise task list for priorities and estimates, where users can:
- create and edit with easy-to-use tools (ie. a text editor)
- sort and do basic calculations manually or with programs
- represent basic dependency concepts -- namely, subtasks and blocking (ie. synchronously preceding) tasks
- share lists publicly or privately
- incrementally migrate to a more (or less) expressive schema
- migrate to a more (or less) feature-rich and/or UI-rich toolset


Specifications

- Format in [YAML](https://yaml.org/).
- Each task is in a list, with each line beginning with `-`.
  - A priority number is first.
    - The higher the number, the higher the priority.
    - Lists often start without any organization, so this is optional: if missing, it has the same priority as the previous task; if this is the first task then the priority is 99.
  - An estimate number comes next.
    - We recommend using a power of 2 for number of hours, ie. 0 = 1 hour and 1 = 2 hours and 2 = 4 hours; note that 9 is basically 3 full-time months, too large to get a good estimate and should be broken down before being worked on.  Smaller increments could be negative, ie. -1 = 30 mins and -2 = 15 minutes.
  - The description is all the rest of the line.
  - That's enough to get started (and it covers the majority of my use cases).  How's that for a simple intro?
  - Add any other feature from [Todo.txt](http://todotxt.org/).
    - `+` can mark a project, eg. `+home` or `+yard` or `+family-reunion`.
    - `@` can mark a context, eg. `@home` or `@in-the-city` or `@family-reunion-2020`.
    - `x` can mark a task done, if you want to keep it in the list.  (todotxt.org puts it on the front but that throws off the alignment.  Use `x`, or move your task to the bottom, or just do what I do and delete the thing... whatever works for you).
    - `<label>:<value` adds other data, eg. `created:2020-05-24`, `due:2020-05-31`, `repeats:monthly`, `id:#implement-ids`, `assignee:did:peer:abc123`, `interested:did:sovrin:321cba`
  - Subtasks (ie. parts of the current task) are created with a nested list, where the parent task line ends with a `:` and the subtasks follow in an indented task list.
    - Alternatively, use a `sub` label with the value of an `id` for another task.
      - The `super` label is the reverse of the `sub` relationship.
  - Dependent tasks are created with a `blocks` label.  (These are tasks that cannot begin until the current one is finished.)
    - The `awaits` label is the reverse of the `blocks` relationship.
  - To point to the same task in another project or task list, use the label `copy`.
  - If you've gotten this far, it's about time to consider migrating to a full-blown DB.  The lists are getting very verbose and hard to read and manage, doncha think?
- For sharing or juggling multiple task lists, start the file with URIs.
  - This might not be a URL, and in such a case would need an external source to map to a location(s), eg. "Local Data" in [Distributed Task Lists](https://github.com/trentlarson/distributed-task-lists).

Example 1

```
- 98 1 install helmet
- 85 3 convert to UUIDs id:#convert
- 75 1 add install instructions to README.md id:#uninstall
- 80 0 install SSL:
  - 77 0 save to publicly hosted git blocks:#uninstall
```

Explanation of Example 1

- Line 1 shows a task of priority 98 and estimation of 2 hours.
- Line 2 shows a task with id `#convert` of priority 85 and estimation of 8 hours.
- The "install SSL" task has other tasks as part of it.
  - Inside the subtask is a reference to the one with label `id` and value "#uninstall".

Example 2, adding globally unique references

```
%TAG ! tag:taskyaml.org,2020:schema
%TAG !council! tag:9d9bc93b-01f3-4efd-b003-36ec53f33d3b.prosperity-council,2020:/tasks.yml
--- !<tag:ee461452-d91c-42ff-9651-19a35e385037.trent.prosperity-todo,2020:/tasks.yml>
- 98 1 install helmet
- 75 1 add install instructions to README.md id:#instruction
- 90 0 install SSL:
  - blocks:
    - 77 0 save to publicly hosted git
  - sub:
    - 80 2 install haproxy
- 85 3 convert to UUIDs
- 80 1 create a link to the specific claim
- copy:!council!#publicize:
  - 70 2 reserve a domain
```

Explanation of Example 2

- The first line declares a `!` primary tag handle for the taskyaml.org schema.
- The second line declares a named tag handle `council` that refers to the globally unique URI for another set of tasks which will be referenced, specifically for the "prosperity-council".  See [an example from the spec here](https://yaml.org/spec/1.2/spec.html#id2783195).
- The third line starts this document (with `---`) and declares a default tag for it which will serve as a globally unique identifier.
See [an example from the spec here](https://yaml.org/spec/1.2/spec.html#id2761803).
- The "install SSL" contains a list of tasks it "blocks" as well as the "sub" tasks inside it.
- The last task is marked with `!council!` to say that it's part of the set of tasks from the `%TAG !council` line, and it's found with an ID of "#publicize" in that set.

I recognize that this use of tags isn't the intended use from the spec; those examples (and all parsers I've found) require the tag references in the document to be the first thing on the line.  I wish it would expand anywhere within the line, so I hope this type of use catches on (with the recognition that it complicates the parsers).

Related Projects

http://todotxt.org/ (... [introducted by the founder of LifeHacker in 2006](https://lifehacker.com/geek-to-live-list-your-life-in-txt-166299) and [defended](https://lifehacker.com/why-i-get-more-done-with-a-plain-text-to-do-list-5743081) and [summarized simply](https://www.howtogeek.com/355890/every-to-do-list-app-sucks-switch-to-todo.txt-instead/) by others since then... yep, there's quite the rabbit-hole here)
- While this is great for personal lists, I want a couple more features to enable sharing with others and do some advanced calculations such as aggregate estimations; task-yaml is more project-centric from the start.
- One thing todotxt.org got wrong is putting too much variability on the front, so it's tougher to quickly parse out the description.  They also don't put the size of the task front-and-center, so it's harder to see relative sizes of tasks (though they've got a good point that the next item on each project should always be a small task).

Notes

See [the tasks for improving this spec & tooling](tasks.yml).
