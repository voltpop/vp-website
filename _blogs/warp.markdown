---
layout: single
permalink: /blogs/warp/
collection: blogs
title: Web Automation Rest Parser
date: 2021-11-01
excerpt_separator: <!--excerpt-->

---

## AKA Warwalrux Awesome Rest Parser
<!--excerpt-->

`warp` is a tool born of necessity... namely the necessity of having to do things, but not wanting to do the clicky bits to get them done. I wrote this script so that I could programmatically make rest calls and daisy chain the output together, so that I could automate larget web processes between multiple disparate sources. 

In a nutshell, I wanted query puppetdb with rest and make a webpage, but _also_ wanted to update jira tickets based on fancy shmancy confluence-fu (and not the other way around).

What was crated was `warp` or "Warwalrux Awesome Rest Parser". 

the script draws from Ansible playbooks in the form of yaml jobfiles which control the scope and sequence of the jobs to be run by `warp`.

## Getting warp

Warp currently lives [here](https://github.com/dfoulks1/warp), and can be run straight from the repository.

## Using warp

`./warp -j jobs/example.yml`

example.yml is an ansible-playbook inspired yaml job file. Jaws will read and run, in turn, each of the defined tasks.

### Job Sessions

The script creates a session (authenticated or not) in order to run the tasks evaluated therein.

A Job **must** have:
* `stub` which serves as the base to which task `uri` are joined with a '/'.
* `tasks` which is a list of tasks to be run in the session.

A Job _may_ have:
* `loop`        // loop through the task.
  * `items`     // list of items to iterate through, may be jinja.
  * `iter_name` // name by which the current item in the iteration can be used. 
* `authentication`
  * `username`
  * `password`
* `verbose` [ True | False ]
* `vars`
* `headers`
* `certificates`

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
* `req`. 
    The request type for the task (GET|POST|DELETE|PUT)

if any of there are not present, the script will bail.

a task _may_ have:
* `stub_override`
    a URL that will override the job stub ad hoc.
* `data`
    YAML formatted request payload
* `output`
  * `type` // ( print | store | var )
  * `name` // 
  * `show` [ content | status_code | headers | template ] // print the content or status_code of the current task

### Output

There are three options for output at this time:
* `store`
* `var`

#### `store` example
Values will be written to a file with name output["name"]. If no name is provided the script will interactively prompt for one. This method is useful for reporting
```
...
    output:
      type: "store"
      show: "content"
      name: "filename.txt"
```

#### `var` example

Values stored as variables can be used in later steps with jinja templating
```
...
    output:
      type: "var"
      show: "content"
      name: "tickets"
```

## Useful REST Documentation

* https://docs.atlassian.com/software/jira/docs/api/REST/8.13.12/
* https://developer.atlassian.com/server/confluence/confluence-rest-api-examples/
