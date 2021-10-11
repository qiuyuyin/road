## merge

合并分支：将建立一个新的commit同时包含这两个分支中的操作

```
git merge newbranch
```

切记，如果要进行合并分支，必须要保证两个分支之间没有存在冲突，如果存在冲突时不可以进行分支合并的。

## rebase

另一种更为清晰的分支移动：

不过不是通过集成来得到分支结构则可以使用

## checkoutmore

```git
git checkout HEAD^|hash^
将head的指向向前移动
git checkout HEAD~4
将head的指向向前移动多个
强行修改分支位置(假设main是你的分支)
git branch -f main HEAD~3 	

```

## 撤销变更：

```
简单的回退
git reset
将之前的版本进行拷贝然后重新commit
git revert
```

