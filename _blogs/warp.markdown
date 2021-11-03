---
layout: single
permalink: /blogs/warp/
collection: blogs
title: Speedy Web Automation with `warp`
date: 2021-11-01
excerpt_separator: <!--excerpt-->

---

## Warwalrux Awesome Rest Parser
<!--excerpt-->

`warp` is a tool born of necessity... namely the necessity of having to do things, but not wanting to do the clicky bits to get them done. I wrote this script so that I could programmatically make rest calls and daisy chain the output together, so that I could automate larget web processes between multiple disparate sources. 

In a nutshell, I wanted query puppetdb with rest and make a webpage, but _also_ wanted to update jira tickets based on fancy shmancy confluence-fu (and not the other way around).

What was crated was `warp` or "Warwalrux Awesome Rest Parser". 

the script draws from Ansible playbooks in the form of yaml jobfiles which control the scope and sequence of the jobs to be run by `warp`.

**Basic Example:**
<div class="term">~$ ./warp -j example.yml</div>

warp is a jobfile parser and jinja interpreter for a requests.session backend. This allows users to define complex processes wherein data is manipulated and passed between disparate web services linking them across a single session using ansible-playbook inspired yaml files.

`example.yml` is one such job file. When run, warp will read the jobfile, establish session details, interpret the tasks and then run, in sequence, each of the defined tasks.

[Job Sessions](#job-sessions)
* [Authentication](#authentication)
* [Certificates](#certificates)
* [Options](#job-options)
[Tasks](#tasks)
* [Loops](#loops)
* [Actions](#actions)
  * [Dump](#dump-tasks)
  * [Request](#req-tasks)
* [Output](#output)
  * [screen](#screen-example)
  * [file](#file-example)
  * [var](#var-example)
* [Options](#task-options)


## Getting Warp
Warp currently lives [here](https://github.com/dfoulks1/warp) and can be run straight from the repository after installing the contents of `requirements.txt`.


## Job Sessions

Warp uses Job Sessions ( an alias to requests.sessions) to manage certificates, auth, and other connection settings for a series of REST calls. The script creates a session (authenticated or not) in order to run the tasks evaluated therein.

A Job **must** have:
* `stub` which serves as the base to which task `uri` are joined with a '/'.
* `tasks` which is a list of tasks to be run in the session.

A Job _may_ have:
* `urlencode`   // set data to be urlencoded as the default payload format
* `auth`
  * `username`
  * `password`
* `verbose` // verbosity boolean
* `vars`
* `headers`

**Basic Job Example**
<div class="term">
---
stub: "https://issues.apache.org/jira/rest/api/2"
headers:
  Content-Type: "application/json"
  Authorization: "basic"
auth:
  username:
  password: 
tasks:
  ...
verbose: True
</div>

### Authentication

If the `authentication` key is present in the job but no `username` or `pasword` values are found, the script will interactively prompt for values.

The script persists in using these as the default credentials for the entire job.


### Certificates

If the `certs` key is present in the job, the script will attempt to add the following:
* `ca_bundle`   // ca bundle certificate
* `client`      // client certifcicate
* `key`         // public key

The script persist in using these settings for the entire job.

### Job Options

Currently there are two job options available:
* `verbose`     // turn on friendly output and show steps
* `urlencode`   // url encode the payload instead of the default json.

## Tasks

a task **must** have:
* `uri` // The resource to be called for the task
* `name` // The friendly display name for the task
* `action` // The action statement for the task

if any of there are not present, the script will bail.

a task _may_ have:
* `loop`    // iteration statement
* `urlencode` // urlencode payload data for _this_ task.
* `stub_override` // a URL that will override the job stub ad hoc.
* `data` // YAML formatted request payload
* `output` // an output statement

**Example Task**
<div class="term">
...
tasks:
  - name: "Authenticate"
    uri: "issue/createmeta"
    action:
      req: "get"
    output:
      write_to: "screen"
...
</div>

### Loops
If the loop key is found in the task object the script will construct items via jinja interpretation. The output will be iterated through and can be referenced by iter_name

<div class="term">
...
    loop:
      iter: {% raw %}"{% for issue in issues %}{{ issue.key }}{% endfor %}"{% endraw %}
      iter_name: "ticket"
</div>

This will allow you to use {% raw %}{{ ticket }}{% endraw %} as a valid variable for a task

### Actions

Task actions are at the core of `warp` functionality. All tasks must have valid action statements. Currently only two types of task are supported:
* `dump`
* `req`

#### `dump` Tasks

a dump task lets a user use jinja templating to manipulate a stored variable. When used in conjunction with an `output` statement this becomes fairly powerful.

<div class="term">
...
action:
  dump: "{{ query_data.nodes }}"
</div>

#### `req` tasks

`req` tasks are the bread and butter of `warp`. They represent the basic REST tasks:
* `get`
* `put`
* `delete`
* `post`

When run the output may be stored or manipulated with an output statement and used in another task or to generate a report.

### Output

Output is the directive for the output of the current task. A valid output directive must contain **both** a `write_to` and a `content` attribute.

There are three options for redirecting output with `write_to` at this time:
* `file`
* `screen`
* `var`

`content` may be any one of:
* `template`    // directive to use a template.
  * additionally a valid jinja2 `template_file` or `template_str` must be provided.
* `raw`         // an alias for output.content
* `content`     // response.content
* `headers`     // response.headers
* `status_code` // response.status_code

If no `content` directive is provided the default is `response.content`

#### `screen` example

Printing stored variable a Jinja Template string
<div class="term">
...
    output:
      write_to: "screen"
      content: "template"
      template_str: "{% raw %}{% for item in items}{{ item.name }}{% endfor %}{% endraw %}"
</div>

Printing stored variable with a Jinja Template file
<div class="term">
...
    output:
      write_to: "screen"
      content: "template"
      template_file: "templates/example.j2"
</div>

Printing task output
<div class="term">
...
    output:
      write_to: "screen"
</div>

#### `file` example
Values will be written to a file with name output["name"]. If no name is provided the script will interactively prompt for one. This method is useful for reporting
<div class="term">
...
    output:
      write_to: "file"
      content: "content"
      name: "filename.txt"
</div>

#### `var` example

Values stored as variables can be used in later steps with jinja templating
<div class="term">
...
    output:
      write_to: "var"
      content: "content"
      name: "tickets"
</div>

### Task Options
Currently there are two job options available:
* `urlencode`   // url encode the payload instead of the default json.
