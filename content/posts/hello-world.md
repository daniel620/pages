---
title: "Hello World - My First Post"
date: 2025-06-26
tags: ["hugo", "getting-started"]
categories: ["blog"]
---

# Hello World

Welcome to my first Hugo post! This demonstrates how to create and manage content.

## Content Management

- Add new posts by creating Markdown files in `content/posts/`
- Use meaningful filenames like `2025-06-26-my-article.md`
- Front matter (the YAML between `---`) controls metadata

## Markdown Examples

### Code Blocks

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Hugo!")
}
```

### Lists and Links

- [Hugo Documentation](https://gohugo.io/documentation/)
- [Markdown Guide](https://www.markdownguide.org/)
- Hugo Book Theme Features

### Tables

| Feature | Supported |
|---------|-----------|
| Search | ✅ |
| Dark Mode | ✅ |
| Mobile | ✅ |

---

## Front Matter Options

You can use these fields in your post front matter:

```yaml
---
title: "Your Post Title"
date: 2025-06-26
tags: ["tag1", "tag2"]
categories: ["category"]
draft: false  # Set to true to hide from public
weight: 10    # Controls ordering
---
```

> Use `tags` and `categories` to organize your content for easy discovery!
