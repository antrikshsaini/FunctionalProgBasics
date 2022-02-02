# Assignment 1, Functional Programming

## TASK 1   Partial function application

### Elm example To build an API URL  with Arity = 3

> apiUrl: String -> String -> String -> String
| apiUrl resource action args =
|   "https://api.example.com/" ++ resource ++ "/" ++ action ++ "?" ++ args
|   
<function> : String -> String -> String -> String


> apiUrlResource: String -> String -> String
| apiUrlResource =
|   apiUrl "version2"
|   
<function> : String -> String -> String

> apiUrlAction: String -> String
| apiUrlAction =
|   apiUrlResource "metrics"
|   
<function> : String -> String

**************** Higher Order Function ****************************
> apiUrlArgs: String
| apiUrlArgs =
|   apiUrlAction "apiKey=1234"
|   
"https://api.example.com/version2/metrics?apiKey=1234" : String

### Outputs
> apiUrlArgs
"https://api.example.com/version2/metrics?apiKey=1234" : String

> apiUrlAction "apiKey=235"
"https://api.example.com/version2/metrics?apiKey=235" : String

> apiUrlResource "semetrics" "apiKey=567"
"https://api.example.com/version2/semetrics?apiKey=567" : String

> apiUrl "version3" "demetrics" "apiKey=789"
"https://api.example.com/version3/demetrics?apiKey=789" : String






### TypeScript Example
```ts
const apiUrl = (resource: string, action: string, args: String): String => `https://api.example.com/${resource}/${action}?${args}`;

const apiUrlResource = (action: string, args: String): String => apiUrl("version2",action,args)

const apiUrlAction = (args: String): String => apiUrlResource("metrics",args)

const apiUrlArgs = apiUrlAction("apiKey=1234")

console.log(apiUrlArgs);
console.log(apiUrlAction("apiKey=234"));
console.log(apiUrlResource("semetrics", "apiKey=456"));
console.log(apiUrl("version3","semetrics", "apiKey=456"));
```
#### Another way of doing
```ts
const apiUrl = (resource:string) => (action:string) => (args:string) : string=> `https://api.example.com/${resource}/${action}?${args}`

const apiUrlResource = (action:string) => (args:string) : string => apiUrl("version2")(action)(args)

const apiUrlAction = (args:string) : string => apiUrlResource("metrics")(args)


console.log(apiUrlResource("metrics")("apikey=1234"))

console.log(apiUrlAction("apikey=456"))

console.log(apiUrlResource("semetrics")(apiUrlAction("apiKey=2333"))
```

## TASK 2   ## Function composition in typescript (`compose`  &  `pipe`))

### Using Compose

```ts
import 'R' from ramda

interface  IPerson {
	name: string;
}
const person: IPerson = {
	name: "John",
};

const getName = (n: IPerson) => n.name

const getLength = (str: string): number => str.length;

const isEven = (num: number): boolean => num % 2 === 0

const composedFn = R.compose(isEven, getLength, getName)(person)
const composedFn2 = R.compose(isEven, getLength)("johny")

console.log(composedFn) //true
console.log(composedFn2) //false
```
 
 ### Using pipe

```ts
import 'R' from ramda

interface  IPerson {
	name: string;
}
const person: IPerson = {
	name: "John",
};

const getName = (n: IPerson) => n.name

const getLength = (str: string): number => str.length;

const isEven = (num: number): boolean => num % 2 === 0

const composedFn = R.pipe(getName,getLength,isEven)(person)
const composedFn2 =  R.pipe(getLength,isEven)("johny")

console.log(composedFn) //true
console.log(composedFn2) //false
```
### Difference Between Compose and Pipe

Compose is Right to Left Association, compose from right to left. The last argument may have any arity(any number of arguments), remaining must be unary (must have only one argument).

Pipe is opposite Left to Right Association. First argument may have any Arity, the remaining arguments must be unary.

### Compose Operator

Array.reduce function
signature of reduce
```
function reduce(callbackfn: (previousValue: T, currentValue: T) => T): T;
```
Lets make simple pipe function left to Right
```
export const pipe = (...fns) =>
  fns.reduce((prevFn, nextFn) => value => nextFn(prevFn(value)));
```
In Typescript with fn1( atleaast one first argument)
```
export const pipe = <R>(fn1: (a: R) => R, ...fns: Array<(a: R) => R>) =>
  fns.reduce((prevFn, nextFn) => value => nextFn(prevFn(value)), fn1);
```
similarly switch order of nextFn and PrevFn

```
export const compose = <R>(fn1: (a: R) => R, ...fns: Array<(a: R) => R>) =>
  fns.reduce((prevFn, nextFn) => value => prevFn(nextFn(value)), fn1);
```
reference [check article on compose function](https://dev.to/ascorbic/creating-a-typed-compose-function-in-typescript-3-351i)

### use of compose in HEYAUTO

when dispatching an action to redux. when requesting something from backend, we dispatch an action to reducer. 
for example 
```ts
const  dispatch = useDispatch();
const  addFaqTopic = compose(dispatch, faqTopicActions.addFaqTopic.request);
```

## TASK 3  (Generic Implementation (class example))

```ts
type  UnaryFunction<A, B> = (_: A) => B;

type  BinaryFunction<A, B, C> = (_: A, _2: B) => C;

const isEqual = (a : string, b : number): boolean => a.length === b

const getLength = (a:string): boolean => a.length % 2 == 0

const arr = [2,3,4]

const partial = <A, B, C>(

g: (a: A, b: B) => C,

a: A

): UnaryFunction<B, C> => {

return (b:B):C => g(a,b)

};

console.log(partial(isEqual,"john")(4))

console.log(partial(isEqual,"john"))
```
