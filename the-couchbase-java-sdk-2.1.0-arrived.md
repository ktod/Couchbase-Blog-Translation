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

このブログポストでは、私は新しい機能についてフォーカスします。もしも全ての変更点についてチェックしたい場合は、リリースノートを参照してください。質問に関しては、フォーラムでいつでも質問できます、また、もしもあなたが、issueであると考えた場合には、我々のJIRAに登録してください。

ところで、私はいくつかの変更点を追加しました、これは、特定のワークロード下ではパフォマンスを改善をもたらします。
本ブログポストには、ベンチマークを掲載していませんが、2.0.3と性能比較すると性能が改善していることを確認できるでしょう。

##SDKの入手方法


リリースバージョンを
<a href="http://search.maven.org/#artifactdetails%7Ccom.couchbase.client%7Cjava-client%7C2.1.0%7Cjar">Maven Central</a> で公開しています。また <a href="http://packages.couchbase.com/clients/java/2.1.0/Couchbase-Java-Client-2.1.0.zip">アーカイブ</a>も提供しています。
```ruby:qiita.rb
<script src="https://gist.github.com/daschl/87edfca23b4f52cab4f0.js"></script>
```

This release brings official (yet still experimental) support for N1QL DP4. It is not backwards compatible with DP3 because the underlying streaming responses have changed quite a bit. Highlights include:

Simple, parametrized and prepared Statements.
Extended query options like timeouts and scan consistency.
Enhanced synchronous and asynchronous QueryResults for more flexible error handling.
Simon wrote a great blog post two weeks ago, so if you want to learn more you should check it out here.

