UICollectionViewDelegateの、targetIndexPathForMoveFromItemAtIndexPathを使った方が綺麗かも？

>During the interactive moving of an item, the collection view calls this method to see if you want to provide a different index path than the proposed path. You might use this method to prevent the user from dropping the item in an invalid location. For example, you might prevent the user from dropping the item in a specific section.

https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionViewDelegate_protocol/#//apple_ref/occ/intfm/UICollectionViewDelegate/collectionView:targetIndexPathForMoveFromItemAtIndexPath:toProposedIndexPath:
