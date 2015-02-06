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

このブログポスト
In this blog post we'll be focussing on the new features. If you want to check out the full list of changes, please refer to the Release Notes. If you have questions you can ask them on the Forums and if you think you've hit an issue, please file it on our JIRA.

Oh and by the way, we've also pushed some changes that should improve the performance under certain workloads. Even if there are no benchmarks in this blog post, there is a good chance that you'll see improved latency and throughput compared to 2.0.3.

##Getting the SDK
<script type="text/javascript">
    var showStr = '<div>こんにちは。</div>';  
    document.write(showStr);
</script>

<p>As always, we are distributing the GA release from <a href="http://search.maven.org/#artifactdetails%7Ccom.couchbase.client%7Cjava-client%7C2.1.0%7Cjar">Maven Central</a> and as an <a href="http://packages.couchbase.com/clients/java/2.1.0/Couchbase-Java-Client-2.1.0.zip">archive</a>.</p>
<script src="https://gist.github.com/daschl/87edfca23b4f52cab4f0.js"></script>

As always, we are distributing the GA release from Maven Central and as an archive.
This release brings official (yet still experimental) support for N1QL DP4. It is not backwards compatible with DP3 because the underlying streaming responses have changed quite a bit. Highlights include:

Simple, parametrized and prepared Statements.
Extended query options like timeouts and scan consistency.
Enhanced synchronous and asynchronous QueryResults for more flexible error handling.
Simon wrote a great blog post two weeks ago, so if you want to learn more you should check it out here.

