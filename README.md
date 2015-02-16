couchbase-scala
===============
This is a library for accessing Couchbase in Scala.

couchbase-scala is [published to maven center](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.sandinh%22%20couchbase-scala)

## Changelogs
we use [Semantic Versioning](http://semver.org/)

##### v7.0.0
+ update play-json 2.3.8, rxjava 1.0.6 (transitive dep at v1.0.4 from couchbase java-client 2.1.0)
+ update couchbase java-client 2.1.0 with changes:
    + default disconnect timeout is increased from 5s to 25s
    + ScalaBucket.query(String) is replaced by query(Statement)
+ RichAsyncViewResult.flatFoldRows now use scConcatMap instead of scFlatMap to preserve order of underlying observable items.
This fixes the bug in sandinh.com's bank-play project: log rows in bank is out-of-created-order
+ RichAsyncViewResult is moved from com.sandinh.rx.Implicits to com.sandinh.couchbase.Implicits

##### v6.1.0
+ fixes SI-9121 by removing com.sandinh.rx.Implicits.{RichFunction1, RichFunction2}
+ remove some `@inline` annotations
+ add scalacOptions: -optimise -Ybackend:GenBCode -Ydelambdafy:method ..

##### v6.0.0
1. add [CompatStringTranscoderLegacy](src/main/scala/com/sandinh/couchbase/transcoder/CompatStringTranscoder.scala#L51) which:
 + decoding a stored document in format of StringDocument OR JsonStringDocument.
 + encoding a String as StringDocument.

  (same as in previous version, [CompatStringTranscoder](src/main/scala/com/sandinh/couchbase/transcoder/CompatStringTranscoder.scala#L39):
 + decoding a stored document in format of StringDocument OR JsonStringDocument.
 + encoding a String as StringDocument.)

  see [CompatStringSpec](src/test/scala/com/sandinh/couchbase/CompatStringSpec.scala)

2. [CBCluster.openBucket](src/main/scala/com/sandinh/couchbase/CBCluster.scala#L24) now has `legacyEncodeString: Boolean` param, default = true. In previous version, CBCluster.openBucket("some_bucket") return a bucket which encode String as JsonString (using CompatStringTranscoder). For better compatibility, from v6.0.0 the return bucket will encode String using CompatStringTranscoderLegacy. (This is in-compatible with v5.x, so we bump to v6.0.0).

##### v5.1.1
only update scala 2.11.5, couchbase java-client 2.0.3

##### v5.1.0
only use CompatStringDocument instead of StringDocument for StrCao, StrCao1, StrCao2

##### v5.0.0
+ move `def bucket: ScalaBucket` to constructor's param in CaoBase, JsCao, JsCao1, JsCao2, StrCao, StrCao1, StrCao2
+ use `com.couchbase.timeout._` keys to config timeout in [duration format](https://github.com/typesafehub/config/blob/master/HOCON.md#duration-format).
see [reference.conf](src/main/resources/reference.conf) for legend
+ note: from this version, config values `com.couchbase._` will not be set to java system properties
(see class `DefaultCouchbaseEnvironment`)
+ add some convenient methods to ScalaBucket: getT, getOrElseT, getJsT
+ CBCluster now auto add `com.sandinh.couchbase.transcoder._` transcoders when openBucket
+ add CompatStringDocument which works exactly like JsonStringDocument permit decoding a stored StringDocument

##### v4.2.0
reverse `getOrElse` changes in v4.1.0:
`getOrElse(x)(null): type mismatch found Null(null) required XX`
is because x is an instance of class X(..) extend AnyVal

##### v4.1.0
note: Do not use this version. see v4.2.0
This version contain some incompatible change:
+ getOrElse method in CaoBase, WithCaoKey1, WithCaoKey2: do not use a separate param list for `default` param
(fix usage problem when getOrElse(x)(null): type mismatch found Null(null) required XX)
+ rename RichAsyncViewRow.{document => doc}.
 We can't name `document` because scala compiler will not implicitly pick that method.

##### v4.0.1
+ some minor change (no bug fix, no new feature)
+ remove crossScalaVersions 2.10

##### v4.0.0
WARNING: when implement a real project at http://sandinh.com, we found that RxScala (& RxJava) is VERY VERY complex
(compare to scala Future).
At first, we have created https://github.com/giabao/scala-future-vs-rxscala to share knowledge to our team.
But after several weeks, we have decided to use Future only! (many dangerousness of Rx have not been mentioned in scala-future-vs-rxscala).
So, we change couchbase-scala to just expose Future as the API.

##### v3.0.2
+ update rxjava 1.0.3
+ add com.sandinh.rx.Implicits.RichObs.subscribeError

##### v3.0.1
only update libs:
```
  "com.couchbase.client"  %  "java-client" % "2.0.2",
  "io.reactivex"          %% "rxscala"     % "0.23.0",
  "io.reactivex"          % "rxjava"       % "1.0.2",
  "com.typesafe.play"     %% "play-json"   % "2.3.7"
```

##### v3.0.0
+ update rxjava 1.0.1
+ typeof CBCluster.openBucket & CaoBase.bucket is changed from ScalaBucket to Observable[ScalaBucket]
+ typeof CBCluster.disconnect() is changed from Boolean to Observable[Boolean]
+ remove CBCluster.disconnect(FiniteDuration)
+ setBulk in WithCaoKey1 & WithCaoKey2 now use concatMap instead of flatMap to preserve ordering of result with the params
+ fixes http://www.couchbase.com/issues/browse/JCBC-642

##### v2.0.1
narrow dependencies from guice to javax.inject

##### v2.0.0
first public version

## Licence
This software is licensed under the Apache 2 license:
http://www.apache.org/licenses/LICENSE-2.0

Copyright 2014 Sân Đình (http://sandinh.com)
