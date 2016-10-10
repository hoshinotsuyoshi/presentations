# バッチ処理でhakoを使おうとしている話

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


> You can run anything: applications, batch jobs, or microservices.
- AWS _ Amazon EC2 Container Service _ Product Details

---

# ECS is...


> You can run anything: applications, **batch jobs**, or microservices.
- AWS _ Amazon EC2 Container Service _ Product Details

---

# ECSでバッチ処理をやるには

---

# `$ aws ecs <subcommand>`

* register-task-definition
    * TaskDefinitionの登録
* run-task
    * Taskを実行
* describe-tasks
    * Taskのステータスを得る

---

# 3つを反映した図

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19222122/29630a3e-8e8c-11e6-8cf7-d448c6c726ce.png)

---

# 他。。

* オートスケーリングさせなきゃね
* (タスク定義が無限に増えていく。。。)

---

# タスク定義が無限に増えていく。。。

---

![fit](https://cloud.githubusercontent.com/assets/1394049/19222122/29630a3e-8e8c-11e6-8cf7-d448c6c726ce.png)

---

# ふう :cry:

---

# hakoを使う

---

# hako

![fit](https://cloud.githubusercontent.com/assets/1394049/19217044/ba3c473c-8e0f-11e6-998b-438d7d56d212.png)

---

# `$ hako oneshot` :gun:

> `hako oneshot` はYAML の定義に従って ECS の RunTask API を呼び出すコマンド
- ECS を利用したオフラインジョブの実行環境 - クックパッド開発者ブログ
- http://techlife.cookpad.com/entry/2016/09/09/235007

---

# `$ hako oneshot`

wraps :tada:

---

# AutoScaling

increments by 1

`autoscaling_group_for_oneshot`

---

# まとめ

* hako oneshotを使うことにより
    * コードを書く必要が減った
    * 余分なTaskDefinition登録が減った
    * AutoScalingも面倒見てくれる
* ドキュメントはまだ無いっぽいけどECSのAPI使った事ある人ならわかりそう



