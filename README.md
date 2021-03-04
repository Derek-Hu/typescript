Interface VS Types:
https://github.com/microsoft/TypeScript/issues/33099
This is working as intended. Object literal types have implicit index signatures---interfaces do not.

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

interface IDefine { [key: string]: any[] }

type InferArgs<T extends IDefine, K extends keyof T> = T[K];

type ICommit<IMS extends IDefine> = <K extends keyof IMutations>(
    action: K,
    ...value: InferArgs<IMS, K>
  ) => any;

type IDispatch<IAS extends IDefine> = <K extends keyof IActions>(
    action: K,
    ...value: InferArgs<IAS, K>
  ) => any;

type Visitor<T extends IDefine, M extends IDefine> = {
    [P in keyof T]: (
        builtin: {
            commit: ICommit<M>,
            dispatch: IDispatch<T>
        }, 
        ...payload: T[P]
    ) => void
}

const actionns: Visitor<IActions, IMutations> = {
    async queryCardDetail({commit, dispatch}, payload) {
        // const nextTip =commit['nextTip'];
        // nextTip('nextTip', 'red', 20);
        commit('nextTip', 'red', 20);
        commit('setState', 'red', 20);
    },
    async queryBalanceSubscribeInfo({commit, dispatch}, { cityCode }) {
        // const queryCardDetail =dispatch['queryCardDetail'];
        // queryCardDetail('queryCardDetail', {});
        dispatch('queryCardDetail', {});

        // const queryBalanceSubscribeInfo =dispatch['queryBalanceSubscribeInfo'];
        // queryBalanceSubscribeInfo('queryBalanceSubscribeInfo', {});
        dispatch('queryBalanceSubscribeInfo');
    }
}
```
