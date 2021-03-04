# typescript

```js
type UnionType = {
    field: "one",
    value: "two"
  } | {
    field: "a" | "b",
    value: "a1" | "b1" | "c1"
  }
  
type InferValue<T extends UnionType, K extends UnionType["field"]> = 
// Use `extends` keyword to limit, refer to TypeScript `Exclude` implementation
T extends (
  // get the matching struct, but it cannot be used directly
  {
    field: K;
    value: any;
  } extends T
    ? T
    : never
)
  ? T["value"]
  : never;

const myFunc = <K extends UnionType["field"]>(
  field: K,
  value: InferValue<UnionType, K>
): any => ({ // any
  field,
  value,
});

myFunc("one", "two") // OK
myFunc("a", "a1") // OK
myFunc("a", "b1") // OK
myFunc("b", "a1") // OK
```

```js
export type IMutations = {
    'setState': [ payload: { name: string; age: number} ],
    'nextTip': [ key: 'green' | 'red', val: number ]
}
export type IActions = {
    'queryCardDetail': [payload: object],
    'queryBalanceSubscribeInfo': [payload: { cityCode: string }]
}
type NameOrId<T extends keyof U, U extends { [key: string]: any[] }> = U[T];
type FO<T extends { [key: string]: any[] }> = {
    [P in keyof T]: (key: P, ...payload: NameOrId<P, T>) => void
}
type FUnion = FO<IMutations>;
type DUnion = FO<IActions>;
type Visitor<T extends {[key: string]: any[]}> = {
    [P in keyof T]: (
        builtin: {
            commit: FUnion,
            dispatch: DUnion
        }, 
        ...payload: T[P]
    ) => void
}

const actionns: Visitor<IActions> = {
    async queryCardDetail({commit, dispatch}, payload) {
        const nextTip =commit['nextTip'];
        nextTip('nextTip', 'red', 20);
    },
    async queryBalanceSubscribeInfo({commit, dispatch}, { cityCode }) {
        const queryCardDetail =dispatch['queryCardDetail'];
        queryCardDetail('queryCardDetail', {});

        const queryBalanceSubscribeInfo =dispatch['queryBalanceSubscribeInfo'];
        queryBalanceSubscribeInfo('queryBalanceSubscribeInfo', {});
    }
}
```
