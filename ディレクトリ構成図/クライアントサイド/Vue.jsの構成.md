```
project-root/
├─ public/                               # 静的ファイル（favicon, robots.txt, OGP画像 等）
│
├─ src/                                  # アプリ本体 (ソースオブトゥルース)
│  ├─ main.ts                            # エントリポイント（薄い起動コードだけ）
│  │
│  ├─ app/                               # アプリ全体のフレーム・起動周り
│  │  ├─ router/                         # ルーティング定義
│  │  │  └─ guards/                      # 認可チェック・ログイン必須などのルートガード
│  │  ├─ layout/                         # App全体の枠（Header / Sidebar / Main / Footer）
│  │  ├─ errors/                         # グローバルエラーページ・エラーハンドリングUI
│  │  └─ providers/                      # DI, Queryクライアント, i18n などのプロバイダ初期化
│  │
│  ├─ components/                        # 業務ロジックを持たない汎用UIコンポーネント
│  │  ├─ ui/                             # BaseButton, BaseInput, Icon など
│  │  └─ layout/                         # Header, Sidebar, Footer など見た目だけのレイアウト部品
│  │
│  ├─ features/                          # 各 <domain> ごとの業務UI＋業務ロジック
│  │  └─ <domain>/                       # 例: chat, project, matching, entry, sales, schedule...
│  │     ├─ ui/                          # <domain> 専用のUI層
│  │     │  ├─ pages/                    # ルーターから呼ばれる薄いページ（Screen からコンテンツを差し込む想定）
│  │     │  ├─ screens/                  # Main の中身となる画面本体（一覧・詳細・編集など）
│  │     │  ├─ components/               # <domain> 専用のUI部品（カード, 行, フォームなど）
│  │     │  ├─ composables/              # <domain> 画面専用のUIロジック (useXxx 系)
│  │     │  └─ styles/                   # <domain> 専用スタイル（スコープCSS, CSS Modules 等）
│  │     │
│  │     └─ logic/                       # <domain> 専用の“ロジックだけ”（UIは持たない）
│  │        ├─ types/                    # DTOなど、<domain> 専用の型定義
│  │        ├─ models/                   # <domain> モデル／エンティティ（DTO→Model変換＋振る舞い）
│  │        ├─ services/                 # ユースケース（ビジネスロジック）例: executeLogin, updateProfile
│  │        ├─ repositories/             # <domain> 向けAPIクライアント層（Axios 等）
│  │        ├─ stores/                   # <domain> 専用状態管理（Pinia ストア）
│  │        ├─ validators/               # <domain> のフォーム／値チェックロジック
│  │        ├─ messages/                 # <domain> 専用メッセージ（エラー文言・ラベル等）
│  │        ├─ constants/                # <domain> 専用定数（ステータス, ロール, フラグ値 等）
│  │        └─ errors/                   # <domain> 専用エラー型・例外（ドメインエラー・バリデーションエラー）
│  │
│  ├─ shared/                            # 全ドメイン横断で使い回す共通ロジック＆スタイル
│  │  ├─ types/                          # 共通DTO・型定義
│  │  ├─ entities/                       # 汎用エンティティ
│  │  ├─ models/                         # 複数 <domain> で使い回す汎用モデル
│  │  ├─ services/                       # 特定 <domain> に属さない共通ユースケース
│  │  ├─ repositories/                   # 共通APIクライアント層（HTTPクライアントのラッパ等）
│  │  ├─ stores/                         # グローバル状態（ログインユーザー, アプリ設定 等）
│  │  ├─ composables/                    # 複数 feature で使うUIロジック (useToast, useConfirm 等)
│  │  ├─ validators/                     # 共通バリデーション
│  │  ├─ constants/                      # 共通定数（APIベースURL, 汎用ステータス, 正規表現 等）
│  │  ├─ messages/                       # 共通メッセージ（汎用エラー, 共通ラベル 等）
│  │  ├─ errors/                         # 共通エラー型（HttpError, AppError などロジック層の例外）
│  │  ├─ utils/                          # 汎用関数（日付フォーマット, 配列操作, 文字列ユーティリティ 等）
│  │  ├─ http/                           # Axiosインスタンスや fetch ラッパなどHTTPクライアント共通層
│  │  └─ styles/                         # 共通スタイル（テーマ変数, デザイントークン, 汎用ユーティリティクラス）
│  │
│  └─ mocks/                             # 開発用モック一式（MSW）
│     ├─ msw/                            # MSW 関連
│     │  ├─ server/                      # Node(テスト・SSR)向け MSW サーバ設定
│     │  └─ browser/                     # ブラウザ向け MSW ワーカー設定
│     ├─ handlers/                       # 各エンドポイントごとのハンドラ定義
│     └─ mock-data/                      # モックに使うデータ
│        └─ <domain>/                    # 例: ドメインごとに区切ったモックデータ
│
├─ __storybook__/                        # Storybook 用設定・ストーリー関連
│  ├─ config/                            # Storybook のメイン設定ファイル群
│  ├─ stories/                           # グローバル or cross-domain なストーリー
│  └─ mocks/                             # Storybook用の専用モック
│
├─ __test__/                             # E2E / 結合 / ユニットなどテスト用ディレクトリ
│  ├─ unit/                              # 単体テスト（ロジック・小さいコンポーネント）
│  ├─ integration/                       # 結合テスト
│  ├─ e2e/                               # E2Eテスト（Playwright）
│  ├─ mutation/                          # ミューテーションテスト
│  ├─ contruct/                          # コントラクトテスト
│  └─ visual-regression/                 # ビジュアルリグレッション（Playwright + Chromatic or reg-suit）
│
├─ __config__/                           # ツール系設定をまとめたい場合の置き場
│  ├─ vite/                              # Vite周りの設定
│  ├─ vitest/                            # テストランナー設定
│  ├─ eslint(biome)/                     # ESLint(またはbiome) 設定の分割
│  └─ tailwind/                          # Tailwind 設定の分割
│
├─ package.json
├─ tsconfig.json
└─ その他 CI 設定や dotfiles (.editorconfig, .eslintrc, .prettierrc, .github 等)
```
