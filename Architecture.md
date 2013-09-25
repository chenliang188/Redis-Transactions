Dashboard will use a simple 2-tier architecture. It is composed of a web application running on top of an in-memory database. The major components of dashboard include:

1. A Redis in-memory database that is set up as an ElastiCache service.
2. A Quartz-driven worker that periodically surveys the sources of metrics and updates the Redis cache.
3. A Java HTTP servlet that receives queries and returns the cached metrics data. It interprets and executes the queries over the Redis cache and assembles the metrics suitable for presentation.
4. A client-side JavaScript framework like D3 that renders the charts.

The technologies to be used will include Gradle, Redis, Spring, Play (Java/Scala), and D3.
