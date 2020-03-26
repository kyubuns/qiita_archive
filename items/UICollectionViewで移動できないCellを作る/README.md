例えば0番目のcellを移動させたくない場合は

https://developer.apple.com/reference/uikit/uicollectionviewdatasource/1618015-collectionview

```swift
override func collectionView(_ collectionView: UICollectionView, canMoveItemAt indexPath: IndexPath) -> Bool {
    return indexPath.row > 0
}
```

と書くと、0番目のcellを移動させることはできなくなります。
が、他のcellを0番目の前に持ってきては出来ます。
いやいやなんでやねん。

ここから追記。
他のcellが0番目のcellにこないように防ぐには、こんな感じにしておくといい感じになります。

```
override func collectionView(_ collectionView: UICollectionView,
                             targetIndexPathForMoveFromItemAt originalIndexPath: IndexPath,
                             toProposedIndexPath proposedIndexPath: IndexPath) -> IndexPath {
    if proposedIndexPath.row == 0 { return IndexPath(row: 1, section: 0) }
    return proposedIndexPath
}
```


コメントで正しい方法を教えていただきました！ありがとうございます！

---
↓間違ってたやり方

結局力技でこんな感じのを追加しました。

```swift
override func collectionView(_ collectionView: UICollectionView, moveItemAt sourceIndexPath: IndexPath, to destinationIndexPath: IndexPath) {
    if sourceIndexPath.row == 0 || destinationIndexPath.row == 0 {
        self.collectionView?.moveItem(at: destinationIndexPath, to: sourceIndexPath)
        return
    }
    // hogefuga
}
```

もっとスマートな解決法あったら教えて下さい。
