---
layout: post
title:  "Alternatives to TypeScript enum"
comments: true
date:   2020-08-05 14:23:00
---


[이곳](https://medium.com/@maxheiber/alternatives-to-typescript-enums-50e4c16600b1)에서 일부 발췌

### Object Literals

```ts
const DIRECTIONS = {
  UP: "UP",
  DOWN: "DOWN"
} as const;
type DIRECTIONS = typeof DIRECTIONS[keyof typeof DIRECTIONS];
```
It works!:
```ts
const d1: DIRECTIONS = DIRECTIONS.UP; // OK
const d1: DIRECTIONS = "UP";         // OK
const d2: DIRECTIONS = "PLASTICS";  // Error
const d3: DIRECTIONS =  // Autocompletes: "UP", "DOWN"
```

The solution relies on the following advanced TS features:
* const assertions to not lose the information about the specific strings in the object.
* typeof to get the type of a variable
* keyof to get a union type for the keys of the object.
* union types
* lookup types to index into an object type
* The distributivity of union types: ObjType["A" | "B"] is the same as ObjType["A"] | ObjType["B"]
* the trick that a type aliases and constants live in separate worlds, so can have the same spelling.
