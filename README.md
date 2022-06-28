# Functional Programming

Some Tasks to understand Functional Programing Basics

## TASK 1   Partial function application, a comparison between typescript and elm

### Background

`f: W -> Y -> Z`
`f: W -> (Y -> Z)`

```elm
add: Int -> Int -> Int

add 2

addTwo: Int -> Int
addTwo = add 2

addTwo 4
```
**note** Functions in elm can be partially applied by default!

### Deliverables

1. Create the same function in both typescript and elm.
2. Demonstrate its partial application.

### Constraints

- The function must have an arity of >=3

### Possible Starters

> let's build an API URL!

```elm
apiUrl: String -> String -> String -> String
apiUrl resource action args = "https://api.example.com/" ++ resource ++ "/" ++ action ++ "?" ++ args
```
> let's just build a URL!

```elm
constructUrl : String -> String -> String -> String
constructUrl protocol domainName extension  =
    protocol ++ "://" ++ domainName ++ "." ++ extension
```

### Useful links

[ellie](https://ellie-app.com/new)

### Notes

- Order of parameters is important! Parameters that you know you will have should be leftmost (default values are leftmost)
- Partial application is a way to produce a new function with less arity.

### Extensions

- Make the function a higher-order function

## Task 1 Solution

### Elm example To build an API URL  with Arity = 3

```
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
```
### Outputs
```
> apiUrlArgs
"https://api.example.com/version2/metrics?apiKey=1234" : String

> apiUrlAction "apiKey=235"
"https://api.example.com/version2/metrics?apiKey=235" : String

> apiUrlResource "semetrics" "apiKey=567"
"https://api.example.com/version2/semetrics?apiKey=567" : String

> apiUrl "version3" "demetrics" "apiKey=789"
"https://api.example.com/version3/demetrics?apiKey=789" : String

```

### TypeScript Example
```ts
const apiUrl = (resource: string, action: string, args: String): String => `https://api.example.com/${resource}/${action}?${args}`;

const apiUrlResource = (action: string, args: String): String => apiUrl("version2",action,args)

const apiUrlAction = (args: String): String => apiUrlResource("metrics",args)

const apiUrlArgs = apiUrlAction("apiKey=1234")
```
### Outputs
```
console.log(apiUrlArgs); //"https://api.example.com/version2/metrics?apiKey=1234"

console.log(apiUrlAction("apiKey=234")); //"https://api.example.com/version2/metrics?apiKey=234" 

console.log(apiUrlResource("semetrics", "apiKey=456"));//"https://api.example.com/version2/semetrics?apiKey=456" 

console.log(apiUrl("version3","semetrics", "apiKey=456"));//"https://api.example.com/version3/semetrics?apiKey=456"
```
### Another way of doing using Typescript
```ts
const apiUrl = (resource:string) => (action:string) => (args:string) : string=> `https://api.example.com/${resource}/${action}?${args}`

const apiUrlResource = (action:string) => (args:string) : string => apiUrl("version2")(action)(args)

const apiUrlAction = (args:string) : string => apiUrlResource("metrics")(args)
```
### Outputs
```ts
console.log(apiUrlResource("metrics")("apikey=1234")) // "https://api.example.com/version2/metrics?apikey=1234" 

console.log(apiUrlAction("apikey=456")) //"https://api.example.com/version2/metrics?apikey=456" 

console.log(apiUrlResource("semetrics")(apiUrlAction("apiKey=2333")) //"https://api.example.com/version2/semetrics?https://api.example.com/version2/metrics?apiKey=2333"
```

## TASK 2  Function composition in typescript (`compose`  &  `pipe`))

> What do you do when you are faced with a difficult problem?

### Background

`f: X -> Y`
`g: Y -> Z`

`h x = g(f(x))`

The signature of this function is `h: X -> Z`

### Deliverables

1. Demonstrate function composition in typescript with `compose`
2. Demonstrate function composition in typescript with `pipe`

What are the differences between `compose` and `pipe` (left vs. right association?)

### Extensions

- Write a compose operator in typescript!
- Where do we use function composition in the `heyauto` codebase? Where should we?

### Useful links

[ramda compose](https://ramdajs.com/docs/#compose)
[ramda pipe](https://ramdajs.com/docs/#pipe)
[fp-ts pipe](https://gcanti.github.io/fp-ts/modules/function.ts.html#pipe)
[fp-ts flow](https://gcanti.github.io/fp-ts/modules/function.ts.html#flow)

## TASK 2 Solution

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

> Class example of a generic implementation

### Background

- A unary operation has one input, a binary operation has 2
- In a generic type or method definition, a type parameter is a placeholder for a specific type that a client specifies when they create an instance of the generic type

```typescript
type UnaryFunction<A, B> = (_: A) => B;
type BinaryFunction<A, B, C> = (_: A, _: B) => C;

const f = <A, B, C>(
  g: BinaryFunction<A, B, C>,
  a: A
): UnaryFunction<B, C> => {};
```

### Deliverables

1. Implement `f`
2. Provide a name for `f` (this should be a name we are all familiar with)
3. Demonstrate the use of `f`

### Useful links

[ts generic types](https://www.typescriptlang.org/docs/handbook/2/generics.html)

## TASK 3 Solution
```ts
type  UnaryFunction<A, B> = (_: A) => B;

type  BinaryFunction<A, B, C> = (_: A, _2: B) => C;

const isEqual = (a : string, b : number): boolean => a.length === b

const arr = [2,3,4]

const partial = <A, B, C>(

g: (a: A, b: B) => C,

a: A

): UnaryFunction<B, C> => {

return (b:B):C => g(a,b)

};

console.log(partial(isEqual,"john")(4)) // true

console.log(partial(isEqual,"john")) // (b) => g(a, b)
```
