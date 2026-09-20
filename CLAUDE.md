# 旅索（タビサク）プロジェクトメモ

国内外の主要都市・空港・旅行系クレジットカードを検索できる旅行情報データベース。レキサク（歴索）と同じ構成（静的HTML＋Vanilla JS＋Supabase CDNクライアント、ビルドなし、Vercel直接デプロイ）で構築。

## 技術構成

- フロント: 静的HTML + Vanilla JS（フレームワーク・ビルドツールなし）
- DB: Supabase（レキサク等と共有のプロジェクト、project ref: `sunxpjbdvidftukiutxg`）
- デプロイ: Vercel（`vercel.json`はキャッシュ無効化ヘッダーのみ、ビルド設定なし）
- 本番URL: https://tabisaku.vercel.app
- GitHub: https://github.com/UKTOSHI/tabisaku

## ページ構成

| ファイル | 内容 |
|---|---|
| `index.html` | ランディングページ |
| `cities.html` | 都市 検索・一覧（国内外、都道府県・タグフィルタ） |
| `airports.html` | 空港 検索・一覧（国・タグフィルタ） |
| `cards.html` | 旅行系クレジットカード 比較・一覧（発行会社・年会費ソート） |
| `admin.html` | 管理画面（都市/空港/カード/タグのCRUD、要ログイン） |

## Supabaseテーブル

すべて`ts_`プレフィックス。レキサク等の既存テーブルとは完全に分離（データ共有なし・都道府県マスタも独自に`ts_prefectures`を新設）。

- `ts_prefectures` — 都道府県マスタ（旅索専用）
- `ts_tags` — タグマスタ（`content_type`: `city` / `airport` / `credit_card`）
- `ts_cities` / `ts_city_tags`
- `ts_airports` / `ts_airport_tags`
- `ts_credit_cards` / `ts_credit_card_tags`

全テーブルにRLSを適用：`anon`ロールは読み取り専用、`authenticated`ロールのみ書き込み可（レキサクと同じGRANT/RLSチェックリストを踏襲）。

## 管理画面ログイン

`admin.html`はレキサクと同じSupabase Authの管理者アカウント（`admin@rekisaku.com`）を再利用してログインする（同一開発者のプロジェクトのため共用）。

---

## 🚧 現在の作業

**初期構築完了・Vercel本番反映済み（2026-09-20）。CLAUDE.md追加はローカルコミットのみ・GitHub未push（コミット`9ef4b42`）。コード変更あり（新規プロジェクト一式）・DB変更あり（`ts_`系テーブル新規作成）。当面の大きな宿題なし。次に着手するとしたら実データ投入（都市・空港・カードの本追加）とOGP画像・favicon整備、およびCLAUDE.md更新分のpush。**

- ✅ **旅索（タビサク）初期構築（完了・main反映済み 2026-09-20）**: ユーザー依頼「レキサクと同じシステム構成で、日本国内・海外の旅行情報（都市・空港・クレジットカード）を検索できるDBサイトを作って」に対応
  - Supabase共有プロジェクト（`sunxpjbdvidftukiutxg`）に`ts_`プレフィックスの新規テーブル群を作成。レキサクの`prefectures`は再利用せず、ユーザー指示「データは分ける」に従い`ts_prefectures`を独自に新設してデータ完全分離
  - 47都道府県データ、動作確認用サンプルデータ（都市3件：東京・上海・ニューヨーク、空港2件：成田・羽田、カード2件：ANA/JALゴールドカード）を投入
  - `index.html`/`cities.html`/`airports.html`/`cards.html`/`admin.html`を新規作成。レキサクのspots.html（全件フェッチ＋クライアント側フィルタ、タグチップ）・admin.html（Supabase Auth・delete→insert方式のタグ同期）のパターンを踏襲
  - ローカルで静的サーバー（`npx serve`）を立てて全ページの動作確認（Supabaseからのデータ取得・検索フィルタ・モーダル詳細表示・admin.htmlのログインゲート）を実施、コンソールエラーなしを確認
  - GitHubリポジトリ`UKTOSHI/tabisaku`を新規作成しpush→Vercelにインポートしてデプロイ、本番URL`https://tabisaku.vercel.app`で動作確認済み
  - 管理者アカウントはレキサクと同じ`admin@rekisaku.com`を再利用する方針（実際のパスワードでのCRUD往復確認はユーザー自身が今後実施）

---

## 📝 セッションログ

