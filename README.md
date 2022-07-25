# bamboobot
RoboSapiensが開発する自律移動ロボットBambooBotの操作用ROSパッケージです。

# Nodeの説明
## register_spot
現在のロボットの位置・姿勢(Pose)をyamlファイルで保存します。

### Subscribers
#### /amcl_pose
amcl_poseの値を取得するため、amcl_poseのトピックが発行されている必要があります。

#### /bamboobot/register_spot
- 型: std_msgs/String
- 指定したスポット名で、現在のamcl_poseをyaml形式でspotsディレクトリ配下に保存します。

```
rostopic pub -1 /bamboobot/register_spot std_msgs/String "room1"
```
例えば、上記のようにトピックを送信した場合、spots/room1.yaml というファイルが生成されます。

## goto_spot
register_spotで登録した位置・姿勢まで移動することができます。move_baseのノードが動作していることが前提です。

### Subscribers
#### /bamboobot/goto_spot
- 型: std_msgs/String
- register_spotで登録したスポット名を渡すと、そのスポットまで移動します。

```
rostopic pub -1 /bamboobot/goto_spot std_msgs/String "room1"
```
例えば、上記のようにトピックを送信した場合、room1の位置まで自動で移動します。

### Publishers
#### move_base_simple/goal

## sequence
jsonで指定した一連の動作をさせることが可能です。
### jsonの説明
#### actions
行動（action）パターンのarrayです。

##### name
actionの名前です。一意になるようにしてください。  
success/failureで指定するため正確に記載してください。

##### command
actionの内容です。arrayで指定をします。  
第１引数に実行する関数名、第２引数にパラメータを指定します。

| 第１引数 | 第２引数 | 備考 |
| --- | --- | --- |
| goto | スポット名 |指定したスポット名の地点まで移動する|
| set_length | 突出量[mm] |BambooShoot Actuatorを指定した長さまで突出させる。一番下まで下げるときは0を指定する。|
| sleep | 停止時間[s] |動作を指定した秒数停止させる|

##### success
actionが成功した後に次に実行するactionの名前を指定します。

##### failure
actionが失敗した後に次に実行するactionの名前を指定します。

#### header
##### first_action
一番最初に実行するactionの名前を指定します。


### Parameters
#### sequence_file
一連の動作をしていするためのjsonファイルのパスです。

### Subscribers
#### /move_base/result
#### /bsactuator/status

### Publishers
#### /bamboobot/goto_spot
#### set_length
