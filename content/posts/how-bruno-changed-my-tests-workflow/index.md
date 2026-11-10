---
title: "How Bruno changed my API tests workflow"
date: 2026-11-10T12:00:00-03:00
draft: false
summary: "How my team ditched a messy shared Postman collection for Bruno, with tests living in the repo and running on GitLab CI."
tags: ["bruno", "testing", "api", "ci-cd"]
---

So, I started my career as a QA engineer, so I know a little bit about test cases, test suites and all that stuff.

The fun part is automating tests, using BDD, Selenium, Cypress or whatever.

But I always hated Postman. That interface, the way you automate tests there... it really sucks.

<img src="postman-logo.svg" alt="Postman logo" width="120" height="120">

## The mess we had

Basically, we have a few APIs under development on my team, and these APIs are deployed using CI/CD on GitLab.

To test the endpoints, we were doing a lot of manual stuff, like thinking about the payload, mocking data, hitting the API and taking notes of the results. And all this shit is boring and error-prone as fuck.

So we started using Postman, BUT Postman has a limit on how many users you can have in the organization. So we were basically passing around the `collections.whatever` file that Postman gives you, and honestly, it was a mess. Everyone was modifying it all the time, changing the data and blablabla.

And then (thank you, Josh and Kennedy from my SmartX team) I learned about BRUNO (such a good name), and brooooo, it was a real game changer.

<img src="bruno-logo.png" alt="Bruno logo" width="120" height="120">

## Why Bruno?

Bruno does basically the same stuff that Postman does, but BETTER: it's lighter, faster and prettier (and the logo is a lot better as well).

You can basically add your test suite to your repository and run the tests in your pipeline workflow, so you can organize your smoke tests, regression tests and the test suite for the feature that you're working on.

And you can run only the suite that you wanna run, pass environment variables and choose the environment as well.

## What a test looks like

This is an example of a test that you can write:

```bru
# Define the metadata on it
meta {
  name: Create Project
  type: http
  seq: 1
}

# Define the request that you wanna send. It can be a GET, POST, PUT, PATCH, DELETE or whatever
post {
  url: {{baseUrl}}/projects
  body: json
  auth: none
}

# Define your headers
headers {
  Content-Type: application/json
}

# Define your body and your body type
body:json {
  {
    "tenantId": "{{tenantId}}",
    "authorId": "11111111-1111-1111-1111-111111111111",
    "name": "test",
    "description": "whatever"
  }
}

# Variables (if you want them)
vars:pre-request {
  tenantId: 6587c283-fa86-4e6d-ac9b-6f0587d31030
}

# And here is the fun part, the assertions (this is where you actually test stuff)
assert {
  res.status: eq 201
  res.body.id: isDefined
  res.body.name: eq test
}

# And you can define pre-request or post-response scripts (it's JavaScript tho)
script:post-response {
  if (res.body && res.body.id) {
    bru.setVar("projectId", res.body.id);
  }
}
```

## Running it

And tcham ram! You write your BRUNO files and run them using the Bruno CLI.

First, install it (you'll need Node):

```sh
npm install -g @usebruno/cli
```

Then just run it:

```sh
cd path/to/your-bruno-folder
bru run
```

Wanna run only your smoke tests on the dev environment? Easy:

```sh
bru run smoke-tests --env dev
```

And if you need to override some variable, just pass it:

```sh
bru run --env staging --env-var tenantId=abc123
```

Of course, you will need to define your environments to keep everything organized, but yeah, it's very simple to do, and you can follow the [documentation](https://docs.usebruno.com/introduction/getting-started).

## Running it on the pipeline

This is where things get really cool. Since the Bruno files live in your repository, you can just add a job to your `.gitlab-ci.yml`:

```yaml
api-tests:
  stage: test
  image: node:22
  script:
    - npm install -g @usebruno/cli
    - cd bruno
    # Secrets come from the GitLab CI/CD variables, so they don't live in the repo
    - bru run --env staging --env-var apiToken=$API_TOKEN --reporter-junit results.xml
  artifacts:
    when: always
    reports:
      junit: bruno/results.xml
```

The `junit` report makes GitLab show the test results right in the pipeline and in the merge request, so you can see which request failed without digging into the logs.
Also, Bruno has a lot more cool stuff, such as MOCKS, which you can use to mock the data of an API (it's better than waiting for your team to build the entire API), and you can run all of this in your CI/CD FOR FREE.

## How it's going on my team

First of all, we migrated all the Postman stuff to Bruno and started testing manually. And yeah, it works fine and it's faster (and the interface is a lot better).

Then we put it directly in our pipelines, so the QAs don't need to collect the evidence and send it to us anymore. They just need to send the link to the GitLab pipeline, and we can check if everything was ok.

And the best part of all: we store the Bruno stuff in our repository, so everyone can contribute and fix things without stepping on each other's toes, unlike the Postman mess we had before.

So yeah, that's it. I'm using Bruno in my personal projects too, and the plan is to fully migrate the team to it.

Good stuff being learned in 2026. Thank you, guys!
