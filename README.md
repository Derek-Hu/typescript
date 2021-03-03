# typescript

```js
export type Instruction =
    | { type: 'local.set', name: string, value: string }
    | { type: 'i32.const', value: object }
    | { type: 'i32.add', left: number, right: number };

interface IndexPageState{
    name: string;
    age: number;
}

interface IGuideReadStatus{
    one: string;
    two: string;
}
type IGS = keyof IGuideReadStatus;
// export type IMutations = 
//     | { fname: 'setState', args: [ payload: Partial<IndexPageState> ] }
//     | { fname: 'nextTip', args: [ key: IGS, val: number ] }
export type IMutations = {
    'setState': [ payload: Partial<IndexPageState> ],
    'nextTip': [ key: IGS, val: number ]
}
// export type IMutationsKeys = keyof IMutations;


export type IActionsUnion =
    | { fname: 'queryCardDetail', args: [payload: object] }
    | { fname: 'queryBalanceSubscribeInfo', args: [payload: { cityCode: string }] }
export type IActions = {
    'queryCardDetail': [payload: object],
    'queryBalanceSubscribeInfo': [payload: { cityCode: string }]
}
type Visito2r<T extends { type: string}> = {
    [P in T['type']]: (instruction: Extract<T, { type: P }>) => void
}

type NameOrId<T extends keyof U, U extends { [key: string]: any[] }> = U[T];
// type IDispatch<T extends {[key: string]: any[]}> = (key: T, ...payload: T['args'] ) => void;

type IDispatch<T extends { fname: string, args: any[] }> = (key: T['fname'], ...payload: T['args'] ) => void;
// type ICommit<T extends { fname: string }> = (key: T['fname'], payload: Extract<T, { fname: T['fname'] }> ) => void;
// type ICommit<T extends { fname: string, args: any[] }> = (key: T['fname'], ...payload: T['args'] ) => void;

type FO<T extends { [key: string]: any[] }> = {
    [P in keyof T]: (key: P, ...payload: NameOrId<P, T>) => void
}
type FUnion = FO<IMutations>;
type DUnion = FO<IActions>;

type IFUKyes = keyof FUnion;

type AIF = FUnion[IFUKyes];
type ICommit<T extends { fname: string, args: any[] }> = (key: T['fname'], ...payload: T['args'] ) => void;
// type ICommit<A extends { [key: string]: any[] }, K> = (key: K, ...payload: A[K] ) => void;
// type Visitor<T extends { fname: string,  args: any[] }> = {
//     [P in T['fname']]: (
//         builtin: {  commit: ICommit<IMutations, IMutationsKeys>, dispatch: IDispatch<IActions> }, 
//         payload: T['args']
//     ) => void
// }
type Visitor<T extends {[key: string]: any[]}> = {
    [P in keyof T]: (
        builtin: {  commit: FUnion,
             dispatch: DUnion
            }, 
        ...payload: T[P]
    ) => void
}
const actionns: Visitor<IActions> = {
    async queryCardDetail({commit, dispatch}, payload) {
        const nextTip =commit['nextTip'];
        nextTip('nextTip', 'two', 20);
        
        nextTip('nextTip', { age: 20});
        commit('setState', { age: 20});
        commit('setState', 'one', 20);

        dispatch('queryCardDetail');
        dispatch('queryBalanceSubscribeInfo')
    },
    async queryBalanceSubscribeInfo({commit, dispatch}, { cityCode }) {
        commit('nextTip', 'one', 12);
        commit('nextTip', 'two', 20);
        commit('nextTip', 'three');
        commit('setState', { age: 20});
        commit('setState', 'one');
        dispatch('queryCardDetail');
        dispatch('queryBalanceSubscribeInfo')
    }
}
```
