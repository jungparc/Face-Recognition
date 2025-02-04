## AI Service > Face Recognition > 顔認識APIガイド

* 顔認識APIを使用するのに必要なAPIを説明します。

## API共通情報
### 事前準備
- APIを使用するにはプロジェクト統合AppkeyまたはサービスAppkeyが必要です。
    - プロジェクト統合Appkeyを使用することを推奨します。
    - プロジェクト統合Appkeyは、プロジェクト設定ページのAPIセキュリティ設定で作成して使用できます。
    - サービスAppkeyは、コンソールの上部「URL & Appkey」メニューで確認できます。




### リクエスト共通情報
- APIを使用するにはセキュリティキーの認証処理が必要です。

[APIドメイン]

| ドメイン |
| --- |
| https://face-recognition.api.nhncloudservice.com |

<span id="input-image-guide"></span>
### 入力画像ガイド

* 入力画像は幅と高さがどちらも80px以上必要です。
    * 顔のサイズが60x60px以上の顔のみ認識できます。
    * 正確に認識するには、大きな画像ほど、顔のサイズも大きくするのが望ましいです。
    * 画像で顔が占める割合が大きいほど、正確に認識できます。
* 入力画像で顔の左右の角度(Yaw)と顔の上下の角度(Pitch)は、どちらも45度以上必要です。
* 入力画像の幅または高さが2048pxを超える場合、原本画像の比率に合わせて幅または高さを最大2048pxに変換して使用します。
* 画像最大サイズ：最大3MB
* サポート画像フォーマット：PNG、JPEG

<span id="common-response"></span>
### レスポンス共通情報

- すべてのAPIリクエストに「200 OK」でレスポンスします。詳細なレスポンス結果はレスポンス本文ヘッダを参照してください。

[レスポンス本文ヘッダ]

| 名前 | タイプ | 説明 |
| --- | --- | --- |
| header.isSuccessful | boolean | true：正常<br>false：エラー |
| header.resultCode | int | 0：正常<br>0より大きい：部分成功<br>負の値：エラー |
| header.resultMessage | string | "SUCCESS"：正常<br>その他：エラーメッセージを返す |

[成功レスポンス本文例]

```json
{
	"header": {
		"isSuccessful": true,
		"resultCode": 0,
		"resultMessage": "Success"
	}
}
```

[失敗レスポンス本文例]

```json
{
	"header": {
		"isSuccessful": false,
		"resultCode": -40000,
		"resultMessage": "InvalidParam"
	}
}
```
## API目次

### グループ作成

- グループを作成するAPIです。作成されたグループに[顔登録](./api-guide/#add-face)を利用して顔を登録できます。

#### リクエスト
[URI]

| メソッド | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |

[Request Body]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| groupId | string | O | "my-group" | ユーザーが登録したグループID<br>[a-z0-9-]{1,255} |


<details>
<summary>リクエスト例</summary>

```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups'  -H 'Content-Type: application/json;charset=UTF-8'  -d '{
    "groupId": "my-group"
}'
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./api-guide/#common-response)で確認可能


<details>
<summary>レスポンス本文例</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|-40000| InvalidParam | パラメータにエラーがある |
|-40010| InvalidGroupID | グループIDエラー |
|-40020| DuplicatedGroupID | 重複したグループID |
|-41000| UnauthorizedAppKey | 承認されていないappKey |
|-50000| InternalServerError | サーバーエラー |

### グループリスト

* グループリストを照会するAPIです。

#### リクエスト

[URI]

| メソッド | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups |


[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |

[URL Parameter]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| limit | int | O | 100 | 最大サイズ<br>0 < limit <= 200 |
| next-token | string  |    | "skljsdioew..."  | グループリストレスポンス本文データから返った値<br/>結果が途切れている場合は、next-tokenを利用して以降の結果を取得できる |


* 注意事項
    * 最初はnext-tokenが存在しません。
    * tokenは特定時間または特定条件で消える場合があります。
    * token発行時、limitは固定されます。
* シナリオ例

* 最初のquery


<details>
<summary>リクエスト例</summary>

```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups?limit={limit}'  -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

* グループリストレスポンス本文データに含まれたnext-tokenを利用してリクエスト


<details>
<summary>リクエスト例</summary>

```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups?limit={limit}&next-token={next-token}'  -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

* next-tokenが存在する場合、limitは変更できず、tokenが発行される時の値に自動設定される

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.groupCount | int | O | 2 | グループ数 |
| data.groups[].groupId | string | O | "group-id" | ユーザーが登録したグループID |
| data.groups[].modelVersion | string | O | "v1.0" | 顔検出モデルバージョン情報 |
| data.nextToken | string | O | "dlkj-210jwoivndslko9d..." | ページングで使用するトークン<br>結果が途切れている場合は、next-tokenを利用して以降の結果を取得できる |

<details>
<summary>レスポンス本文例</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "groupCount": 2,
        "groups": [{
            "groupId": "group-id",
            "modelVersion": "v1.0"
        }, {
            "groupId": "my-group",
            "modelVersion": "v1.0"
        }],
        "nextToken":"dlkj-210jwoivndslko9d..."
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|-40000| InvalidParam | パラメータにエラーがある |
|-40040| InvalidTokenError | 無効なトークンを使用 |
|-41000| UnauthorizedAppKey | 承認されていないappKey |
|-50000| InternalServerError | サーバーエラー |

### グループ詳細情報

* グループID、モデルバージョン、グループに登録した顔の数など、特定グループの詳細情報を照会するAPIです。

#### リクエスト
[URI]

| メソッド | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| group-id | ユーザーが登録したグループID<br>[a-z0-9-]{1,255} |

<details>
<summary>リクエスト例</summary>

```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}'  -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須かどうか | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.groupCount | int | O | 1 | グループ数 |
| data.groups[].groupId | string | O | "group-id" | ユーザーが登録したグループID |
| data.groups[].modelVersion | string | O | "v1.0" | 顔検出モデルバージョン情報 |
| data.groups[].createTime | string | O | "2020-11-04T12:36:24" | グループを作成した時間 |
| data.groups[].faceCount | int |   | 365 | グループに登録した顔の数 |

