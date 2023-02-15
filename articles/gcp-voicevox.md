---
title: "GCPでVOICEVOXを動かす"
emoji: "🥕"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gcp", "voicevox", "docker"]
published: false
---

VOICEVOXは、音声合成エンジンで、エディタ+エンジン+コアの3つのコンポーネントからできています。[ref](https://github.com/VOICEVOX/voicevox/blob/main/docs/%E5%85%A8%E4%BD%93%E6%A7%8B%E6%88%90.md)
今回は、GCPのCloud Runを使って、VOICEVOXのエンジン+コアを動かします。

## Cloud Run
[Cloud Run](https://cloud.google.com/run/docs/quickstarts/build-and-deploy?hl=ja)は、コンテナをデプロイするサービスです。以下のようなメリットがあるので、自分の用途ではこのサービスが最適だと思いました。

- ネットワークの設定が不要（難しいことをやらなければ）
- リクエストが来たときに自動でCPUを割り当ててコンテナを起動してくれる
- **無料枠が多い**
  - 180,000 vCPU 秒 (1CPUなら50時間)
  - 360,000 GiB 秒 (2GiBなら50時間)
  - 200万リクエスト

個人で使うなら、無料枠を使い切ることはないと思います。