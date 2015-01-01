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

JCacheでは、CachingProviderは、キャッシュの生成時にCacheManagerを取得するために使用されます。
各キャッシュは、CacheConfigurationを使用して設定されます。

本プロダクトでは、Java SDK 2.0.0により提供されるClusterとBucketに依存しています。Buketを適切に設定するために、CouchbaseConfiguratoinを本プロダクトで使用されなければなりません(後述のように、CouchabseConfiguration.builder().build()は適正なデフォルト値を提供します)。

各CouchbaseCacheManagerは、CouchaseClusterインスタンスを保持しています。これらは、コネクション・ストリングのリストを使用してブートストラップされます。コネクション・ストリングのデフォルトは、localhostですが、キャッシングプロバイダのsetBootstrapを呼び出して(キャッシュマネージャを生成するよりも)設定を変更することができます。

CouchbaseCacheManagerは新しいCouchbaseCache(これはSDKからのBucketにより返されます)を生成・取得して使用されます。

では、短いながら、完全な例を紹介します。この例は、以下の要件を満たす必要があります:
* クラスタのアドレスはlocalhost:8091とする
* バケット名は"jcache"(password:"jcache")とする

コードサンプル:


`CouchbaseCachingProvider cachingProvider = new CouchbaseCachingProvider();`

`//if your cluster has no localhost node, customize the bootstrap list`

`//cachingProvider.setBootstrap("192.168.1.2", "node3.mydomain.org");`

`//if you already use the Java SDK somewhere else in your application, you should reuse Environment`

`//cachingProvider.setEnvironment(myCouchbaseEnvironmentUsedSomewhereElse);`

``

`//create a cache manager identified by the default URI and ClassLoader`

`CouchbaseCacheManager cacheManager = cachingProvider.getCacheManager();`

``

`//create a cache named myFirstCache`

`CouchbaseCache<String, String> cache = cacheManager.createCache("myFirstCache",` 

`CouchbaseConfiguration.builder("myFirstCache").build());`

``

`//cache a String`

`cache.put("myKey", "myValue");`

``

`//get it back from the cache`

`String inCache = cache.get("myKey");`



Note that cache managers are identified by an URI and a ClassLoader, and are only created if no previous CacheManager was registered for the same identifiers (otherwise the method returns the existing manager).
キャッシュマネージャは、URIとClassLoaderにより特定され、そして前述のCacheManagerは同じ識別子のために登録される(さもなければ、このメソッドは現存するマネージャを返却します)ことを留意してください。

##キャッシング方法のカスタマイズ

We saw that by default, the caching implementation tries to connect to a cluster reachable on localhost, and that we can use CouchbaseConfiguration.builder("cacheName").build() as a default for the configuration of a cache. But was can we customize through CouchbaseConfiguration?
デフォルトにみられるように、キャッシングの実装は、ローカルホストの到達可能なクラスタに接続を試みます。そして、キャッシュの設定のために、デフォルトとしてCouchbaseConfiguration.builder("cacheName").build()が使用されます。しかし、CouchbaseConfigurationを通じて設定をカスタマイズできるでしょうか？

##JCache APIによる一般的な設定について

The CouchbaseConfiguration is based on a default MutableConfiguration (which defines common settings from the API). One can change these settings either by mutating the configuration once it is built, or passing a CompleteConfiguration to be copied to the builder's useBase(CompleteConfiguration base) method.
CouchbaseConfigurationは、デフォルトのMutableConfiguration(これは、APIから通常の設定行います)を基本にしています。
ビルダーのuseBaseメソッド(CompleteConfigurationをベースにした)をコピーされるためのCompleteConfigurationをパッシングもしくは、一度ビルドした設定を変更することにより、設定を変更することができます。


##Bucketの使用について

You have two options: either share a single bucket for multiple caches, prefixing the keys of each cache in the shared bucket, or use a dedicated bucket for a given cache.
2つのオプションが選択できます: 複数のキャッシュのために唯一のbucketを共有し、この共有Bucketに各キャッシュに応じてプレフィックスしたkeyにより使用するか、もしくは、各キャッシュごとに専用のbucketを使用することになります。

By default, a shared bucket named jcache is used (expected password: "jcache"). The default prefix is the name of the cache followed by an underscore.
デフォルトでは、"jcached"という名称の共有Buket(パスワードは"jcache"と想定する)を使用します。(Keyに付与される)デフォルトのプレフィックスは、キャッシュ名+アンダースコア("_")となります。