<details>
<summary>レスポンス本文例</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "groupCount": 1,
        "groups": [{
            "groupId": "group-id",
            "modelVersion": "v1.0",
            "createTime": "2020-09-29T14:34:12",
            "faceCount": 365
        }]
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|-40000| InvalidParam | パラメータにエラーがある |
|-40030| NotFoundGroupError | グループIDが見つからない |
|-41000| UnauthorizedAppKey | 承認されていないappKey |
|-50000| InternalServerError | サーバーエラー |

### グループ削除

* 特定グループと、そのグループに属す顔情報を完全に削除するAPIです。

#### リクエスト

[URI]

| メソッド | URI |
| --- | --- |
| DELETE | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| group-id | ユーザーが登録したグループID<br>[a-z0-9-]{1,255} |


<details>
<summary>リクエスト例</summary>

```
$ curl -X DELETE '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}'  -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>



#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./api-guide/#common-response)で確認可能

<details>
<summary>レスポンス本文例</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|-40000| InvalidParam | パラメータにエラーがある |
|-40030| NotFoundGroupError | グループIDが見つからない |
|-41000| UnauthorizedAppKey | 承認されていないappKey |
|-50000| InternalServerError | サーバーエラー |

### 顔検出

* 入力画像から顔を検出するAPIです。
* 検出した顔から顔、目、鼻、口などの位置情報と信頼度の値を返します。
* 入力画像から顔が大きい順に最大20個の顔を検出します。
* 入力画像はbase64でエンコードされた画像バイトまたは、画像のURLで伝達できます。
* 入力画像の詳細は「[入力画像ガイド](./api-guide/#input-image-guide)」を参照してください。

#### リクエスト
[URI]
 
| メソッド | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/detect |

[Path Variable]
 
| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |

[Request Body]

| 名前 | タイプ | 必須かどうか | 例 | 説明 |
| --- | --- | --- | --- | --- |
| image.url | string |  | "https://..." | 画像のURL<br>image.url、image.bytesのどちらか1つが必要 |
| image.bytes | blob |  | "/0j3Ohdk==..." | base64でエンコードされた画像バイト<br>image.url、image.bytesのどちらか1つが必要 |

* image.url、image.bytesのどちらか1つが必要です。


<details>
<summary>リクエスト例</summary>
 
```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/detect'  -H 'Content-Type: application/json;charset=UTF-8'  -d '{
    "image": {
        "url":"https://..."
    }
}'
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.faceDetailCount | int | O | 1 | 検出した顔の数 |
| data.faceDetails[].bbox | object | O | - | 画像内から検出した顔の境界ボックス(bounding box)情報 |
| data.faceDetails[].bbox.x0 | float | O | 0.123 | 画像内で検出した顔boxのx0座標 |
| data.faceDetails[].bbox.y0 | float | O | 0.123 | 画像内で検出した顔boxのy0座標 |
| data.faceDetails[].bbox.x1 | float | O | 0.123 | 画像内で検出した顔boxのx1座標 |
| data.faceDetails[].bbox.y1 | float | O | 0.123 | 画像内で検出した顔boxのy1座標 |
| data.faceDetails[].landmarks | array | O | - | 顔の特徴 |
| data.faceDetails[].landmarks[].type | string | O | "leftEye" | 有効な値リスト:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLib" |
| data.faceDetails[].landmarks[].y | float | O | 0.362 | 顔の特徴のy座標 |
| data.faceDetails[].landmarks[].x | float | O | 0.362 | 顔の特徴のx座標 |
| data.faceDetails[].orientation | object | O | 0.362 | 顔の角度 |
| data.faceDetails[].orientation.x | float | O | 15.303436 | 顔の左右角度(Yaw) |
| data.faceDetails[].orientation.y | float | O | -9.222179 | 顔の上下角度(Pitch) |
| data.faceDetails[].orientation.z | float | O | -7.97249 | 水平面に対する顔の角度(Roll) |
| data.faceDetails[].confidence | float | O | 99.9123 | 顔の認識信頼度 |


<details>
<summary>レスポンス本文例</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "faceDetailCount": 1,
        "faceDetails": [{
            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "landmarks": [{
                    "type": "leftEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "rightEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "nose",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "leftLip",
                    "x": 0.415,
                    "y": 0.513
                }, {
                    "type": "rightLip",
                    "x": 0.415,
                    "y": 0.513
                }
            ],
            "orientation": {
                "x": 15.303436,
                "y": -9.222179,
                "z": -7.97249
            },
            "confidence": 99.8945155187
        }]
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|-40000| InvalidParam | パラメータにエラーがある |
|-41000| UnauthorizedAppKey | 承認されていないappKey |
|-45020| ImageTooLargeException | 画像サイズ超過 |
|-45040| InvalidImageFormatException | サポートしていない画像フォーマット |
|-45050| InvalidImageURLException | 無効な画像URL |
|-45060| ImageTimeoutError | 画像ダウンロード時間超過 |
|-50000| InternalServerError | サーバーエラー |

