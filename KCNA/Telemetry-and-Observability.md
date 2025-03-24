# Telemetry and Observability

- Use `kubectl logs <pod_name> -c <container_name_optional>` to view the logs for a container
- Distributed system tracing tracks requests across a complex application consisting of multiple components and services,  each request has a unique identifier which helps you understand what is going on as requests make their way through an entire system
- A **Trace** is data about a request as it moves through the system, a set of related events across multiple components
- A **Span** is part of a **trace**, representing the request moving through the segment (component) of the system