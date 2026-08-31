# Body（肉体）

Body は AI Tuber システムの「肉体」であり、物理的な入出力と外部サービス連携を担当します。

---

## 役割と責任

**Body の役割**:
- **音声合成**: VoiceVox による音声生成
- **映像制御**: OBS Studio による表情切り替え・録画・配信
- **YouTube 連携**: Live 配信作成・コメント取得
- **REST API 提供**: Saint Graph への操作インターフェース

---

##モード

### Streamer モード（本番配信用）

**場所**: `src/body/streamer/`  
**ポート**: `8002`

**機能**:
- VoiceVox 音声合成
- OBS Studio 制御
- YouTube Live 配信・コメント取得
- センテンス順次再生

### CLI モード（開発・デバッグ用）

**場所**: `src/body/cli/`  
**ポート**: `8000`

**機能**:
- 標準入出力
- シンプルなコメント管理
- 開発用の軽量実装

---

## ディレクトリ構成

```
src/body/
├── rest.py                        # REST API サーバー共通実装 (BodyApp)
├── service.py                     # 共通インターフェース (BodyServiceBase)
├── streamer/
│   ├── main.py                    # エントリポイント
│   ├── service.py                 # ビジネスロジック (StreamerBodyService)
│   ├── voice_adapter.py           # VoiceVox アダプター
│   ├── obs_adapter.py             # OBS WebSocket アダプター
│   ├── youtube_live_adapter.py    # YouTube Live API
│   ├── youtube_comment_adapter.py # コメント取得サブプロセス管理
│   ├── youtube_comment_fetcher.py # コメント取得スクリプト
│   ├── youtube_auth.py            # OAuth 認証
│   ├── utils.py                   # ユーティリティ
│   ├── scripts/                   # 認証ヘルパー等
│   └── obs/                       # OBS コンテナ設定・アセット取得 (download_assets.py)
├── cli/
│   ├── main.py                    # エントリポイント
│   ├── service.py                 # ビジネスロジック (CLIBodyService)
│   └── io_adapter.py              # 標準入出力アダプター
└── __init__.py
```

---

## 責任範囲

### Streamer モード

| 責任 | 実装 |
|------|------|
| 音声合成 | VoiceVox API 呼び出し、WAV 生成 |
| 映像制御 | OBS WebSocket で表情切り替え |
| 音声再生 | OBS メディアソース制御、再生時間計算 |
| 録画制御 | OBS 録画開始・停止 |
| 配信制御 | YouTube Live API、OBS ストリーミング |
| 資産管理 | `StorageClient` によるアセット起動時取得 |

### CLI モード

| 責任 | 実装 |
|------|------|
| テキスト出力 | 標準出力に発話内容を表示 |
| コメント入力 | 標準入力からコメントを取得 |
| デバッグ | 開発時の動作確認 |

---

## REST API エンドポイント

### 共通エンドポイント

- `POST /api/speak` - 発話
- `POST /api/change_emotion` - 表情変更
- `GET /api/comments` - コメント取得

### Streamer 固有エンドポイント

- `POST /api/broadcast/start` - 配信/録画開始（統合）
- `POST /api/broadcast/stop` - 配信/録画停止（統合）

詳細は [通信プロトコル](../../architecture/communication.md) を参照してください。

---

## 関連ドキュメント

- [GCE プロビジョニング](./provisioning.md) - startup.sh の振る舞い
- [Streamer 概要](./streamer/README.md) - Streamerモード
- [CLI 概要](./cli/README.md) - CLI モード
- [システム概要](../../architecture/overview.md) - 全体アーキテクチャ
