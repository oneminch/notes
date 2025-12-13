---
alias: Object-Relational Mapping
---

- Object-Relational Mapping is a technique for interacting with a database using the object-oriented paradigm of a programming language.
- ORMs basically serve as an abstraction layer between the application and the database providing increased developer productivity.
- Instead of writing raw [[SQL]] queries, we can use ORM libraries such as SQLAlchemy ([[Python]]) and Sequelize ([[JavaScript|JS]]) to interact with a database.

```js
const sqlz = new Sequelize({
    dialect: 'sqlite',
    storage: 'path/to/db.sqlite'
});

// Define a model
const User = sqlz.define('User', {
    firstName: {
        type: DataTypes.STRING
    },
    lastName: {
        type: DataTypes.STRING
    }
}, { ... });

// Create a new user
const userJane = await User.create({ firstName: "Jane", lastName: "Doe" });

// Find all users
const users = await User.findAll();

// Use raw queries
const users = await sequelize.query('SELECT * FROM Users');
```
