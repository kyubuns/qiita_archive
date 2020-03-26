# モンキーテストって？

https://www.google.co.jp/search?q=モンキーテスト

なんかぐぐってみると思ったより皆が飼ってるサルは賢いんだなぁと思うのですが、ここでいうモンキーテストは「適当にランダムなボタンを押す」ことです。

# どういうこと？？

スクリプトが勝手に画面に見えている押せるボタンをランダムで押したり、スクロールをガチャガチャやってみたりしてくれます。
百聞は一見にしかずということで動画を取ってみました。
https://www.youtube.com/watch?v=nO27t4djuBE

# 何の役にたつの？

エラーが発生するまで適当にボタンを押し続けてくれることで、想定していなかった挙動や、画面遷移を発見することが出来るかもしれません。

# やってみたい！

コードはgithubに上がってます。
TestRunner->PlayModeでRunAllすると、モンキーが適当に50回ボタンを押して、何もエラーが発生しないことを確認してくれます。
また、ロード画面などが存在しても問題なく動くようになっています。
（押せるボタンが無い間は待つ。1秒以上押せるボタンが出てこなかった時はタイムアウトしてエラーになる）
さらに、Monkeyコンポーネントを適当なゲームオブジェクトにくっつけることで、無限に適当にボタンを押してくれます。
放置する時はこちらが便利です。
https://github.com/kyubuns/WakuTest

# ついでに、WakuTestについて

UIのテストをシンプルに書きたいなーというところから生まれました。
今のところ、Click()、Wait("GameObject名")の2つの機能を持っていて、
* Click() - 押せるかどうかを確認してからクリックしてくれる、クリック出来ない(他のGameObjectに隠れている、非アクティブである)場合はエラー。
* Wait() - そのゲームオブジェクトが出現するまで待つ。
という機能です。
ご意見お待ちしております。

```csharp
namespace WakuTest.Sample
{
	public class WakuTestSample : UISceneTest
	{
		public WakuTestSample() : base("Sample")
		{
		}

		[UnityTest]
		public IEnumerator ClickAButton_ChangeText()
		{
			yield return SetupEventSystemAsync();
			for (int i = 1; i < 10; ++i)
			{
				Button("AButton").Click();
				Assert.AreEqual(Text("AText").text, i.ToString());
			}
		}

		[UnityTest]
		public IEnumerator ClickBButton_NoChangeTexts()
		{
			yield return SetupEventSystemAsync();
			for (int i = 1; i < 10; ++i)
			{
				Button("BButton").Click();
				Assert.AreEqual(Text("AText").text, "0");
				Assert.AreEqual(Text("CText").text, "0");
			}
		}

		[UnityTest]
		public IEnumerator OpenAndCloseWindow()
		{
			yield return SetupEventSystemAsync();
			Button("OpenWindowButton").Click();
			yield return WaitAsync("CloseWindowButton");
			Button("CloseWindowButton").Click();
		}

		[UnityTest]
		public IEnumerator MonkeyTest_RandomButton()
		{
			yield return SetupEventSystemAsync();

			Debug.Log("--- MonkeyTest ---");
			for (int i = 0; i < 50; ++i)
			{
				yield return WaitAsync(() => {
					var button = GameObject.FindObjectsOfType<Button>().Where(x => x.IsClickable()).Where(x => x.name != "RaiseExceptionButton").RandomAtOrDefault();
					if (button == null) return false;

					Debug.Log(Time.time + " Click " + button.name);
					button.Click();
					return true;
				});
			}
		}
	}
}
```
