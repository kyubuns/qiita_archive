canvasサイズの変更はどこ寄せにするか等引数が多く汎用的に使えるようにするのは難しいなと思ったのでプラグインに導入することは諦めました

実現方法としましては、93行目付近

```
    copiedDoc = app.activeDocument.duplicate(activeDocument.name.slice(0, -4) + '.copy.psd');
```

の直後に処理を入れてもらえれば実現できます。

```
    copiedDoc = app.activeDocument.duplicate(activeDocument.name.slice(0, -4) + '.copy.psd');
    copiedDoc.resizeCanvas(copiedDoc.width * 10.0, copiedDoc.height * 10.0,AnchorPosition.TOPLEFT);
```

これで、横10.0%,縦10.0%サイズの左上寄せでキャンバスのリサイズが出来ます。
ご確認ください。
参考: http://www.openspc2.org/book/PhotoshopCS2/easy/layerset/006/
