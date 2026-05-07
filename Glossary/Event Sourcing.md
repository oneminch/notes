- Event sourcing is a way of storing application data as a **sequence of events** instead of just saving the latest state. 
- Each change, like “order created” or “item added,” is recorded as an immutable event, and the current state is rebuilt by replaying those events.
- Provides a full history of what happened, which makes auditing, debugging, and reconstructing past states easier.
    - Useful in systems where traceability matters, like finance, logistics, and complex business workflows.
- The primary downside is complexity: managing event schemas, rebuilding state from history, and concurrency and long-term storage.

- **How it works**
    - A user action happens.
    - The system records an event describing that change.
    - Those events are stored in order in an event store.
    - To know the current state, the system replays the event history.

- **Example**: 
    - In a simple online order system: instead of storing only “order = shipped,” event sourcing would store “order created,” “item added,” “payment accepted,” and “order shipped,” then derive the current order status from that history.

---
## Further

### Videos 🎥

- [Event Sourcing Explained using Football (YouTube)](https://www.youtube.com/watch?v=xPmQxYIi5fA)