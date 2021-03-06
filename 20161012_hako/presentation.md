# バッチ処理でhakoを使う話

### ![fit](https://emoji.slack-edge.com/T02DQ8HS2/github/16e2e1324585a8f8.png) @hoshinotsuyoshi / feedforce Inc.

### 2016/10/12

^
「バッチ処理でhakoを使う話」というタイトルで
ほしのつよしが発表いたします。

---

# Me ![fit](https://qiita-image-store.s3.amazonaws.com/0/30715/profile-images/1473685544)

* ![fit](https://emoji.slack-edge.com/T02DQ8HS2/github/16e2e1324585a8f8.png) @hoshinotsuyoshi
* ![fit](https://blog.st-hatena.com/images/common/meta-icon-global.png) http://hoppie.hatenablog.com/
* ![15%](http://pix.iemoji.com/images/emoji/apple/ios-9/256/green-heart.png) ![90%](https://emoji.slack-edge.com/T02DQ8HS2/docker2/2afb50947218a8ac.jpg)
* ![15%](http://pix.iemoji.com/images/emoji/apple/ios-9/256/green-heart.png) ![40%](https://emoji.slack-edge.com/T02DQ8HS2/ruby/0fb603f6e739e0fe.png)
* 2014〜 feedforce Inc.

^
ほしのつよしと申します。
フィードフォースという会社でエンジニアをやっています。
dockerとかrubyとかが好きです。
ブログも書いたりしています。

---

# バッチ処理 :clock1:

* cronだと
   * 高い可用性を得るのが難しい
   * リソースに応じたスケジューリングが難しい

^
今回のテーマはバッチ処理なんですが、
皆さんご存知のcronだと、こういう問題があると思います。
可用性とか冗長性とか。
1台のインスタンスにcrontab登録すると、そのインスタンスが死んだときに困ってしまいます。

---

# ECS

![fit](https://img.stackshare.io/service/1908/amazon-ecs.png)

^
そこでECSなんですが

---


# EC2 Container Service Product Details

![fit](https://img.stackshare.io/service/1908/amazon-ecs.png)

❝ You can run anything: applications, **batch jobs**, or microservices. ❞

^
まずECSってバッチ処理できるの？と思われる方もいらっしゃるかもしれないですが、
このとおり、公式にできるよって書いてあります。

---

# ECSでバッチ処理するには

1. TaskDefinitionの登録して
   * `$ aws ecs register-task-definition`
2. Taskを実行する！
   * `$ aws ecs run-task`
3. 以上！(基本的には)

^
で、ECSでバッチ処理するには。
まずTaskDefinitionというタスク定義を定義して、
Taskを実行する`run-task`というAPIを叩くことになります。
で、これで以上、なんですが、

---

# その他の問題

* オートスケーリングさせなきゃ… :cry:
* TaskDefinitionが際限なく増加してく… :cry:
    * デプロイのたびにTaskDefinition登録してた
    * 登録済みかどうかのチェックがだるい
* …

^
インスタンスが忙しくなってきたら、さすがにオートスケーリングさせなきゃ、とか、
TaskDefinitionが無限に増える問題とかがあります。
私のチームではTaskDefinitionをymlで表現して
デプロイのときにそれを登録する、ということをしていました。
みなさんTaskDefinitionってどうされているんでしょうか。
増えまくってもぶっちゃけ気にしないという感じでしょうか。
あとで聞きたいです

---

## 2016年9月

![fit](http://1.bp.blogspot.com/-RJRt_Hv37Kk/VMIu-CCBpII/AAAAAAAAq2E/JsIJ8pPwmuY/s800/calender_takujou.png)

^
で、時は流れて。
最近良さそうなツールが出ました。

---

## ?「hakoのoneshot :gun: 良さそう」

^
チームのメンバーにhakoのoneshot良さそうですよ、って教えてもらいました。

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19217044/ba3c473c-8e0f-11e6-998b-438d7d56d212.png)

^
hako っていうのはDocker Containerのdeployツールで、
クックパッドの中で使われているツールとのことです。
rubygemsで公開されています。

---

# `$ hako oneshot` :gun:

❝ `hako oneshot` はYAML の定義に従って ECS の RunTask API を呼び出すコマンド ❞
####- ECS を利用したオフラインジョブの実行環境 - クックパッド開発者ブログ

^
で、サブコマンドの hako oneshotっていうのは、
デーモンとかではなく1回きりのジョブの実行です。
先程出てきたRunTask APIってやつを叩きます。

---

# こんなかんじになった

^
で、早速ですがこれを採用したところこんな感じになりました

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19277616/44cb1700-9015-11e6-920f-e9a893b490e8.png)

^
CloudWatchEventで定時にイベント発火し、
lambdaでhako oneshotを叩きます。
hako oneshotはlambdaで実行するには時間が長すぎるのでこれ用の
ワーカーを用意します。
hako oneshotがきっかけとなり、ジョブ自体はECSの別クラスタで動きます。

---

# :gun: の中身

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19277839/20f2cbc4-9016-11e6-8a32-955d1aa245a8.png)

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19270692/3c60dafa-8ffc-11e6-9522-07ce9949c2d0.png)

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19270697/402f275e-8ffc-11e6-93b0-289079066c49.png)

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19270701/43fdb9b8-8ffc-11e6-9e1b-baa574e8c499.png)

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19270704/4733cb18-8ffc-11e6-9add-3f3f4b1cfd5e.png)

---

# :gun: TaskDefinitionが増えづらい :tada:

* TaskDefinitionを重複登録しない仕組みがある
    * (たまに登録されちゃうパターンがあるｹﾄﾞ)
        * (ソース読んで直したり、yamlの書き方で回避する)

---

# :gun: 自動scale out :tada:

* リソースが足りないときの自動scale out
* `autoscaling_group_for_oneshot` を指定すればおｋ
    * 設定例) ![inline 150%](https://cloud.githubusercontent.com/assets/1394049/19270958/93e1f330-8ffd-11e6-8165-4cbb2191e057.png)
* リソースが足りない場合はdesiredが **1** 増える
    * この **1** はハードコード

---

# まとめ

* hako oneshot :gun: を使うことにより
    * 余分なTaskDefinition登録が減った
    * 自動scale outも面倒見てくれる
    * ECS API叩く**自前コードが減った**

---

### 参考文献

- AWS _ Amazon EC2 Container Service _ Product Details
  - https://aws.amazon.com/ecs/details/?nc1=h_ls
- eagletmt/hako
  - https://github.com/eagletmt/hako

---

### 参考文献

- ECS を利用したオフラインジョブの実行環境 - クックパッド開発者ブログ
  - http://techlife.cookpad.com/entry/2016/09/09/235007

