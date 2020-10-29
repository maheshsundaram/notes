# 📈 d3 with react 🏹 hooks
**Let's set up the foundation for bringing together [d3](https://github.com/d3/d3/wiki) and [react](https://reactjs.org/docs/getting-started.html) with [hooks](https://reactjs.org/docs/hooks-intro.html)!**

⚡ *Just want the code? [Jump to the end](#tldr)*

It's an exciting time to get started with React with the new Hooks paradigm for handling stateful logic in components.

It's **also** an exciting time to get started with d3 as it too now offers a more declarative API called the "general update pattern" or [data join](https://bost.ocks.org/mike/join/).

✨ Even better, this new API for d3 works really nicely with React hooks! Previously with React class based components it wasn't so simple to work with d3 and React, which required working with lifecycle hooks (like `componentDidMount` and so on).

Let's dive in!

---

When using d3 with React, there are two ways we can manipulate the DOM:

1. Let React handle everything and only use d3's math and positioning utilities
2. Let React handle everything except for the `<svg>`, which we'll pass to d3 to handle

🛶 Both ways have their merits but we're going with #2 because:

- it's simpler to get started with
- you can re-use other (non-React) d3 code
- React hooks makes this quite painless
- we get to use d3's transitions and animations

🍱 With that context, let's write some code.

First, let's [create new a react project with npx](https://create-react-app.dev/docs/getting-started):

```
npx create-react-app d3-react-hooks
```

After that all installs, open up `App.jsx`, remote its contents and replace it with this:

```jsx
import React from "react";

function App() {
  return (
    <div>
    </div>
  );
}

export default App;
```

So, we have our basic React app ready. Now let's add d3. First, install it:

```
npm install d3 --save
```

Then add it to `App.js`:

```diff
import React from "react";
+import { select } from "d3";
```

Here we'lll be using [`select`](https://github.com/d3/d3-selection/blob/v1.4.1/README.md#select) from d3.

Then we need to add an `<svg>`:

```diff
 function App() {
   return (
     <div>
+      <svg />
     </div>
   );
 }
```

Now pass the `<svg>` to d3. React hooks makes this really easy — we can use the [`useRef`](https://reactjs.org/docs/hooks-reference.html#useref) hook to allow d3 to access the `<svg>` element.

```diff
-import React from "react";
+import React, { useRef } from "react";
 import { select } from "d3";

 function App() {
+  const svgRef = useRef(null);
+
   return (
     <div>
-      <svg />
+      <svg ref={svgRef} />
     </div>
   );
 }
```

So how do we actually start rendering a chart? We want to render our app/component and then pass the `<svg>` to d3, and the [`useEffect` hook](https://reactjs.org/docs/hooks-reference.html#useeffect) is perfect for that — the app renders, then the `useEffect` hook runs, giving control of the `<svg>` over to d3:

```diff
-import React, { useRef } from "react";
+import React, { useRef, useEffect } from "react";
 import { select } from "d3";

 function App() {
   const svgRef = useRef(null);

+  useEffect(() => {
+    const svg = select(svgRef.current);
+
+    svg
+      .selectAll("circle")
+      .data([ 1 ])
+      .join("circle")
+      .attr("r", 50)
+      .attr("cx", 100)
+      .attr("cy", 100)
+      .attr("stroke", "#f582ae")
+      .attr("fill", "#f582ae")
+  });
+
   return (
     <div>
       <svg ref={svgRef} />
     </div>
   );
 }
```

This bit draws a pink circle — you would want to replace it with whatever you're drawing:

```tsx
svg
  .selectAll("circle")
  .data([ 1 ])
  .join("circle")
  .attr("r", 50)
  .attr("cx", 100)
  .attr("cy", 100)
  .attr("stroke", "#f582ae")
  .attr("fill", "#f582ae")
```

☕ What about dynamic charts? We can use the `useState` hook for handling data, and pass that as a dependency to `useEffect` to re-run when it changes.

```diff
-import React, { useRef, useEffect } from "react";
+import React, { useRef, useEffect, useState } from "react";
 import { select } from "d3";

 function App() {
   const svgRef = useRef(null);
+  const [data, setData] = useState([1]);

   useEffect(() => {
     const svg = select(svgRef.current);

     svg
       .selectAll("circle")
-      .data([ 1 ])
+      .data(data)
       .join("circle")
       .attr("r", 50)
       .attr("cx", 100)
       .attr("cy", 100)
       .attr("stroke", "#f582ae")
       .attr("fill", "#f582ae")
-  });
+  }, [data]);

   return (
     <div>
       <svg ref={svgRef} />
     </div>
   );
 }
```

So now if we have `data` set dynamically (like with an API call or user input) the chart will re-render.

And that's it!

---

# tldr

🧁 Here's all the code together:

```tsx
import React, { useRef, useEffect, useState } from "react";
import { select } from "d3";

function App() {
  const svgRef = useRef(null);
  const [data, setData] = useState([1]);

  useEffect(() => {
    const svg = select(svgRef.current);

    svg
      .selectAll("circle")
      .data(data)
      .join("circle")
      .attr("r", 50)
      .attr("cx", 100)
      .attr("cy", 100)
      .attr("stroke", "#f582ae")
      .attr("fill", "#f582ae")
  }, [data]);

  return (
    <div>
      <svg ref={svgRef} />
    </div>
  );
}

export default App;
```
