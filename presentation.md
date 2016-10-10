# バッチ処理でhakoを使う話

---

# 私

* :github: hoshinotsuyoshi
* :facebook: tsuyoshi.hoshino
* :twitter: hoppiestar

* Interested in docker, ruby
* feedforce.Inc.

---

# バッチ処理

* cron
   * 高可用性を得るのが難しい
   * リソースに応じたスケジューリングが難しい

---

# ECSや!

---

# ECS is...


❝ You can run anything: applications, batch jobs, or microservices. ❞
- AWS _ Amazon EC2 Container Service _ Product Details

---

# ECS is...


❝ You can run anything: applications, **batch jobs**, or microservices. ❞
- AWS _ Amazon EC2 Container Service _ Product Details

---

# ECSでバッチ処理するには

---

# `$ aws ecs <subcommand>`

* `$ aws ecs register-task-definition`
    * TaskDefinitionの登録
* `$ aws ecs run-task`
    * Taskを実行

---

# 他考えること。。

* オートスケーリングさせなきゃね
* (TaskDefinitionが無限に増えていく。。。)
    * アプリのデプロイのたびにTaskDefinition登録してた

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19253350/84c91b80-8f85-11e6-919f-9d575e7cee7c.png)

---

# ふう :cry:

---

# 「hakoのoneshot良さそう」

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19217044/ba3c473c-8e0f-11e6-998b-438d7d56d212.png)

---

# `$ hako oneshot` :gun: 

❝ `hako oneshot` はYAML の定義に従って ECS の RunTask API を呼び出すコマンド ❞
- ECS を利用したオフラインジョブの実行環境 - クックパッド開発者ブログ
- http://techlife.cookpad.com/entry/2016/09/09/235007

---

# `$ hako oneshot`:gun:

面倒見てくれる + α :tada:

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19253350/84c91b80-8f85-11e6-919f-9d575e7cee7c.png)

---

# TaskDefinitionが増えづらい :tada:

* 余分なTaskDefinitionを登録しない仕組みがある
    * たまに登録されちゃうパターンがある
        * ソース読んで直したり、yamlの書き方で回避する

---

# 自動scale out :tada:

* リソースが足りないときの自動scale out
* `autoscaling_group_for_oneshot` を指定すればおｋ
    * 例) ![inline 150%](https://cloud.githubusercontent.com/assets/1394049/19253505/73245e52-8f86-11e6-956f-489b072acdf8.png)
* リソースが足りない場合はdesiredが **1** 増える

---

# まとめ

* hako oneshot :gun: を使うことにより
    * ECS API叩く自前コードが減った
    * 余分なTaskDefinition登録が減った
    * 自動scale outも面倒見てくれる

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19222122/29630a3e-8e8c-11e6-8cf7-d448c6c726ce.png)

