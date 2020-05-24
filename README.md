# task-yaml

This is for a concise task list where the priorities and estimates are obvious, easily sorted and parsed, and can represent the basic dependency concepts -- namely, children/subtasks and blocking tasks.

- Format in YAML.
- Start with a URI for this list.
  - This may not be a URL and in that case would need an external resource to map to a location(s).
- For each task:
  - First is a priority number.  The higher the number the higher the priority.
  - Second is an estimate of effort.  We recommend using a power of 2 for number of hours, ie. 0 = 1 hour and 1 = 2 hours and 2 = 4 hours; note that 9 is basically 3 full-time months, too large to get a good estimate and should be broken down before being worked on.
- `!blocks` tag marks tasks that cannot begin until that task is finished.
  - `!awaits` tag is the reverse of `!blocks` relationship
- `!contains` tag marks tasks may be nested to show sub-tasks (which all must be done before the parent is considered finished)
  - `!ispartof` tag is the reverse of `!contains` relationship

Example

```
%TAG !group tag:9d9bc93b-01f3-4efd-b003-36ec53f33d3b.prosperity-group,2020:tasks
--- !<tag:ee461452-d91c-42ff-9651-19a35e385037.trent.prosperity-todo,2020:tasks>
- 98 1 install helmet
- 90 0 install SSL
  - !blocks
    - 77 0 save to publicly hosted git
    - *instruction 
  - !contains
    - 80 2 install haproxy
- 85 3 convert to UUIDs
- 80 1 create a link to the specific claim
- &instruction
  - 05 1 add install instructions to README.md
- !group &publicize
  - 70 2 reserve a domain
```

Explanation of that Example
- The first line declares a tag `group` that refers to the globally unique URI for another set of tasks which will be referenced, specifically for the "prosperity group".  See another example [here](https://yaml.org/spec/1.2/spec.html#id2782457).
- The second line starts this document and declares a default tag for it which will serve as a globally unique identifier.
See another example [here](https://yaml.org/spec/1.2/spec.html#id2761803).
- Lines 3 & 4 show some tasks, of priority 98 and 90, and estimations of 2 hours and 1 hour, respectively.
- Line 5 tells that the second task "blocks" and therefore must be done before the tasks inside it that list
- The second blocked task is a reference to the one with ID "instruction" (found later as `&instruction`).
- The last task is marked with `!group` to say that it's part of the set of tasks from the first line, and it's found with an ID of "publicize" in that set.


Notes
- I'd actually prefer to have the references (eg. "instruction") NOT at the beginning of the node because they clutter the UI, but the YAML anchors really seem to be the best fit for this.  (An alternative is a custom string, eg "ID:stuff" somewhere in the task.  Both are ugly.)

Tasks
```
- 70 2 parse & model in JSON
  - !blocks
    - 50 4 show graphical dependencies
- &calc-time
  - 60 4 calculate time before finishing, a la https://github.com/trentlarson/Schedule-Forecast
- 60 2 add teams (related to *calc-time )
```