* Shared cache can be changed via useSharedBucket(String name, String password).
共有キャッシュは、useSharedBucket(String name, String password)を通じて設定を変更することができます。
* Key prefix in a shared cache context can be changed by using withPrefix(String prefix).
共有キャッシュ中のKeyのプレフィックスもまた、withPrefix(String prefix)を使用することで変更することができます。
* Alternative method of using dedicated cache can be activated by using useDedicatedBucket(String name, String password) (it will reset the prefix).
専用のキャッシュを使用する方法では、useDedicatedBucket(String name, String password)を使用することでアクティベートすることができます。(この呼び出しにより、プレフィックスはリセットされます)

##Viewを用いたキャッシュ内の全てのアイテムのリストの取得

Part of the JCache API allows to get all items in a cache, or iterate over them. The best way to achieve that in Couchbase is to use views. So this implementations expects a view to be available to list all items in a cache.
JCache APIの一部は、キャッシュ内の全てのアイテムを取得したり、イテレータとして取得することができます。この機能をCouchbaseで実現するベストな方法は、Viewを使用することです。このため、本プロダクトでは、Viewにより、キャッシュ内の全てのアイテムのリストが取得できることを想定しています。

* By default, the expected design document and view are jcache and the cacheName.
デフォルトでは、想定するデザインドキュメント名は"jcache"で、View名はcacheNameです。
* The design document can be changed by calling viewAllDesignDoc(String designDocName).
このデザインドキュメントは、viewAllDesignDoc(String designDocName)により変更可能です。
* The view name can be changed by calling viewAllViewName(String viewName).
View名は、viewAllViewName(String viewName)により変更可能です。
* Alternatively use viewAll(String designDocName, String viewName) to change both.
その他の設定変更方法としてはviewAll(String designDocName, String viewName)により双方を変更することができます。

The user is expected to create the correct view in the correct bucket for each cache. Don't forget that keys are probably prefixed in the bucket (unless you explicitely used a dedicated bucket).
ユーザは、各キャッシュに対して、適切なViewを適切なBucketに対して生成されていることを想定しています。KeyはBucket内でプレフィックスされちることを忘れないでください。(専用のBucketを明確に使用することなく)

##今後について

This developer preview showcases the general direction we went with this implementation, and has most JCache operations working in a minimal capacity (there's no proper locking yet, so operations described as atomic in the spec, like getAndPut, should not be considered as such).
本デベロッパー・プレビューは、我々が本プロダクトで行おうとしている全般的な方向性を示しています。そして、ほとんどのJCacheオペレーションは、最小限の容量で動作し(まだロック機構は実現いません、このため、オペレーションは、スペック中、アトミックとして描写されています。例えばgetAndPutがそうですが、本来の動作として認識するべきではありません。)

The remaining things to implement in order to have a full specification coverage are: - improving and completing statistics gathering - locking, atomicity of a sub-set of operations - adding support for listeners - adding support for EntryProcessors - implementing read-through and write-through - adding annotation support
残余の事項としては、仕様の全てをカバーするように実装をすすめる必要があります: - 統計収集機能の完全な実装と改善 - ロック機構, オペレーションのサブセットとしての原子性 - リスナーのためのサポートの追加 - EntryProcessorsのサポート - リード・スルー、ライト・スルーの実装 - アノテーション・サポートの追加


##最後に

I hope this will be of interest to you. If you want to learn more about JCache or the Java SDK (and maybe come back here later), here are some resources:
私は、本プロダクトに興味を抱いていただくこと希望します。もしも、JCache及びJava SDK(おそらく後でここに戻ることになるでしょう)についてもっと理解したい場合は以下のリソースを参照ください。

* The JCache Specification(https://github.com/jsr107/jsr107spec)
* The SDK 2.0 Documentation(http://docs.couchbase.com/developer/java-2.0/java-intro.html)

If you have some suggestions or feedback to give, please do! The best place to do so is in the comments below or in the official forums.
もしも、提案及びフィードバックがありましたら、是非お知らせください！コメントをされる場合の最も適切な場所としてオフィシャル・フォーラム(https://forums.couchbase.com/c/java-sdk)があります。

You can also file Issues in our bug tracker (use the "Couchbase Java Client" project, aka JCBC, and use JCache component).
その他にもIssueやバグ・トラッカー(https://issues.couchbase.com/browse/JCBC/component/11910)に保存することもできます("Couchbase Java Client"プロジェクトや、aka JCBC、JCacheを使用してください)

Contributions are also welcome! You would have to sign our CLA (see open-source doc) and let us validate that you did before submitting a pull-request on GitHub.
コントリビューションもまたウェルカムです！我々のCLAの認証(open-source (http://www.couchbase.com/open-source)ドキュメント)が必要で、GitHubのpull-requestをサブミットする前にバリデートを行ってください。

I hope you enjoyed this preview. Happy coding!
このプレビューでエンジョイしていただけることを希望します。ハッピー・コーディング！

