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

## Getting warp

Warp currently lives [here](https://github.com/dfoulks1/warp), and can be run straight from the repository.

## Using warp

<div class="term">~$ ./warp -j jobs/example.yml</div>

example.yml is an ansible-playbook inspired yaml job file. Jaws will read and run, in turn, each of the defined tasks.

### Job Sessions

The script creates a session (authenticated or not) in order to run the tasks evaluated therein.

A Job **must** have:
* `stub` which serves as the base to which task `uri` are joined with a '/'.
* `tasks` which is a list of tasks to be run in the session.

A Job _may_ have:
* `authentication`
* `verbose` // boolean value
* `vars`    // a dictionary of values to be ingested as python dict.
* `headers` // a dictionary of values to be converted to headers
* `certificates`    // SSL certificate settings

#### Authentication

If the `authentiation` key is present in the job but no `username` or `pasword` values are found, the script will interactively prompt for values.


#### Certificates

If the `certs` key is present in the job, the script will attempt to add the following:
* `ca_bundle`   // ca bundle certificate
* `client`      // client certifcicate
* `key`         // public key

The script persist in using these settings for the entire job.


### Tasks

a task **must** have:
* `uri`
    The resource to be called for the task
    If the required `uri` is none. specify `none`.
* `name`
    The friendly display name for the task
* `action`. 
    The action step for the current task.

if any of there are not present, the script will bail.

a task _may_ have:
* `stub_override`   // a URL that will override the job stub ad hoc.
* `data`            // YAML formatted request payload
* `loop`            // loop through the task.
* `output`          // print the content or status_code of the current task

#### Action

A task requires an `action` statement.

##### `req`

Standard web request. Valid values are:
* get
* put
* post
* delete

##### `dump`

This is used to dump values to screen / catch them as output. Dump string is interpreted as jinja template.

#### Loop

If the `loop` key is found in the task object the script will construct `items` via jinja interpretation. The output will be iterated through and can be referenced by `iter_name`

<div class="term">...
    loop:
      iter: "{% raw %}{% for issue in issues %}{{ issue.key }}{% endfor %}{% endraw %}"
      iter_name "ticket"
</div>

would then allow you to use "{{ ticket }}" as a valid variable.

#### Output

There are three options for output at this time:
* `store`
* `var`

##### `store` example
Values will be written to a file with name output["name"]. If no name is provided the script will interactively prompt for one. This method is useful for reporting

<div class="term">...
    output:
      type: "store"
      show: "content"
      name: "filename.txt"
</div>

##### `var` example

Values stored as variables can be used in later steps with jinja templating

<div class="term">...
    output:
      type: "var"
      show: "content"
      name: "tickets"
</div>

## Useful REST Documentation

* https://docs.atlassian.com/software/jira/docs/api/REST/8.13.12/
* https://developer.atlassian.com/server/confluence/confluence-rest-api-examples/
