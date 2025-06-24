```
PS C:\Users\taashitnm\Documents\poc> npm  i
npm warn deprecated @storybook/jest@0.2.3: In Storybook 8, this package functionality has been integrated to a new package called @storybook/test, which uses Vitest APIs for an improved experience. When upgrading to Storybook 8 with 'npx storybook@latest upgrade', you will get prompted and will get an automigration for the new package. Please migrate when you can.
npm warn deprecated @storybook/testing-library@0.2.2: In Storybook 8, this package functionality has been integrated to a new package called @storybook/test, which uses Vitest APIs for an improved experience. When upgrading to Storybook 8 with 'npx storybook@latest upgrade', you will get prompted and will get an automigration for the new package. Please migrate when you can.
npm warn deprecated @storybook/expect@28.1.3-5: In Storybook 8, this package functionality has been integrated to a new package called @storybook/test, which uses Vitest APIs for an improved experience. When upgrading to Storybook 8 with 'npx storybook@latest upgrade', you will get prompted and will get an automigration for the new package. Please migrate when you can.

added 1044 packages, and audited 1045 packages in 2m

200 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
PS C:\Users\taashitnm\Documents\poc> 
```
```
  "overrides": {
    "storybook": "$storybook",
    "glob": "^10.4.5",
    "rimraf": "^5.0.10"
  }
  ```
PS C:\Users\taashitnm\Documents\poc> npm ls inflight
>> npm ls rimraf
>> npm ls glob
>>
poc@1.0.0 C:\Users\taashitnm\Documents\poc
├─┬ reg-keygen-git-hash-plugin@0.14.3
│ └─┬ reg-suit-util@0.14.3
│   └─┬ glob@7.2.3
│     └── inflight@1.0.6
└─┬ reg-suit@0.14.4
  └─┬ reg-suit-core@0.14.4
    └─┬ reg-cli@0.18.10
      ├─┬ del@6.1.1
      │ └─┬ rimraf@3.0.2
      │   └─┬ glob@7.2.3
      │     └── inflight@1.0.6 deduped
      └─┬ glob@7.2.3
        └── inflight@1.0.6 deduped

poc@1.0.0 C:\Users\taashitnm\Documents\poc
└─┬ reg-suit@0.14.4
  └─┬ reg-suit-core@0.14.4
    ├─┬ reg-cli@0.18.10
    │ └─┬ del@6.1.1
    │   └── rimraf@3.0.2
    └── rimraf@5.0.10

poc@1.0.0 C:\Users\taashitnm\Documents\poc
├─┬ @testing-library/vue@8.1.0
│ └─┬ @vue/test-utils@2.4.6
│   └─┬ js-beautify@1.15.4
│     └── glob@10.4.5 deduped
├─┬ @vitest/coverage-istanbul@3.1.4
│ └─┬ test-exclude@7.0.1
│   └── glob@10.4.5
├─┬ reg-keygen-git-hash-plugin@0.14.3
│ └─┬ reg-suit-util@0.14.3
│   └── glob@7.2.3
├─┬ reg-suit@0.14.4
│ └─┬ reg-suit-core@0.14.4
│   ├─┬ reg-cli@0.18.10
│   │ ├─┬ del@6.1.1
│   │ │ └─┬ rimraf@3.0.2
│   │ │   └── glob@7.2.3
│   │ └── glob@7.2.3
│   └─┬ rimraf@5.0.10
│     └── glob@10.4.5 deduped
└─┬ tailwindcss@3.4.17
  └─┬ sucrase@3.35.0
    └── glob@10.4.5 deduped

先ほども話した「Storeの永続化でBASE64を2回かけて難読化する」って話があったと思うんですが、  
それで少し調べていたところ `javascript-obfuscator` というライブラリがありました。

これは、ビルド後のJavaScriptファイルを対象に、コード全体を自動的に難読化できるものです。  
BASE64のような単純な文字列変換よりも、構文ごと崩してくれるので可読性がかなり下がります。  
例えば変数名が全部 `_0xabc123` みたいな形に置き換わったりして、  
ぱっと見で内容が分からなくなるレベルです。

もちろんセキュリティ対策としては「気休めレベル」ではあるんですが、   
BASE64の代わりにこれを使うのも一つの手かなと思いました。

参考: 
・https://www.npmjs.com/package/javascript-obfuscator
・https://qiita.com/u83unlimited/items/970f819d1fafa325bfbf
・https://yururi-do.com/javascript-obfuscator-2022/
・https://scan.netsecurity.ne.jp/article/2024/09/25/51660.html

https://pmwbv-portalv001.almcloud.nttdata.com/ads/7143tfcDmy/yysk/_workitems/edit/7303/

http://gitlab.yysk.cicd.local/takahashitnm/poc
http://gitlab.yysk.cicd.local/takahashitnm/poc
あとで契約する
https://carparking.jp/detail/1087662/

WSLセットアップガイド作成

Evernoteに環境構築手順に関する内容を随時記載しておいて、新規PCがきたときに設定を再現できるようにする（CursorのオートコンプリートをOFFとか）。

ーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーー
まさに 「AIコンシェルジュ」 とか 「AIエージェントアシスタント」 の領域ですね。
特に、音声での会話＋IoT接続＋PC操作＋スクリプト実行＋常時スタンバイ という点を組み合わせると、次のような技術を組み合わせることになります。

実現に必要な主要技術
1. 音声インターフェース（VUI）
音声認識（ASR）: Whisper（OpenAI）、Google Speech-to-Text など
音声合成（TTS）: Coqui TTS、Google TTS、Amazon Polly など
音声処理エンジン: Mycroft、Rhasspy（オープンソース）、Alexa、Google Assistant など
2. 自律エージェント（AIエージェント）
対話モデル: LLM（GPT-4、Claude、Geminiなど）
会話管理: Rasa、LangChain、Chatbot Frameworks
スクリプト実行: Python+LangChain（PC操作のオートメーション）
3. IoTデバイス連携
プロトコル: MQTT、WebSockets
デバイス制御: Home Assistant、Node-RED、Raspberry Pi
API接続: スマート家電のAPIを活用
4. キャラクターのモデリング & アニメーション
3Dキャラ: VRM（Vroidモデル）、MetaHuman
アニメーション: Live2D、Unity＋VRChat
ボイス同期: Coqui TTS、VITS（VITS-fast）での発話連携
5. UI/UX（常時スタンバイ＆チャット対応）
Web UI: React + WebRTCでブラウザUI
PC UI: Electronでデスクトップアプリ化
スマホ連携: PWA（Progressive Web App）
理想のAIコンシェルジュの動き
ユーザーの呼びかけ（音声 or チャット）に反応

例: 「〇〇、今の予定は？」
処理: 音声認識→対話AI処理→スケジュール取得→音声返答
PCでスクリプトを実行

例: 「このPythonスクリプトを動かして」
処理: 音声解析→PC内のスクリプト実行→結果通知
IoTデバイス制御

例: 「エアコンをつけて」「部屋の照明を青に」
処理: APIまたはMQTTでデバイス操作
仮想キャラクターとして発話・表情変化

動作例: 声に合わせて口パク、表情変化、ジェスチャー
技術: VRM + WebRTC or Live2D + Unity
これを作るなら？
第一段階: チャット＋スクリプト実行 → AIエージェントとして会話処理
第二段階: VUI（音声入力/出力）追加
第三段階: IoT連携 & キャラクター表現強化（VRM or Live2D）
これ、最終的には 「未来のAIアシスタント」 みたいな感じになりそうですね！
実際に開発進めるなら、どの部分から手をつけるか考えてみてもいいかも！





