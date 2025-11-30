```
backend-root/
├─ Cargo.toml                           # Rust パッケージ定義
├─ Cargo.lock                           # ロックファイル（CI 用）
├─ .env                                 # ローカル用環境変数
├─ .env.example                         # 環境変数のサンプル
├─ sonar-project.properties             # SonarQube 設定（必要なら）
│
├─ migrations/                          # DBマイグレーション (sqlx, refinery, goose など)
│  ├─ XXXXXXXXXX_create_users.sql
│  └─ ...                               # テーブル追加・変更の履歴
│
├─ src/                                 # サーバー本体（ソースオブトゥルース）
│  ├─ main.rs                           # エントリポイント（超薄く：appのbootstrapを呼ぶだけ）
│  │
│  ├─ app/                              # アプリ全体のフレーム・起動周り（Composition Root）
│  │  ├─ config/                        # 設定読み込み (env, configファイル) と設定値の型
│  │  ├─ router/                        # ルーティング定義（Axum Router を組み立てる・各domainルータをマウント）
│  │  ├─ middleware/                    # アプリ共通ミドルウェア (logging, tracing, cors, authチェック 等)
│  │  ├─ context/                       # AppContext 的な DI コンテナ（DBプール, 各Repository実装, セッションストア 等）
│  │  └─ bootstrap/                     # main.rs から呼ばれる起動処理 run()（サーバbind, シャットダウン処理 等）
│  │
│  ├─ shared/                           # 全ドメイン横断の共通ロジック
│  │  ├─ error/                         # 共通エラー型 (AppError, Result エイリアス 等)
│  │  ├─ types/                         # 共通型 (Id, Timestamp, Pagination 等)
│  │  ├─ db/                            # DB接続・プール管理 (PgPool 生成 等)
│  │  ├─ tracing/                       # ログ / トレーシング初期化 (tracing-subscriber 設定)
│  │  ├─ security/                      # パスワードハッシュ, JWT, 権限チェック 等の共通処理
│  │  └─ util/                          # 汎用ユーティリティ（文字列, 日付, パス操作 等）
│  │
│  ├─ features/                         # 各 <domain> ごとの機能モジュール
│  │  └─ <domain>/                      # 例: user, project, auth, matching, entry, billing ...
│  │     ├─ domain/                     # ドメイン層（ビジネスルールの中心）
│  │     │  ├─ entities/                # エンティティ (User, Project, MatchingJob 等)
│  │     │  ├─ value_objects/           # 値オブジェクト (Email, UserName, Money 等)
│  │     │  ├─ repository/              # リポジトリトレイト (UserRepository など、DB知らないインターフェース)
│  │     │  └─ errors/                  # ドメインエラー (DomainError, ValidationError 等)
│  │     │
│  │     ├─ usecase/                    # アプリケーション層（ユースケースサービス）
│  │     │  ├─ services/                # 「何をするか」を表現するユースケース (create_user, update_project 等)
│  │     │  └─ dto/                     # ユースケース I/O の DTO（HTTP とは独立した入出力型）
│  │     │
│  │     ├─ infra/                      # インフラ層（具体的な実装）
│  │     │  ├─ repositories/            # repository トレイトの実装 (DBアクセス, キャッシュ 等)
│  │     │  ├─ models/                  # DBテーブル対応構造体 (sqlx::FromRow 等)
│  │     │  └─ external/                # 外部APIクライアント (Google OAuth, 他サービス連携 等)
│  │     │
│  │     └─ interface/                  # 外界との境界
│  │        └─ http/                    # HTTP API (Axum 用)
│  │           ├─ handlers/             # Axum ハンドラー (extract で入力, JSONレスポンス など)
│  │           ├─ routes/               # この <domain> 専用の Router（app/router からマウントされる）
│  │           ├─ request/              # HTTPリクエスト用 DTO（Json<...> 等で受ける型）
│  │           └─ response/             # HTTPレスポンス用 DTO（APIコントラクトとしての型）
│  │
│  └─ bin/                              # 複数バイナリがある場合 (バッチ, worker 等)
│     └─ worker.rs                      # 例：キューコンシューマー用プロセスなど
│
├─ tests/                               # Rust 標準の integration tests（cargo test が拾う）
│  ├─ api/                              # HTTP API 統合テスト (実サーバ or テスト用Routerに対して reqwest で叩く)
│  ├─ domain/                           # ドメインロジックの集約テスト（ユースケースよりもコアなビジネスルール）
│  └─ infra/                            # リポジトリや外部APIクライアントの統合テスト（テストDB / mockサーバ利用）
│  # ※ 各ディレクトリ配下の mod.rs or xx_tests.rs が実際の test エントリ
│
├─ benches/                             # ベンチマーク (criterion 等でパフォーマンス測定)
│
├─ scripts/                             # 開発支援スクリプト
│  ├─ dev.sh                            # 開発サーバ起動
│  ├─ migrate.sh                        # DBマイグレーション実行
│  └─ seed.sh                           # 開発用シードデータ投入
│
└─ __config__/                          # ツール・CI 向け設定（コードから import しない系）
   ├─ sonar/                            # sonar-project.properties を分割したい場合など
   ├─ ci/                               # GitHub Actions / GitLab CI 用テンプレ YAML
   ├─ docker/                           # Dockerfile, docker-compose 断片
   ├─ fmt/                              # rustfmt.toml, clippy 設定 等
   └─ env/                              # 環境変数テンプレや schema 定義 等
```
