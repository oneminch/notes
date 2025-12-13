---
alias: Containers
---

- A lightweight alternative to traditional [[virtualization]].
- It allows to package applications and their dependencies into containers, which can run consistently across different computing environments.
- Containers share the host OS kernel, but isolate the application processes, which means that containers are more efficient in terms of resource usage compared to [[Virtualization|VM]]s, as they do not require a full OS for each instance, leading to faster startup times and reduced overhead.
- **Benefits**:
    - Portability
    - Scalability
    - Fault Tolerance
    - Agility
- **Use Cases**:
    - Microservices Architecture
        - Containerization aligns well with microservices, where applications are broken down into smaller, independently deployable services.
        - Each microservice can run in its own container, simplifying deployment and management.
    - Cloud Migration
        - Legacy applications can be encapsulated in containers, facilitating smoother transitions to cloud environments without extensive rewrites.
    - DevOps Practices
        - Containers enable consistent environments for development, testing, and production, which enhances collaboration between development and operations teams.

![[Containers vs. VMs]]

---

## Further

### Videos 🎥

![Containerization Explained](https://www.youtube.com/watch?v=0qotVMX-J5s&pp=ygUGZG9ja2Vy "Containerization Explained")
