# Exhaustive Reducer in Typescript

Explanation is WIP, but hopefully the code read straightforwardly:

```ts
const RED = "RED";

type RedAction = {
  type: typeof RED;
}

const BLUE = "BLUE";

type BlueAction = {
  type: typeof BLUE;
}

const GREEN = "GREEN";

type GreenAction = {
  type: typeof GREEN;
}

type Action =
  RedAction |
  BlueAction |
  GreenAction;

type State = {
  value: number;
  color: typeof RED | typeof BLUE | typeof GREEN;
}

function reducer(action: Action, state: State): State {
  switch (action.type) {
    case RED:
      return { ...state, color: RED };
    case BLUE:
      return { ...state, color: BLUE };
    // case GREEN:
    //   return { ...state, color: GREEN };
    default:
      return ((_: never): never => {
        throw new Error("Reducer is not exhaustive");
      })(action);
    //   ^^^^^^ Argument of type 'GreenAction' is not assignable to parameter of type 'never'.
    // Since the GREEN case is missing (commented out) there is a compile error. The world is type safe again.
  }
}
```
