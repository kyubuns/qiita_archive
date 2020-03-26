Unity5.3で追加されたWaitUntilがめっちゃ便利

```Share.cs
public IEnumerator ScreenShot()
{
    var captureDirectory = Application.persistentDataPath;
    var captureFileName = "ScreenShot.png";
    var capturePath = Path.Combine(captureDirectory, captureFileName);

    if(File.Exists(capturePath)) File.Delete(capturePath);
    Application.CaptureScreenshot(captureFileName);
    yield return new WaitUntil(() => { return File.Exists(Application.isEditor ? captureFileName : capturePath); });
    // HogeHoge
}
```
