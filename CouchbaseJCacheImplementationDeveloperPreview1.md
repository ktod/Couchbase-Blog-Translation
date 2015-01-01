#Couchbase JCache Implementation Developer Preview 1
原文
http://blog.couchbase.com/jcache-dp1
著者
Simon Baslé

SDKエンジニアリングチームを代表して、私は、JCache specification(JSR107)の実装が完了し、デベロッパープレビューを利用可能になったことをアナウンスいたします。

JCache specificaton は、開発者がアプリケーションの開発に際し、キャッシングに関する標準APIを使用することを可能にします。
本実装は、Couchbase Server及びJava SDK 2.0の使用を前提として、効率的かつ高性能のキャッシングの（仕組み）を可能にするものです。

私達は、この初期プロセスにおいて、開発者の皆様にお試しいただき、本プロダクトに関しフィードバック及び提案を受け付けることを希望いたします。
これは、本実装が、完全に終了したということを意味することではなく、いくつかの事項についてはなお作業の途上にありますが、基本的なオペレーションや設定は機能している状態であることにご留意ください。


本プロダクトの新しい用途をぜひ発見してください！

##利用方法
Maven Centralにて公開する予定ですが、現時点では、Couchbase JCache implemaentationは、Couchbase社のMavenレポジトリより利用することができます。
pom.xmlに以下の依存関係を追記します:


`<dependencies>`

`    <dependency>`

`        <groupId>com.couchbase.client</groupId>`

`        <artifactId>java-cache</artifactId>`

`        <version>1.0.0-dp</version>`

`    </dependency>`

`</dependencies>`

 
 

`<repositories>`

`    <repository>`

`        <id>couchbase</id>`

`        <name>couchbase repo</name>`

`        <url>http://files.couchbase.com/maven2</url>`

`        <snapshots><enabled>false</enabled></snapshots>`

`    </repository>`

`</repositories>`


その他の選択肢としては、直接jarファイル(http://files.couchbase.com/maven2/com/couchbase/client/java-cache/1.0.0-dp/)を取得することができます。(java-cache, java-client, java-coreライブラリも取得する必要があります。)

さらに関心をお持ちの方ならば、githubから直接最新のコード(https://github.com/couchbaselabs/couchbase-java-cache)を取得してビルドすることもできます。

##概要

In JCache, a CachingProvider is resolved and used to obtain a CacheManager, in turn used to create Caches. Each cache can be configured using a CacheConfiguration.
JCacheでは、CachingProviderは、キャッシュの生成時に、CacheManagerを取得するために使用されます。
各キャッシュは、CacheConfigurationを使用して設定されます。

This implementation relies on the Cluster and Bucket introduced in the Java SDK 2.0.0. In order to correctly configure the underlying bucket, a CouchbaseConfiguration must be used in this implementation (but note that CouchbaseConfiguration.builder().build() should provide sane defaults, see below).
本プロダクトでは、Java SDK 2.0.0 により提供されるClusterとBucketに依存しています。Buketを適切に設定するために、CouchbaseConfiguratoinは、本プロダクトで使用されなければなりません(後述のように、CouchabseConfiguration.builder().build()は適正なデフォルト値を提供します)。

Each CouchbaseCacheManager has an underlying CouchbaseCluster instance. These are bootstrapped using a list of connection strings that default to localhost but can be changed by calling setBootstrap on the caching provider prior to creating the cache manager.
各CouchbaseCacheManagerは、CouchaseClusterインスタンスを保持しています。これらはブートストラップされます。

The CouchbaseCacheManager can then be used to create and obtain a new CouchbaseCache, which is backed by a Bucket from the SDK.

Let's see a short complete example. This example requires the following couchbase context:

A cluster reachable on localhost:8091
A bucket named "jcache" (password: "jcache") in this cluster
Once that is the case, the following snippet can be run:

CouchbaseCachingProvider cachingProvider = new CouchbaseCachingProvider();
//if your cluster has no localhost node, customize the bootstrap list
//cachingProvider.setBootstrap("192.168.1.2", "node3.mydomain.org");
//if you already use the Java SDK somewhere else in your application, you should reuse Environment
//cachingProvider.setEnvironment(myCouchbaseEnvironmentUsedSomewhereElse);

//create a cache manager identified by the default URI and ClassLoader
CouchbaseCacheManager cacheManager = cachingProvider.getCacheManager();

//create a cache named myFirstCache
CouchbaseCache<String, String> cache = cacheManager.createCache("myFirstCache", CouchbaseConfiguration.builder("myFirstCache").build());

//cache a String
cache.put("myKey", "myValue");

//get it back from the cache
String inCache = cache.get("myKey");
Note that cache managers are identified by an URI and a ClassLoader, and are only created if no previous CacheManager was registered for the same identifiers (otherwise the method returns the existing manager).

Customizing The Way Caching Is Done

We saw that by default, the caching implementation tries to connect to a cluster reachable on localhost, and that we can use CouchbaseConfiguration.builder("cacheName").build() as a default for the configuration of a cache. But was can we customize through CouchbaseConfiguration?

Common Settings in the JCache API

The CouchbaseConfiguration is based on a default MutableConfiguration (which defines common settings from the API). One can change these settings either by mutating the configuration once it is built, or passing a CompleteConfiguration to be copied to the builder's useBase(CompleteConfiguration base) method.

What Bucket To Use?

You have two options: either share a single bucket for multiple caches, prefixing the keys of each cache in the shared bucket, or use a dedicated bucket for a given cache.

By default, a shared bucket named jcache is used (expected password: "jcache"). The default prefix is the name of the cache followed by an underscore.

Shared cache can be changed via useSharedBucket(String name, String password).
Key prefix in a shared cache context can be changed by using withPrefix(String prefix).
Alternative method of using dedicated cache can be activated by using useDedicatedBucket(String name, String password) (it will reset the prefix).
Relying On Views To List All Items In A Cache

Part of the JCache API allows to get all items in a cache, or iterate over them. The best way to achieve that in Couchbase is to use views. So this implementations expects a view to be available to list all items in a cache.

By default, the expected design document and view are jcache and the cacheName.
The design document can be changed by calling viewAllDesignDoc(String designDocName).
The view name can be changed by calling viewAllViewName(String viewName).
Alternatively use viewAll(String designDocName, String viewName) to change both.
The user is expected to create the correct view in the correct bucket for each cache. Don't forget that keys are probably prefixed in the bucket (unless you explicitely used a dedicated bucket).

What's Next?

This developer preview showcases the general direction we went with this implementation, and has most JCache operations working in a minimal capacity (there's no proper locking yet, so operations described as atomic in the spec, like getAndPut, should not be considered as such).

The remaining things to implement in order to have a full specification coverage are: - improving and completing statistics gathering - locking, atomicity of a sub-set of operations - adding support for listeners - adding support for EntryProcessors - implementing read-through and write-through - adding annotation support

Conclusion

I hope this will be of interest to you. If you want to learn more about JCache or the Java SDK (and maybe come back here later), here are some resources:

The JCache Specification
The SDK 2.0 Documentation
If you have some suggestions or feedback to give, please do! The best place to do so is in the comments below or in the official forums.

You can also file Issues in our bug tracker (use the "Couchbase Java Client" project, aka JCBC, and use JCache component).

Contributions are also welcome! You would have to sign our CLA (see open-source doc) and let us validate that you did before submitting a pull-request on GitHub.

I hope you enjoyed this preview. Happy coding!
