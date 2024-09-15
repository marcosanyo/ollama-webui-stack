# 概要
- ホストOSに`ollama`を直接インストールせず`ollama`サーバーと`open-webui`サーバーをdockerで同時に立ち上げるためのリポジトリ。
- gpt-4oで作成しました。
- docker-compose化しました。
- ポートを変更した場合は、`curl`コマンドのURLも適宜変更してください。
- GPUメモリ8GBのWindows 11でWSLを使用し、phi-3とllama3.1モデルが問題なく動作することを確認しました。

# 前提条件

- DockerとDocker Composeがインストールされていること。

# セットアップ手順

1. `.env`ファイルを編集して、使用するポートを設定します。デフォルトの設定は以下の通りです。

   ```env
   # Open WebUI port
   OPEN_WEBUI_PORT=3000

   # Ollama port
   OLLAMA_PORT=11434
   ```

   必要に応じてポート番号を変更してください。

2. Ollamaでモデルをダウンロードします。

   - `docker exec`を使用してダウンロードする場合:

     ```bash
     docker exec -it ollama/ollama:latest ollama pull phi3
     ```

     `phi3`はダウンロードするモデル名です。

   - WebUIの管理者パネルからダウンロードする場合:
     - 管理者パネルにアクセスし、ダウンロードしてください。

3. Docker Composeでサービスをビルドして起動します。

   ```bash
   docker compose up --build -d
   ```

   このコマンドで`ollama`と`open-webui`がバックグラウンドで起動します。

# 推論エンドポイントの利用方法

`ollama`の推論エンドポイントを利用するには、以下のように`curl`コマンドを使用します。

- **逐次メッセージを受け取る場合**:

  ```bash
  curl http://localhost:11435/api/chat -d '{
    "model": "phi3",
    "messages": [
      { "role": "user", "content": "Hello!"}],
    "stream": true
  }'
  ```

  `stream`を`true`に設定すると、メッセージが逐次返ってきます。

- **一度にメッセージを受け取る場合**:

  ```bash
  curl http://localhost:11435/api/chat -d '{
    "model": "phi3",
    "messages": [
      { "role": "user", "content": "Hello!"}],
    "stream": false
  }'
  ```

  `stream`を`false`に設定すると、一度にメッセージが返ってきます。

# Web UIの利用

ブラウザで以下のURLにアクセスすると、`open-webui`のユーザーインターフェースに入ることができます。

```
http://localhost:3000/
```
