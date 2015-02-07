#The Couchbase Java SDK 2.1.0 arrived!
原文
http://blog.couchbase.com/the-couchbase-java-sdk-2.1.0-arrived

投稿日
February 4, 2015

原著者
Michael Nitschinger

チーム全体を代表して、Couchbase Java SDK 2.1.0がついにリリースされたことをご報告します。
2.1は、2.0に比較して、大量の新しい機能が追加されたとともに、同じぐらい多数の小規模なエンハンスメントやバグフィックスが提供されます。

これは、ますます多くのユーザがアプリケーションをアップグレードすることと同じく、新しいユーザがリアクティブ・データアクセス・パターンを探求したことにより、素晴らしい価値あるフィードバックがもたらされた結果です。

このブログポストでは、私は新しい機能についてフォーカスします。もしも全ての変更点についてチェックしたい場合は、
<a href="http://docs.couchbase.com/developer/java-2.1/release-notes.html">リリースノート</a>を参照してください。質問に関しては、<a href="https://forums.couchbase.com/">フォーラム</a>でいつでも質問できます、また、もしもあなたが、issueであると考えた場合には、我々の<a href="http://issues.couchbase.com/browse/JCBC">JIRA</a>に登録してください。

ところで、私はいくつかの変更点を追加しました、これは、特定のワークロード下ではパフォマンスを改善をもたらします。
本ブログポストには、ベンチマークを掲載していませんが、2.0.3と性能比較すると性能が改善していることを確認できるでしょう。

##SDKの入手方法


リリースバージョンを
<a href="http://search.maven.org/#artifactdetails%7Ccom.couchbase.client%7Cjava-client%7C2.1.0%7Cjar">Maven Central</a> で公開しています。また <a href="http://packages.couchbase.com/clients/java/2.1.0/Couchbase-Java-Client-2.1.0.zip">アーカイブ</a>も提供しています。

dependency.xmlのリンク
```
<script src="https://gist.github.com/daschl/87edfca23b4f52cab4f0.js"></script>
```

本リリースは、N1QL DP4に対するオフィシャルサポート(未だ試験段階ですが)を提供します。これは、DP3との後方互換性を持っていません。なぜならば、基盤となるストリーミングレスポンスが若干変更されたためです。

主な変更点：

* シンプルで、パラメタライズされ、調整されたステートメント
* タイムアウトやスキャン・コンシステンシのような、拡張されたクエリオプション。
* 強化された、同期・非同期のQueryResultによりもたらされる、より柔軟なエラーハンドリング。

サイモンが、素晴らしいブログポストを２週間前にポストしています、もしもこの点について詳しく知りたい方は、ぜひサイモンの<a href="http://blog.couchbase.com/n1ql-dp4-java-sdk">ブログ</a>をご参照ください。

##Spatial View Queryのサポート

さらに、View Queryのサポートを追加しました。Couchbase Server 3.0.2では以前として実験的サポートである点に注意してください。しかし、間もなくオフィシャルにサポートが開始される予定です。2.1.0はまた、より古いCouchbase Serverリリースに対してもコンパチブルではありません、理由はレスポンスフォーマットが若干変更されたためです。

簡単な例として、店舗情報を格納する場合を想像してください。：


Spatial Viewで定義できるデータフォーマットをベースにしていて、latitudeとlongitudeのみをインデックスできるだけでなく、店舗の開店時間なども格納できます：


さらに、3次元空間（地理×時刻）に対するクエリも行うことができます。位置情報(Bounding Box)と時間帯情報(Time Range)を条件として、開店している店舗に対する検索を行うことができます。

今後ブログポスト＆サンプルを追加していきます。近い将来に私達は、（Spatial Viewについて）Couchbase Server側でフルサポート版が提供されることを期待できます。


##アイドルサポート(Heartbeats/Keepalive)

特定のソケットへのクライアント接続について、データロードが何も無い場合、フィアーウォール(もしくはその他の機器)が、不要となったコネクションであると判断し、この接続を切断する可能性があります。
これを防ぐため、SDKは、アイドル状態のソケットについて、ハートビート・メッセージを30秒毎に送信しています。これらのメッセージは、もちろんもしも通常のトラフィックが一定のインターバルの内に流れている場合には送信されません。

このインターバルについて、変更することができるようになりました、また、この値を0にすることで、このインタバールを機能停止にすることができるようになりました。


##Pluggableリトライ戦略

多数の方からリクエストされた機能として、リクエストを直ちにディスパッチすることが不可能である場合に、早期に失敗させる方法の提供というものがありました。例えば、ノード障害や、クラスタのフェールオーバの間、ドキュメントの一部（この対象となったノード上に存在する（ドキュメント集合全体のうちの）特定のパーティションについては全てのターゲット（ドキュメント））は、書き込むことができません。デフォルトでは、SDKは、少し後になって、オペレーションをリトライを試み、最終的には呼び出し側のタイムアウトに至るでしょう。

新しい、フェール・ファーストモードでは、これまでに代わり、直ちにリクエストをキャンセルし、（リクエストがリトライすべきか否かを判断可能な）呼び出し側へ、より早期にフィードバック・ループを提供します。
The new fail fast mode instead would immediately cancel the request, providing faster feedback loops to the caller who then can determine if the request should be retried or not. This new strategy can be enabled on the environment like this:


In addition, we made the retry strategy pluggable so you can even define your own. Since this is quite advanced it is not covered in this blog post, but you can expect more information soon in the documentation on that topic. In the meantime if you are curious, just check out the (quite simple) strategies that ship with the SDK.

Finally, a configurable "maximum request lifetime" has been added to the environment which is utilized by the default "best effort" strategy to determine if the request should still be retried or is cancelled instead. This is needed in order to avoid requests circling around for a very long time and take up precious slots in the RingBuffers.

##Subscribable Event Bus

A generic event bus has been added to the environment which is utilized by the core and the client to publish events to potential application subscribers. Currently, only Bucket open/close and Node connect/disconnect events are published, but in the future we plan to greatly extend this by also collecting and publishing performance metrics and other types of events and warnings.

It is very easy to subscribe and react to those kind of events, thanks to RxJava and the streaming nature of our Observables:



##DNS SRV Bootstrap

It is now possible to fetch the list of bootstrap nodes through a DNS SRV record. This allows system administrators to centralize their bootstrap node list config in a very easy manner. It needs to be enabled on the environment to make it work. You can find more information here.

##What's next?

While we already have many ideas for a 2.2 release, we are now taking a step back and are planning to further stabilize this branch with bugfix releases as needed. In addition, we are shifting the focus to enhanced framework and "up the stack" integration - so stay tuned in the next couple of weeks for blog posts and announcements!
