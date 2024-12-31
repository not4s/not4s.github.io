---
title: "[STL] std::vector vs std::list"
permalink: /programming-languages/cpp/vector-vs-list/

author_profile: true
sidebar:
  nav: sidebar
  sticky: false

categories:
  - C++
tags: 
  - C++
  - STL
  - Vectors
  - Lists
layout: single

# Table of Contents
toc: true
toc_label: "Contents"
toc_icon: "compass"
toc_sticky: true
toc_levels: 1, 2, 3
---
<!--  -->
<!--  -->

# Introduction
When working with C++ in my projects, one of the choices I often face is deciding between std::vector and std::list.
 Both are fundamental STL containers, but they are built for different purposes. Understanding how they work under the hood and where their usage is optimal can help us write more efficient and readable code.

# Implementation
Let’s dive into the basics of how std::vector and std::list work. While we rarely need to worry about their internals in day-to-day coding, having a solid grasp of their implementations can help us understand their performance characteristics.

### std::vector
`std::vector` is essentially an dynamic array. It grows as you add elements, doubling its capacity when needed to minimize resizing overhead. Here's a simplified version of what it might look like:
```cpp
namespace std {
    template <typename T>
    class vector {
    public:
        vector()
            : size_(0), capacity_(1), array_(new T[1]) {}

        explicit vector(size_t size)
            : size_(size), capacity_(size), array_(new T[size]) {}

        void push_back(const T& elem) {
            if (size_ == capacity_) {
                capacity_ *= 2;
                T* newArray = new T[capacity_];
                for (size_t i = 0; i < size_; ++i) {
                    newArray[i] = array_[i];
                }
                delete[] array_;
                array_ = newArray;
            }
            array_[size_++] = elem;
        }

    private:
        size_t size_;
        size_t capacity_;
        T* array_;
    };
}
```
Notice how push_back triggers a resize when the capacity is exceeded.
This resizing step involves copying all existing elements into a new array, which is why adding elements to a std::vector is `O(1)` on average, but `O(n)` in the worst case.

### std::list
`std::list` is a doubly linked list. Each node contains the data and two pointers: one to the previous node and one to the next. Here’s a simplified version:
```cpp
namespace std {
    template <typename T>
    class list {
    private:
        struct listNode {
            T elem_;
            listNode* next_;
            listNode* prev_;
            explicit listNode(const T& elem)
                : elem_(elem), next_(nullptr), prev_(nullptr) {}
        };

    public:
        list()
            : head_(nullptr), tail_(nullptr) {}

        void add_node(const T& elem) {
            listNode* newNode = new listNode(elem);
            if (!head_) {
                head_ = tail_ = newNode;
            } else {
                tail_->next_ = newNode;
                newNode->prev_ = tail_;
                tail_ = newNode;
            }
        }

    private:
        listNode* head_;
        listNode* tail_;
    };
}
```
This structure makes `std::list` great for frequent insertions and deletions because you only need to adjust a couple of pointers. However, since nodes aren’t stored contiguously, traversing a `std::list` can be slower due to potential cache misses.

# Traversal: Memory Locality in Action
Imagine walking through a library. A std::vector is like having all the books you need on a single shelf, while a std::list is like having them scattered across the entire building.  

The main difference between traversing the std::vector and the std::list breaks down to two parts - memory locality and iteration methods. To begin with, std::vector is a dynamic array in which all of its elements are contiguously aligned with each other. On the other hand, std::list is a doubly linked list where each of its node could be scattered around memory. How does this affect performance in general? When the CPU loads a cache line, it often brings adjacent memory into the cache because it assumes nearby data would soon be accessed due to spatial locality. This, in most cases benefits vector traversal than list traversal due to is contiguous nature in memory. In addition, the std::vector supports random iterator access, which makes it cheaper (O(1) access) to a specific element in the data structure while to search for an element in a std::list, without any other techniques or data structures to supplement searches, it has to be traversed sequentially from the beginning of the std::list (O(n) access).  

# Memory Usage
std::vector and std::list also have different memory footprints.

- std::vector: You have the overhead of size_ and capacity_ variables, plus any unused capacity in the allocated array.
- std::list: Each node comes with the overhead of two pointers (next_ and prev_), which adds up if you’re storing a lot of small elements.  

This overhead can be a deciding factor, especially in memory-constrained environments.

# When to Use Each
From my experience, here are some simple rules of thumb:

- Use std::vector when:

    - You need random access to elements (O(1) access time).
    - Performance is critical, and you want to leverage spatial locality.
    - Your data structure mostly grows at the end.

- Use std::list when:

    - You’re frequently inserting or deleting elements in the middle.
    - You don’t need random access.
    - You’re okay with the memory overhead.

# Conclusion
Choosing between std::vector and std::list is rarely a one-size-fits-all decision. It depends on the specific requirements of your application and the trade-offs you're willing to make in terms of performance and memory usage. As I’ve discovered in my own projects, understanding the underlying mechanics of these data structures not only helps in making better choices but also deepens your appreciation for how the STL is designed.  

I hope this post has clarified some of the differences and practical use cases for these two containers. If you’ve come across interesting edge cases, performance hacks, or real-world scenarios involving std::vector or std::list, I’d love to hear about them! Feel free to share your thoughts in the comments or reach out directly. After all, the best insights often come from shared experiences and discussions.