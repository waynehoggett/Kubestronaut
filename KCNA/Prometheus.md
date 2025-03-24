# Prometheus

 - **Prometheus** is an open source tool for monitoring and alerting, it's primary focus is collecting metric data
 - Prometheus metric types:
    - **Counter** - A single number that only only increase or reset to zero
    - **Gauge**  - A single number that can go up or down
    - **Histogram** - Counts observations that fit into configurable buckets, keeps a count of how many items fit into each bucket
    - **Summary** - Similar to a histogram, but it uses dynamic quantiles over a sliding time window, basically the buckets are determined automatically
- Use **Grafana** to build useful visualizations of Prometheus data