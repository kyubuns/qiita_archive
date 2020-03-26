Nested Prefabの公式対応を待ち続けているkyubunsです。
Unity5.xではscene/prefabファイルの形式が変わって、衝突を起こしにくいフォーマットになるそうですね！！
超期待してます！！
つまり、最初に言いたいことは、**この記事を真面目に読んでも、5.xでフォーマット変わっちゃうし、そんな得しないかもしれません**

が、まだ時代は4.x。↑の話も5.0では入らない予定っぽいので、暫くは今のフォーマットと仲良くしていかないといけません。
なので、ここでは、Scene/Prefabファイルをテキストエディタで編集できるようになるための情報をまとめておきたいと思います。

# この記事を読むと

prefabファイルのコンフリクトが解決できるようになる・・・かもしれませんが、
たいていぶつかるときは大事故なので、あまり役には立ちません。

# 関連記事

ちょうど1週間前のwordijpさんの[Unityの.metaファイルに書かれているguidについて](http://qiita.com/wordijp/items/3e4b87756fd11c893a9d)を読むと理解が深まると思います！！

# 前準備

Editor SettingsのAsset SerializationをForce Textに変更しておいてください。

# GameObject1つの簡単なPrefab

以下のようなGameObject1つを持ったPrefabを作ります。
HogeFugaというコンポーネントは自分で作ったSerializeFieldを2つ持ってるだけのスクリプトです。
![Test_unity_-_unicalendar_-_PC__Mac___Linux_Standalone.png](https://qiita-image-store.s3.amazonaws.com/0/6459/31e4d341-f8d8-6175-82db-b117278e139d.png)

さっそくテキストエディタで開いて見ましょう。

```csharp
%YAML 1.1
%TAG !u! tag:unity3d.com,2011:
--- !u!1 &100000
GameObject:
  m_ObjectHideFlags: 0
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  serializedVersion: 4
  m_Component:
  - 4: {fileID: 400000}
  - 65: {fileID: 6500000}
  - 114: {fileID: 11400000}
  m_Layer: 0
  m_Name: Test
  m_TagString: Untagged
  m_Icon: {fileID: 0}
  m_NavMeshLayer: 0
  m_StaticEditorFlags: 0
  m_IsActive: 1
--- !u!4 &400000
Transform:
  m_ObjectHideFlags: 1
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  m_GameObject: {fileID: 100000}
  m_LocalRotation: {x: 0, y: 0, z: 0, w: 1}
  m_LocalPosition: {x: 0, y: 0, z: 0}
  m_LocalScale: {x: 1, y: 1, z: 1}
  m_Children: []
  m_Father: {fileID: 0}
  m_RootOrder: 0
--- !u!65 &6500000
BoxCollider:
  m_ObjectHideFlags: 1
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  m_GameObject: {fileID: 100000}
  m_Material: {fileID: 0}
  m_IsTrigger: 0
  m_Enabled: 1
  serializedVersion: 2
  m_Size: {x: 100, y: 100, z: 1}
  m_Center: {x: 0, y: 0, z: 0}
--- !u!114 &11400000
MonoBehaviour:
  m_ObjectHideFlags: 1
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  m_GameObject: {fileID: 100000}
  m_Enabled: 1
  m_EditorHideFlags: 0
  m_Script: {fileID: 11500000, guid: ba67021371bc7479087065875833f130, type: 3}
  m_Name: 
  m_EditorClassIdentifier: 
  testValue1: 1
  testValue2: 2
--- !u!1001 &100100000
Prefab:
  m_ObjectHideFlags: 1
  serializedVersion: 2
  m_Modification:
    m_TransformParent: {fileID: 0}
    m_Modifications: []
    m_RemovedComponents: []
  m_ParentPrefab: {fileID: 0}
  m_RootGameObject: {fileID: 100000}
  m_IsPrefabParent: 1
  m_IsExploded: 1
```

上から順々に見ていきます。

## ヘッダー

特に何も言うことがない、ただのヘッダーです。
うっかりテキストで編集してると消してしまって読み込めないよ！って言われることがあるので、
頭の片隅には置いておきましょう。

```
%YAML 1.1
%TAG !u! tag:unity3d.com,2011:
```

## RootのGameObject

ここからが本番です。
これ1つ読めるようになれば、あとはちょろいもんです。

```
--- !u!1 &100000
```
まずはこの部分。
あ、言い忘れてました。Prefab読むときは [クラスIDリファレンス](http://docs.unity3d.com/Manual/ClassIDReference.html) を開いておきましょう。

!u!の後ろの1は、クラスIDを示しています。
この場合は「1」なので、GameObjectです。
![Unity_-_Manual__YAML_Class_ID_Reference.png](https://qiita-image-store.s3.amazonaws.com/0/6459/f0ae0745-8d6a-d8e5-be5b-153c2fe52a8b.png) ← クラスIDリファレンスより。

その後ろの&100000はfileIDを示しています。
fileIDは、ファイル内(Prefab内 or Scene内)でユニークなIDです。
Prefabの場合、必ずRootのObjectが100000番になっている気がします。

さてさて、続きを見ていきましょう。

```
GameObject:
  m_ObjectHideFlags: 0
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  serializedVersion: 4
  m_Component:
  - 4: {fileID: 400000}
  - 65: {fileID: 6500000}
  - 114: {fileID: 11400000}
  m_Layer: 0
  m_Name: Test
  m_TagString: Untagged
  m_Icon: {fileID: 0}
  m_NavMeshLayer: 0
  m_StaticEditorFlags: 0
  m_IsActive: 1
```

FileID:100000がGameObjectであること、そして、そのGameObjectが持っているメンバ変数の値が乗ってます。
m_Name: Testとなっていることから、先ほど作った↓このオブジェクトだということが分かります。
![新規作成_-_Qiita.png](https://qiita-image-store.s3.amazonaws.com/0/6459/eaf43686-750b-a8ec-e587-54425df7aee3.png)

## 参照の読み方

まず、参照の見方を先に説明しておきます。
{fileID: 12345} -> fileID:12345を参照している。
{fileID: 12345, guid: abcde, type: 2} -> guidがabcdeというファイルの中のfileID:12345を参照している。
FileIDは、ファイル内でユニークなだけで、ファイルをまたぐと被る事が出てくるために、他のファイルのFileIDを参照している箇所では必ずguidがセットでついてます。
type:2は謎です。
ちなみに、FileID: 0はnullみたいなものです。

## コンポーネント

大切なところから見ていきましょう。
このGameObjectが持っているコンポーネントです。

```
m_Component:
  - 4: {fileID: 400000}
  - 65: {fileID: 6500000}
  - 114: {fileID: 11400000}
```

4, 65, 114は、全てClassIDです。
それぞれ、Transform, BoxCollider, MonoBehaviour。
先ほど自分で作ったGameObjectにある要素そのままですね。

では、この参照先を見に行ってみましょう！

## Transform

```
  - 4: {fileID: 400000}
```

全てのGameObjectはTransformに通ず。
prefabファイル内を400000で検索をかけてみると

```
--- !u!4 &400000
Transform:
  m_ObjectHideFlags: 1
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  m_GameObject: {fileID: 100000}
  m_LocalRotation: {x: 0, y: 0, z: 0, w: 1}
  m_LocalPosition: {x: 0, y: 0, z: 0}
  m_LocalScale: {x: 1, y: 1, z: 1}
  m_Children: []
  m_Father: {fileID: 0}
  m_RootOrder: 0
```

classIDが4なのでTransformで間違いないですね。

m_GameObject: {fileID: 100000}
ここは、自分が張り付いているGameObjectのfileIDです。
FileID: 100000はついさっきまで見ていたやつです。
Unity4.xあたりから、m_RootOrderというのが追加されました。
Rootからの深さが保存されてるみたいです。

## MonoBehaviour

```
- 114: {fileID: 11400000}
```

次に、ここを見に行ってみます。

```
--- !u!114 &11400000
MonoBehaviour:
  m_ObjectHideFlags: 1
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  m_GameObject: {fileID: 100000}
  m_Enabled: 1
  m_EditorHideFlags: 0
  m_Script: {fileID: 11500000, guid: ba67021371bc7479087065875833f130, type: 3}
  m_Name: 
  m_EditorClassIdentifier: 
  testValue1: 1
  testValue2: 2
```

m_Prefab**はいったん気にしないでください。
わかりやすいのは、SerializeFieldになっているtestValue1, testValue2、そのままテキストにでてますね。
なので、テキストエディタでここの値をtestValue1: 12345とかにするとUnityEditor上でもちゃんと変わります。
これを覚えておけば、sedで一気に値を変更する、なんてこともできますね。
やりませんけどね。

![新規作成_-_Qiita.png](https://qiita-image-store.s3.amazonaws.com/0/6459/21f0223f-9306-1c3d-98a5-0b7fe3915344.png)

後ここ。
m_Script: {fileID: 11500000, guid: ba67021371bc7479087065875833f130, type: 3}
このguidは、HogeFuga.cs.metaのguidに一致しています。
ここに書いてあるfileIDと、typeは、謎です。

## その他

m_Layerとか、m_TagStringとか自明な物は置いといて、あまり解説がされてるのを見たことない箇所を詳しく説明！
横道にそれる感じになるので読み飛ばしてもOKです！

### m_PrefabParentObject / m_PrefabInternal

m_PrefabParentObjectは、SceneにPrefabを置いたときに、どのPrefabファイルが置かれたか、という情報が保存されています。
例えば、Test.prefab.metaが

```Test.prefab.meta
fileFormatVersion: 2
guid: cd809162f4ad143a4a2021da8cf3b7ee
NativeFormatImporter:
  userData: 
```
このようになっていたとして、
Test.prefabをSceneに設置した場合、そのSceneファイルには以下のようなObjectが増えます。
(本当はPrefabが持っているObject分増えるけれど、1つだけ抜き出し)

```Test.scene
--- !u!1 &1523426135
GameObject:
  m_ObjectHideFlags: 0
  m_PrefabParentObject: {fileID: 100000, guid: cd809162f4ad143a4a2021da8cf3b7ee, type: 2}
  m_PrefabInternal: {fileID: 1234644206}
  serializedVersion: 4
  m_Component:
  - 4: {fileID: 1523426138}
  - 65: {fileID: 1523426137}
  - 114: {fileID: 1523426136}
  m_Layer: 0
  m_Name: Test
  m_TagString: Untagged
  m_Icon: {fileID: 0}
  m_NavMeshLayer: 0
  m_StaticEditorFlags: 0
  m_IsActive: 1
```

ここのm_PrefabParentObjectを見ると、先ほどのTest.prefab.metaのguidが一致していて、参照関係にあることが分かります。
次に気になるのは、m_PrefabInternalです。
このFileIDをSceneファイルの中から検索してみます

```Test.scene
--- !u!1001 &1234644206
Prefab:
  m_ObjectHideFlags: 0
  serializedVersion: 2
  m_Modification:
    m_TransformParent: {fileID: 0}
    m_Modifications:
    - target: {fileID: 400000, guid: cd809162f4ad143a4a2021da8cf3b7ee, type: 2}
      propertyPath: m_LocalPosition.x
      value: 0
      objectReference: {fileID: 0}
    - target: {fileID: 400000, guid: cd809162f4ad143a4a2021da8cf3b7ee, type: 2}
      propertyPath: m_LocalPosition.y
      value: 0
      objectReference: {fileID: 0}
    - target: {fileID: 400000, guid: cd809162f4ad143a4a2021da8cf3b7ee, type: 2}
      propertyPath: m_LocalPosition.z
      value: 0
      objectReference: {fileID: 0}
    - target: {fileID: 400000, guid: cd809162f4ad143a4a2021da8cf3b7ee, type: 2}
      propertyPath: m_LocalRotation.x
      value: 0
      objectReference: {fileID: 0}
    - target: {fileID: 400000, guid: cd809162f4ad143a4a2021da8cf3b7ee, type: 2}
      propertyPath: m_LocalRotation.y
      value: 0
      objectReference: {fileID: 0}
    - target: {fileID: 400000, guid: cd809162f4ad143a4a2021da8cf3b7ee, type: 2}
      propertyPath: m_LocalRotation.z
      value: 0
      objectReference: {fileID: 0}
    - target: {fileID: 400000, guid: cd809162f4ad143a4a2021da8cf3b7ee, type: 2}
      propertyPath: m_LocalRotation.w
      value: 1
      objectReference: {fileID: 0}
    - target: {fileID: 400000, guid: cd809162f4ad143a4a2021da8cf3b7ee, type: 2}
      propertyPath: m_RootOrder
      value: 1
      objectReference: {fileID: 0}
    m_RemovedComponents: []
  m_ParentPrefab: {fileID: 100100000, guid: cd809162f4ad143a4a2021da8cf3b7ee, type: 2}
  m_RootGameObject: {fileID: 1523426135}
  m_IsPrefabParent: 0
  m_IsExploded: 1
```

見るところがいっぱいありますが1つずつ。
まず、classIDが1001番。リファレンスで検索すると「Prefab」と書いてあります。
Prefabの実体(GameObject)という意味では無く、Prefabに関する情報を保持しておく箇所という感じ。
次に、m_RootGameObject: {fileID: 1523426135}、このfileIDは、ついさっき出てきた実体のGameObjectのFileIDです。（この記事内をこのIDで検索しても見つかります。）
m_Modificationは名前通り「Prefabを設置した後に、変更が加えられた値」の情報です。

# 階層構造を持ったPrefab

ここまで大丈夫でしょうか？
何も考えずに目に付いた物から説明しているので、理解しにくい構成になってると思いますが、
自分も日本語書くのがそろそろ辛くなってきたので書き直すことはたぶんないでしょう。

次に階層構造を持ったPrefabにいってみましょう。

こんな簡単なGameObjectを3つ連ねた物を作ってPrefab化して開いて見ます。
![t.png](https://qiita-image-store.s3.amazonaws.com/0/6459/571c8c8d-ace3-7854-38a3-0aa2faba9222.png)

```
%YAML 1.1
%TAG !u! tag:unity3d.com,2011:
--- !u!1 &100000
GameObject:
  m_ObjectHideFlags: 1
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  serializedVersion: 4
  m_Component:
  - 4: {fileID: 400000}
  m_Layer: 0
  m_Name: 3
  m_TagString: Untagged
  m_Icon: {fileID: 0}
  m_NavMeshLayer: 0
  m_StaticEditorFlags: 0
  m_IsActive: 1
--- !u!1 &100002
GameObject:
  m_ObjectHideFlags: 0
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  serializedVersion: 4
  m_Component:
  - 4: {fileID: 400002}
  m_Layer: 0
  m_Name: 2
  m_TagString: Untagged
  m_Icon: {fileID: 0}
  m_NavMeshLayer: 0
  m_StaticEditorFlags: 0
  m_IsActive: 1
--- !u!1 &100004
GameObject:
  m_ObjectHideFlags: 0
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  serializedVersion: 4
  m_Component:
  - 4: {fileID: 400004}
  m_Layer: 0
  m_Name: 1
  m_TagString: Untagged
  m_Icon: {fileID: 0}
  m_NavMeshLayer: 0
  m_StaticEditorFlags: 0
  m_IsActive: 1
--- !u!4 &400000
Transform:
  m_ObjectHideFlags: 1
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  m_GameObject: {fileID: 100000}
  m_LocalRotation: {x: 0, y: 0, z: 0, w: 1}
  m_LocalPosition: {x: 0, y: 0, z: 0}
  m_LocalScale: {x: 1, y: 1, z: 1}
  m_Children: []
  m_Father: {fileID: 400002}
  m_RootOrder: 0
--- !u!4 &400002
Transform:
  m_ObjectHideFlags: 1
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  m_GameObject: {fileID: 100002}
  m_LocalRotation: {x: 0, y: 0, z: 0, w: 1}
  m_LocalPosition: {x: 0, y: 0, z: 0}
  m_LocalScale: {x: 1, y: 1, z: 1}
  m_Children:
  - {fileID: 400000}
  m_Father: {fileID: 400004}
  m_RootOrder: 0
--- !u!4 &400004
Transform:
  m_ObjectHideFlags: 1
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 100100000}
  m_GameObject: {fileID: 100004}
  m_LocalRotation: {x: 0, y: 0, z: 0, w: 1}
  m_LocalPosition: {x: 0, y: 0, z: 0}
  m_LocalScale: {x: 1, y: 1, z: 1}
  m_Children:
  - {fileID: 400002}
  m_Father: {fileID: 0}
  m_RootOrder: 0
--- !u!1001 &100100000
Prefab:
  m_ObjectHideFlags: 1
  serializedVersion: 2
  m_Modification:
    m_TransformParent: {fileID: 0}
    m_Modifications: []
    m_RemovedComponents: []
  m_ParentPrefab: {fileID: 0}
  m_RootGameObject: {fileID: 100004}
  m_IsPrefabParent: 1
  m_IsExploded: 1
```

コンポーネントを1つも持ってないので結構シンプルになります。
ここで注目して貰いたいのは、Transformが持っているm_Children, m_Father。
そう、Transformが持ってるんです。
Scriptを書いているときに自分の1個上のGameObjectを取ろうとすると

```csharp
transform.parent.gameObject
```

というコードになる意味が、なんとなく理解出来るようになりますね。
分かるとは思うのですが、念のため1つだけ追ってみましょう。
「3というGameObjectの親のGameObjectは何か」を調べてみます。
しんどくなってきたので、↑のテキストを好きなエディター(ここで宗教戦争をするつもりはありません。お好きな物をどうぞ。）にコピペして下の説明読みながら探してみてください。
- m_Name: 3のGameObjectを探します。
- そのGameObjectの持っているTransformのFileIDを探します。→ 400000
- FileID: 400000のObjectを見に行って、その中のm_FatherになっているFileIDを探します。→400002
- FileID: 400002が、どのGameObjectに張り付いているか(m_GameObjectの値)を調べます→ 100002
- FileID: 100002のGameObjectのm_Nameを見ると「2」というGameObjectで有ることがわかります。

ほら！！もうPrefabファイル読めますね！！
書き直しもできますね！！
コードレビュー時にPrefabファイルあってもdiff読んでレビューできますね！！

# 最後にちょっとだけSceneファイルを覗いてみる

Sceneファイルも、だいたいPrefabと同じです。
Test.unityを開いて見ます。

```Test.unity
%YAML 1.1
%TAG !u! tag:unity3d.com,2011:
--- !u!29 &1
SceneSettings:
  m_ObjectHideFlags: 0
  m_PVSData: 
  m_PVSObjectsArray: []
  m_PVSPortalsArray: []
  m_OcclusionBakeSettings:
    smallestOccluder: 5
    smallestHole: .25
    backfaceThreshold: 100
--- !u!104 &2
RenderSettings:
  m_Fog: 0
  m_FogColor: {r: .5, g: .5, b: .5, a: 1}
  m_FogMode: 3
  m_FogDensity: .00999999978
  m_LinearFogStart: 0
  m_LinearFogEnd: 300
  m_AmbientLight: {r: .200000003, g: .200000003, b: .200000003, a: 1}
  m_SkyboxMaterial: {fileID: 0}
  m_HaloStrength: .5
  m_FlareStrength: 1
  m_FlareFadeSpeed: 3
  m_HaloTexture: {fileID: 0}
  m_SpotCookie: {fileID: 0}
  m_ObjectHideFlags: 0
--- !u!127 &3
LevelGameManager:
  m_ObjectHideFlags: 0
--- !u!157 &4
LightmapSettings:
  m_ObjectHideFlags: 0
  m_LightProbes: {fileID: 0}
  m_Lightmaps: []
  m_LightmapsMode: 1
  m_BakedColorSpace: 0
  m_UseDualLightmapsInForward: 0
  m_LightmapEditorSettings:
    m_Resolution: 50
    m_LastUsedResolution: 0
    m_TextureWidth: 1024
    m_TextureHeight: 1024
    m_BounceBoost: 1
    m_BounceIntensity: 1
    m_SkyLightColor: {r: .860000014, g: .930000007, b: 1, a: 1}
    m_SkyLightIntensity: 0
    m_Quality: 0
    m_Bounces: 1
    m_FinalGatherRays: 1000
    m_FinalGatherContrastThreshold: .0500000007
    m_FinalGatherGradientThreshold: 0
    m_FinalGatherInterpolationPoints: 15
    m_AOAmount: 0
    m_AOMaxDistance: .100000001
    m_AOContrast: 1
    m_LODSurfaceMappingDistance: 1
    m_Padding: 0
    m_TextureCompression: 0
    m_LockAtlas: 0
--- !u!196 &5
NavMeshSettings:
  m_ObjectHideFlags: 0
  m_BuildSettings:
    agentRadius: .5
    agentHeight: 2
    agentSlope: 45
    agentClimb: .400000006
    ledgeDropHeight: 0
    maxJumpAcrossDistance: 0
    accuratePlacement: 0
    minRegionArea: 2
    widthInaccuracy: 16.666666
    heightInaccuracy: 10
  m_NavMesh: {fileID: 0}
--- !u!1 &912467296
GameObject:
  m_ObjectHideFlags: 0
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 0}
  serializedVersion: 4
  m_Component:
  - 4: {fileID: 912467301}
  - 20: {fileID: 912467300}
  - 92: {fileID: 912467299}
  - 124: {fileID: 912467298}
  - 81: {fileID: 912467297}
  m_Layer: 0
  m_Name: Main Camera
  m_TagString: MainCamera
  m_Icon: {fileID: 0}
  m_NavMeshLayer: 0
  m_StaticEditorFlags: 0
  m_IsActive: 1
--- !u!81 &912467297
AudioListener:
  m_ObjectHideFlags: 0
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 0}
  m_GameObject: {fileID: 912467296}
  m_Enabled: 1
--- !u!124 &912467298
Behaviour:
  m_ObjectHideFlags: 0
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 0}
  m_GameObject: {fileID: 912467296}
  m_Enabled: 1
--- !u!92 &912467299
Behaviour:
  m_ObjectHideFlags: 0
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 0}
  m_GameObject: {fileID: 912467296}
  m_Enabled: 1
--- !u!20 &912467300
Camera:
  m_ObjectHideFlags: 0
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 0}
  m_GameObject: {fileID: 912467296}
  m_Enabled: 1
  serializedVersion: 2
  m_ClearFlags: 1
  m_BackGroundColor: {r: .192156866, g: .301960796, b: .474509805, a: .0196078438}
  m_NormalizedViewPortRect:
    serializedVersion: 2
    x: 0
    y: 0
    width: 1
    height: 1
  near clip plane: .300000012
  far clip plane: 1000
  field of view: 60
  orthographic: 0
  orthographic size: 5
  m_Depth: -1
  m_CullingMask:
    serializedVersion: 2
    m_Bits: 4294967295
  m_RenderingPath: -1
  m_TargetTexture: {fileID: 0}
  m_TargetDisplay: 0
  m_HDR: 0
  m_OcclusionCulling: 1
  m_StereoConvergence: 10
  m_StereoSeparation: .0219999999
--- !u!4 &912467301
Transform:
  m_ObjectHideFlags: 0
  m_PrefabParentObject: {fileID: 0}
  m_PrefabInternal: {fileID: 0}
  m_GameObject: {fileID: 912467296}
  m_LocalRotation: {x: 0, y: 0, z: 0, w: 1}
  m_LocalPosition: {x: 0, y: 1, z: -10}
  m_LocalScale: {x: 1, y: 1, z: 1}
  m_Children: []
  m_Father: {fileID: 0}
  m_RootOrder: 0
```

ヘッダーの下にSceneSettingsとかRenderSettingsとかScene特有の設定が諸々ありますね。
でも順々に見ていけば、さっきのPrefabが読めていれば、Sceneファイルも余裕で読めるはずです！
是非お試しを！

# 後書き

いやー、「アドベントカレンダーだからカレンダー作ってみました」って記事にならなくてよかった。
本当によかった。
![かれんだー.png](https://qiita-image-store.s3.amazonaws.com/0/6459/959dd143-0c27-b15b-3bf0-b591ec82bb5b.png)


# メモ

ClassIDReference http://docs.unity3d.com/Manual/ClassIDReference.html
