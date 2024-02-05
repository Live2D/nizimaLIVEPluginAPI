# Method 一覧

## RegisterPlugin

nizima LIVE にプラグインを登録する。

### Request Data Type

```typescript
Name: string,
Developer?: string,
Version?: string,
Icon?: string
```

- Name: プラグインの名前  
- Developer: 開発者名  
- Version: プラグインのバージョン  
- Icon: Base64 でエンコードされた PNG ファイル。正方形の画像を使用しないと表示が崩れる可能性がある。

### Response Data Type

```typescript
Token: string
```

## EstablishConnection

nizima LIVE に再接続するときに使用する。  
RegisterPlugin で登録した Name と、取得した Token を使用する。

### Request Data Type

```typescript
Name: string,
Token: string,
Version?: string
```

### Response Data Type

```typescript
Enabled: boolean
```

## RegisterPluginPath

プラグインの実行ファイルのパスを指定することで、nizima LIVE からプラグインを起動することができるようになります。  
起動できる要件は以下の通りです。  
- 絶対パスであること  
- パスを登録した時点から実行ファイルのハッシュ値が変化していないこと

起動できなくなった場合でも、再度 SetPluginPath を使用してパスを登録することで、ハッシュが再計算され起動できるようになります。

nizima LIVE からプラグインを起動した際には、プラグインのコマンドライン引数に `-l -p <port>` が渡されます。  
`<port>` は WebSocket のポート番号です。

### Request Data Type

```typescript
PluginPath: string
```

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidPluginPath`

## NotifyEnabledChanged

このイベントは Request を送っていなくても通知される。  
プラグインが有効になっているかどうかがわかる。

### Event Data Type

```typescript
Enabled: boolean
```

## DelayTest

プラグイン開発用の機能。  
設定した Delay ミリ秒後に Response が返ってくる。

### Request Data Type

```typescript
Delay: number
```

### Response Data Type

```typescript
{}
```

## GetConnectionStatus

nizima LIVE への接続状態を取得する。

### Request Data Type

```typescript
{}
```

### Response Data Type

```typescript
Established: boolean,
Enabled: boolean
```

## NotifyLaunchButtonClicked

このイベントは Request を送っていなくても通知される。  
プラグイン接続中にプラグインマネージャーで起動ボタンがクリックされたときに通知される。

### Event Data Type

```typescript
{}
```

## SetCubismParameterValues

指定したモデルの CubismParameter の値を設定する。  
最後に値を設定してから 500ms の間はその値を維持する。  
複数のプラグインやスクリプトから同じパラメータの値を設定した場合の動作は未定義。

### Request Data Type

```typescript
ModelId: string,
CubismParameterValues: Array<{
  Id: string,
  Value: number
}>
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`

## GetCubismParameterValues

指定したモデルの CubismParameter の現在値が取得できる。

### Request Data Type

```typescript
ModelId: string,
CubismParameterIds?: Array<string>
```

ModelId には Live2D アイテムの ItemId も設定可能。  
CubismParameterIds の指定がない場合はすべてのパラメータを取得する。

### Response Data Type

```typescript
CubismParameterValues: Array<{
  Id: string,
  Value: number
}>
```

### Error Types

* `InvalidModelId`

## GetCubismParameters

指定したモデルの CubismParameter 一覧が取得できる。

### Request Data Type

```typescript
ModelId: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
CubismParameters: Array<{
  Id: string,
  Name: string,
  DefaultValue: number,
  Max: number,
  Min: number
}>
```

### Error Types

* `InvalidModelId`

## SetLiveParameterValues

指定したモデルの LiveParameter の値を設定する。  
最後に値を設定してから 500ms の間はその値を維持する。  
複数のプラグインやスクリプトから同じパラメータの値を設定した場合の動作は未定義。

### Request Data Type

```typescript
ModelId?: string,
LiveParameterValues: Array<{
  Id: string,
  Value: number
}>
```

ModelId には Live2D アイテムの ItemId も設定可能。  
ModelId の指定がない場合はすべてのモデルに適用される。

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`

## GetLiveParameterValues

指定したモデルの LiveParameter の現在値が取得できる。

### Request Data Type

```typescript
ModelId: string,
LiveParameterIds?: Array<string>
```

ModelId には Live2D アイテムの ItemId も設定可能。  
LiveParameterIds の指定がない場合はすべてのパラメータを取得する。

### Response Data Type

```typescript
LiveParameterValues: Array<{
  Id: string,
  Value: number
}>
```

### Error Types

* `InvalidModelId`

## ResetCubismParameterValues

指定したモデルの CubismParameter の値をデフォルト値に戻す。

### Request Data Type

```typescript
ModelId: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`

## InsertLiveParameters

LiveParameter を追加する。  
パラメータの追加は nizima LIVE に保存されないので、プラグインを接続するたびにパラメータの追加を行うこと。

### Request Data Type

```typescript
LiveParameters: Array<{
  Id: string,
  Group?: string,
  Base: number,
  Max: number,
  Min: number,
  Repeat?: boolean
}>
```

Id が同じパラメータは存在できないので、nizima LIVE に元からあるパラメータや他のプラグインと被らない Id を使用すること。  
また、シンプルな Id は将来的に nizima LIVE が使用することで衝突する可能性があるため、使用しないほうが良い。  
Max、Min、Base について、Min < Max かつ Min ≤ Base ≤ Max となっていない場合は InvalidData を返す。  
Repeat はパラメータの Max と Min がつながる場合に true を設定する。（角度 0～360 など）

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidData`

## GetLiveParameters

現在 nizima LIVE 上に存在する LiveParameter の一覧が取得できる。  
LiveParameter にはトラッキングの結果などが入る。

### Request Data Type

```typescript
{}
```

### Response Data Type

```typescript
LiveParameters: Array<{
  Id: string,
  CreatedBy: string,
  Group: string,
  Base: number,
  Max: number,
  Min: number,
  Repeat: boolean
}>
```

- CreatedBy: パラメータの作成者が入る。nizima LIVE またはプラグイン名。  
- Repead: パラメータの Max と Min がつながる場合に true となる。ハンドトラッキングの角度などが該当する。

## NotifyFrameUpdated

描画の更新毎の通知を有効にする。  
各モデルのパラメータの現在値が取得できる。

### Request Data Type

```typescript
Enabled?: boolean
```

### Response Data Type

```typescript
{}
```

### Event Data Type

```typescript
Models: Array<{
  ModelId: string,
  LiveParameterValues: Array<{
    Id: string,
    Value: number
  }>,
  CubismParameterValues: Array<{
    Id: string,
    Value: number
  }>
}>
```

## GetScenes

すべてのシーンと、それに属するモデルとアイテムの情報を取得する。

### Request Data Type

```typescript
{}
```

### Response Data Type

```typescript
Scenes: Array<{
  SceneId: string,
  Models: Array<{
    Local: boolean,
    Name: string,
    ModelId: string,
    SceneId: string,
    ModelPath?: string,
    IconPath?: string,
    PositionX: number,
    PositionY: number,
    Scale: number,
    Rotation: number
  }>,
  Items: Array<{
    Name: string,
    ItemType: "Live2D" | "Animated" | "Still",
    ItemId: string,
    SceneId: string,
    ItemPath: string,
    IconPath: string,
    PositionX: number,
    PositionY: number,
    Scale: number,
    Rotation: number
  }>
}>
```

- PositionX: ウィンドウの右端が 1、左端が -1  
- PositionY: ウィンドウの上端が 1、下端が -1  
- Scale: 表示倍率。デフォルトは 1  
- Rotation: 度数法で指定する角度。デフォルトは 0 で反時計回りが正

## NotifyScenesChanged

新しいウィンドウでモデルを開いた時やウィンドウを閉じた時など、シーンの数が増減したときの通知を有効にする。

### Request Data Type

```typescript
Enabled?: boolean
```

### Response Data Type

```typescript
{}
```

### Event Data Type

```typescript
Scenes: Array<{
  SceneId: string,
  Models: Array<{
    Local: boolean,
    Name: string,
    ModelId: string,
    SceneId: string,
    ModelPath?: string,
    IconPath?: string,
    PositionX: number,
    PositionY: number,
    Scale: number,
    Rotation: number
  }>,
  Items: Array<{
    Name: string,
    ItemType: "Live2D" | "Animated" | "Still",
    ItemId: string,
    SceneId: string,
    ItemPath: string,
    IconPath: string,
    PositionX: number,
    PositionY: number,
    Scale: number,
    Rotation: number
  }>
}>
```

- PositionX: ウィンドウの右端が 1、左端が -1  
- PositionY: ウィンドウの上端が 1、下端が -1  
- Scale: 表示倍率。デフォルトは 1  
- Rotation: 度数法で指定する角度。デフォルトは 0 で反時計回りが正

## GetCurrentSceneId

最後にフォーカスされたウィンドウの SceneId を取得する。

### Request Data Type

```typescript
{}
```

### Response Data Type

```typescript
SceneId: string
```

### Error Types

* `CurrentSceneUnavailable`

## NotifyCurrentSceneChanged

ウィンドウのフォーカスが変わった際の通知を有効にする。  
フォーカスされたウィンドウの SceneId が取得できる。

### Request Data Type

```typescript
Enabled?: boolean
```

### Response Data Type

```typescript
{}
```

### Event Data Type

```typescript
SceneId: string
```

## RemoveModel

画面上に表示されているモデルを指定して削除する。

### Request Data Type

```typescript
ModelId: string
```

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`

