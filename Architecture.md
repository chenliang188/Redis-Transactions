Dashboard uses a simple 2-tier architecture. It is composed of a web application running on top of an in-memory database. The major components include:

1. A Redis in-memory database that is set up as an ElastiCache service.
2. A Quartz-driven worker that periodically reads the sources of metrics and populates the caching layer.
3. A servlet that maps queries to metrics data.
4. A client-side JavaScript framework (D3) that renders the charts.