### 2026-09-20（旅索（タビサク）初期構築・Vercel本番デプロイまで完了）✅main反映済み・コード変更あり（新規プロジェクト一式）・DB変更あり（`ts_`系テーブル新規作成）
- ユーザー依頼「レキサクと同じシステム構成（Vercel+Supabase）で、日本国内・海外の旅行情報を検索できるものを作って。都市の情報・クレカの情報・飛行機の情報など、旅行に特化したデータベース。まずアスクアンドクエスチョンで作って。タイトルは旅索（タビサク）」に対応
- **要件確認（AskUserQuestion）**: 技術構成はレキサクと完全に同じ（静的HTML＋Vanilla JS＋Supabase CDN、ビルドなし）に決定。Supabaseは既存の共有プロジェクトを使うが、テーブル・データはレキサクと完全に分離する方針に決定（ユーザー「レキサクとテーブル（データは分ける）」の指示を受け、都道府県マスタも`prefectures`を再利用せず`ts_prefectures`を独自新設に変更）。クレカ情報＝旅行系クレカの比較コンテンツDB（個人のカード管理ではない）、飛行機情報＝成田・羽田など主要空港の情報、都市情報＝国内外の主要都市（上海・NY・台北等）と確定
- **設計**: Plan agentで詳細設計（スキーマ・ページ構成・実装順序・検証方法）を作成。レキサクの実ファイル（`admin.html`のSupabaseクライアント埋め込み・認証ゲート・delete→insert方式のタグ同期パターン、`spots.html`の全件フェッチ＋クライアント側フィルタパターン、`vercel.json`）を直接読んで踏襲パターンを確認
- **Supabase実装**: `sunxpjbdvidftukiutxg`プロジェクトに`ts_prefectures`/`ts_tags`/`ts_cities`/`ts_city_tags`/`ts_airports`/`ts_airport_tags`/`ts_credit_cards`/`ts_credit_card_tags`を作成。全テーブルにanon=SELECT/authenticated=ALLのRLSポリシーを適用（`get_advisors`でレキサク側の既存warning以外に新規の問題がないことを確認）。47都道府県＋動作確認用サンプルデータ（都市3・空港2・カード2）を投入
- **フロント実装**: `index.html`（ランディング＋注目都市の動的表示）、`cities.html`/`airports.html`/`cards.html`（検索・フィルタ・タグチップ・モーダル詳細）、`admin.html`（タブ切替式CRUD、都市/空港/カード共通のconfig駆動フォーム＋タグ管理タブ）を新規作成。`vercel.json`/`robots.txt`/`sitemap.xml`/`manifest.json`/`README.md`も作成
- **動作確認**: `.claude/launch.json`で`npx serve .`のローカルサーバーを起動し、Browserツールで全ページを検証。index/cities/airports/cardsでSupabaseからのデータ取得・フィルタ・モーダル表示が正常動作、admin.htmlのログインゲートが誤パスワードで正しく拒否されることを確認。コンソールエラーなし
- **Git/デプロイ**: ローカルでgit init→コミット。GitHubへの未ログインを検知したためユーザーにログインを依頼→ログイン後、Claude in Chromeで`github.com/new`から`UKTOSHI/tabisaku`（Public、空リポジトリ）を作成。ユーザーに都度確認（AskUserQuestion）を取ってから`git push -u origin main`（auto modeのBash分類器が最初のpush試行をブロック→ユーザーに再確認して実行）。続けてVercelにログイン済みのChromeセッションで`vercel.com/new`からGitHubリポジトリをインポートし、Root Directory`./`・環境変数なしでデプロイ。本番URL`https://tabisaku.vercel.app`（プロジェクト設定のDomainsタブで確認、デプロイ直後のハッシュ付きURLとは別）で動作確認済み
- **教訓**: git push・GitHubリポジトリ作成・Vercelデプロイのような共有システムへの操作は、事前にプラン承認を得ていても実行の都度ユーザーに確認を取るべき（auto modeのBash分類器もpushを自動ブロックする挙動だった）。また「本番URL」を聞かれた際、デプロイ直後にVercelが返すハッシュ付きURL（`tabisaku-xxxxx-uktoshis-projects.vercel.app`）は個別デプロイのプレビューURLであり、安定した本番ドメイン（`tabisaku.vercel.app`）はプロジェクト設定のDomainsタブで別途確認する必要がある
- ユーザーから「Claude.mdを作成して」の指示を受け、本ファイル（`CLAUDE.md`）をグローバル設定のプロジェクト一覧に倣った形式（技術構成・DBスキーマ・🚧現在の作業・📝セッションログ）で新規作成し、レキサクの`CLAUDE.md`の書式（現在の作業＝太字サマリ＋✅完了ログ、セッションログ＝日付見出し＋箇条書き）を踏襲。ローカルでコミット（`9ef4b42`）のみ実施し、GitHubへのpushは今回未実施（ユーザーが「終了」と言うたびに現在の作業・セッションログを更新する運用は今後も継続）
