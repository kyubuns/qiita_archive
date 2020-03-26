動作検証してないですが、下の方にある
```
    options = new ExportOptionsSaveForWeb();
    options.format = format;
    options.optimized = true;
    options.interlaced = false;
```
の部分を、
```
    options = new ExportOptionsSaveForWeb();
    options.format = SaveDocumentType.JPEG;
    options.optimized = false;
    options.interlaced = false;
    options.quality = 100;//100%の画質
    options.lossy = 0;
```
に書き換えてみてくださいー
細かいオプションはこちらに乗ってます
https://qiita.com/yudough/items/8240cd1a84f09e833df3
