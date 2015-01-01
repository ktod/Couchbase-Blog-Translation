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
`pom.xml`に以下の依存関係を追記します:

     <dependencies>
         <dependency>
             <groupId>com.couchbase.client</groupId>
             <artifactId>java-cache</artifactId>
             <version>1.0.0-dp</version>
        </dependency>
    </dependencies>

    <repositories>
        <repository>
            <id>couchbase</id>
            <name>couchbase repo</name>
            <url>http://files.couchbase.com/maven2</url>
            <snapshots><enabled>false</enabled></snapshots>
        </repository>`
    </repositories>`


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

    CouchbaseCachingProvider cachingProvider = new CouchbaseCachingProvider();
    //ノードがlocalhostで動作していない場合、cachingProviderをbootstrap listを指定してカスタマイズする
    //setBootstrap("192.168.1.2", "node3.mydomain.org");
    //すでにアプリケーションの何処かでJava SDKを使用しているならば環境を再利用する
    //cachingProvider.setEnvironment(myCouchbaseEnvironmentUsedSomewhereElse);
    
    //キャッシュマネージャを生成する(デフォルトのURLとClassLoader）
    CouchbaseCacheManager cacheManager = cachingProvider.getCacheManager();
    
    //キャッシュを生成(キャッシュ名 myFirstCache)
    CouchbaseCache<String, String> cache = cacheManager.createCache("myFirstCache",
    CouchbaseConfiguration.builder("myFirstCache").build());
    
    //文字列をキャッシュ
    cache.put("myKey", "myValue");
    
    //キャッシュから値を取得
    String inCache = cache.get("myKey");


このキャッシュマネージャは、URIとClassLoaderにより特定され、そしてこれまでに同じ識別子のために登録されたCacheManagerが無い場合にのみ生成されることに留意してください。(さもなければ、このメソッドは現存するマネージャを返却します)

##キャッシング方法のカスタマイズ

デフォルトでは、キャッシング機構はローカルホストの到達可能なクラスタに接続を試みるように実装されています。そして、キャッシュの設定として、デフォルトでCouchbaseConfiguration.builder("cacheName").build()が使用することができます。しかし、CouchbaseConfigurationを通じて設定をカスタマイズことはできるでしょうか？

##JCache APIによる一般的な設定について

CouchbaseConfigurationは、MutableConfiguration(これはAPIから通常の設定を行います)をベースにしています。
設定変更のためには、一度ビルドした設定を変更するか、ビルダーのuseBaseメソッド(CompleteConfigurationをベースにしています)をコピーするためにCompleteConfigurationを送信することによりこれを行うことができます。


##Bucketの使用について

2つのオプションが選択できます: 複数のキャッシュのために唯一のbucketを共有し、この共有Bucketに各キャッシュに応じて各keyにプレフィックスを付与することにより使用する方法か、もしくは、各キャッシュごとに専用のbucketを使用する方法です。

デフォルトでは、"jcached"という名称の共有Buket(パスワードは"jcache")を使用します。(Keyに付与される)デフォルトのプレフィックスは、キャッシュ名+アンダースコア("_")となります。

* 共有キャッシュは、useSharedBucket(String name, String password)を通じて設定を変更することができます。
* 共有キャッシュ中のKeyのプレフィックスは、withPrefix(String prefix)を使用することで変更することができます。
* 専用のキャッシュを使用する方法では、useDedicatedBucket(String name, String password)を使用することでアクティベートすることができます。(この呼び出しにより、プレフィックスはリセットされます)

##Viewを用いたキャッシュ内の全てのアイテムのリストの取得

JCache APIの一部は、キャッシュ内の全てのアイテムを取得したり、イテレータとして取得することができます。この機能をCouchbaseで実現する最適な方法は、Viewを使用することです。このため本プロダクトでは、Viewにより、キャッシュ内の全てのアイテムのリストが取得できることを想定しています。

* デフォルトでは、デザインドキュメント名は"jcache"で、View名はcacheNameです。
* このデザインドキュメントは、viewAllDesignDoc(String designDocName)により変更可能です。
* View名は、viewAllViewName(String viewName)により変更可能です。
* その他の設定変更方法としてはviewAll(String designDocName, String viewName)により双方を変更することができます。

ユーザは、各キャッシュに対して、適切なViewと適切なBucketに対して生成されていることを想定しています。KeyはBucket内でプレフィックスされるであろうということを忘れないでください。(特定のBucketを明確に使用することなく)

##今後について

本デベロッパー・プレビューは、我々が本プロダクトで行おうとしている全般的な方向性を示しています。そして、ほとんどのJCacheオペレーションは、最小限の容量で動作し(まだロック機構は実現いません、このため、オペレーションは、スペック中、アトミックとして描写されています。例えばgetAndPutがそうですが、本来の動作として認識するべきではありません。)

残余の事項としては、仕様の全てをカバーするように実装をすすめる必要があります: - 統計収集機能の完全な実装と改善 - ロック機構, オペレーションのサブセットとしての原子性 - リスナーのためのサポートの追加 - EntryProcessorsのサポート - リード・スルー、ライト・スルーの実装 - アノテーション・サポートの追加


##最後に

私は、本プロダクトに興味を持っていただくこと希望します。もしも、JCache及びJava SDK(おそらく後でここに戻ることになるでしょう)についてもっと理解したい場合は以下のリソースを参照ください。:

* The JCache Specification(https://github.com/jsr107/jsr107spec)
* The SDK 2.0 Documentation(http://docs.couchbase.com/developer/java-2.0/java-intro.html)

もしも、提案及びフィードバックがありましたら、是非お知らせください！コメントをされる場合の最も適切な場所としてオフィシャル・フォーラム(https://forums.couchbase.com/c/java-sdk)があります。

その他にもIssueやバグ・トラッカー(https://issues.couchbase.com/browse/JCBC/component/11910)に保存することもできます("Couchbase Java Client"プロジェクトや、aka JCBC、JCacheを使用してください)

コントリビューションもまたウェルカムです！我々のCLAの認証(open-source (http://www.couchbase.com/open-source)ドキュメント)が必要で、GitHubのpull-requestをサブミットする前にバリデートを行ってください。

このプレビューでエンジョイしていただけることを希望します。

ハッピー・コーディング！

