---
title: "Why Each React Suspense Needs its Error Boundary"
description: "Error boundaries are more relevant now than ever"
date: May 1 2000
draft: true
---

TLDR: React Suspense relies on promises being `thrown` for showing a `fallback` while the promise is pending. This will necessitate errors being caught by another `Suspense`-like component... enter `ErrorBoundary`.
