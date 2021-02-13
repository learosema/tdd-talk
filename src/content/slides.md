# TDD with Jest+TypeScript

---

# Hi! I'm Lea Rosema

- Pronouns: she/her
- Junior Frontend Developer at SinnerSchrader
- Digital artist, addicted to [Codepen](https://codepen.io)
- [https://lea.codes/](https://lea.codes/)

---

# Structure of this talk

- Introduction to TDD (7m)
- Kinds of tests (7m)
- Jest+TypeScript Basic Setup (7m)
- What are mocks and how to do them in jest (7m)
- Some tipps and tricks (7)
- Go through real life code examples (7m)
- Q&A

---

# Introduction to TDD

- TDD stands for Test Driven Development
- Methodology in Extreme Programming
- Kent Back first wrote about it in 2003
- Most important concept: tests first approach

---

# Tests first approach?

- Think about your problem
- Split your problem into smaller pieces
- Solve your problems in the mantra of TDD

---

# TDD Mantra in detail

- Write a test for one small piece
- The test fails
- Implement as much to make the test pass
- Tests pass :)
- Clean code, refactor if necessary
- Repeat

---

# Advantages of TDD

- Rewarding :)
- Your progress becomes visible
- Ideal for pair/mob programming
- Reduces debugging time
- Integratable with CI/CD tools

---

# Kinds of tests

- Unit tests
- Integration tests
- Snapshot tests
- Component tests
- Visual tests
- End 2 end tests

---

# Unit tests

- test single functions in an isolated way
- external dependencies are out of scope and are abstracted away
- database queries, API fetches are replaced (mocked) by fixed results

---

# Integration tests

- test how a group of single units work together

---

# Snapshot tests

- check if the markup of a component renders correctly
- can be useful for small pieces
- not recommended for very complex components
- better: component tests/visual tests
- [in-depth guide](https://jestjs.io/docs/en/snapshot-testing)

---

# Component tests

- Test functionality of a component
- Similar to unit tests as you test components outside the context of your app
- Ie. test if specific classNames apply when enabling component attributes
- Works great with [Storybook](https://storybook.js.org/docs/react/workflows/unit-testing)

---

# Visual tests

- test if your component matches a screenshot
- Works great with [Storybook](https://storybook.js.org/docs/react/workflows/visual-testing)

---

# End-to-end tests

- Test the complete functionality of your app
- Puppeteer - uses a headless chromium
- Cypress - popular & developer friendly
- Selenium - uses standardized WebDriver protocol (also works with IE)
- Appium (like Selenium but for mobile browsers)

---

# Project setup with jest and TypeScript

- via Babel
  - transpiles the test code to JavaScript
  - does no type-checking
  - you can do type-checking via the `tsc` compiler
- via ts-jest
  - no need to setup babel
  - type checking out of the box :)

---

# Getting Started

```sh
mkdir my-project
git init
npm init -y
npm i typescript jest @types/jest ts-jest -D
npx typescript --init
npx ts-jest config:init
```

---

# `jest.config.js`

```js
module.exports = {
  preset: "ts-jest",
  testEnvironment: "node",
};
```

---

# Test environments

- `node` - node-like environment
- `jsdom` - browser environment

---

# Layout of a test

- `describe`
  - `beforeAll`, `beforeEach`
  - `afterAll`, `afterEach`
  - `test` / `it`
    - `expect().toBe()`

---

# Example

```js
describe("distance unit tests", () => {
  it("should return the distance between 2 points", () => {
    const b = { x: 1, y: 1 };
    const a = { x: 4, y: 5 };
    expect(distance(a, b)).toBe(5);
  });
});
```

---

# Example implementation

```js
type Point = { x: number, y: number };

export function distance(a: Point, b: Point) {
  const { abs, hypot } = Math;
  return hypot(abs(a.x - b.x, a.y - b.y));
}
```

---

# Mocks

- make functions out of test scope return expected results
- abstract away dependencies for your isolated tests
- Example for mocks: network calls, database queries, user input events

---

# How to mock in jest

- import a mock from npm
- overwrite a function with a mock implementation

---

# Example

## canvas 2d/webgl mock

```sh
npm i jest-webgl-canvas-mock -D
```

```js
module.exports = {
  preset: "ts-jest",
  testEnvironment: "jsdom",
  setupFiles: ["jest-webgl-canvas-mock"],
};
```

---

# Example: jest.mock 1/2

```js
import fetch from "node-fetch";
import { mocked } from "ts-jest/utils";

import { getPerson } from "./star-wars-api";

jest.mock("node-fetch", () => jest.fn());
```

---

# Example: jest.mock 2/2

```js
describe("My test", () => {
  it("should fetch Luke Skywalker from the Star Wars API", async () => {
    mocked(fetch).mockImplementation((): Promise<any> => {
      return Promise.resolve({
        json: () => Promise.resolve({ name: "Luke Skywalker" }),
      });
    });
    const person = await getPerson(1);
    expect(person.name).toBe("Luke Skywalker");
  });
});
```

---

# Mock by reassigning

```js
let now = 0;
const originalPerfNow: () => number = global.performance.now;

beforeEach(() => {
  now = 0;
  global.performance.now = () => now;
});

afterEach(() => {
  global.performance.now = originalPerfNow;
});
```

---

# React-specific additions

- `create-react-app` sets up a ready-to-use test environment for you

---

# More React-specific stuff

```sh
# DOM testing for React components
npm i enzyme enzyme-adapter-react-16
npm i @types/enzyme @types/enzyme-adapter-react-16 -D

# snapshot-testing
npm i react-test-renderer -D
```

---

# Asset imports 1/2

## `./jest/stub-transformer.js`

```js
module.exports = {
  process: () => "module.exports = {};",
  getCacheKey: () => "stub-transformer",
};
```

---

# Asset imports 2/2

## `jest.config.js`

```js
module.exports = {
  transform: {
    "\\.(css|less|scss)$": "./jest/stub-transformer.js",
  },
};
```

---

# Real life code example

- [https://github.com/shader-art/shader-art/](https://github.com/shader-art/shader-art/)

---

# Thank you :)

## Resources

- [https://jestjs.io](https://jestjs.io)
- [https://kulshekhar.github.io/ts-jest/](https://kulshekhar.github.io/ts-jest/)
- [https://enzymejs.github.io/enzyme/](https://enzymejs.github.io/enzyme/)
-
- [Mirjam Bäuerlein @ RuhrJS 2019 - a tale of dog training and test-driven development](https://www.youtube.com/watch?v=V7QRcnnMoKI)
