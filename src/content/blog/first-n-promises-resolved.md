---
title: "First N Promises Resolved"
description: "Examples for how to get the first n promises resolved from an array."
date: Mar 29 2024
---

With Javascript at the moment, it is really easy to get the first 1 or _l_ promises resolved in a list, where _l_ is the length of the list. This is achieved with `Promise.race` and `Promise.all` respectively. When creating production applications `Promise.allResolved` is normally preferred since it will resolve even if one of the promises errors, but for the purposes of this post I will assume that all promises resolve successfully.

Let's say you have a list of 3 promises and you want to obtain the first two that resolve. To do this, we can group all possible pairs of two promises, use `Promise.all` on the two, then combine them into a larger array, where we `race` for the first two to be resolved.

In the past, I took the approach of testing all possible combinations, but recently realized you can simply race one at a time until `n` is reached by returning the index along with each promise result.

```js
async function promiseSome(promises, n) {
  const promisesCopy = [...promises];
  const result = [];

  while (result.length < n) {
    // track index of each promise so we can rule them out as each race completes
    const [winningResult, winningIndex] = await Promise.race(
      promisesCopy.map(async (p, i) => [await p, i])
    );

    promisesCopy.splice(winningIndex, 1);
    result.push(winningResult);
  }

  return result;
}
```

Now we can test this out we can make some promises using `setTimeout`:

```js
const testPromise = (ms, message) =>
  new Promise((resolve) =>
    setTimeout(() => {
      resolve(`${message}: took ${ms} ms`);
    }, ms)
  );

const p1 = testPromise(3000, "Promise One");
const p2 = testPromise(1000, "Promise Two");
const p3 = testPromise(500, "Promise Three");
const p4 = testPromise(1100, "Promise Four");
const promises = [p1, p2, p3, p4];

(async () => {
  const firstThree = await promiseSome(promises, 3);
  console.log(firstThree);
  // OUTPUT: ['Promise Three: took 500 ms', 'Promise Two: took 1000 ms', 'Promise Four: took 1100 ms']
  const firstTwo = await promiseSome(promises, 2);
  console.log(firstTwo);
  // OUTPUT: [ 'Promise Three: took 500 ms', 'Promise Two: took 1000 ms' ]
})();
```

Now we can effectively get the first _n_ promises resolved from a list of promises whenever we want!
