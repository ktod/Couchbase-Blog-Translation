#Facebook Game of Year, Powered by NoSQL
原文
http://blog.couchbase.com/cookie-jam-and-couchbase

投稿日
December 18, 2014

原著者
Doug Laird

SGNのCookie Jamは、Facebookのゲームオブザイヤーに選ばれました(
http://time.com/3626297/facebook-games-2014/
). 
この作品は、ソーシャル・モバイル・ゲーミングの交差点に位置しています。
これは、ごく短期間のハイパー成長と継続的な長期の成功を利用する機会に恵まれました。このことは一種のチャレンジでした。
SGNは、Cookie Jamを5月にリリースしました、そして、数ヶ月の内にFacebook上の500万ものプレイヤーを獲得し、そして先週、Facebookのゲームオブイヤーの栄冠に輝きました。 - 全ては8ヶ月の内に起こったことです。(
http://www.businessinsider.com/facebook-names-cookie-jam-the-new-candy-crush-2014-12
)しかしながら、Facebookは2015年にデベロッパーがFacebookとモバイルプラットフォーム向けにゲームを公開することを可能にすることの重要性を認識しました。  (
http://www.theguardian.com/technology/2014/dec/09/facebook-cookie-jam-best-game-candy-crush
)

ソーシャル・モバイルゲームにおいては、高いパフォーマンスと、ユーザ・エクスペリエンスの向上に対応できる許容性が不可欠です。アプリケーションは高いレスポンスが不可欠です。もしも、アプリケーションが遅かったり、遅さを見せたり、レスポンスを返さないと、ユーザはそのゲームをプレイしてくれません。おそらくプレイヤーは二度とプレィしてくれなくなるでしょう。アプリケーションは、ミリセカンド以下のデータアクセスが求められます。そして24時間×365日サービスを提供し続けなければなりません。この要件はそのサービスの初日から求められます。もしもアプリケーションがヒットしたならば、新しいチャレンジに直面します。 - スケーラビリティです。

今日、Cookie Jamは、500万プレイヤーに対してリリース当初と同様のユーザエクスペリエンスを提供しています。
8ヶ月の間に、SGNは、ダウンタイムや、パフォーマンスの低下を招かずにユーザサポートを100万人規模にまでスケールしました。
SGNはNoSQLを、中でもCouchbase Serverを導入しました。
SGNは、5月中は、リリースしたソーシャルモバイルゲームのために数台のノードをメンテナンスしていました。彼らは、さらに数台のノードをCookie Jamの成長に合わせて追加しました。そして、この間ダウンタイムに見舞われることはありませんでした。数台のノードで、SGNは数百万のCookie Jamプレイシャーをサポートしました。

SGNは、トップ10に入るゲーム会社ですが、CEOのChris DeWolfeはゲーム開発者やスタジオにとって、パーティに参加するには遅すぎることはないと信じています
(
http://www.builtinla.com/2014/05/05/what-you-need-know-about-mobile-gaming-according-sgn-s-chris-dewolfe
)
SGNは年に4〜5タイトルのゲームをリリースすることを計画している。これらがヒットすると仮定して、そして継続的に新しいコンテンツを追加し、既存のゲームに対しては新しいフィーチャーを追加することになります。
DeWolfeはゲームデベロッパーにPOCを始めることを推奨しています。POCの要件は敏捷性(agility),柔軟性(flexibility),効率性(efficiency)です。

Couchbase Serverは、ドキュメント型データベースで、JSONに基づく柔軟なデータモデルを提供します。スキーマレスのデータモデルは、アプリケーション開発者に、データモデルを定義することを可能
Couchbase Server is a document database. It features a flexible data model based on JSON. A schemaless data model enables app developers to define the data model and to iterate without having to request and wait for schema changes. After a game is released, it can be supported with a small cluster on-premise or in the cloud. After it becomes a hit, developers can continue to add new content and features with the same agility and flexibility leveraged during initial development. As the number of players continues to increases, Couchbase Server nodes can be added on-demand, as necessary to support more users while maintaining performance. It’s a cost effective solution for developing and maintaining social, mobile games.

Today, developers can build social, mobile games even faster with Couchbase Mobile. Couchbase Mobile includes a lightweight, embedded database for native, offline data access and a synchronization gateway to push and pull data from Couchbase Server. It’s a cross platform solution that relieves developers from having to write custom data access and synchronization code. Whereas iCloud is limited to Apple and iOS and Cloud Save is limited to Google and Android, Couchbase Mobile enables developers to synchronized data across different platforms with a database deployed to the cloud of their choice.

When it comes to social, mobile gaming, NoSQL is the future.
