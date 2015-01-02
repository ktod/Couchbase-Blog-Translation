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
)
However, Facebook recognizes the importance of ensuring developers can publish games to Facebook and mobile platforms (
http://www.theguardian.com/technology/2014/dec/09/facebook-cookie-jam-best-game-candy-crush
)in 2015.

A social, mobile game must meet high performance and availability requirements to meet increasing user experience expectations. The app must be responsive. If it’s slow or appears slow or is unresponsive, users will not play it. They may never play it again. The app requires sub-millisecond access to data, and it must be available 24 hours a day, 365 days a year. These are the requirements on day one. When it becomes a hit, it faces a new challenge - scalability.

Today, Cookie Jam provides the same user experience with 5 million players it did the day it was released. In 8 months, SGN scaled its infrastructure to support millions of users without suffering downtime or performance degradation. SGN leveraged NoSQL. In particular, Couchbase Server. SGN was maintaining a few nodes in May to support the social, mobile games they’ve released. They added a few more nodes to support the growth of Cookie Jam, and they did it without suffering downtime. With several nodes, SGN supports millions of Cookie Jam players.

SGN may be one of the big 10 gaming companies, but Chris DeWolfe, CEO, believes(
http://www.builtinla.com/2014/05/05/what-you-need-know-about-mobile-gaming-according-sgn-s-chris-dewolfe
) it’s not too late for game developers and studios to join the party. SGN plans to release four or five games a year, assuming they will be hits, and to continue to add new content and features to existing games. DeWolfe recommends game developers begin with proofs of concepts. This requires agility, flexibility, and efficiency.

Couchbase Server is a document database. It features a flexible data model based on JSON. A schemaless data model enables app developers to define the data model and to iterate without having to request and wait for schema changes. After a game is released, it can be supported with a small cluster on-premise or in the cloud. After it becomes a hit, developers can continue to add new content and features with the same agility and flexibility leveraged during initial development. As the number of players continues to increases, Couchbase Server nodes can be added on-demand, as necessary to support more users while maintaining performance. It’s a cost effective solution for developing and maintaining social, mobile games.

Today, developers can build social, mobile games even faster with Couchbase Mobile. Couchbase Mobile includes a lightweight, embedded database for native, offline data access and a synchronization gateway to push and pull data from Couchbase Server. It’s a cross platform solution that relieves developers from having to write custom data access and synchronization code. Whereas iCloud is limited to Apple and iOS and Cloud Save is limited to Google and Android, Couchbase Mobile enables developers to synchronized data across different platforms with a database deployed to the cloud of their choice.

When it comes to social, mobile gaming, NoSQL is the future.