## ChangeModel

指定したモデルを別のモデルに変更する。

### Request Data Type

```typescript
ModelId: string,
ModelPath: string
```

### Response Data Type

```typescript
ModelId: string
```

### Error Types

* `InvalidModelId`
* `InvalidModelPath`

## AddModel

指定したモデルを、指定したシーンに追加する。

### Request Data Type

```typescript
SceneId?: string,
ModelPath: string
```

SceneId を指定しない場合は新規ウィンドウで表示する。

### Response Data Type

```typescript
ModelId: string
```

### Error Types

* `InvalidModelPath`

## RegisterModel

nizima LIVE にモデルを登録する。

### Request Data Type

```typescript
ModelPath: string,
IconPath?: string
```

### Response Data Type

```typescript
ModelPath: string
```

### Error Types

* `InvalidModelPath`

## GetRegisteredModelIcons

指定したモデルのアイコンデータを取得する。

### Request Data Type

```typescript
ModelPaths: Array<string>
```

### Response Data Type

```typescript
RegisteredModelIcons: Array<{
  ModelPath: string,
  Icon: string
}>
```

- Icon: Base64 でエンコードされたアイコン画像

## GetRegisteredModels

nizima LIVE に登録されているモデルの一覧を取得する。

### Request Data Type

```typescript
{}
```

### Response Data Type

```typescript
RegisteredModels: Array<{
  Name: string,
  ModelPath: string,
  IconPath: string,
}>
```

## ResetPose

選択したモデルの Pose をリセットする。

### Request Data Type

```typescript
ModelId: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`

## GetModels

表示されているモデルの一覧を取得する。

### Request Data Type

```typescript
{}
```

### Response Data Type

```typescript
Models: Array<{
  Local: boolean,
  Name: string,
  ModelId: string,
  SceneId: string,
  ModelPath?: string,
  IconPath?: string,
  PositionX: number,
  PositionY: number,
  Scale: number,
  Rotation: number
}>
```

- Local: コラボモデルの時 false になる  
- PositionX: ウィンドウの右端が 1、左端が -1  
- PositionY: ウィンドウの上端が 1、下端が -1  
- Scale: 表示倍率。デフォルトは 1  
- Rotation: 度数法で指定する角度。デフォルトは 0 で反時計回りが正

## NotifyModelsChanged

画面上のモデルが追加・削除された際の通知を有効にする。

### Request Data Type

```typescript
Enabled?: boolean
```

### Response Data Type

```typescript
{}
```

### Event Data Type

```typescript
Models: Array<{
  Local: boolean,
  Name: string,
  ModelId: string,
  SceneId: string,
  ModelPath?: string,
  IconPath?: string,
  PositionX: number,
  PositionY: number,
  Scale: number,
  Rotation: number
}>
```

- Local: コラボモデルの時 false になる  
- PositionX: ウィンドウの右端が 1、左端が -1  
- PositionY: ウィンドウの上端が 1、下端が -1  
- Scale: 表示倍率。デフォルトは 1  
- Rotation: 度数法で指定する角度。デフォルトは 0 で反時計回りが正

## MoveModel

指定したモデルを移動する。

### Request Data Type

```typescript
ModelId: string,
Absolute?: boolean,
PositionX?: number,
PositionY?: number,
Scale?: number,
Rotation?: number,
Delay?: number,
InterpolationType?: string
```

- Absolute: 未指定の場合 false  
  - true の場合、ウィンドウ上の絶対的な座標や回転を指定して移動する  
  - false の場合、現在の位置から相対的に移動する  
- PositionX: ウィンドウの右端が 1、左端が -1  
- PositionY: ウィンドウの上端が 1、下端が -1  
- Scale: 表示倍率。デフォルトは 1  
- Rotation: 度数法で指定する角度。デフォルトは 0 で反時計回りが正  
- Delay: モデルの移動時間（秒）  
  - コラボモデルでは 0 に固定される  
