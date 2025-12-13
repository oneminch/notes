- ==Inheritance== involves designing objects around _what they are_
    - Objects have 'is-a' relationship.

```js
class Animal {
	constructor(name) {
		this.name = name;
	}

	eat() {
		console.log(`${this.name} is eating.`);
	}
}

class Bird extends Animal {
	constructor(name) {
		super(name);
	}

	fly() {
		console.log(`${this.name} is flying.`);
	}
}

const robin = new Bird("Robin");
robin.eat();
robin.fly();
```

- ==Composition== involves designing objects around _what they do_.
    - Objects have 'is-a' relationship.

```js
const eater = (animal) => ({
	eat: () => console.log(`${animal.name} is eating.`),
});

const flyer = (animal) => ({
	fly: () => console.log(`${animal.name} is flying.`),
});

const eatingFlyer = (name) => {
	let animal = {
		name: name,
	};

	return Object.assign({}, eater(animal), flyer(animal));
};

const robin = eatingFlyer("Robin");
robin.eat();
robin.fly();
```
