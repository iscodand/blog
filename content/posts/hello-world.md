---
title: "Hello, world"
date: 2026-08-31T22:00:00-03:00
draft: false
summary: "Why I'm starting this blog, and a quick tour of everything it can render."
tags: ["meta", "writing"]
---

I've been meaning to keep a written record of what I learn for a while. Notes
in a private folder never survive — they rot, get duplicated, and I never go
back to them. Writing in public is a forcing function: if I can't explain it,
I don't understand it yet.

So this is the plan: short posts, no schedule, no audience to please.

## What goes here

- Things I had to figure out the hard way
- Notes from books and papers, condensed
- Small experiments that didn't deserve a repo

> The best way to learn is to write about it as if teaching someone else.

## Everything this theme renders

Inline `code` looks like this, and a block looks like this:

```go
package main

import "fmt"

// greet says hello to whoever shows up.
func greet(name string) string {
	if name == "" {
		name = "world"
	}
	return fmt.Sprintf("Hello, %s!", name)
}

func main() {
	fmt.Println(greet(""))
}
```

A table:

| Command             | What it does                |
| ------------------- | --------------------------- |
| `hugo server -D`    | Runs locally, drafts on     |
| `hugo new content`  | Creates a post from a stub  |
| `hugo --minify`     | Builds into `public/`       |

And an ordered list, for good measure:

1. Read something
2. Fail to explain it
3. Read it again
4. Write the post

That's it. See you in the next one.
