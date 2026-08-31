# AI Tuber ドキュメント

AI Tuber は、**魂（Saint Graph）**、**肉体（Body）**、**精神（Mind）** の三位一体で構成される AITuber システムです。

---

## クイックスタート

初めての方は [セットアップガイド](./knowledge/setup.md) から始めてください。

---

## アーキテクチャ

システム全体の設計:

- [システム概要](./architecture/overview.md) - 三位一体構造の説明
- [データフロー](./architecture/data-flow.md) - 処理シーケンス
- [通信プロトコル](./architecture/communication.md) - REST/MCP 仕様
- [CI/CD 自動化](./architecture/cicd.md) - Cloud Build トリガー構成

---

## Components

### Saint Graph（魂）

意思決定エンジン:

- [概要](./components/saint-graph/README.md)
- [コアロジック](./components/saint-graph/core-logic.md) - Agent とターン処理
- [ニュース配信](./components/saint-graph/news-delivery.md) - ニュース管理
- [Body クライアント](./components/saint-graph/body-client.md) - REST クライアント
- [プロンプト設計](./components/saint-graph/prompts.md) - プロンプトシステム

### Body（肉体）

ストリーミング制御:

- [概要](./components/body/README.md)
- [GCE プロビジョニング](./components/body/provisioning.md) - startup.sh の振る舞い
- **Streamer モード**:
  - [概要](./components/body/streamer/README.md) - アーキテクチャと API リファレンス
  - [OBS 制御](./components/body/streamer/obs.md) - 表情切り替え・音声再生・配信/録画
  - [音声合成](./components/body/streamer/voice.md) - VOICEVOX 連携
  - [YouTube 配信管理](./components/body/streamer/youtube_live.md) - 配信枠作成・OAuth 認証
  - [YouTube コメント取得](./components/body/streamer/youtube_comments.md) - サブプロセスによるチャット取得
- **CLI モード**:
  - [概要](./components/body/cli/README.md)

### Mind（精神）

キャラクター定義:

- [概要](./components/mind/README.md) - キャラクター構成要素とアセット作成ガイド
- [VOICEVOX 辞書管理](./components/mind/voicevox-dictionary.md)

### Tools（MCP 拡張）

AI が自律的に呼び出す外部ツール:

- [天気ツール](./components/tools/weather.md) - Open-Meteo による天気予報取得

### Scripts（運用スクリプト）

配信パイプラインを支えるジョブ:

- [ニュース収集](./components/scripts/news-collector.md) - ニュースエージェント

---

### Infra（インフラ抽象化）

ローカル / GCP の差異を吸収する抽象化レイヤー:

- **[StorageClient](../src/infra/storage_client.py)** - ファイル取得先の抽象化 (`FileSystem` / `GCS`)
- **[SecretProvider](../src/infra/secret_provider.py)** - 機密情報取得先の抽象化 (`Env` / `GCP Secret Manager`)

---

## ガイド

利用方法と参考資料:

- [セットアップ](./knowledge/setup.md) - 環境構築と起動方法
- [YouTube 配信セットアップ](./knowledge/youtube-setup.md) - OAuth 認証と配信設定
- [開発者ガイド](./knowledge/development.md) - 開発環境とテスト実行
- [トラブルシューティング](./knowledge/troubleshooting.md) - よくある問題と解決方法

---

## ドキュメント構成

### `/architecture/` - アーキテクチャ設計
システム全体の設計と通信仕様

### `/components/` - コンポーネント仕様
各サービスの技術仕様を三位一体構造で整理:
- `saint-graph/` - 魂（意思決定）
- `body/` - 肉体（入出力制御）
- `mind/` - 精神（キャラクター定義）
- `tools/` - MCP 拡張ツール（天気など）
- `scripts/` - 運用スクリプト（ニュース収集など）

### `/knowledge/` - ナレッジベース
セットアップ、開発、トラブルシューティング、過去の知見

---

## 貢献

新しいドキュメントを追加する場合は、三位一体構造に従って適切なディレクトリに配置してください。

---

**最終更新**: 2026-08-31
