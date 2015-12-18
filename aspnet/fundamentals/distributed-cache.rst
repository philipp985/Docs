Working with a Distributed Cache
================================
By `Steve Smith`_

Distributed caches can improve the performance and scalability of ASP.NET 5 applications, especially when hosted in a cloud or server farm environment. This article explains how to work with ASP.NET 5's built-in distributed cache abstractions and supported implementations.

.. contents:: In this article:
  :local:
  :depth: 1

`Download sample from GitHub <https://github.com/aspnet/docs/aspnet/fundamentals/distributed-cache/sample>`_. 

What is a Distributed Cache
---------------------------
A distributed cache is an implementation of a cache that is shared by multiple application servers (see :ref:`caching-basics` to learn more). The information in the cache is not stored in the memory of individual web servers, and the cached data is available to all of the application's servers. This provides several advantages:

1. Cached data is the same between all web servers, so users don't see different results depending on which web server handles their request
2. Cached data survives web server restarts and deployments. Individual web servers can be removed or added without impacting the cache.
3. The source data store has fewer requests made to it (than with multiple in-memory caches or no cache at all).

Of course, like any cache, a distributed cache can dramatically improve an application's responsiveness, since typically data can be retrieved from the cache much faster than from a relational database (or web service).

Configuring a distributed cache for use by your ASP.NET 5 applications depends on the specific implementation chosen. This article describes how to configure both Redis and SQL Server implementations below. Regardless of which implementation is selected, the application interacts with the cache using a common `IDistributedCache <https://github.com/aspnet/Caching/blob/1.0.0-rc1/src/Microsoft.Extensions.Caching.Abstractions/IDistributedCache.cs>`_ interface.

The DistributedCache Interface
------------------------------
The ``IDistributedCache`` interface includes both synchronous and async methods. The interface allows items to get added, retrieved, and removed from the distributed cache implementation. The complete list of methods supported by the interface is:

Connect, ConnectAsync
	Confirms that a connection with the cache can be established. Not required; the operations below will call the appropriate ``Connect`` method internally as needed.

Get, GetAsync
	Takes a string key and retrieves a cached item as a ``byte[]`` if found in the cache.
	
Set, SetAsync
	Adds an item (as ``byte[]``) to the cache using a string key.
	
Refresh, RefreshAsync
	Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).
	
Remove, RemoveAsync
	Removes a cache entry based on its key.

In your ASP.NET 5 application, you can configure the specific implementation of ``IDistributedCache`` in your ``Startup`` class, and add it to the container in the ``ConfigureServices`` method. Of course, you will also need to specify any necessary dependencies in ``project.json``. Once this configuration is in place, your application's :doc:`middleware` or MVC Controller classes can request an instance of ``IDistributedCache`` via their constructor, and these will be provided via :doc:`dependency-injection`.

.. note:: There is no need to use a Singleton or Scoped lifetime for ``IDistributedCache`` instances (at least for the built-in implementations). You can also create an instance wherever you might need one, but this can make your code harder to test, and violates the `Explicit Dependencies Principle <http://deviq.com/explicit-dependencies-principle/>`_.

Using a Redis Distributed Cache
-------------------------------
Redis

Using a SQL Server Distributed Cache
-------------------------------
SQL Server

Recommendations
---------------
Choose Redis or SQL Server based on your existing infrastructure/environment and youre team's experience.

Only choose the in-memory implementation for testing purposes, not in production environments.