<span id="add-face"></span>
### 顔登録

* 入力画像から検出した顔を特定グループに登録するAPIです。
* 入力画像から顔のboxを検出し、検出した顔boxから顔の特徴をベクトルで抽出します。この時、入力画像と入力画像から検出した顔画像は保存しません。
* 抽出したベクトルデータは暗号化してデータベースに保存します。
* 保存したベクトルデータは、[フェイスIDで顔検索](./api-guide/#search-by-face-id)、[画像で顔検索](./api-guide/#search-by-image) APIで特徴ベクトルとして使用します。
* 入力画像はbase64でエンコードされた画像バイトまたは、画像のURLで伝達できます。
* 入力画像の詳細は「[入力画像ガイド](./api-guide/#input-image-guide)」を参照してください。
* "imageId"は入力画像に付与される値で、"externalImageId"はユーザーが直接付与できる値です。ユーザーは"imageId"と"externalImageId"を利用して画像またはフェイスIDにラベリングしてインデックスのように活用できます。
* "imageId"と"externalImageId"は[グループ内顔リスト](./api-guide/#face-list-in-a-group)と[フェイスIDで顔検索](./api-guide/#search-by-face-id)、[画像で顔検索](./api-guide/#search-by-image) APIのレスポンスで返されます。
* 1つのグループに登録できる顔の数は最大10万個です。
 
#### リクエスト

[URI]
 
| メソッド | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |
 
[Path Variable]
 
| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| group-id | ユーザーが登録したグループID<br>[a-z0-9-]{1,255} |
 

[Request Body]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| image.url | string |  | "https://..." | 画像のURL<br>image.url、image.bytesのどちらか1つが必要 |
| image.bytes | blob |  | "/0j3Ohdk==..." | base64でエンコードされた画像バイト<br>image.url、image.bytesのどちらか1つが必要 |
| externalImageId | string |  | "image01.jsp" | ユーザーが画像またはフェイスIDにラベリングするために伝達する値<br>[a-zA-Z0-9\_.-:]+<br>1 <limit <= 255 |
| limit | int | O | 3 | 入力画像から認識した顔のうち、サイズが大きい順にソートしてグループに登録する最大顔数<br>0< limit <= 20 |

* image.url、image.bytesのどちらか1つが必要です。

 

<details>
<summary>リクエスト例</summary>
 
```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}'  -H 'Content-Type: application/json;charset=UTF-8'  -d '{
    "image": {
        "url": "https://..."
    },
    "externalImageId": "image01.jpg",
    "limit": 3
}'
```

</details>


#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | 顔検出モデル情報 |
| data.addedFaceCount | int | O | 1 | 登録した顔の数 |
| data.addedFaces[].bbox | object | O | - | 画像内から検出した顔の境界ボックス(bounding box)情報 |
| data.addedFaces[].bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.addedFaces[].bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.addedFaces[].bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.addedFaces[].bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.addedFaces[].faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | フェイスID |
| data.addedFaces[].imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 画像ID<br>1つの画像IDに複数のフェイスIDが存在する場合がある |
| data.addedFaces[].externalImageId | string |  | "image01.jpg" | リクエストから伝達された値 |
| data.addedFaces[].confidence | float | O | 99.9123 | 顔の認識信頼度 |
| data.addedFaceDetails[].bbox | object | O | - | 画像内から検出した顔の境界ボックス(bounding box)情報 |
| data.addedFaceDetails[].bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.addedFaceDetails[].bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.addedFaceDetails[].bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.addedFaceDetails[].bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.addedFaceDetails[].landmarks | array | O | - | 顔の特徴 |
| data.addedFaceDetails[].landmarks[].type | string | O | "leftEye" | 有効な値リスト:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLib" |
| data.addedFaceDetails[].landmarks[].y | float | O | 0.362 | 顔の特徴のy座標 |
| data.addedFaceDetails[].landmarks[].x | float | O | 0.362 | 顔の特徴のx座標 |
| data.addedFaceDetails[].orientation | object | O | 0.362 | 顔の角度 |
| data.addedFaceDetails[].orientation.x | float | O | 15.303436 | 顔の左右の角度(Yaw) |
| data.addedFaceDetails[].orientation.y | float | O | -9.222179 | 顔の上下の角度(Pitch) |
| data.addedFaceDetails[].orientation.z | float | O | -7.97249 | 水平面に対する顔の角度(Roll) |
| data.addedFaceDetails[].confidence | float | O | 99.9123 | 顔の認識信頼度 |
| data.notAddedFaceCount | int | O | 1 | 登録していない顔の数 |
| data.notAddedFaces[].bbox | object | O | - | 画像内から検出した顔の境界ボックス(bounding box)情報 |
| data.notAddedFaces[].bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.notAddedFaces[].bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.notAddedFaces[].bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.notAddedFaces[].bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.notAddedFaces[].landmarks | array | O | - | 顔の特徴 |
| data.notAddedFaces[].landmarks[].type | string | O | "leftEye" | 有効な値リスト:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLib" |
| data.notAddedFaces[].landmarks[].x | float | O | 0.362 | 顔の特徴のx座標 |
| data.notAddedFaces[].landmarks[].y | float | O | 0.362 | 顔の特徴のy座標 |
| data.notAddedFaces[].orientation | object | O | 0.362 | 顔の角度 |
| data.notAddedFaces[].orientation.x | float | O | 15.303436 | 顔の左右角度(Yaw) |
| data.notAddedFaces[].orientation.y | float | O | -9.222179 | 顔の上下角度(Pitch) |
| data.notAddedFaces[].orientation.z | float | O | -7.97249 | 水平面に対する顔の角度(Roll) |
| data.notAddedFaces[].confidence | float | O | 99.9123 | 顔の認識信頼度 |

* data.addedFacesDetailsはdata.addedFacesの詳細情報であり、重複または保存されない情報です。

<details>
<summary>レスポンス本文例</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "modelVersion": "v1.0",
        "addedFaceCount": 1,
        "addedFaces": [{

            "faceId": "87db50d4-f2c6-b8ea-05ed-9f201309fd92",
            "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
            "externalImageId": "image01.jpg",
            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "confidence": 99.8945155187

        }],
        "addedFaceDetails": [{

            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "landmarks": [{
                    "type": "leftEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "rightEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "nose",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "leftLip",
                    "x": 0.415,
                    "y": 0.513
                }, {
                    "type": "rightLip",
                    "x": 0.415,
                    "y": 0.513
                }
            ],
            "orientation": {
                "x": 15.303436,
                "y": -9.222179,
                "z": -7.97249
            },
            "confidence": 99.8945155187

        }],

        "notAddedFaceCount": 1,
        "notAddedFaces": [{

            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "landmarks": [{
                    "type": "leftEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "rightEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "nose",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "leftLip",
                    "x": 0.415,
                    "y": 0.513
                }, {
                    "type": "rightLip",
                    "x": 0.415,
                    "y": 0.513
                }
            ],
            "orientation": {
                "x": 15.303436,
                "y": -9.222179,
                "z": -7.97249
            },
            "confidence": 99.8945155187

        }]

    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|40070| ServiceQuotaExceededPartialSuccessException | 1つのグループに登録できる最大顔数を超過しました。一部のみ登録成功 |
|-40000| InvalidParam | パラメータにエラーがある |
|-40030| NotFoundGroupError | グループIDが見つからない |
|-40070| ServiceQuotaExceededException | 1つのグループに登録可能な最大顔数を超過 |
|-41000| UnauthorizedAppKey | 承認されていないappKey |
|-45020| ImageTooLargeException | 画像サイズ超過 |
|-45040| InvalidImageFormatException | サポートしていない画像フォーマット |
|-45050| InvalidImageURLException | 無効な画像URL |
|-45060| ImageTimeoutError | 画像ダウンロード時間超過 |
|-50000| InternalServerError | サーバーエラー |

### 顔の削除

* グループに登録した特定の顔を削除するAPIです。

#### リクエスト
[URI]
 
| メソッド | URI |
| --- | --- |
| DELETE | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id} |
 
[Path Variable]
 
| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| group-id | ユーザーが登録したグループID<br>[a-z0-9-]{1,255} |
| face-id | 登録されたフェイスID |
 

<details>
<summary>リクエスト例</summary>

```
$ curl -X DELETE '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}'  -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./api-guide/#common-response)で確認可能


<details>
<summary>レスポンス本文例</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|-40000| InvalidParam | パラメータにエラーがある |
|-40030| NotFoundGroupError | グループIDが見つからない |
|-40050| NotFoundFaceIDError | フェイスIDが見つからない |
|-41000| UnauthorizedAppKey | 承認されていないappKey |
|-50000| InternalServerError | サーバーエラー |

<span id="face-list-in-a-group"></span>
### グループ内顔リスト

* 特定グループに登録した顔情報リストを照会するAPIです。
* 登録が新しい順で顔情報配列を返します。

#### リクエスト
[URI]
 
| メソッド | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces |
 
[Path Variable]
 
| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| group-id | ユーザーが登録したグループID<br>[a-z0-9-]{1,255} |
 
[URL Parameter]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| limit | int  | O  | 100  | 最大サイズ<br>0 < limit <= 200 |
| next-token | string  |    | "skljsdioew..."  | "グループリストレスポンス本文データ"で返した値<br/>結果が途切れている場合は、next-tokenを利用して以降の結果を取得できる |
 

* 注意事項
    * 最初はnext-tokenが存在しません。
    * tokenは特定時間または特定条件で消える場合があります。
    * token発行時、limitは固定されます。
* シナリオ例

* 最初のquery

 
<details>
<summary>リクエスト例</summary>

```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces?limit={limit}'  -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>


* グループリストレスポンス本文データに含まれたnext-tokenを利用してリクエスト
 

<details>
<summary>リクエスト例</summary>

```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces?limit={limit}&next-token={next-token}'  -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>


* next-tokenが存在する場合、limitは変更できず、tokenが発行される時の値に自動設定される


#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | 顔検出モデル情報 |
| data.faceCount | int | O | 2 | 検出した顔の数 |
| data.faces[].bbox | object | O | - | 顔の登録時に使用した画像で顔の境界ボックス(bounding box)情報 |
| data.faces[].bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.faces[].bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.faces[].bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.faces[].bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.faces[].confidence | float | O | 99.9123 | 顔の登録時に使用した画像で顔の認識信頼度 |
| data.faces[].faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | フェイスID |
| data.faces[].imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 画像ID<br>1つの画像IDに複数のフェイスIDが存在することがある |
| data.faces[].externalImageId | string |  | "image01.jpg" | ユーザーが画像に登録した値 |
| data.nextToken | string | O | "dlkj-210jwoivndslko9d..." | ページングで使用するトークン<br>結果が途切れている場合は、next-tokenを利用して以降の結果を取得できる |


<details>
<summary>レスポンス本文例</summary>


```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "modelVersion": "v1.0",
        "faceCount": 2,
        "faces": [{
                "faceId": "17db50d4-f2c6-b8ea-05ed-9f201309fd92",
                "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                "externalImageId": "image01.jpg",
                "bbox": {
                    "x0": 0.36,
                    "y0": 0.21,
                    "x1": 0.612,
                    "y1": 0.715
                },
                "confidence": 99.8945155187
            }, 
            {
                "faceId": "87db50d4-f2c6-b8ea-05ed-9f201309fd92",
                "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                "externalImageId": "image01.jpg",
                "bbox": {
                    "x0": 0.36,
                    "y0": 0.21,
                    "x1": 0.612,
                    "y1": 0.715
                },
                "confidence": 99.8945155187
            }],
        "nextToken":"dlkj-210jwoivndslko9d..."
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|-40000| InvalidParam | パラメータにエラーがある |
|-40030| NotFoundGroupError | グループIDが見つからない |
|-40040| InvalidTokenError | 無効なトークンを使用 |
|-41000| UnauthorizedAppKey | 承認されていないappKey |
|-50000| InternalServerError | サーバーエラー |

<span id="search-by-face-id"></span>
### フェイスIDで顔検索

* フェイスIDで特定グループから顔を検索するAPIです。
* 類似度が最も高い順序で、一致する顔情報の配列を返します。

#### リクエスト
[URI]
 
| メソッド | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id} |
 
[Path Variable]
 
| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| group-id | ユーザーが登録したグループID<br>[a-z0-9-]{1,255} |
| face-id | 比較するフェイスID |
 
[URL Parameter]
 
| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| limit | int  | O  | 100  | 探す最大値。<br>0 < limit <= 4096 |
| threshold | int  | O  | 90  | マッチングするかどうかを判断する類似度の基準値<br>0 < threshold <= 100 |
 

<details>
<summary>リクエスト例</summary>
 
```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}?limit={limit}&threshold={threshold}'  -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>

#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.matchFaceCount | int | O | 2 | 入力画像から検出した最も大きい顔と一致する顔の数 |
| data.matchFaces[].face.bbox | object | O | - | 顔の登録時に使用した画像で顔の境界ボックス(bounding box)情報 |
| data.matchFaces[].face.bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.matchFaces[].face.bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.matchFaces[].face.bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.matchFaces[].face.bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.matchFaces[].face.confidence | float | O | 99.9123 | 顔の登録時に使用した画像で顔の認識信頼度 |
| data.matchFaces[].face.faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | フェイスID |
| data.matchFaces[].face.imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 画像ID<br>1つの画像IDに複数のフェイスIDが存在することがある |
| data.matchFaces[].face.externalImageId | string |  | "image01.jpg" | ユーザーが画像に登録した値 |
| data.matchFaces[].similarity | float | O | 98.156 | 0～100の値を持つ類似度 |


<details>
<summary>レスポンス本文例</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "matchFaceCount": 2,
        "matchFaces": [{
                "face": {
                    "faceId": "87db50d4-f2c6-b8ea-05ed-9f201309fd92",
                    "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                    "externalImageId": "image01.jpg",
                    "bbox": {
                        "x0": 0.36,
                        "y0": 0.21,
                        "x1": 0.612,
                        "y1": 0.715
                    },
                    "confidence": 99.8945155187
                },
                "similarity": 99.8945155187
            },
            {
                "face": {
                    "faceId": "17db50d4-f2c6-b8ea-05ed-9f201309fd92",
                    "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                    "externalImageId": "image01.jpg",
                    "bbox": {
                        "x0": 0.36,
                        "y0": 0.21,
                        "x1": 0.612,
                        "y1": 0.715
                    },
                    "confidence": 99.8945155187
                },
                "similarity": 79.8945155187
            }
        ]
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|-40000| InvalidParam | パラメータにエラーがある |
|-40030| NotFoundGroupError | グループIDが見つからない |
|-40050| NotFoundFaceIDError | フェイスIDが見つからない |
|-41000| UnauthorizedAppKey | 承認されていないappKey |
|-50000| InternalServerError | サーバーエラー |

<span id="search-by-image"></span>
### 画像で顔検索

* 入力画像から検出した最も大きい顔を使用して特定グループに属す顔と一致するかどうかを比較します。
* 入力画像はbase64でエンコードされた画像バイトまたは、画像のURLで伝達できます。
* 入力画像の詳細は「[入力画像ガイド](./api-guide/#input-image-guide)」を参照してください。
* 類似度が最も高い順序で、一致する顔情報の配列を返します。

#### リクエスト
[URI]
 
| メソッド | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/search |
 
[Path Variable]
 
| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| group-id | ユーザーが登録したgroup id<br>[a-z0-9-]{1,255} |
 
[URL Parameter]
 
| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| limit | int  | O  | 100  | 探す最大値<br>0 < limit <= 4096 |
| threshold | int  | O  | 90  | マッチングするかどうかを判断する類似度の基準値<br>0 < threshold <= 100 |

[Request Body]
 
| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| image.url | string |  | "https://..." | 画像のURL<br>image.url、image.bytesのどちらか1つが必要 |
| image.bytes | blob |  | "/0j3Ohdk==..." | base64でエンコードされた画像バイト<br>image.url、image.bytesのどちらか1つが必要 |

* image.url、image.bytesのどちらか1つが必要です。


<details>
<summary>リクエスト例</summary>
 
```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/search?limit={limit}&threshold={threshold}'  -H 'Content-Type: application/json;charset=UTF-8'  -d '{
    "image": {
        "url": "https://..."
    }
}'
```

</details>


#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.matchFaceCount | int | O | 2 | 入力画像から検出した最も大きい顔と一致する顔の数 |
| data.matchFaces[].face.bbox | object | O | - | 顔の登録時に使用した画像で顔の境界ボックス(bounding box)情報 |
| data.matchFaces[].face.bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.matchFaces[].face.bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.matchFaces[].face.bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.matchFaces[].face.bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.matchFaces[].face.confidence | float | O | 99.9123 | 顔の登録時に使用した画像で顔の認識信頼度 |
| data.matchFaces[].face.faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | フェイスID |
| data.matchFaces[].face.imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 画像ID<br>1つの画像IDに複数のフェイスIDが存在することがある |
| data.matchFaces[].face.externalImageId | string |  | "image01.jpg" | ユーザーが画像に登録した値 |
| data.matchFaces[].similarity | float | O | 98.156 | 0～100の値を持つ類似度 |
| data.sourceFace.bbox | object | O | - | 入力画像内から検出した最も大きい顔の境界ボックス(bounding box)情報 |
| data.sourceFace.bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.sourceFace.bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.sourceFace.bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.sourceFace.bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.sourceFace.confidence | float | O | 99.9123 | 入力画像内から検出した最も大きい顔の認識信頼度 |


<details>
<summary>レスポンス本文例</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "modelVersion": "v1.0",
        "matchFaceCount": 2,
        "matchFaces": [{
                "face": {
                    "faceId": "87db50d4-f2c6-b8ea-05ed-9f201309fd92",
                    "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                    "externalImageId": "image01.jpg",
                    "bbox": {
                        "x0": 0.36,
                        "y0": 0.21,
                        "x1": 0.612,
                        "y1": 0.715
                    },
                    "confidence": 99.8945155187
                },
                "similarity": 99.8945155187
            },
            {
                "face": {
                    "faceId": "17db50d4-f2c6-b8ea-05ed-9f201309fd92",
                    "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                    "externalImageId": "image01.jpg",
                    "bbox": {
                        "x0": 0.36,
                        "y0": 0.21,
                        "x1": 0.612,
                        "y1": 0.715
                    },
                    "confidence": 99.8945155187
                },
                "similarity": 79.8945155187
            }
        ],
        "sourceFace": {
            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "confidence": 99.8945155187
        }
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|-40000| InvalidParam | パラメータにエラーがある |
|-40030| NotFoundGroupError | グループIDが見つからない |
|-41000| UnauthorizedAppKey | 承認されていないappKey |
|-45020| ImageTooLargeException | 画像サイズ超過 |
|-45040| InvalidImageFormatException | サポートしていない画像フォーマット |
|-45050| InvalidImageURLException | 無効な画像URL |
|-45060| ImageTimeoutError | 画像ダウンロード時間超過 |
|-50000| InternalServerError | サーバーエラー |

### 顔比較

* 基準画像(sourceImage)と比較画像(targetImage)から検出した顔がどれくらい類似しているかを比較します。
* 基準画像から検出した顔のうち、最も大きい顔(基準顔)のみ使用します。
* 入力画像はbase64でエンコードされた画像バイトまたは、画像のURLで伝達できます。
* 入力画像の詳細は「[入力画像ガイド](./api-guide/#input-image-guide)」を参照してください。
* 類似度が最も高い順序で、一致する顔情報の配列を返します。

#### リクエスト
[URI]
 
| メソッド | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/compare |
 
[Path Variable]
 
| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| threshold | マッチングするかどうかを判断する類似度の基準値<br>0 < threshold <= 100 |
 
[Request Body]
 
| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| sourceImage | object | O | - | 顔を比較する時、基準になる画像<br/>(=referenceImage) |
| sourceImage.url | string |  | "https://..." | 画像のURL<br>image.url、image.bytesのどちらか1つが必要 |
| sourceImage.bytes | blob |  | "/0j3Ohdk==..." | base64でエンコードされた画像バイト<br>image.url, image.bytesのどちらか1つが必要 |
| targetImage | object | O | - | 比較対象になる顔が含まれる画像<br/>(=comparisonImage) |
| targetImage.url | string |  | "https://..." | 画像のURL<br>image.url、image.bytesのどちらか1つが必要 |
| targetImage.bytes | blob |  | "/0j3Ohdk==..." | base64でエンコードされた画像バイト<br>image.url, image.bytesのどちらか1つが必要 |



<details>
<summary>リクエスト例</summary>
 
```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/compare?threshold={threshold}'  -H 'Content-Type: application/json;charset=UTF-8'  -d '{
    "sourceImage": {
        "url": "https://..."
    },
    "targetImage": {
        "url": "https://..."
    }
}'
```

</details>


#### レスポンス

* [レスポンス本文ヘッダ説明省略]
    * [レスポンス共通情報](./api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | 顔検出モデル情報 |
| data.matchedFaceDetailCount | int | O | 1 | マッチングした顔の数 |
| data.matchedFaceDetails[].faceDetail.bbox | object | O | - | 画像内から検出した顔の境界ボックス(bounding box)情報 |
| data.matchedFaceDetails[].faceDetail.bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.matchedFaceDetails[].faceDetail.bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.matchedFaceDetails[].faceDetail.bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.matchedFaceDetails[].faceDetail.bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.matchedFaceDetails[].faceDetail.landmarks | array | O | - | 顔の特徴 |
| data.matchedFaceDetails[].faceDetail.landmarks[].type | string | O | "leftEye" | 有効な値リスト:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLib" |
| data.matchedFaceDetails[].faceDetail.landmarks[].x | float | O | 0.362 | 顔の特徴のx座標 |
| data.matchedFaceDetails[].faceDetail.landmarks[].y | float | O | 0.362 | 顔の特徴のy座標 |
| data.matchedFaceDetails[].faceDetail.orientation | object | O | 0.362 | 顔の角度 |
| data.matchedFaceDetails[].faceDetail.orientation.x | float | O | 15.303436 | 顔の左右角度(Yaw) |
| data.matchedFaceDetails[].faceDetail.orientation.y | float | O | -9.222179 | 顔の上下角度(Pitch) |
| data.matchedFaceDetails[].faceDetail.orientation.z | float | O | -7.97249 | 水平面に対する顔の角度(Roll) |
| data.matchedFaceDetails[].faceDetail.confidence | float | O | 99.9123 | 顔の認識信頼度 |
| data.matchedFaceDetails[].similarity | float | O | 98.156 | 0～100の値を持つ類似度 |
| data.unmatchedFaceDetailCount | int | O | 1 | マッチングしていない顔の数 |
| data.unmatchedFaceDetails[].faceDetail.bbox | object | O | - | 画像内から検出した顔の境界ボックス(bounding box)情報 |
| data.unmatchedFaceDetails[].faceDetail.bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.unmatchedFaceDetails[].faceDetail.bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.unmatchedFaceDetails[].faceDetail.bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.unmatchedFaceDetails[].faceDetail.bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.unmatchedFaceDetails[].faceDetail.landmarks | array | O | - | 顔の特徴 |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].type | string | O | "leftEye" | 有効な値リスト:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLib" |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].x | float | O | 0.362 | 顔の特徴のx座標 |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].y | float | O | 0.362 | 顔の特徴のy座標 |
| data.unmatchedFaceDetails[].faceDetail.orientation | object | O | 0.362 | 顔の角度 |
| data.unmatchedFaceDetails[].faceDetail.orientation.x | float | O | 15.303436 | 顔の左右角度(Yaw) |
| data.unmatchedFaceDetails[].faceDetail.orientation.y | float | O | -9.222179 | 顔の上下角度(Pitch) |
| data.unmatchedFaceDetails[].faceDetail.orientation.z | float | O | -7.97249 | 水平面に対する顔の角度(Roll) |
| data.unmatchedFaceDetails[].faceDetail.confidence | float | O | 99.9123 | 顔の認識信頼度 |
| data.unmatchedFaceDetails[].similarity | float | O | 98.156 | 0～100の値を持つ類似度 |
| data.sourceFace.bbox | object | O | - | 入力画像内から検出した最も大きい顔の境界ボックス(bounding box)情報 |
| data.sourceFace.bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.sourceFace.bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.sourceFace.bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.sourceFace.bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.sourceFace.confidence | float | O | 99.9123 | 入力画像内から検出した最も大きい顔の認識信頼度 |


<details>
<summary>レスポンス本文例</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "modelVersion": "v1.0",
        "matchedFaceDetailCount": 2,
        "matchedFaceDetails": [{
            "faceDetail": {
                "bbox": {
                    "x0": 0.36,
                    "y0": 0.21,
                    "x1": 0.612,
                    "y1": 0.715
                },
                "landmarks": [{
                        "type": "leftEye",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "rightEye",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "nose",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "leftLip",
                        "x": 0.415,
                        "y": 0.513
                    }, {
                        "type": "rightLip",
                        "x": 0.415,
                        "y": 0.513
                    }
                ],
                "orientation": {
                    "x": 15.303436,
                    "y": -9.222179,
                    "z": -7.97249
                },
                "confidence": 99.8945155187
            },
            "similarity": 90.654
        }, {
            "faceDetail": {
                "bbox": {
                    "x0": 0.36,
                    "y0": 0.21,
                    "x1": 0.612,
                    "y1": 0.715
                },
                "landmarks": [{
                        "type": "leftEye",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "rightEye",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "nose",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "leftLip",
                        "x": 0.415,
                        "y": 0.513
                    }, {
                        "type": "rightLip",
                        "x": 0.415,
                        "y": 0.513
                    }
                ],
                "orientation": {
                    "x": 15.303436,
                    "y": -9.222179,
                    "z": -7.97249
                },
                "confidence": 99.8945155187
            },
            "similarity": 90.654
        }],
        "unmatchedFaceDetailCount": 1,
        "unmatchedFaceDetails": [{
                "faceDetail": {
                    "bbox": {
                        "x0": 0.36,
                        "y0": 0.21,
                        "x1": 0.612,
                        "y1": 0.715
                    },
                    "landmarks": [{
                            "type": "leftEye",
                            "x": 0.415,
                            "y": 0.513
                        },
                        {
                            "type": "rightEye",
                            "x": 0.415,
                            "y": 0.513
                        },
                        {
                            "type": "nose",
                            "x": 0.415,
                            "y": 0.513
                        },
                        {
                            "type": "leftLip",
                            "x": 0.415,
                            "y": 0.513
                        }, {
                            "type": "rightLip",
                            "x": 0.415,
                            "y": 0.513
                        }
                    ],
                    "orientation": {
                        "x": 15.303436,
                        "y": -9.222179,
                        "z": -7.97249
                    },
                    "confidence": 99.8945155187
                },
                "similarity": 60.654
            }

        ],
        "sourceFace": {
            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "confidence": 99.8945155187
        }
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|-40000| InvalidParam | パラメータにエラーがある |
|-41000| UnauthorizedAppKey | 承認されていないappKey |
|-45020| ImageImageTooLargeException:{Source/Target} | {Source/Target} Image:画像サイズ超過 |
|-45040| ImageInvalidImageFormatException:{Source/Target} | {Source/Target} image:サポートしない画像フォーマット |
|-45050| ImageInvalidImageURLException:{Source/Target} | {Source/Target} image：無効な画像URL |
|-45060| ImageImageTimeoutError:{Source/Target} | {Source/Target} image:画像ダウンロード時間超過 |
|-50000| InternalServerError | サーバーエラー |


<span id="verify"></span>
### 顔検証
* 事前に登録された特定の顔のフェイスIDと、入力画像から検出した顔を比較して類似度値を返す機能です。
* [顔登録](./api-guide/#add-face)を利用して顔を登録できます。
* 入力画像から検出した顔のうち、最も大きい顔のみを使用します。  
* 入力画像はbase64でエンコードされた画像バイトで伝達するか、画像URLで伝達できます。
* 入力画像についての詳細は、[入力画像ガイド](./api-guide/#input-image-guide)を参照してください。

#### リクエスト
[URI]

| メソッド | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/verify/groups/{group-id}/faces/{face-id} |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合AppkeyまたはサービスAppkey |
| group-id | ユーザーが登録したグループID<br>[a-z0-9-]{1,255} |
| face-id | 登録されたフェイスID |


[Request Body]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| compareImage.url | string |  | "https://..." | 画像のURL<br>compareImage.url、compareImage.bytesのいずれかが必要 |
| compareImage.bytes | blob |  | "/0j3Ohdk==..." | base64でエンコードされた画像バイト<br>compareImage.url、compareImage.bytesのいずれかが必要 |

* compareImage.url, compareImage.bytesのいずれかが必要です。


<details>
<summary>リクエスト例</summary>

```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/verify/groups/{group-id}/faces/{face-id}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "compareImage": {
        "url": "https://..."
    }
}'
```

</details>


#### レスポンス

* [レスポンス本文ヘッダ説明省略]
  * [レスポンス共通情報](./api-guide/#common-response)で確認可能

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.similarity | float | O | 98.156 | 0～100の値を持つ類似度 |
| data.face | object | O | - | 顔登録APIを利用して登録した顔 |
| data.face.bbox | object | O | - | 画像内から検出した顔の境界ボックス(bounding box)情報 |
| data.face.bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.face.bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.face.bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.face.bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.face.confidence | float | O | 99.9123 | 顔の認識信頼度 |
| data.face.faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | フェイスID |
| data.face.imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 画像ID<br>1つの画像IDに複数のフェイスIDが存在することがある |
| data.face.externalImageId | string |  | "image01.jpg" | ユーザーが画像に登録した値 |
| data.sourceFace | object | O | - | Request Bodyに伝達した画像 |
| data.sourceFace.bbox | object | O | - | 画像内から検出した顔の境界ボックス(bounding box)情報 |
| data.sourceFace.bbox.x0 | float | O | 0.123 | 画像内から検出した顔boxのx0座標 |
| data.sourceFace.bbox.y0 | float | O | 0.123 | 画像内から検出した顔boxのy0座標 |
| data.sourceFace.bbox.x1 | float | O | 0.123 | 画像内から検出した顔boxのx1座標 |
| data.sourceFace.bbox.y1 | float | O | 0.123 | 画像内から検出した顔boxのy1座標 |
| data.sourceFace.confidence | float | O | 99.9123 | 顔の認識信頼度 |



<details>
<summary>レスポンス本文例</summary>

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": "SUCCESS"
  },
  "data": {
    "similarity": 87.074165,
    "face": {
      "bbox": {
        "x0": 0.828,
        "y0": 0.07829181494661921,
        "x1": 0.886,
        "y1": 0.2117437722419929
      },
      "confidence": 0.998737,
      "faceId": "bd21a5d1-64bc-4723-a041-71d720fe56d8",
      "imageId": "165b63c9-9564-4a65-b3f6-7bb64d4fe9f9",
      "externalImageId": "user-defined-external-image-id"
    },
    "sourceFace": {
      "bbox": {
        "x0": 0.7567164179104477,
        "y0": 0.13097713097713098,
        "x1": 0.8671641791044776,
        "y1": 0.33264033264033266
      },
      "confidence": 0.999286
    }
  }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | 説明 |
| --- | --- | --- |
|-40000| InvalidParam | パラメータにエラーがある |
|-40030| NotFoundGroupError | グループIDが見つからない |
|-40050| NotFoundFaceIDError | フェイスIDが見つからない |
|-41000| UnauthorizedAppKey | 承認されていないAppkey |
|-45020| ImageTooLargeException | 画像サイズ超過 |
|-45040| InvalidImageFormatException | サポートしていない画像フォーマット |
|-45050| InvalidImageURLException | 無効な画像URL |
|-45060| ImageTimeoutError | 画像ダウンロード時間超過 |
|-50000| InternalServerError | サーバーエラー |