- InterpolationType: 移動時の補間方法を指定する。デフォルトは Linear  
  - https://doc.qt.io/qt-6/qeasingcurve.html#Type-enum

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`

## SetDrawablesColor

指定したモデルの Drawable の色を変更する。

### Request Data Type

```typescript
ModelId: string,
DrawableIds: Array<string>,
MultiplyColor?: {
  Float?: boolean,
  Red: number,
  Green: number,
  Blue: number,
  Alpha?: number
},
ScreenColor?: {
  Float?: boolean,
  Red: number,
  Green: number,
  Blue: number,
  Alpha?: number
}
```

ModelId には Live2D アイテムの ItemId も設定可能。  
MultiplyColor や ScreenColor の指定がない場合は素の状態に戻る。  
Color について  
- Float が null または false のとき  
  - RGBA は 0-255 の値で指定する  
- Float が true のとき  
  - RGBA は 0-1 の値で指定する

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`

## SetPartsColor

指定したモデルの Part の色を変更する。

### Request Data Type

```typescript
ModelId: string,
PartIds: Array<string>,
MultiplyColor?: {
  Float?: boolean,
  Red: number,
  Green: number,
  Blue: number,
  Alpha?: number
},
ScreenColor?: {
  Float?: boolean,
  Red: number,
  Green: number,
  Blue: number,
  Alpha?: number
}
```

ModelId には Live2D アイテムの ItemId も設定可能。  
MultiplyColor や ScreenColor の指定がない場合は素の状態に戻る。  
Color について  
- Float が null または false のとき  
  - RGBA は 0-255 の値で指定する  
- Float が true のとき  
  - RGBA は 0-1 の値で指定する

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`

## SetModelColor

指定したモデルの色を変更する。

### Request Data Type

```typescript
ModelId: string,
MultiplyColor?: {
  Float?: boolean,
  Red: number,
  Green: number,
  Blue: number,
  Alpha?: number
},
ScreenColor?: {
  Float?: boolean,
  Red: number,
  Green: number,
  Blue: number,
  Alpha?: number
}
```

ModelId には Live2D アイテムの ItemId も設定可能。  
MultiplyColor や ScreenColor の指定がない場合は素の状態に戻る。  
Color について  
- Float が null または false のとき  
  - RGBA は 0-255 の値で指定する  
- Float が true のとき  
  - RGBA は 0-1 の値で指定する

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`

## GetDrawables

モデルの Drawable 一覧を取得する。

### Request Data Type

```typescript
ModelId: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
Drawables: Array<{
  Id: string
}>
```

### Error Types

* `InvalidModelId`

## GetParts

モデルの Part 一覧を取得する。

### Request Data Type

```typescript
ModelId: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
Parts: Array<{
  Id: string,
  Name: string
}>
```

### Error Types

* `InvalidModelId`

## GetPartsTree

モデルを根として Part と Drawable を木構造にしたものを取得する。

### Request Data Type

```typescript
ModelId: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
PartNode: {
  Id: string,
  Name: string,
  Type: "Model" | "Part" | "Drawable",
  Children: Array<typeof PartNode>
}
```

### Error Types

* `InvalidModelId`

## GetCurrentModelId

最後に選択されたモデルの ModelId を取得する。

### Request Data Type

```typescript
{}
```

### Response Data Type

```typescript
SceneId: string,
ModelId: string
```

### Error Types

* `CurrentSceneUnavailable`
* `CurrentModelUnavailable`

## NotifyCurrentModelChanged

GetCurrentModelId で取得できる ModelId が変更されたことの通知を有効にする。

### Request Data Type

```typescript
Enabled?: boolean
```

### Response Data Type

```typescript
{}
```

### Event Data Type

```typescript
ModelId: string
```

## RemoveItem

画面上に表示されているアイテムを指定して削除する。

### Request Data Type

```typescript
ItemId: string
```

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidItemId`

## AddItem

指定したアイテムを、指定したシーンに追加する。

### Request Data Type

```typescript
SceneId: string,
ItemPath: string
```

- ItemPath: 以下のいずれかのパス  
  - Live2D アイテムの model3.json のパス  
  - アニメーションアイテムのディレクトリのパス  
  - 静止画アイテムの画像のパス

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidSceneId`
* `InvalidItemPath`

## GetRegisteredItems

nizima LIVE に登録されているアイテムの一覧を取得する。

### Request Data Type

