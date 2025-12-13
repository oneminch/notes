- A powerful query language for APIs that offers several key advantages over traditional REST APIs. 
- Enables declarative data fetching.
- Uses a strongly-typed schema to define the structure of the API. 
	- The schema describes the types of data available and the relationships between them.
- Expose a single endpoint and responds to queries, rather than multiple endpoints for different resources like in [[APIs#RESTful|REST]], which simplifies API management and versioning.
- Typically only `POST` and `GET` request methods are supported.
- **Benefits**
	- Have smaller payload sizes when compared to REST APIs.
	- Removes the need for versioning.
	- Saves multiple round trips.

```graphql
type User {
	id: ID
	name: String
}

type Query {
	user(id: ID): User
}
```

## Queries

- Queries allow clients to request specific data fields they need.

```graphql
query {
	user(id: "123") {
		name
	}
}
```

- Fields and queries can accept arguments to filter or customize results.

```graphql
query {
	user(id: "123") {
		name
		posts(limit: 5) {
			title
		}
	}
}
```

- Aliases allow querying the same field multiple times with different arguments.

```graphql
query {
	user1: user(id: "123") { name }
	user2: user(id: "456") { name }
}
```

- Fragments are reusable units of a query, useful for reducing duplication.

```graphql
fragment UserFields on User {
	name
	email
}

query {
	user(id: "123") {
		...UserFields
	}
}
```

- Variables enable dynamic queries by passing values separately from the query structure.

```graphql
query($userId: ID!) {
	user(id: $userId) {
		name
	}
}
```

## Mutations

- Mutations are used to modify data on the server. 
- They are defined similarly to queries but are used for create, update, and delete operations.

## Tools

### Apollo Client

#### Set Up

```jsx
import { ApolloClient, InMemoryCache, ApolloProvider } from '@apollo/client';

const client = new ApolloClient({
	uri: 'https://graphql-endpoint.com/graphql',
	cache: new InMemoryCache()
});

function App() {
	return (
		<ApolloProvider client={client}>
			{/* Your app components */}
		</ApolloProvider>
	);
}
```

#### Queries

```jsx
import { useQuery, gql } from '@apollo/client';

const GET_USERS = gql`
	query GetUsers {
		users {
			id
			name
			email
		}
	}
`;

function UserList() {
	const { loading, error, data } = useQuery(GET_USERS);
	
	if (loading) return <p>Loading...</p>;
	if (error) return <p>Error: {error.message}</p>;

	return (
		<ul>
			{data.users.map(user => (
				<li key={user.id}>{user.name}</li>
			))}
		</ul>
	);
}
```

##### With Variables

```jsx
const GET_USER = gql`
	query GetUser($id: ID!) {
		user(id: $id) {
			name
			email
		}
	}
`;

function UserProfile({ userId }) {
		const { loading, error, data } = useQuery(GET_USER, {
				variables: { id: userId },
		});
		
		if (loading) return <p>Loading...</p>;
		if (error) return <p>Error: {error.message}</p>;
		
		return (
				<div>
						<h2>{data.user.name}</h2>
						<p>{data.user.email}</p>
				</div>
		);
}
```

#### Mutations

```jsx
import { useMutation, gql } from '@apollo/client';

const ADD_USER = gql`
		mutation AddUser($name: String!, $email: String!) {
				addUser(name: $name, email: $email) {
						id
						name
						email
				}
		}
`;

function AddUserForm() {
		const [addUser, { data, loading, error }] = useMutation(ADD_USER);
		
		const handleSubmit = (e) => {
				e.preventDefault();
				addUser({ variables: { name: "John Doe", email: "john@example.com" } });
		};
		
		if (loading) return <p>Submitting...</p>;
		if (error) return <p>Error: {error.message}</p>;
		
		return (
				<form onSubmit={handleSubmit}>
						{/* Form fields */}
						<button type="submit">Add User</button>
				</form>
		);
}
```

##### Updating the Cache

- After a mutation, it's generally a good idea to update the Apollo cache.

```jsx
const [addUser] = useMutation(ADD_USER, {
	update(cache, { data: { addUser } }) {
		const { users } = cache.readQuery({ query: GET_USERS });
		cache.writeQuery({
			query: GET_USERS,
			data: { users: [...users, addUser] },
		});
	}
});
```

### Apollo Server

#### Define Schema

```js
// schema.js
const { gql } = require('apollo-server');

module.exports = gql`
		type User {
				id: ID!
				firstName: String!
				lastName: String!
		}
		
		type Query {
				users: [User]
				user(id: ID!): User
		}
		
		type Mutation {
				createUser(firstName: String!, lastName: String!): User
				deleteUser(id: ID!): String
		}
`;
```

#### Create Resolvers

```js
// resolvers.js
let users = []; // In-memory data store

module.exports = {
		Query: {
				users: () => users,
				user: (parent, { id }) => users.find(user => user.id === id),
		},
		Mutation: {
				createUser: (parent, { firstName, lastName }) => {
						const newUser = { id: `${users.length + 1}`, firstName, lastName };
						users.push(newUser);
						return newUser;
				},
				deleteUser: (parent, { id }) => {
						const userIndex = users.findIndex(user => user.id === id);
						if (userIndex === -1) throw new Error("User not found");
						users.splice(userIndex, 1);
						return `User with id ${id} deleted`;
				},
		},
};
```

#### Set Up Server

```js
// server.js
const { ApolloServer } = require('apollo-server');
const typeDefs = require('./schema');
const resolvers = require('./resolvers');

const server = new ApolloServer({ typeDefs, resolvers });

server.listen().then(({ url }) => {
		console.log(`🚀 Server ready at ${url}`);
});
```

#### Test Operations

```graphql
mutation {
		createUser(firstName: "John", lastName: "Doe") {
				id
				firstName
				lastName
		}
}
```

```graphql
query {
		users {
				id
				firstName
				lastName
		}
}
```

```graphql
mutation {
		deleteUser(id: "1")
}
```

---
## Further

### Reads 📄

- [You probably don't need GraphQL](https://mxstbr.com/thoughts/graphql)
