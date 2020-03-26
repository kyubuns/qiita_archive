http://qiita.com/kyubuns/items/d2831e4b0e0eff1892c7#comment-4b62323585b8a5c3d659
こちらと同じようにスクリプトに直接手を入れてもらうことになります。

```
    copiedDoc = app.activeDocument.duplicate(activeDocument.name.slice(0, -4) + '.copy.psd');
```

となっている箇所を

```
    copiedDoc = app.activeDocument.duplicate(activeDocument.name.slice(0, -4) + '.copy.psd');
    copiedDoc.resizeImage(copiedDoc.width * 10.0, copiedDoc.height * 10.0);
```

のように書き換えることで、10%のサイズにリサイズが出来ます。（他の%にしたい場合は10.0の部分を書き換えてください）
解像度を指定して書き出す場合は、

```
    copiedDoc = app.activeDocument.duplicate(activeDocument.name.slice(0, -4) + '.copy.psd');
    copiedDoc.resizeImage(100.0, 100.0);
```

これで100px x 100pxで書き出しになります。

http://www.openspc2.org/book/PhotoshopCS/easy/canvas/002/index.html
