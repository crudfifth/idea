## 1.1 Git Hooksおよび事前検証設定

本節では、Git操作におけるプリコミットフック（pre-commit hook）を活用し、ローカル開発段階でコードの静的検証や整形、テストを自動実行するための設定手順を示す。  
チーム開発において品質基準を均一に保ち、未整形コード・未検証コードの混入を防止する目的で導入する。

---

### 1.1.1 概要

Git Hooksとは、Gitが特定のイベントを実行する前後に任意のスクリプトを動作させる仕組みである。  
本構成では、`pre-commit` に以下の処理を設定し、**コミット直前の自動検証・整形を実施可能とする**。

| 設定項目             | 役割                                           |
|----------------------|------------------------------------------------|
| `simple-git-hooks`   | Git Hookの定義およびスクリプト紐付け           |
| `lint-staged`        | ステージングされた差分ファイルのみを対象とした検証実行 |
| `npm-run-all`        | 複数スクリプトの逐次実行制御                   |

---

### 1.1.2 推奨設定例（package.json）

以下は、pre-commitフックに検証処理を設定するための最小構成例である。

```json
{
  "simple-git-hooks": {
    "pre-commit": "npm run precommit"
  },
  "scripts": {
    "prepare": "simple-git-hooks",
    "precommit": "npm-run-all lint-staged:run test:precommit",
    "lint-staged:run": "npx lint-staged",
    "test:precommit": "vitest run --passWithNoTests"
  },
  "lint-staged": {
    "*.{js,ts,vue}": [
      "eslint --fix",
      "prettier --write"
    ]
  }
}
```
> ※ルールが煩雑になり可読性が低下する場合や、ルール群を複数プロジェクトで再利用する場合には、
lint-staged.config.ts（または .js）を作成し設定を分離することを推奨する。
---

### 1.1.3 各項目の役割と意図

#### 1.1.3.1. `simple-git-hooks`

- Gitの `pre-commit` イベントに対して任意のnpmスクリプトを紐付ける。
- `"prepare"` スクリプトによって `npm install` 時に自動的に `.git/hooks/` に適用される。

#### 1.1.3.2. `lint-staged`

- Gitでステージングされた差分ファイルに限定して `eslint` および `prettier` を実行。
- プロジェクト全体への一律処理と比較し、実行時間を最小化できる。
- 対象拡張子は `.js`, `.ts`, `.vue` 等、プロジェクトの言語構成に応じて定義。

#### 1.1.3.3. `vitest run`

- コミット前にユニットテストを自動実行し、コードの正当性を検証。
- `--passWithNoTests` オプションを指定することで、該当ファイルが存在しないケースでも通過可能となる。

---

### 1.1.4 補足事項

- 検証処理に時間がかかりすぎるとUXを損ねるため、コミット時点では**最低限の検証に絞る**ことが望ましい。
- フック処理が長時間化する場合は、テストのみCIに委譲する等、段階的な実行戦略を検討する。
- その他の処理（型検査、コンフリクト検知、依存性監査など）も追加可能であるが、事前検証項目はチーム内合意の上で段階的に設定することが望ましい。

---

## 1.2 SonarQube構成設計

本節では、コード品質管理プラットフォームである **SonarQube** を用いた静的解析の導入手順および設定項目の概要を記載する。  
SonarQubeはコードの静的分析（バグ、コードスメル、テストカバレッジなど）を行うため、**CI/CDにおける品質ゲート管理**を目的として導入される。

---

### 1.2.1 構成全体の概要

SonarQubeを導入する際には、以下の構成ファイル・設定ファイルへの追記が必要となる。

| 対象                       | 主な設定内容                                                |
|----------------------------|-------------------------------------------------------------|
| `sonar-project.properties` | ソース・テスト・除外対象・レポート出力先等の定義           |
| `vitest.config.ts`         | テストレポート（JUnit形式）・カバレッジレポートの出力設定 |
| `package.json`（任意）     | Sonarスキャン用のスクリプト追加                            |
| `sonar-scan.cjs`（任意）   | 環境変数を用いた `sonar-scanner` 実行スクリプト（Node.js） |

