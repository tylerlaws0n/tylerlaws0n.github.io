---
title: "A Story of Three Component Libraries"
description: "What was once hard, is now less hard."
date: Jun 25 2024
---

Components are the blocks we build our UIs with. If made well, development is a breeze; just take what you need, wire it up with your state, and voilà! There is your fully formed UI (hopefully 😅).

Today, I see three primary ways to deliver a design system and common tooling through a component library:

- **Use something off-the-shelf** (e.g., Material UI or Mantine UI)
- **Build your own from scratch**
- **Use accessible and customizable components for composition**

## Off-the-Shelf Solutions

Using an off-the-shelf component library is often the most enticing option for starting a greenfield project. The promise is that everything is ready to use from day one, allowing you to get your project up and running quickly.

### The Appeal

The appeal is straightforward: speed (to get started) and simplicity. At the beginning of a project, you typically want to see something functional on the screen as quickly as possible. Libraries like Material UI (MUI) offer a vast array of pre-built components that you can integrate immediately.

### The Drawbacks

Issues can arise as your project matures and you move from a "just get it running" stage to one that involves more customization and design refinement. MUI uses styled-components, which ties you to a particular styling methodology (at least for the components).

#### Runtime CSS-in-JS Concerns

A quick note on runtime CSS-in-JS (like styled-components): while it provides a good developer experience (DX), it can burden the browser with unnecessary styling calculations at runtime. Libraries like [Vanilla Extract](https://vanilla-extract.style/) and [StyleX](https://stylexjs.com/) address this by handling styles at build time.

**Sidenote:** Normally the footprint is small enough that a team overlooks this as an "issue", but for me it just seems that styling things with zero runtime cost should be the suggested default.

**My Suggested Default:** [TailwindCSS](https://tailwindcss.com)

- Generates styles at build time
- Keeps styles colocated with the code (easier DX and no more orphan CSS classes)
- Design Systems can be delivered through a shared base config

### Customization Challenges (With Off the Shelf)

Customizing a component in MUI (or similar libs) will involve targeting internal elements with potentially flaky class names, as shown below:

```tsx
import * as React from "react";
import Slider, { SliderProps } from "@mui/material/Slider";
import { alpha, styled } from "@mui/material/styles";

const SuccessSlider = styled(Slider)<SliderProps>(({ theme }) => ({
  width: 300,
  color: theme.palette.success.main,
  "& .MuiSlider-thumb": {
    "&:hover, &.Mui-focusVisible": {
      boxShadow: `0px 0px 0px 8px ${alpha(theme.palette.success.main, 0.16)}`,
    },
    "&.Mui-active": {
      boxShadow: `0px 0px 0px 14px ${alpha(theme.palette.success.main, 0.16)}`,
    },
  },
}));

export default function StyledCustomization() {
  return <SuccessSlider defaultValue={30} />;
}
```

This approach not only burdens the client with styling calculations (runtime css-in-js), but also lacks a safety net if the component library updates the layout of its dom or class names.

## Building Your Own Library From Scratch

If you've been burned by trying to scale up a component library and work with a design team that wants to use their own design system with a "batteries-included" component library, you may have been tempted to throw it all out and start over from scratch. Or maybe this was just me. Either way, this is deceptively difficult. All of the difficulty is (unsurprisingly) in the details.

### My Experience

The following were my assumptions with our library to start:

- No business need for accessibility or localization on our proprietary apps (I'll come back to this point later)
- 90% of our use case was for simple, but custom styled elements: buttons, dropdowns, modals, and others
- We were moving projects to React (from elm) and wanted a shared place for delivering different React specific components and f11y

I chose to go with tailwind after reading their landing page and getting sold on the vision haha. We planned out the sharing of the config and how to start using tailwind in other projects, but it has really been a huge hit as I reflect ~3 years later.

My reasoning for starting from scratch was the realization that I could skip learning or inheriting debt from any third party library for components and create things specific to exactly what we needed. We could also implicitly enforce developer consistency through how the components are built and what props we make accessible.

The difficulties of rolling my own library came up mostly when dealing with the details of things like keyboard controlling of components. This is what libraries like [Radix](https://www.radix-ui.com/) are great for and something I wish I knew more about at the time in hindsight.

Owning your own layer for customizing styles, behavior, and props as needed is incredibly powerful. This being said, Radix (and other Headless component options) have done a fantastic job getting components into a place where you can get performance, accessibility, and really good DX/UX for (almost) free.

## Build Your Own with Headless Components

The term 'Headless' mystified me for years. I unfortunately took a long time to look it up and really internalize its meaning. Part of the reason for this, I think, is related to hearing about headless CMS's around the same time I heard about headless components 🤯.

**To put it simply: the head is the part we take off when we want our own custom layer to build on top of.**

**For Components:** We take the head off so that we can style to our design system and write our own API for how to develop with our components.

**For CMSs:** Take the content out of the code for customizing content asynchronously from your development work.

### Enter shadcn/ui

[shadcn/ui](https://ui.shadcn.com/) is absolutely brilliant! Not only does it use tailwind :), but it also gives the ideal guide to rolling your own component library with headless libraries that bake in all the usability and accessibility you could ever dream of.

Components now can continue to be fully controlled and owned by you team in partnership with the design team, but in contrast to rolling your own entirely alone, you get the essential web primitives the browser is missing for free!

## In Summary

Building components and tools to support your engineering teams is always going to be a sizeable undertaking. That being said, it has never been easier than it is today: components are already broken up into the logical set of pieces that build modern web apps and customization is trivial.
