---
title: "Category Theory Rant"
excerpt: "A casual and opinionated dive into category theory, its relevance to programming, and why it's both fascinating and frustrating."
date: 2025-02-21
tags:
  - category theory
  - math
  - functional programming
  - rants
author: "Hamza Mateen"
draft: false
toc: true
layout: "blog"
readingTime: 5
slug: "category-theory-rant"
coverImage: "/images/category-theory.png"
---

In **Category Theory**, the following relation is **isomorphic** and not **identical**:      
**h . (g . f) = (h . g) . f** 

 This ***implies*** that there exists a **composition** that transforms the former category into 
 latter one and it is **not** the ***identity*** composition. 

**Corollary** to this idea is that two categories are **identical** if and only if one of them 
can be transformed into another **only through** the ***identity*** composition.

pretty cool right? never really thought about identity of an object to another through 
such a primitive lens!
