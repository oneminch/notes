- Web browsers provide a [[JavaScript]] API that can be used to measure the performance of code thru the `Performance` interface.
- Among the methods for this interface, `performance.now()` returns a point in time where it's called, and it's precise to one thousandth of a millisecond.
- This method is similar to the `Date.now()` method with a few key differences.
    - `performance.now()` return time elapsed relative to the start of the program. The values returned from this method increase independent from the system clock. It is more precise.
    - `Date.now()` returns time elapsed relative to ==the Unix epoch== and it is dependent on the system clock. It is also less precise.

```js
const list = new Array(10000).fill(1);
const searchNum = 10;

let t0 = performance.now();
for (let num of list) {
	if (num === searchNum) {
		console.log("Found!");
		break;
	}
}
let t1 = performance.now();

console.log(`Searching took ${t1 - t0} ms to run.`);
```
