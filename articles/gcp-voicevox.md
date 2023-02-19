---
title: "GCPでVOICEVOXを動かす"
emoji: "🥕"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gcp", "voicevox", "docker"]
published: true
---

VOICEVOXは、音声合成エンジンで、エディタ+エンジン+コアの3つのコンポーネントからできています。[ref](https://github.com/VOICEVOX/voicevox/blob/main/docs/%E5%85%A8%E4%BD%93%E6%A7%8B%E6%88%90.md)
今回は、GCPのCloud Runを使って、VOICEVOXのエンジン+コアを動かします。

# Cloud Run
[Cloud Run](https://cloud.google.com/run/docs/quickstarts/build-and-deploy?hl=ja)は、コンテナをデプロイするサービスです。以下のようなメリットがあるので、自分の用途ではこのサービスが最適だと思いました。

- ネットワークの設定が不要（難しいことをやらなければ）
- リクエストが来たときに自動でCPUを割り当ててコンテナを起動してくれる
- **無料枠が多い**
  - 180,000 vCPU 秒 (1CPUなら50時間)
  - 360,000 GiB 秒 (2GiBなら50時間)
  - 200万リクエスト

個人で使うなら、無料枠を使い切ることはないと思います。

# 走らせよう

Dockerイメージには、VOICEVOX公式のCPU版を使います。
[voicevox/voicevox_engine:cpu-ubuntu20.04-latest](https://hub.docker.com/r/voicevox/voicevox_engine)

GCPの[Cloud Run](https://console.cloud.google.com/run?hl=ja)にアクセスし、有効になっていなければ、有効にします。

サービスの作成をクリックし、以下のように設定します。その他はデフォルトのままでOKです。

- コンテナイメージのURL: `voicevox/voicevox_engine:cpu-ubuntu20.04-latest`
- サービス名: 任意
- 認証：未認証の呼び出しを許可
- コンテナ、ネットワーキング、セキュリティ>コンテナポート: `50021`
- メモリ: `2GiB`

![settings1](/images/gcp-voicevox/settings1.png) 

![settings2](/images/gcp-voicevox/settings2.png)  

![settings3](/images/gcp-voicevox/settings3.png)  

これらの設定（特に認証系やメモリ）は、必要に応じて変更してください。
メモリに関しては、長文を合成したい場合は、`2GiB`より大きい値を設定する必要があります。

作成をクリックすると、サービスが作成されます。しばらくすると、サービスが走り始めます。


# 使ってみる

サービスが開始すると、自動で固有のURLが割り当てられます。このURLにアクセスして、ドキュメントを見てみましょう。
※割り当てられたURLは、サービスの詳細画面から確認できます。

今回のURLは、`https://test-voicevox-wov65wrvrq-uc.a.run.app`でした。
VOICEVOXはFastAPIで作られているため、`/docs`にアクセスすると、ドキュメントが見れました。

![document](/images/gcp-voicevox/docs.png)  
