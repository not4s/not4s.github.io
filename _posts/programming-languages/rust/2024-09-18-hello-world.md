---
title: "Rust Hello World"
permalink: /programming-languages/rust/hello-world/

author_profile: true
sidebar:
  nav: sidebar
  sticky: false

categories:
  - Rust
tags: 
  - C++
  - C++20
  - Coroutines
layout: single

# Table of Contents
toc: true
toc_label: "Contents"
toc_icon: "compass"
toc_sticky: true
toc_levels: 1, 2, 3
---
<!--  -->

## Introduction
For any programming language I dig into, I remember there was always the final boss waiting for me: coroutines. For such reasons, I have always regarded coroutines as a complex concept that would take technical expertise to understand. However, coroutines are not complex concepts at all. I would rather say that they exist to simplify things than to unnecessarily complicate things.

### What are Coroutines and why are they useful?
In the high-level, Coroutines are a general control structure that allows suspending and resuming execution at certain points. In other words, coroutines are functions that have that pause and resume button.

Coroutines are advantageous for simplifying asynchronous programming that has grown in importance over the years and promoting efficient usage of resources.

To elaborate, I remember being overwhelmed by the software design patterns that I have never encountered before when I started my first ever internship back when I was a student. The concept of callbacks were alien-like to me, and to make sense of the code flow I had to dig in ten layers of functions and classes to understand how things were implemented. Such patterns are heavily used in networking, I/O-bound tasks, and UI applications, all which have a significant presence in modern software engineering. Using coroutines allow asynchronous code to be written in a sequential style but execute asynchrounously in the backgound, and reduces the cognitive overhead of dealing with async constructs.

Furthermore, coroutines provide a way to manage concurrency without threads, which can be resource-intensive. Instead of creating new threads, coroutines suspend and resume execution at specific points (e.g., waiting for I/O), which helps optimize resource usage like CPU and memory. In one of my internships, I also remember playing around with thread pools to complete the task of profiling the thread pool for any efficiencies and to optimise it based on my findings. As a result of my experimentations with the thread pool, I ended up finding that certain threads were overutilised while some were underutilised and implemented code to balance them out for resource efficiency. Using coroutines to replace traditional threads for concurrency would have improved my solution by enabling cooperative multitasking, where I would have the power to explicitly controls when the coroutine should yield control, making it a lightweight solution compared to traditional threads. In highly concurrent systems, creating a new thread for each task can be inefficient. Coroutines can run millions of lightweight tasks concurrently without the overhead of thread management, ideal for large-scale web servers or real-time applications.

With Callbacks
```cpp
#include <iostream>
#include <functional>
#include <thread>
#include <chrono>

// Simulating asynchronous operation with a callback
void fetchDataFromAPI(std::function<void(int)> callback) {
    std::thread([callback]() {
        std::this_thread::sleep_for(std::chrono::seconds(2));  // Simulate network delay
        callback(42);  // Return some data
    }).detach();
}

void fetchDataAndProcess() {
    fetchDataFromAPI([](int result1) {
        std::cout << "First result: " << result1 << std::endl;
        // Now fetch the second data after first completes
        fetchDataFromAPI([result1](int result2) {
            std::cout << "Second result: " << result2 << std::endl;
            int finalResult = result1 + result2;
            std::cout << "Processed result: " << finalResult << std::endl;
        });
    });
}

int main() {
    fetchDataAndProcess();
    std::this_thread::sleep_for(std::chrono::seconds(5));  // Wait for all async work to complete
    return 0;
}
```

With Coroutines
```cpp
#include <iostream>
#include <future>
#include <coroutine>
#include <thread>
#include <chrono>

// Simulating an asynchronous task returning a future
std::future<int> fetchDataFromAPI() {
    return std::async([]() {
        std::this_thread::sleep_for(std::chrono::seconds(2));  // Simulate network delay
        return 42;  // Return some data
    });
}

// Coroutine to fetch and process data sequentially
std::future<void> fetchDataAndProcess() {
    int result1 = co_await fetchDataFromAPI();  // First async fetch
    std::cout << "First result: " << result1 << std::endl;

    int result2 = co_await fetchDataFromAPI();  // Second async fetch
    std::cout << "Second result: " << result2 << std::endl;

    int finalResult = result1 + result2;
    std::cout << "Processed result: " << finalResult << std::endl;
    co_return;
}

int main() {
    fetchDataAndProcess().get();  // Wait for the coroutine to complete
    return 0;
}
```

