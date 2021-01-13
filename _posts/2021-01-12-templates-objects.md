---
layout: post
title: "Template Objects"
tags: [Arquitecturas, Architecture, Clean Code, Código Limpio, DDD, Domain Driven Design]
---

# Entities
<p align="center">
  <img width="50%" src="https://user-images.githubusercontent.com/22304957/104355679-c7bb8300-54e9-11eb-8f7e-65232562fde6.png">
</p>

### Referencias:
1. Not meant to be extended from
2. Properties can be mutable
3. Use named constructors as meaningful ways of instantiating the object
4. Validate argument. Instantiate a new copy. Assign arguments to properties. Record domain event(s)
5. Pass data relevant to the job, plus contextual information (current time, current user, etc.)
6. Validate input arguments. Validate state transitions. Record domain events
7. Limit the number of query methods exposing state
8. Return recorded domain events


# Value Objects
<p align="center">
  <img width="50%" src="https://user-images.githubusercontent.com/22304957/104355676-c68a5600-54e9-11eb-8da0-70b07cc0a995.png">
</p>

### Referencias:

1. Not meant to be extended from
2. All properties are immutable
3. Named constructors as meaningful ways of instantiating the object.
4. Validate arguments. Instantiate a new copy. Assign arguments to properties. Record domain event(s)
5. Use declarative names for modifiers (e.g. with...())
6. Return a modified copy of the original
7. Limit the number of query methods

# Services
<p align="center">
  <img width="50%" src="https://user-images.githubusercontent.com/22304957/104355680-c7bb8300-54e9-11eb-828a-026c27fd6083.png">
</p>

### Referencias:

1. Not meant to be extended from
2. All properties are immutable
3. All arguments are required. Don’t inject contextual information; make the service reusable
4. Dependencies are actual dependencies, not service locators
5. Only assign arguments to properties
6. Pass data relevant to the job, plus contextual information (current time, current user, etc.)
7. Validate input arguments. Perform the task. Produce side effects
8. Validate input arguments. Produce no side effects, only return information
