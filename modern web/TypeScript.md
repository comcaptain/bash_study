# Guide for the impatient

references:

- [TypeScript: Documentation - TypeScript for JavaScript Programmers (typescriptlang.org)](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)

## Add type to everywhere

```typescript
class Calculator {
	num1: number;
	readonly num2: number; // readonly is same as final in java

	constructor(num1: number, num2: number) {
		this.num1 = num1;
		this.num2 = num2;
	}

	sum(): string {
		return `${this.num1} + ${this.num2} = ${this.num1 + this.num2}`
	}
}
```

## Duck Typing

*If it walks like a duck and it quacks like a duck, then it must be a duck*

```typescript
interface Point {
	x: number;
	y: number;
}

class MyPoint {
	x: number;
	y: number;
    // This is an optional type
    note?: string;

	constructor(x: number, y: number) {
		this.x = x;
		this.y = y;
	}
}

function showPoint(point: Point): string {
	return `(${point.x}, ${point.y})`;
}

// MyPoint didn't implement interface Point, but since properties are the same, so they are treated as same type
console.info(showPoint(new MyPoint(3, 4)));
// You can even use a plain object
console.info(showPoint({ x: 5, y: 6 }));
// Extra properties is also fine
const rect = { x: 33, y: 3, width: 30, height: 80 };
console.info(showPoint(rect));
```

## Inheritance

```typescript
interface Point {
	x: number;
	y: number;

	display(): string;
}

class MyPoint implements Point {
	x: number;
	y: number;

	constructor(x: number, y: number) {
		this.x = x;
		this.y = y;
	}

	display(): string {
		return `My point: ${this.x}, ${this.y}`;
	}
}

class GrandChildPoint extends MyPoint {

	constructor(x: number, y: number) {
		super(x, y);
	}

	display(): string {
		return super.display() + ": from grandchild";
	}
}

console.info(new GrandChildPoint(3, 4).display())
```

## Generics

```typescript
// If there is a non-number element in the array, compiler error would be reported
const nums: Array<number> = [1, 2, 3];
```

```typescript
class FixedSizeList<T> {
	private values: Array<T>;
	capacity: number;

	constructor(capacity: number) {
		this.values = []
		this.capacity = capacity;
	}

	add(value: T): boolean {
		if (this.values.length >= this.capacity) return false;
		this.values.push(value);
		return true;
	}

	get(index: number): T {
		return this.values[index];
	}

	size(): number {
		return this.values.length;
	}
}
const list = new FixedSizeList(3);
for (let i = 0; i < 5; i++) console.info(list.add(i)); // 3 true, 2 false
for (let i = 0; i < list.size(); i++) console.info(list.get(i));// 0, 1, 2
```

## Union Type

```typescript
// id's type can be either number or string
function printID(id: number | string): void {
	console.info(`ID is ${id}`)
}
printID(1)
printID("32")
```

## Type Alias

Give type an alias

```typescript
// This is very similar to interface
type Point = {
  x: number;
  y: number;
};
type ID = number | string
```

The aliases can be used in the same way as interface

## Object Type

With this, you can declare a type on the fly

```typescript
// note is optional property
function printPoint(point: { x: number, y: number, note?: string }) {
	console.info(`${point.note ? point.note + ": " : ""}(${point.x}, ${point.y})`);
}
printPoint({ x: 3, y: 4 }); // (3, 4)
printPoint({ x: 3, y: 4, note: "Special point" }); // Special point: (3, 4)
```

## Function Type

```tsx
type QCheckboxProps = {
	name: string,
	label: string,
	value: string,
	checkedValues: readonly string[],
	onChange: (newCheckedValues: readonly string[], event: ChangeEvent<HTMLInputElement>) => void
}
```



# Detail

## Basic Types

### Boolean

```
let isDone: boolean = false;
```

### Number

As in JavaScript, all numbers in TypeScript are floating point values.

```
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
```

### String

```
let color: string = "blue"
color = 'red'
let sentence: string = `Hello, my name is ${color}`
```

### Array

```
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];
```

### Tuple

Tuple type allows you to express an aray where the type of a fixed number of elements is known
```
// Declare variable x, whose type is tuple
let x: [string, number];
x = ["hello", 10];
// Error will be reported
x = ["hello", "world"]
```

When accessing an element outside the set of known indicies, a union type is used

```
x[3] = "world"; // OK, 'string' can be assigned to 'string 
 number'
console.log(x[5].toString()); // OK, 'string' and 'number' both have 'toString'

x[6] = true; // Error, 'boolean' isn't 'string | number'
```

### Enum

```
enum RGB { RED, GREEN, BLUE }
console.info(`Red: ${RGB.RED}`); // output is 0

enum RGB2 { RED = 3, GREEN, BLUE }
console.info(`Blue: ${RGB2.BLUE}`); // output is 5

enum RGB3 { RED = "Red", GREEN = "Green", BLUE = "Blue" }
console.info(`Green: ${RGB3.GREEN}`); // output is Green
```

### Any

```
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false;

let strValue: string = notSure; // ok because notSure can be any type, including string. Although actually it's not

notSure.ifNotExists(); // ok, ifItExists might exist at runtime

let prettySure: Object = 4;
prettySure.toFixed(); // error because Object.toFixed does not exist. As you can see, any is more general than Object

```

### Void

`void` typed variable can only be assigned `undefined` or `null`. So it's only useful when used as function's return type

### Null and Undefined

Type used for value `null` and and `undefined` respectively.

By default type `null` and `undefined` are subtypes of all other types, so you can assign `null` and `undefined` value to variable of any type

However, when using the `--strictNullChecks` flag, `null` and `undefined` value can only assigned to `void` and `null`/`undefined` typed variable.

In case you want to pass in either a `string` or `undefined` or `null`, you can use union type `string | undefined | null`

### Never

Normally, no value can be assigned to `never` typed variable. So it can only be used in two cases:

- As function's return type when that function will never return (e.g. always throws an exception or has an infinite loop)
- When condition-statements has covered all possible types:
  ```
  enum Color {Red, Green, Blue}
  function test(c: Color) {
      switch (c) {
          case Color.Red:
          case Color.Green:
          case Color.Blue:
              return "abc";
      }
      let d: never = c;
  }
  ```
  Then value d's type is `never` and can be used to invoke function that accepts never-typed parameter

### Object

Like java, `object` is ancestor of all non-primitive types. Primitive types contain `number`, `string`, `boolean`, `symbol`, `null` or `undefined`

### Type assertion (or type cast)

It looks like type casting in java, but in fact it's different: it does not throw any error even if type casting is wrong (e.g. the value is a number but you cast it as string). It's only used by compiler to do type checking.

This does not work at runtime because the compiled js does not support type at all...

```
let someValue: any = "abc"
let strLength: number = (<string>someValue).length;
strLength = (someValue as string).length;
```