## How Coroutines Work
Suspend, Resume, and Complete<br>
Basic lifecycle of a coroutine: suspension points (co_await), resuming execution, and final completion (co_return).

Coroutine States and Execution Flow<br>
Diagram showing the state transitions, from coroutine creation, suspension, to completion. Helps readers visualize how control flows within a coroutine.

Differences Between Coroutines and Functions<br>
Explaination on how coroutines differ from traditional functions by having multiple suspension points.

## Coroutines in C++
Unlike higher-level languages like Python, Javascript, or C#, C++ required a more low-level, flexible, and performance-oriented solution to fit its system programming model. The challenge for C++ was to design a coroutine system that maintained this low-level control and minimal overhead, while still providing the simplicity and readability that other languages offered with coroutines. We will dive into the details

Efficiency: Coroutines in C++ are stackless and incur minimal overhead. Traditional thread-based approaches consume memory for stacks, but C++ coroutines allocate only what is necessary to maintain state, allowing for lightweight, highly scalable coroutines.

Compatibility: The coroutine design fits within the existing C++ language constructs, like RAII, exceptions, and the type system. The system needed to integrate cleanly with C++'s existing features, including template metaprogramming, lambdas, and smart pointers.

Low-level control: Unlike other languages where coroutines are typically high-level abstractions, C++ developers needed low-level access to the lifecycle of a coroutine. This led to the introduction of std::coroutine_handle, which provides direct manipulation of coroutine execution.

## Key Concepts and Syntax
Core Keywords: co_await, co_return, co_yield

Introduction of the core keywords for working with coroutines and what they signify:<br>
co_await for waiting on asynchronous results<br>
co_yield for yielding values<br>
co_return to return a value or signal completion<br>

Coroutine Handle and Promise Types<br>
The role of the std::coroutine_handle and promise types in managing the coroutine lifecycle.

Basic Code Example<br>
Simple code example showing a basic coroutine function.

```cpp
#include <iostream>
#include <coroutine>

struct ReturnObject {
    struct promise_type {
        ReturnObject get_return_object() { return {}; }
        std::suspend_never initial_suspend() { return {}; }
        std::suspend_never final_suspend() noexcept { return {}; }
        void return_void() {}
        void unhandled_exception() {}
    };
};

ReturnObject my_coroutine() {
    std::cout << "Hello from coroutine!\n";
    co_return;
}

int main() {
    auto coro = my_coroutine();
}
```
Explain each part step by step?

## Asynchronous Programming with Coroutines
Using co_await for Asynchronous Tasks<br>
Explaination on how co_await can be used for asynchronous operations (like I/O, network calls).

Code Example with Async Operations<br>
Example where a coroutine handles asynchronous tasks, using std::future or a similar async mechanism.

```cpp
#include <iostream>
#include <future>

std::future<void> async_coro() {
    std::cout << "Starting async task...\n";
    co_await std::async([] {
        std::this_thread::sleep_for(std::chrono::seconds(2));
        std::cout << "Async task completed.\n";
    });
    co_return;
}

int main() {
    async_coro().get();
}
```

Discussion on how coroutines can simplify asynchronous workflows compared to traditional approaches.

## Advanced Coroutine Concepts
Coroutine Return Types: std::future, Generators, and More<br>
Explaination on how coroutines can return different types depending on the use case (std::future, generators, etc.).

Generators and co_yield<br>
Dive into co_yield for generator-style coroutines, showing an example of yielding values lazily.

Using Coroutines in Concurrency<br>
Discussion on how coroutines can be integrated with multi-threading and concurrency, providing an architecture diagram that explains how coroutines interact with threads.

<h2>Performance Considerations</h2>
Coroutine Overhead<br>
The overhead associated with coroutines and how they can be optimized.

Memory Management and Coroutines<br>
How memory is managed in coroutines, including coroutine frames, stackless nature, and when they are deallocated.

<h2>Use Cases and Best Practices</h2>
Real-World Applications<br>
Highlight of scenarios where coroutines can be beneficial, such as game loops, networking, and GUI programming.

Common Pitfalls and How to Avoid Them<br>
Common mistakes when using coroutines and best practices to avoid issues like deadlocks or unexpected behavior.

<h2>Conclusion</h2>
Recap of Key Points<br>
Summary of the advantages and key concepts of coroutines in C++.

Additional Resources?<br>
Links to further readings, documentation, or talks (need research).

<!-- Include architecture diagrams for asynchronous workflows and coroutine state transitions.
     Show how different parts of a coroutine interact (e.g., caller, callee, suspension points) through flow diagrams. -->