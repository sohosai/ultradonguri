# Ultradonguri

超正統派テロップ送出システムのデプロイ版

- サーバ (ultradonguri-server)
- コントローラ (ultradonguri-controller)

がすべて1つのcomposeにまとまっている統合環境です。

## システム構成

- **server** (ポート8080): OBS WebSocketと通信してシーンを切り替え
- **client** (ポート5173): Webベースのコントローラー / ビューア

## 事前準備

### 1. OBSの設定

OBSでWebSocketサーバーを有効にしてください：

- ツール → WebSocket サーバー設定
- WebSocketサーバーを有効化
- ポート: 4455（デフォルト）
- パスワードを設定

### 2. 環境設定

`.env.example` を `.env` にコピーして、OBS WebSocket のパスワードなどを設定してください。`.env` はgitignoreされています。

```powershell
Copy-Item .env.example .env
```

```env
ADDRESS=host.docker.internal:4455
PASSWORD=your_obs_password
NORMAL_SCENE_NAME=Normal_Scene
CM_SCENE_NAME=CM_Scene
BURARI_SCENE_NAME=Burari_Scene
CONTROLLER_ADDRESS=http://localhost:5173,http://127.0.0.1:5173
SCENE_BACKUP_PATH=/app/data/scene.json
TZ=Asia/Tokyo
PERFORMANCES_FILE=./performances.json
```

`PASSWORD` が未設定の場合は `docker compose` がエラーになります。

### 3. イベントデータ

`performances.json` をこのディレクトリに置いてください。gitにはpushしません。

`performances.sample.json` をコピーして編集するか、`.env` で `PERFORMANCES_FILE` を指定します。未指定の場合は `performances.sample.json` が使われます。

```powershell
Copy-Item performances.sample.json performances.json
```

```env
PERFORMANCES_FILE=./performances.json
```

このJSONはcontrollerの `/performances.json` としてマウントされます。内容を差し替えると、controllerをリロードした際に新しいデータが採用されます（内容が変わっていない場合はlocalStorageの編集内容が維持されます）。

## 起動方法

```bash
cd ultradonguri
docker compose pull
docker compose up -d
```

起動後、以下のURLでアクセスできます：

- **コントローラーUI**: http://localhost:5173/
- **ビューア**: http://localhost:5173/viewer
- **サーバーAPI**: http://localhost:8080
- **controller/viewer間WebSocket**: ws://localhost:5173/burari/ws

**必ず `localhost` または `127.0.0.1` でアクセスしてください。** PC名やLAN IPで開くとCORS/WebSocketの許可外になります。

## データの管理

- `performances.json`: イベントデータ（gitignore）
- `videos/`: ぶらり旅の動画アップロード先（gitignore）
- `server-data/`: OBSシーンのバックアップ（gitignore）

## 開発者向け情報

### 使用するイメージ

- `ghcr.io/sohosai/ultradonguri-server:production`
- `ghcr.io/sohosai/ultradonguri-controller:production`

### イメージの更新

```bash
docker compose pull
docker compose up -d
```

### 注意

- controllerイメージにはビルド時の `VITE_OSECHI_BASE_URL` が埋め込まれています。osechiのURLやCORS設定を変更する場合はcontrollerイメージの再ビルドが必要です。
- `videos/` は動画アップロードの永続化用です。ディレクトリが無い場合は作成してください。
