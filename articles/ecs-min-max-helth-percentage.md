---
title: "ECS 最小ヘルス率、最大ヘルス率について"
emoji: "😈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["aws", "ecs"]
published: true
---

# はじめに

ECSサービススケジューラはデプロイ戦略を決める際、`最小ヘルス率`、`最大ヘルス率`を使用します。
この`最小ヘルス率`、`最大ヘルス率`はデプロイタイプ（`ローリングデプロイ`、`ブルーグリーンデプロイ`）、起動タイプ（`EC2`、`Fargate`）によって意味合いが異なります。

# 最小ヘルス率

__デプロイタイプが`ローリングデプロイ`、起動タイプが`EC2`である場合__
- デプロイ時にステータスが`RUNNING`であるタスクのMin Size(最小数)を、Desired Capacityのパーセント値で表したものです（起動タイプが`EC2`の場合、`DRAINING`ステータスのタスクも`最小ヘルス率`に考慮されます）。
  - 例えば、Desired Capacityが4で、デプロイ時に2個のタスクを`RUNNING`ステータスに保ちたい場合、最小ヘルス率を50%(4 * 0.5 = 2)に設定します。
  この場合、スケジューラは2個の新規タスクを開始する前に2個の既存タスクを停止することができます。
- デフォルト値は100%です。

__デプロイタイプが`Blue/Greenデプロイ` 、起動タイプが`EC2`である場合__
- デフォルト値（100%）が使用されます。

__起動タイプが`Fargate`の場合__
- デプロイ戦略を決める際、`最小ヘルス率`は使用されません。

# 最大ヘルス率

__デプロイタイプが`ローリングデプロイ`、起動タイプが`EC2`である場合__
- デプロイ時にステータスが`RUNNING`または`PENDING`であるタスクのMax Size(最大数)を、Desired Capacityのパーセント値で表したものです（起動タイプが`EC2`の場合、`DRAINING`ステータスのタスクも`最大ヘルス率`に考慮されます）。
- この設定によりデプロイのバッチサイズを定義することができます。
  - 例えば、Desired Capacityが4で、デプロイを1タスクづつ行いたい場合、最大ヘルス率を125%(4 * 1.25 - 4 = 1)に設定します。
  この場合、スケジューラは1個の既存タスクを停止する前に1個の新規タスクを開始できます。
- デフォルト値は200%です。 

__デプロイタイプが`Blue/Greenデプロイ` 、起動タイプが`EC2`である場合__
- デフォルト値（200%）が使用されます。

__起動タイプが`Fargate`の場合__
- デプロイ戦略を決める際、`最大ヘルス率`は使用されません。


# 参考

https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/update-service.html
https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/task-lifecycle.html