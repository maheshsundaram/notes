# 🗺️ Mapped Types Medley 🎶
**Let's learn about mapped and conditional types in TypeScript and how to use them together with an inferred type variable!**

[Mapped types](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types) and [conditionals](https://www.typescriptlang.org/docs/handbook/advanced-types.html#conditional-types) are somewhat common now in TypeScript, but paired together with [the `infer` keyword](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html#type-inference-in-conditional-types) and [improved support for arrays and tuples](https://github.com/Microsoft/TypeScript/pull/26063), there are some neat new things to learn that can be very powerfully useful.

To work through these ideas, we'll explore a bit of code that sets up types and functions that let us declare type safe routes in [react-router](https://reactrouter.com/web/guides/quick-start) that must take a specific shape of params.

That is, given a template route like `"/organization/:organizationId"` we'd like to generate routes like `"/organization/123"` in a type safe way. When using react-router's built-in `generatePath` method, there is no guarentee of any type safety for the params passed in:

```ts
const organizationRoute = "/organization/:organizationId"

const path = generatePath(organizationRoute, { lol: "gotchu" })
```
This will cause a runtime error because it is expecting an object like `{ organizationId: "123" }`

Here is the API that we want to be able to achieve:

```ts
organizationRoute.create({ lol: "gotchu" });
```

Here the compiler warns us that we can't do this!

`Error: Property "lol" does not exist in type { organizationId: string }`

With `organizationRoute` we want a route template of `"/organization/:organizationId"` **and** we want to enforce the interpolated params passed in. That means that we want at compile time to ensure that the params for this route are of type `{ organizationId: string }`.

Let's set up the types to do this

```ts
type Param<T extends string = string> = {
  param: T;
};

type PathItem<T extends string = string> = string | Param<T>;

type Params<T extends PathItem[]> = {
  [K in keyof T]: T[K] extends Param<infer N> ? N : never;
};

type Route<T extends PathItem[]> = {
  template(): string;
  // => e.g. return "/organization/:organizationId"
  create(params: { [key in Params<T>[number]]: string }): string;
  // => create({ organizationId: "123" })
  // => "/organization/123"
};
```

Now let's walk through each type, and at the end we'll build it all back up to our goal.

## `type Param`

```ts
type Param<T extends string = string> = {
  param: T;
};
```

The first type `Param` is our container for the param properties. Using "organizationId" as an example:

```ts
const organizationId: Param = { param: "organizationId" }
```

This type (and all the others) uses the `extends` keyword.

As a reminder, it helps to think of `extends` as *`is equal to or more specific than`*

Here type `T` is equal to or more specific than type `string`. For example, the string literal type `"organizationId"` is more specific than the type `string`.

## `type PathItem`

```ts
type PathItem<T extends string = string> = string | Param<T>;
```

The type `PathItem` is a union of `Param` types or string literals. So for example, given the route template `"/organization/:organizationId"` we could declare it as type `PathItem[]` like this:

```ts
const organizationRoute: PathItem[] = [ "organization", { param: "organizationId" } ]
```

## `type Params`

```ts
type Params<T extends PathItem[]> = {
  [K in keyof T]: T[K] extends Param<infer N> ? N : never;
};
```

Finally we get to type `Params` the main dish demonstrating what we set out to learn: a mapped type that uses infer in a returned conditional value type.

### Mulling over mapped types

Consider the following mapped type:

```ts
type M<T, X> = { [K in keyof T]: X }
```

You can identify a mapped type by the `in` keyword:

```ts
  [K in keyof T]
```

where `keyof T` is the type for all property names of type `T` as a union of string literal types (`keyof` is a lookup type, or [index access type](https://www.typescriptlang.org/docs/handbook/advanced-types.html#index-types))

You can think of mapped types like calling `.map` or `.filter` methods on a type (instead of on a value).

Here type `M` returns a new type which substitutes the value type `T[K]` for `X`. 

```ts
type M<T, X> = { [K in keyof T]: X }
// For mapping object types, this can loosely be thought of as:
// Object.keys(T).reduce((R, K) => ({ ...R, [K]: X }))
```

In a mapped type, the left side in the square brackets are the property names to iterate over and the right side is the resultant value type. You can read this example as "for every K in keyof T, substitute type T[K] with type X".

Mapped types are very commonly over object-like types:

```ts
type Value = { value: number }
type Box = { box: string }

// substitute `string` for type `Value`
const boxedValue: M<Box, Value> = { box: { value: 20 } }
```

The type declaration "shape" may deceive you into thinking mapped types are only object-like as this looks very object-y like other `type = { prop: valueType }` type declarations. However mapped types can also be over array and tuple types:

```ts
// substitute string for number
type NumberArray = M<string[], number>
const nums: NumberArray = [1, 2, 3]
```

Whether the return type is object- or array/tuple-like depends on the input type. In the above examples, type `Box` is an object-like type, so the resultant type is too, but type `string[]` is an array type, so the resultant type is an array.

### Questioning Conditionals

Coming back to type `Params`, the substitution of `T[K]` for `X` is conditional, or rather `T[K]` is substituted with `X` or `Y`.

```ts
type Params<T extends PathItem[]> = {
  [K in keyof T]: T[K] extends Param<infer N> ? N : never;
};
```

You are probably already familiar with some of TypeScript's utility types that underneath use a conditional type, such as `NonNullable`.

```ts
type NonNullable<T> = T extends null | undefined ? never : T;
```

You can identify a conditional type by the ternary operator:

```ts
condition ? truthy expression : falsey expression
```

Similar to how mapped types are like `.map` over types instead of values, a conditional type is like a ternary for types instead of values.

### Interlude into `infer`

`infer` is a keyword (that can only be) used within the `extends` clause of a conditional type. It creates a new inferred(!) type variable. It also must be in the truthy branch of the conditional type.

Another way to say it is that `infer` declares a new type variable that is the "unpacked" inner type from the outer generic.

A simplified an example should make this clear:

```ts
type UnpackedPromise<T> = T extends Promise<infer P> ? P : T;
// Read this as:
// if type T is equal to or more specific than type Promise,
// return the type variable P, otherwise return T

type S = UnpackedPromise<Promise<string>> // => string
type N = UnpackedPromise<Promise<number>> // => number
type M = UnpackedPromise<string>          // => string
```

A very useful and well known utility type that uses `infer` is the type `ReturnType`:

```ts
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;
// Read this as:
// if type T is equal to or more specific than the function,
// return the return type of the function as R, otherwise return any
```

### Mapped Manifestation

Let's put it all together now...

```ts
type Params<T extends PathItem[]> = {
  [K in keyof T]: T[K] extends Param<infer N> ? N : never;
  // Read this as:
  // for each key K in keyof T, if T[K] is equal to or more specific than Param<N>,
  // return N, otherwise return never
};
```

Here is a concrete example of a value of type `Params`:
```ts
type OrgRoute = Params<["organization", { param: "organizationId" }, { param: "sourceId" }]>;
// which reduces down to => [never, "organizationId", "sourceId"]
```

We're quite close to our goal! We can create a union of string literal types and "filter" out the type `never` by using the `number` index signature:

```ts
type OrgRoute = Params<["organization", { param: "organizationId" }, { param: "sourceId" }]>;
type OrgRouteParams = OrgRoute[number]
// => "organizationId" | "sourceId"
```

And finally we can then use the resultant union of string literals in a mapped type to enforce the shape of the params in the `create` method:

```ts
type Route<T extends PathItem[]> = {
  template(): string;
  create(params: { [key in Params<T>[number]]: string }): string;
  // e.g. type Params = { [key in "organizationId" | "sourceId"]: string }
  // which is the same as type Params = { organizationId: string; sourceId: string }
};
```

Let's create some utility methods for creating params and routes:

```ts
const param = <P extends string>(param: P): Param<P> => ({ param });

const route = <T extends PathItem[]>(...pathItems: T): Route<T> => {
  const isParam = (p: PathItem): p is Param => !!(p as Param).param;

  return {
    template: () =>
      "/" + pathItems
        .map(p => (isParam(p) ? `:${p.param}` : p)).join("/"),

    create: params =>
      "/" + pathItems
        .map(p => (isParam(p) ? params[p.param as keyof typeof params] : p))
        .join("/")
  };
};
```

And now we've arrived! We can now create a route that enforces the shape of its params:

```ts
const organizationId = param("organizationId")
const organizationRoute = route("organization", organizationId)
organizationRoute.create({ organizationId: "123" })
```

---

Thank you for meandering through a bit about mapped types with me!
