例えばアイテムのアイコンにレアリティの枠をつけるとか、そういうときに使うやつです。
サクッと書けるかと思ったらアルファの計算が思ったより面倒くさかったのでメモ。

```csharp
public void CreateTexture(string baseImagePath, string frameImagePath)
{
    // Textureのread/write設定をいじらないため、直にReadAllBytesで読む
    var baseImage = new Texture2D(0, 0);
    baseImage.LoadImage(File.ReadAllBytes(baseImagePath));
    var basePixels = baseImage.GetPixels();

    var frameImage = new Texture2D(0, 0);
    frameImage.LoadImage(File.ReadAllBytes(frameImagePath));
    var framePixels = frameImage.GetPixels();

    Assert.AreEqual(basePixels.Length, framePixels.Length);

    var outputPixels = new Color[basePixels.Length];
    for (var i = 0; i < basePixels.Length; ++i)
    {
        var d = basePixels[i];
        var f = framePixels[i];
        var a = f.a + d.a * (1f - f.a);
        if (a > 0f)
        {
            var r = (f.r * f.a + d.r * d.a * (1f - f.a)) / a;
            var g = (f.g * f.a + d.g * d.a * (1f - f.a)) / a;
            var b = (f.b * f.a + d.b * d.a * (1f - f.a)) / a;
            outputPixels[i] = new Color(r, g, b, a);
        }
        else
        {
            outputPixels[i] = new Color(0f, 0f, 0f, a);
        }
    }

    var newImage = new Texture2D(baseImage.width, baseImage.height, TextureFormat.ARGB32, false);
    newImage.SetPixels(outputPixels);

    var bytes = newImage.EncodeToPNG();
    File.WriteAllBytes(baseImagePath, bytes);
}
```
