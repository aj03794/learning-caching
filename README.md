# Caching

- https://blog.bytebytego.com/p/a-crash-course-in-caching-part-1

## Intro

![img.png](./images/img.png)

- Simple caching technique
  - When data is requested, the service first checks the cache for data
  - If the data is present in the cache, return it
  - If data not found, service retrieves if from storage, stores it in cache, and then returns 
    it to the client

![img_1.png](./images/img_1.png)


### Differences between Caches and Storage

- Cache systems store frequently accessed data in memory
- Storage systems store data on disks
- Cache systems typically have a much smaller data capacity than storage systems because 
  memory is more expensive than disk
  - Caches can only store a subset of the total dataset

--------------

- Data stored in caches are not designed for long-term data persistence and durability
- Caches are used to increase performance

-------------

- Caches are optimized for supporting heavy traffic and high concurrency
- Storage systems are better suited for durably storing and managing large amounts of data

------------

- If data that is requested is available in the cache, it is known as a `cache hit`, otherwise 
  it is a `cache miss`
- System performs better when there are more cache hits
- `Cache hit ratio` is measure for cache effectiveness

-----------

- Caching is an effective solution where:
  - Data changes infrequently
  - The same data is accessed multiple times
  - Same output is produced repeatedly
  - Results of time-consuming queries or calculations are worth caching

## Cache Deployment

### In-process Cache

- Cache looked in app itself
- Limited data capacity and is restricted by memory size
- Cached data is lost if process restarts

![](./images/img_2.png)

### Inter-process Cache

- Local cache
- Runs in a separate process on the same machine
- No data loss when application process restarts
- No network cost
- Business logic and cache on the same host can complicate operation and maintenance costs
- Cached data will be lost if host crashes

![](./images/img_3.png)

### Remote Cache

- Cache deployed on separate machine
- Better scalability
- Easier to deploy and maintain
- Can be shared by multiple apps
- Accessing data requires a network call
- Redis and Memcached are typically deployed on remote machine

![](./images/img_4.png)

## Why do we need to cache

- Crucial to improve performance and reduce latency
- `Amdahl's law` states that the faster the cache is, the more speedup can be achieved
- By optimizing performance of the cache, the system can achieve greater speed and efficiency

-----------------

- Data access patterns likely follow the Pareto distribution (80/20 rule), where a small amount 
  of data is responsible for a large amount of traffic
- By caching this small amount of frequently accessed data, the cache can intercept most of the 
  requests, reducing load on the storage system

----------------

- Caching comes with a cost
- Requires more resources and adds complexity to system's implementation
- In a distributed env, maintaining data consistency and availability becomes a challenge, and 
  edge cases must be considered

## Cache replacement and invalidation

- Optimizing cache hits requires anticipating data access patterns and preloading the cache as 
  much as possible
- Predicting these patterns can be a challenging task
- `Principle of locality` cab be helpful here

--------------

- Temporal locality refers to tendency for recently accessed data to  be accessed again in the 
  near future
  - Example: trending tweet

--------------

- Spatial locality refers to the tendency for data that is close to recently accessed data to be 
  accessed soon after
  - Example, in a database, when a value is taken from a sequence, it is likely that the next 
    values in the sequence will be accessed later

### Cache replacement

- Best approach is to store most frequently accessed data in the cache in order to maximize its 
  effectiveness
- Several cache replacement policies
  - Least Recently Used (LRU)
    - discards the least recently used items first based on their last accessed timestamp
    - This is suitable for caching hot keys
  - Least Frequently Used (LFU)
    - Discards least frequently used items first based on their usage count
      - Used for caching trending tweets, and is often used in conjunction with LRU to filter 
        the most recent popular tweets
  - Allowlist policy
    - Data items in the allowlist will not be evicted from the cache
    - Suitable where popular data items cna be identified in advance and preloaded into the cache

### Cache invalidation

- Crucial process in maintaining data consistency between storage and cache
- When data is modified, updating both storage and cache simultaneously can be challenging
- Cache invalidation techniques are used to remove or refresh stale data in the cache

![](./images/img_5.png)

#### Invalidation when modifying

- Actively invalidating data in cache when the application modifies the data in storage
- Widely used technique

#### Invalidation when reading

- App checks validity of data when it is retrieved from cache
- If data is stale, read from storage and written to cache
- Can add more complexity to add

#### Time-to-live (TTL)

- App sets a lifetime for the cached data, and the data is removed or marked as invalid once the 
  TTL expires
- Popular option