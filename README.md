# 旅索（タビサク）

国内外の主要都市・空港・旅行系クレジットカードを検索できる旅行情報データベース。

## 技術構成

レキサク（歴索）と同じ構成。ビルドなしの静的HTML＋Vanilla JS＋Supabase（CDNクライアント）。Vercelに直接デプロイする。

- フロント: 静的HTML + Vanilla JS（フレームワーク・ビルドツールなし）
- DB: Supabase（共有プロジェクト、レキサクと同じ project ref: `sunxpjbdvidftukiutxg`）
- デプロイ: Vercel（`vercel.json` はキャッシュ無効化ヘッダーのみ）

## ページ構成

| ファイル | 内容 |
|---|---|
| `index.html` | ランディングページ |
| `cities.html` | 都市 検索・一覧（国内外） |
| `airports.html` | 空港 検索・一覧 |
| `cards.html` | 旅行系クレジットカード 比較・一覧 |
| `admin.html` | 管理画面（都市/空港/カード/タグのCRUD、Supabase Auth認証必須） |

## Supabaseテーブル

すべて `ts_` プレフィックス。レキサク等の既存テーブルとは完全に分離（データ共有なし）。

- `ts_prefectures` — 都道府県マスタ（旅索専用、レキサクの`prefectures`とは別実体）
- `ts_tags` — タグマスタ（`content_type`: `city` / `airport` / `credit_card`）
- `ts_cities` / `ts_city_tags`
- `ts_airports` / `ts_airport_tags`
- `ts_credit_cards` / `ts_credit_card_tags`

全テーブルにRLSを適用：`anon`ロールは読み取り専用、`authenticated`ロールのみ書き込み可。

## 管理画面ログイン

`admin.html` はレキサクと同じSupabase Authの管理者アカウント（`admin@rekisaku.com`）でログインする。

## ローカル動作確認

ビルド不要。`file://` だとCORSの制約があるため簡易サーバー経由で確認する。

```bash
npx serve .
```

## デプロイ

Vercelに接続し、ビルドコマンドなしの静的サイトとしてデプロイする。
