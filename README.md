# Task YAML

This defines a standard for a concise task list for priorities and estimates.  The goals are:
- Make it easy to create, read, and judge next actions with only a text editor.
  - The priority is most important. (This is implicit in the order of the list.)
  - The estimate is next most important. (This is represented by an optional number at the start of the line.).
  - The descriptions should line up for quick browsing.
    - Next to the estimate, the context is the most likely resource constraint so that should come first in the description.
  - Child and dependent tasks should be clear.

Tools should allow users to accomplish the following:
- create and edit with easy-to-use tools (ie. a text editor)
- sort and do basic calculations manually or with programs
- represent basic dependency concepts -- namely, subtasks and blocking tasks
- share lists publicly or privately
- incrementally migrate to a more (or less) expressive schema
- migrate to a more (or less) feature-rich and/or UI-rich toolset

## Specifications (v 2)

- Format in [YAML](https://yaml.org/).
- Optionally, start the file with a "tasks:" line.  This is important when the file contains other info (eg. a "log").
- Each task is in a list, with each line beginning with `-`.
  - Each item has a priority just above the next in the list.
  - An estimate number may come first.
    - Any numeric value as the first item must be an estimate.
    - This is typically a number of hours.
      - The recommendation is to line these up by prefixing single-digit numbers with a "0" (if the range is 00-99). Sub-hour lengths are easy with decimals, so ".1" would be lowest visually appealing length; that's precisely 6 minutes... and if really care to distinguish between .1 and .3, more power to you.
    - Another decent approach is to use a power of 2 for number of hours, ie. 0 = 1 hour and 1 = 2 hours and 2 = 4 hours; note that 9 is basically 3 full-time months, too large to get a good estimate and should be broken down before being worked on. Smaller increments could be negative, ie. -1 = 30 mins and -2 = 15 minutes. But we recognize that most non-computer-scientists would find this hard to translate.
  - Labels like `<label>:<value>` add other data, eg. `created:2020-05-24`, `due:2020-05-31`, `repeats:monthly`, `id:implement-ids`, `assignee:did:peer:abc123`, `interested:did:sovrin:321cba`
    - The `id` label is commonly used as a unique identifier for tasks. The value can be almost anything, but human-readable names are helpful. For example: `id:new-light-fixtures`
    - The `ref` will refer to another task (even in another project).  It's best to put it as the only thing on that line, since all other details are loaded from the original task. Global references are of the format `ref:taskyaml:...#SOME-ID`; local references (inside the same file) are simpler, eg: `ref:#SOME-ID`
  - The description is all the rest of the line.
  - That's enough to get started.  How's that for a simple intro?
  - Subtasks (ie. parts of the current task) are created with a nested list, where the parent task line ends with a `:` and the subtasks follow in an indented task list (yes, always a list).
    - Alternatively, use a `subtasks` label with the value being the list of tasks (somtimes expressed as `id:OTHER-ID`).
      - Under consideration: The `supertasks` label shows the reverse of the `subtasks` relationship.  Note that this should be a list.
  - Dependent tasks are created with a `blocks` label.  (These are tasks that cannot begin until the current one is finished.)  Again, this is always a list.
    - Under consideration: The `awaits` label shows the reverse of the `blocks` relationship.  (Yes, a list.)
  - Much like the `<label>:<value>` convention, you can add other markers from [Todo.txt](http://todotxt.org/).
     - Under consideration: `+` can mark a project/supertask, eg. `+home` or `+yard` or `+family-reunion`.
        - This should be treated as the ID for that parent task/project.  Tools may wish to show a project in the same list, together with the tasks, since a project is just a long-running task, eh.
        - in other words, this is a shortcut for `supertasks` (see above).
    - Under consideration: Other Todo.txt features are interesting and could be useful if they look good to you.  However, they don't really have any meaning in task-yaml.
      - `@` can mark a context, eg. `@home` or `@in-the-city` or `@family-reunion-2020`.
      - `x` can mark a task done, if you want to keep it in the list.  (todotxt.org puts it on the front; if you do that, the recommendation is to move that line to the bottom of the file so that it doesn't get confusing in the middle of the rest of the list and throw off the alignment.  Use `x`, move your task to the bottom, or just do what I do and delete the thing... whatever works).
  - If you've gotten this far, it's about time to consider migrating to a full-blown DB.  The lists are getting very verbose and hard to read and manage, doncha think?
- For sharing or juggling multiple task lists, start the file with URIs.
  - This might not be a URL, and in such a case would need an external source to map to a location(s), eg. "Local Data" in [Distributed Task Lists](https://github.com/trentlarson/distrinet/tree/master/app/features/task-lists).

Danger - beware colons `:`!

- Don't put a space after `:`! (I tell myself that I always put a space before a colon if it's not `label:value`, and that helps remind me that it's special.) Reason being that: if you ever have a colon followed by a space, YAML takes that as an object and it'll mess up the parsed results if the rest of your task isn't also formatted that way. It takes discipline and it's easy to forget! Here are the two cases where you can safely use it:
  - Surrounded by other characters, like in labels (eg. `id:start-fire`).
  - At the very end of a task line, where the following lines are lists (eg. subtasks).  The recommendation is always to put a space before it in that case, eg ` :`; that helps solidify this rule in your mind that it's something special.

Danger - avoid hashes `#`' !

- Hash symbols mark comments, so everything to the end of the line would be ignored.

Warning - no numbers first

- If a task description starts with a number, it could be parsed as an estimate. (I tell myself that I like to spell out my numbers, eg. "Two", and that helps me check myself.)

[Here is a good online tool to test your YAML](https://onlineyamltools.com/validate-yaml), and [here is one to check the JSON output](https://onlineyamltools.com/convert-yaml-to-json).

## Example 1

```
- 2 get groceries
- 1 write to Aunt Kate
- 1 +home laundry
- 2 +home fix sprinklers
```

#### Explanation of Example 1

- The time alloted to "write" and "laundry" is 1 hour, while "groceries" and "sprinklers" are 2 hours.

## Example 2, with priorities and IDs and subtasks

```
- .5 install helmet
- 08 switch to UUIDs id:convert
- 02 add install instructions to README.md id:install-docs
- 01 install SSL :
  - .5 save to publicly hosted git
  blocks: ref:#install-docs
```

#### Explanation of Example 2

- Line 1 shows a task estimated at 1/2 hour.
- Line 2 shows a task with id `convert` estimated at 8 hours.
- Line 3 shows a task with id `install-docs`.
- The "install SSL" task is linked to other tasks:
  - The "save" task is a subtask estimated at 30 minutes.
  - The referred task is a dependent task, meaning that this should happen before the `install-docs` item.

## Example 3, under consideration: shortcuts for globally unique references

Note that this part is experimental: some parsers fail to parse tags as shown.

```
%TAG ! tag:taskyaml.org,2020:schema
%TAG !council! tag:9d9bc93b-01f3-4efd-b003-36ec53f33d3b.prosperity-council,2020:/tasks.yaml
--- !<tag:ee461452-d91c-42ff-9651-19a35e385037.trent.prosperity-todo,2020:/tasks.yaml>
- 98 1 install helmet
- 75 1 add install instructions to README.md id:instruction
- 90 0 install SSL:
  blocks:
    - 77 0 save to publicly hosted git
  sub:
    - 80 2 install haproxy
- 85 3 convert to UUIDs
- 80 1 create a link to the specific claim
- id:!council!#publicize:
  - 70 2 reserve a domain
```

#### Explanation of Example 3

- The first line declares a `!` primary tag handle for the taskyaml.org schema.
- The second line declares a named tag handle `council` that refers to the globally unique URI for another set of tasks which will be referenced, specifically for the "prosperity-council".  See [an example from the spec here](https://yaml.org/spec/1.2/spec.html#id2783195).
- The third line starts this document (with `---`) and declares a default tag for it which will serve as a globally unique identifier.
See [an example from the spec here](https://yaml.org/spec/1.2/spec.html#id2761803).
- The "install SSL" contains a list of tasks it "blocks" as well as the "sub" tasks inside it.
- The last task is marked with the tag of `!council!` to show that it's part of the set of tasks from the `%TAG !council` line, and it's found with an ID of "publicize" in that set.

## Tooling &c

[This page at ObservableHQ](https://observablehq.com/d/83a90a6d6cd11999) can be used to visualize a project in Task YAML.

See [the tasks for improving this spec & tooling](tasks.yaml).

## Related Projects

http://todotxt.org/ (... [introducted by the founder of LifeHacker in 2006](https://lifehacker.com/geek-to-live-list-your-life-in-txt-166299) and [defended](https://lifehacker.com/why-i-get-more-done-with-a-plain-text-to-do-list-5743081) and [summarized simply](https://www.howtogeek.com/355890/every-to-do-list-app-sucks-switch-to-todo.txt-instead/) by others since then... yep, there's quite the rabbit-hole here)

- While this is great for personal lists, I want a couple more features to enable sharing with others and do some advanced calculations such as aggregate estimations; task-yaml is more project-centric from the start.

- One thing todotxt.org got wrong is putting too much variability on the front, so it's tougher to quickly parse out the description.  They also don't put the size of the task front-and-center, so it's harder to see relative sizes of tasks (though they've got a good point that the next item on each project should always be a small task).
