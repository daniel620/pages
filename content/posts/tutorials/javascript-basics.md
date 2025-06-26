---
title: "JavaScript Fundamentals"
date: 2025-06-26
tags: ["javascript", "programming", "frontend"]
categories: ["tutorials"]
weight: 3
---

# JavaScript Fundamentals

Master the basics of JavaScript programming.

## Variables and Functions

```javascript
// Variables
const name = "John";
let age = 25;

// Functions
function greet(person) {
    return `Hello, ${person}!`;
}

// Arrow functions
const add = (a, b) => a + b;
```

## DOM Manipulation

```javascript
// Select elements
const button = document.querySelector('#myButton');
const output = document.getElementById('output');

// Add event listener
button.addEventListener('click', () => {
    output.textContent = 'Button clicked!';
});
```
