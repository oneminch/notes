- Like [[Server-Sent Events]] and [[WebSockets]], polling is a real-time data technique where the client repeatedly checks a data source at regular intervals to see if new data is available.

```js
const pollData = () => {
	fetch("https://api.example.com/data")
		.then((response) => response.json())
		.then((data) => {
			console.log("New data:", data);
		})
		.catch((error) => console.error("Error:", error))
		.finally(() => {
			// Schedule next poll (every 5 seconds)
			setTimeout(pollData, 5000);
		});
};

pollData();
```
