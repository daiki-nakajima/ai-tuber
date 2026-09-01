# 天気ツール (tools-weather)

`src/tools/weather/` は、Saint Graph（魂）が **MCP (Model Context Protocol)** 経由で自律的に呼び出す天気予報取得サーバーです。
Body の REST API とは異なり、AI (LLM) がツールとして認識し、必要と判断したときに自律的に利用します。

---

## 役割

- **天気情報の取得**: [Open-Meteo API](https://open-meteo.com/)（API キー不要）を使用して、指定された場所と日付の天気を取得します。
- **MCP サーバー**: `FastMCP` を用いた SSE (Server-Sent Events) トランスポートの MCP サーバーとして動作します。
- **日本語応答**: WMO 天気コードを日本語（晴天、くもり、雷雨など）に変換し、AI が読み上げやすい文字列を返します。

---

## 構成

```
src/tools/weather/
├── main.py           # FastMCP サーバー定義・エントリポイント
├── tools.py          # 天気取得ロジック (Open-Meteo 連携)
├── Dockerfile
└── requirements.txt
```

---

## 提供ツール

### `get_weather(location: str, date: str = None) -> str`

| 引数 | 説明 |
|------|------|
| `location` | 都市名や地域名（例: `東京`, `福岡`, `Tokyo`）。ジオコーディング API で緯度経度に解決されます。 |
| `date` | 日付。`YYYY-MM-DD` 形式、または相対指定（`today`, `tomorrow`, `day after tomorrow`）。未指定時は現在の天気＋今日の予報を返します。 |

**処理フロー**:

1. **ジオコーディング**: Open-Meteo Geocoding API で地名から緯度経度を解決
2. **天気取得**: Open-Meteo Forecast API で現在の天気と日次予報（weathercode / 最高・最低気温）を取得
3. **フォーマット**: WMO コードを日本語説明に変換し、読み上げ可能な文字列を構築

**応答例**:

```
福岡 の天気（参照元: Open-Meteo）:
現在: 晴れ, 気温 20.5°C
今日の予報: くもり, 最高 22.1°C, 最低 15.3°C
```

**エラーハンドリング**: 例外をスローせず、`'{location}' という場所は見つかりませんでした。` のようなエラーメッセージ文字列を返します（Result パターン）。AI はこのメッセージをそのまま視聴者への説明に利用できます。

---

## エンドポイント

| パス | 説明 |
|------|------|
| `/sse` | MCP SSE エンドポイント（Saint Graph が接続） |
| `/health` | ヘルスチェック（`{"status": "ok"}` を返却） |

---

## 環境変数

| 変数名 | デフォルト | 説明 |
|--------|-----------|------|
| `PORT` | `8001` | サーバーの待機ポート |

---

## Saint Graph からの利用

Saint Graph は環境変数 `WEATHER_MCP_URL`（デフォルト: `http://tools-weather:8001/sse`）で本サーバーに接続し、`McpToolset` としてエージェントに登録します。

```python
# src/saint_graph/saint_graph.py での登録イメージ
connection_params = SseConnectionParams(url=weather_mcp_url)
toolset = McpToolset(connection_params=connection_params)
```

Cloud Run 環境で `WEATHER_MCP_URL` が未設定の場合、MCP 機能は無効化されます（警告ログのみで起動は継続）。

---

## 実装上の注意

- **DNS Rebinding Protection 無効化**: Docker ネットワーク内でホスト名 (`tools-weather`) による接続を許容するため、`enable_dns_rebinding_protection = False` を設定しています。
- **タイムアウト**: 外部 API 呼び出しは 10 秒でタイムアウトします。

---

## 関連ドキュメント

- [Tools ディレクトリの配置ルール](../../../src/tools/README.md)
- [通信プロトコル](../../architecture/communication.md) - MCP 通信仕様
- [Saint Graph コアロジック](../saint-graph/core-logic.md) - McpToolset の登録
