# Ren (紅月れん) - Character Package

このディレクトリには、AITuber「紅月れん」の完全なキャラクターパッケージが含まれています。
プラグインとして追加・削除可能な構成になっています。

## ディレクトリ構成

```
data/mind/ren/
├── README.md           # このファイル
├── persona.md          # キャラクター設定（性格、口調、背景など）
├── mind.json           # 技術メタデータ（VOICEVOX の speaker_id など）
├── user_dict.json      # VOICEVOX ユーザー辞書（固有名詞の読み方）
├── presets.yaml        # VOICEVOX プリセット
└── assets/             # OBS で使用するアセット
    ├── normal01.mp4    # 通常モーション（感情ごとに複数バリエーション可）
    ├── fun01.mp4       # 楽しいモーション
    ├── joyful〜.mp4 / sad〜.mp4 / angry〜.mp4 / silent〜.mp4
    ├── ai_normal.png   # 静止画立ち絵（旧形式・フォールバック用）
    └── bgm.mp3         # BGM
```

**注**: 配信フェーズごとのシステムプロンプト（intro / news_reading / closing など）はキャラクター固有ではなく、`src/saint_graph/system_prompts/` に共通プロンプトとして配置されています。キャラクターの個性は `persona.md` で定義します。

## 各ファイルの読み込まれ方

- **persona.md / mind.json**: Saint Graph 起動時に `PromptLoader` が読み込みます（ローカル or GCS）。
- **user_dict.json / presets.yaml**: `docker-compose.yml` により、このディレクトリ全体が VOICEVOX Engine のデータディレクトリ (`/home/user/.local/share/voicevox-engine-dev`) としてマウントされ、エンジンが直接参照します。
- **assets/**: OBS コンテナ起動時に `download_assets.py` が取得し、OBS のシーン定義から参照されます。

## OBS シーン設定

シーン定義 (`src/body/streamer/obs/config/basic/scenes/Untitled.json`) には以下のソースが登録されています。

### メディアソース（表情・モーション）

感情ごとにループ再生されるモーション動画です。`obs_adapter.py` の `EMOTION_MAP` により感情タグからソース名に変換されます。

- `normal` → 通常（`neutral` タグに対応）
- `joyful` → 喜び（`happy` / `joyful` タグに対応）
- `fun` → 楽しい
- `sad` → 悲しい（`sorrow` タグにも対応）
- `angry` → 怒り
- `silent` → 発話していない待機状態

### メディアソース（音声）

- `voice` → `/app/shared/voice/speech_0000.wav`（Body が生成した音声）
- `BGM` → `/app/assets/bgm.mp3`

## 使用方法

1. コンテナ起動: `docker compose up`
2. VNC アクセス: `http://localhost:8080/vnc.html`
3. `body-streamer` サービスが自動的に表情を切り替え、音声を再生します

## カスタマイズ

- **persona.md**: キャラクターの性格や口調を変更
- **mind.json**: `speaker_id`（VOICEVOX 話者）などの技術設定を変更
- **user_dict.json**: 固有名詞の読み方を登録（詳細: [VOICEVOX 辞書管理](../../../docs/components/mind/voicevox-dictionary.md)）
- **assets/**: 独自のアバターモーション・画像に差し替え

## 他のキャラクターの追加

新しいキャラクターを追加する場合は、`data/mind/` 配下に同じ構造のディレクトリを作成し、環境変数 `CHARACTER_NAME` で切り替えてください：

```
data/mind/
├── ren/      # 紅月れん（既存）
└── aoi/      # 新キャラクター（例）
    ├── persona.md
    ├── mind.json
    └── assets/
```

詳細は [Mind コンポーネント概要](../../../docs/components/mind/README.md) を参照してください。
