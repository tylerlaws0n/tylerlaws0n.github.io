---
title: "A Story of Three Component Libraries"
description: "What was once hard, is now less hard."
date: Jun 22 2024
---

Components are the blocks we build our UIs with. If made well, development is a breeze; just take what you need, wire it up with your state, and voilà! There is your fully formed UI (hopefully 😅).

Today, there are primarily three distinct ways to deliver your company's design system and common tooling through a component library:

- **Use something off-the-shelf** (e.g., Material UI or Mantine UI)
- **Build your own from scratch**
- **Use accessible and customizable components for composition**

## Off-the-Shelf Solutions

Using an off-the-shelf component library is often the most enticing option for starting a greenfield project. Everything is ready to use from day one, allowing you to get your project up and running quickly.

### The Appeal

The appeal is straightforward: speed and simplicity. At the beginning of a project, you typically want to see something functional on the screen as quickly as possible. Libraries like Material UI (MUI) offer a vast array of pre-built components that you can integrate immediately.

### The Drawbacks

However, issues can arise as your project matures and you move from a "just get it running" stage to one that involves more customization and design refinement. For example, MUI uses styled-components, which ties you to a particular styling methodology.

#### Runtime CSS-in-JS Concerns

A quick note on runtime CSS-in-JS (like styled-components): while it provides a good developer experience (DX), it can burden the browser with unnecessary styling calculations. Styles associated with component props are available at build time, so requiring the client to finalize these styles in the browser is inefficient. Libraries like [Vanilla Extract](https://vanilla-extract.style/) and [StyleX](https://stylexjs.com/) address this by handling styles at build time.

```tsx
<Button variant="contained">Contained</Button>
```

Tailwind CSS has become popular for its performance benefits and simplicity, making it a preferred choice for many modern projects.

### Customization Challenges

For example, customizing a component in MUI might involve targeting internal elements with potentially flaky class names, as shown below:

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

This approach not only burdens the client with styling calculations but also lacks a safety net if the library updates its internal structure, as CSS lacks type safety.

### A Summary on this approach

Using an off-the-shelf component library can be beneficial if you are certain that your customization needs will remain minimal. Libraries like MUI offer a well-constructed set of accessible components but come with performance and customization trade-offs.

## Building Your Library From Scratch

If you've been burned by trying to scale up a component library and cater to a design team that wants to use their own design system with a "batteries-included" component library, you may have been tempted to throw it all out and start over from scratch. Or maybe this was just me. Either way, this is deceptively difficult. All of the difficulty is in the details that you and I were not considering.

### My Experience

This is a project I took on with the following business needs in mind:

- No business need for accessibility or localization on our proprietary apps (I'll come back to this point later)
- 90% of our need was for simple, but custom styled buttons, dropdowns, modals, and other basic components
- We were moving projects to React and wanted a shared place for delivering different React specific components and f11y

I recognized that for what we needed, I could use tailwind and roll the components myself within about a month or so. I could skip learning or inheriting debt from any third party library for components and create things specific to exactly what we needed. We could also implicitly enforce developer consistency through how the components are built and what props are accessible. This has been almost entirely a really successful project.

Building your own keyboard controlling of components though is an incredible waste of time. This is what libraries like [Radix](https://www.radix-ui.com/) are great for.

Owning your own layer for customizing styles, behavior, and props as needed is incredibly powerful. This being said, using your own code for all of the behavior and construction of your components is probably not a wise use of time. Radix has done a fantastic job getting components into a state where you can get performance, accessibility, and really good DX/UX for (almost) free.

## Build Your Own with Headless Components

The term 'Headless' mystified me for years. I unfortunately took a long time to look it up and really internalize its meaning. Part of the reason for this for me I think is related to me hearing about headless CMS's around the same time I heard about headless components.

> To put it simply: the head is the part we take off when we want our own custom layer to build on top of.

**For Components:** We take the head off so that we can style to our design system and write our own API for how to develop with our components.

**For CMSs:** Take the content out of the code for customizing content asynchronously from your development work.

### Enter shadcn/ui

[shadcn/ui](https://ui.shadcn.com/) is absolutely brilliant! Not only does it use tailwind :), but it also gives the ideal guide to rolling your own component library with headless libraries that bake in all the usability and accessibility you could ever dream of.

Components now can continue to be fully controlled and owned by you team in partnership with the design team, but in contrast to rolling your own entirely alone, you get the essential web primitives the browser is missing for free!

## In Summary

Building components and tools to support your set of engineering teams is always going to be a sizeable undertaking. That being said, it has never been easier than it is today: components are broken up into the logical set of pieces that build modern web apps and customization is trivial.
