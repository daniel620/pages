---
title: "Go Programming Introduction"
date: 2025-06-25
tags: ["go", "golang", "backend", "systems"]
categories: ["programming"]
weight: 2
---

# Go Programming Introduction

Learn the basics of Go (Golang) programming language.

## Why Go?

- Fast compilation
- Built-in concurrency
- Simple syntax
- Great for microservices

## Basic Program Structure

```go
package main

import (
    "fmt"
    "net/http"
)

func main() {
    fmt.Println("Hello, Go!")
    
    http.HandleFunc("/", handler)
    http.ListenAndServe(":8080", nil)
}

func handler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello from Go server!")
}
```

## Goroutines and Channels

```go
package main

import (
    "fmt"
    "time"
)

func worker(id int, jobs <-chan int, results chan<- int) {
    for j := range jobs {
        fmt.Printf("Worker %d processing job %d\n", id, j)
        time.Sleep(time.Second)
        results <- j * 2
    }
}

func main() {
    jobs := make(chan int, 100)
    results := make(chan int, 100)

    // Start 3 workers
    for w := 1; w <= 3; w++ {
        go worker(w, jobs, results)
    }

    // Send 5 jobs
    for j := 1; j <= 5; j++ {
        jobs <- j
    }
    close(jobs)

    // Collect results
    for a := 1; a <= 5; a++ {
        <-results
    }
}
```
