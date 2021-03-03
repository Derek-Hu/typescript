# typescript

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