```typescript
{}
```

### Response Data Type

```typescript
RegisteredItems: Array<{
  Name: string,
  ItemType: "Live2D" | "Still" | "Animated",
  ItemPath: string,
  IconPath: string,
  Description: string,
  Favorite: boolean,
  EffectorWeight: number,
  Fps: number
}>
```

- ItemPath: 以下のいずれかのパス  
  - Live2D アイテムの model3.json のパス  
  - アニメーションアイテムのディレクトリのパス  
  - 静止画アイテムの画像のパス

## GetItems

表示されているアイテムの一覧を取得する。

### Request Data Type

```typescript
{}
```

### Response Data Type

```typescript
Items: Array<{
  Name: string,
  ItemType: "Live2D" | "Still" | "Animated",
  ItemId: string,
  SceneId: string,
  ItemPath: string,
  IconPath: string,
  PositionX: number,
  PositionY: number,
  Scale: number,
  Rotation: number
}>
```

- PositionX: ウィンドウの右端が 1、左端が -1  
- PositionY: ウィンドウの上端が 1、下端が -1  
- Scale: 表示倍率。デフォルトは 1  
- Rotation: 度数法で指定する角度。デフォルトは 0 で反時計回りが正

## NotifyItemsChanged

画面上のアイテムが追加・削除された際の通知を有効にする。

### Request Data Type

```typescript
Enabled?: boolean
```

### Response Data Type

```typescript
{}
```

### Event Data Type

```typescript
Items: Array<{
  Name: string,
  ItemType: "Live2D" | "Animated" | "Still",
  ItemId: string,
  SceneId: string,
  ItemPath: string,
  IconPath: string,
  PositionX: number,
  PositionY: number,
  Scale: number,
  Rotation: number
}>
```

- PositionX: ウィンドウの右端が 1、左端が -1  
- PositionY: ウィンドウの上端が 1、下端が -1  
- Scale: 表示倍率。デフォルトは 1  
- Rotation: 度数法で指定する角度。デフォルトは 0 で反時計回りが正

## MoveItem

指定したアイテムを移動する。

### Request Data Type

```typescript
ItemId: string,
Absolute?: boolean,
PositionX?: number,
PositionY?: number,
Scale?: number,
Rotation?: number,
Delay?: number,
InterpolationType?: string
```

- Absolute: 未指定の場合 false  
  - true の場合、ウィンドウ上の絶対的な座標や回転を指定して移動する  
  - false の場合、現在の位置から相対的に移動する  
- PositionX: ウィンドウの右端が 1、左端が -1  
- PositionY: ウィンドウの上端が 1、下端が -1  
- Scale: 表示倍率。デフォルトは 1  
- Rotation: 度数法で指定する角度。デフォルトは 0 で反時計回りが正  
- Delay: モデルの移動時間（秒）  
- InterpolationType: 移動時の補間方法を指定する。デフォルトは Linear  
  - https://doc.qt.io/qt-6/qeasingcurve.html#Type-enum

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidItemId`

## StopMotion

指定したモデルのモーションを停止する。  
モーション再生前の状態に戻したい場合は ResetPose と ResetCubismParameterValues を使用する。

### Request Data Type

```typescript
ModelId: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`

## StartMotion

指定したモデルのモーションを再生する。

### Request Data Type

```typescript
ModelId: string,
MotionPath: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`
* `InvalidMotionPath`

## GetMotions

指定したモデルのモーション一覧を取得する。

### Request Data Type

```typescript
ModelId: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
Motions: Array<{
  Name: string,
  MotionPath: string
}>
```

### Error Types

* `InvalidModelId`

## StopAllExpressions

指定したモデルの表情をすべてオフにする。

### Request Data Type

```typescript
ModelId: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`

## StopExpression

指定したモデルの表情をオフにする。

### Request Data Type

```typescript
ModelId: string,
ExpressionPath: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`
* `InvalidExpressionPath`

## StartExpression

指定したモデルの表情を再生する。

### Request Data Type

```typescript
ModelId: string,
ExpressionPath: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
{}
```

### Error Types

* `InvalidModelId`
* `InvalidExpressionPath`

## GetExpressions

指定したモデルの表情一覧を取得する。

### Request Data Type

```typescript
ModelId: string
```

ModelId には Live2D アイテムの ItemId も設定可能。

### Response Data Type

```typescript
Expressions: Array<{
  Name: string,
  ExpressionPath: string,
  Active: boolean
}>
```

### Error Types

* `InvalidModelId`