---

### 1.2.2 Sonar設定ファイル（sonar-project.properties）

```properties
# ソースコード解析対象のルートディレクトリ
sonar.sources=src

# テストコードの存在箇所（Vitest等）
sonar.test=**/__tests__/**
sonar.test.inclusions=**/__tests__/**
sonar.exclusions=**/__tests__/**

# JUnit形式のテスト結果ファイル
sonar.testExecutionReportPaths=report/test-report.xml

# JavaScript / TypeScript / Vueファイルを解析対象とする
sonar.javascript.file.suffixes=.js,.jsx
sonar.typescript.file.suffixes=.ts,.tsx,.vue

# カバレッジレポート（lcov形式）
sonar.typescript.lcov.reportPaths=coverage/lcov.info
sonar.javascript.lcov.reportPaths=coverage/lcov.info

# ESLint出力レポート（JSON形式）
sonar.eslint.reportPaths=report/report.json
```

> ※ `__tests__` 配下の取り扱いはプロジェクト方針に応じて調整してください。

---

### 1.2.3 `vitest.config.ts` への追加設定

```ts
test: {
  // テストレポート（JUnit形式）と SonarQube 向け JSON 出力
  reporters: [
    ['json', { outputFile: 'report/test-report.json' }],
    ['vitest-sonar-reporter', { outputFile: 'report/test-report.xml' }]
  ],
  coverage: {
    // カバレッジ出力（lcov形式）
    reporter: ['text', 'lcov'],
    reportsDirectory: 'coverage',
    include: ['src/**/*.{js,jsx,ts,tsx}'],
    exclude: ['src/**/index.ts']
  }
}
```

| 項目                    | 内容                                               |
|-------------------------|----------------------------------------------------|
| `report/test-report.xml`| テスト結果（JUnit形式）をSonarに連携              |
| `coverage/lcov.info`    | カバレッジ結果（lcov形式）をSonarに連携            |

---

### 1.2.4 SonarQubeスキャンの自動実行

SonarQubeへのスキャンをコマンドラインから柔軟に実行したい場合、環境変数で対象プロジェクトやトークンを切り替える用途でスクリプトを使用することがある。

> 本スクリプトは、CI設定がない開発初期段階や、ローカルで手動スキャンを行いたいケースにおいて有用である。

#### 1.2.4.1 スクリプト定義例（`sonar-scan.cjs`）

```js
require('dotenv').config()
const { execSync } = require('child_process')

const sonarHost = process.env.SONAR_HOST_URL
const sonarToken = process.env.SONAR_TOKEN
const project_id = process.env.project_id

if (!sonarHost || !sonarToken || !project_id) {
  console.error("ERROR: SONAR_HOST_URL, SONAR_TOKEN または project_id が設定されていません")
  process.exit(1)
}

const cmd = `sonar-scanner \
  -Dsonar.host.url=${sonarHost} \
  -Dsonar.token=${sonarToken} \
  -Dsonar.projectKey=${project_id} \
  -Dsonar.projectName=${project_id}`

console.log(`Running: ${cmd}`)
execSync(cmd, { stdio: 'inherit' })
```

---

#### 1.2.4.2 実行方法（ローカルまたはCI）

##### ローカル環境での手動実行

```bash
SONAR_HOST_URL=https://sonar.example.com \
SONAR_TOKEN=xxxxxxxxxxxxxxxx \
project_id=yysk-project \
node sonar-scan.cjs
```

##### CI/CDにおける定義（GitHub Actions 例）

```yaml
- name: SonarQube Scan
  run: node sonar-scan.cjs
  env:
    SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
    SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
    project_id: yysk-project
```

> ※ sonar-scanner CLIのインストールまたは公式Dockerイメージの利用が別途必要です。

---

### 1.2.5 導入時の留意点

- **出力パスの整合性**：`vitest.config.ts`と`sonar-project.properties`での指定パスが一致していること。
- **`.vue` を対象に含める場合**：`sonar.typescript.file.suffixes` に `.vue` を含める。
- **CI環境での実行**：`sonar-scanner` コマンドの導入が事前に済んでいる必要がある。

---
