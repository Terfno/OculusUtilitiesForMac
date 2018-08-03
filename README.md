# OculusUtilitiesForMac
Terfnoが頑張ったOculusUtilitiesを公開するだけのリポジトリ
## Unity project
Afterのフォルダはエラー解消後です。

Beforeのフォルダはエラー解消前です。

---

# macOSでUnity使ってOculusGOの開発を初めてやったときに情弱がハマったとこ
## Agenda
- OculusUtilitiesが公式でサポートされてない話

- UnityEditor.BuildPlayerWindow+BuildMethodExceptionとかいう謎のエラーに苦しめられる話

1に関しては正確な答えをググると出てきますが，私は理解できませんでした。
で，2に関してなんですが，ググって出てくる情報では対処できなかったので私がいろいろ試して解決した方法を紹介します。
クソ情弱な高専生がこれを乗り越えた話をしませう。
いろいろな記事を読んでも私は理解できなかったので，私と同等のITリテラシーの方に理解できるよう努力します。
### 私の環境
macOS High Sierra
version:10.13.5
Unity 2018.1.6f1
私はUnity初挑戦でUnityに対する知見０でした。
またVRも友人のPSVRを５分ほどやった以外はOculusGoが初めてです。
Andoroidの開発経験も０でした。
それでは見てみましょう
## OculusUtilitiesが公式でサポートされてない話
### Unityがオギャった
内容
```
Assets/Oculus/VR/Scripts/OVRPlugin.cs(2927,15): error CS0103: The name `OVRP_1_15_0' does not exist in the current context
```
これがいっぱい出ました。
いい子にしてろよ…
### ググった
なんなら以下のページで問題解決できる。
私は無理だった(雑魚)
Qiitaの記事では以下の記事読みましたね。
[Unity と Mac で開発する Oculus Go アプリ #9 OVRPlugin.cs error CS0103: The name `OVRP_1_15_0' does not exist in the current context](https://qiita.com/Sam/items/d050db69b5e2a4929672)

---

で，公式のフォーラムが以下ですね

[Error when loading in Oculus Utilities for Unity](https://forums.oculusvr.com/developer/discussion/66847/error-when-loading-in-oculus-utilities-for-unity)


---

他にはこの記事を読みました。
[OCULUS GOでアプリ作ってみた2](http://yataya2000.com/article/oculusgo2/)

### ググった結論
OCULUS GOでアプリ作ってみた2にて以下のように書いてありますが，様々な記事に同様の内容がありました。
多分これが正解なんでしょう
大体エラーが起きている付近のコードは以下のようになっているので
```
 - - - - - - - - - - - - - - - 
if (version >= OVRP_1_15_0.version)
{
Result result = OVRP_1_15_0.ovrp_GetNodeFrustum2(nodeId, out frustum);
if (result != Result.Success)
{
return false;
}
else
{
return true;
}
}
else
{
return false;
}
 - - - - - - - - - - - - - - -
↓これをこうする
- - - - - - - - - - - - - - - 
#if !OVRPLUGIN_UNSUPPORTED_PLATFORM
if (version >= OVRP_1_15_0.version)
{
Result result = OVRP_1_15_0.ovrp_GetNodeFrustum2(nodeId, out frustum);
if (result != Result.Success)
{
return false;
}
else
{
return true;
}
}
else
#endif
{
return false;
}
- - - - - - - - - - - - - - -
```
エラーの起きている箇所によって若干コード異なることもあったけど、このやり方でエラーをひとつずつ潰したら治りました。

へーなるほど

わからん

わかんないんだよ！

解決した後だからわかるけどド素人には分からなかったんだよ！

→一重に勉強不足

わからないので人に聞いてみた


---
```
UnityとOculus強くて時間がある方助けていただけないでしょうか。
Oculusが提供しているアセット(?)でリモコンを表示したりしようとしています。
OSはmacOS HighSierra 10.13.5です。
Unityはversion 2018.1.6f1 personalです。
アセットを導入した直後から
Assets/Oculus/VR/Scripts/OVRPlugin.cs(2927,15): error CS0103: The name `OVRP_1_15_0' does not exist in the current context
というようなエラーが出ています。
Googleで検索するといろいろな記事が出てきましたが，イマイチ内容が理解できませんでした。
すでにUnityの再インストール，MacBookProの再起動，JDK，SDKの確認をしましたがいずれも改善されませんでした。
←どの解決方法も的外れな気がします。
よろしくおねがいします。

```
---

様々な人の協力を得て具体的な解決策を見つけました。

### 最終的な解決策
語彙力無いのでとりあえず修正したソースコード公開するなり。

https://github.com/Terfno/OculusUtilitiesForMac


---

## UnityEditor.BuildPlayerWindow+BuildMethodExceptionとかいう謎のエラーに苦しめられる話
### Unityがオギャる
Build Faild するんですよ

エラー・メッセージは以下のようなやつ
```
UnityEditor.BuildPlayerWindow+BuildMethodException
```
### ググった
[【Unity2018対応】Androidビルドでエラーが出る場合の対処法](http://nn-hokuson.hatenablog.com/entry/2017/09/05/202327)
このページが参考になりました。

が，私の場合解決しなかったので上記のページで解決しない場合は私と同様の箇所でハマっている可能性があります。

いろいろ設定みてた結果変えたらイケたとこ

----
```
Unity→Build Settings→Player Settings→Other Settings
```
----

この場所にMinimum API Levelっていう設定項目と，Target API Levelっていう設定項目があると思います。

![画像](https://github.com/Terfno/OculusUtilitiesForMac/blob/master/image/スクリーンショット%202018-07-14%2018.27.49.png)

これねエラーが出るときの設定は以下の状態でした。

![画像](https://github.com/Terfno/OculusUtilitiesForMac/blob/master/image/スクリーンショット%202018-07-14%2018.28.52.png)

エラー出るときの設定状態エラーを解消する設定はこんな感じになりました。

![画像](https://github.com/Terfno/OculusUtilitiesForMac/blob/master/image/スクリーンショット%202018-07-14%2018.29.44.png)

エラーを解消したときの設定状態多分これ私くらいしかしてないミスなんじゃないですかね…

同じとこでハマってる人がいれば助けになるかと思います。
