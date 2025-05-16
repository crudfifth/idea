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





