# Git branch
## Branches in a Nutshell
為了理解 Git 分支的實現方式，我們需要回顧一下 Git 是如何儲存資料的。Git 保存的不是檔差異或者變化量，而只是一系列檔快照。

在 Git 中提交時，會保存一個提交（commit）物件，該物件包含一個指向暫存內容快照的指標，包含本次提交的作者等相關附屬資訊，包含零個或多個指向該提交物件的父物件指標：首次提交是沒有直接祖先的，普通提交有一個祖先，由兩個或多個分支合併產生的提交則有多個祖先。

為直觀起見，我們假設在工作目錄中有三個檔，準備將它們暫存後提交。暫存操作會對每一個檔計算校驗和（即第一章中提到的 SHA-1 雜湊字串），然後把當前版本的檔快照保存到 Git 倉庫中（Git 使用 blob 類型的物件存儲這些快照），並將校驗和加入暫存區域：

	$ git add README test.rb LICENSE
	$ git commit -m 'initial commit of my project'

-- Todo

## 建立分支
Git 保存著一個名為 HEAD 的特別指標，指向正在工作中的本地分支。  
使用`git branch <branch name>`則可以建立新分支，但是不會切換過去。  
使用`git checkout <branch name>`則可以切換到該分之，此時 HEAD 就指向該分支。

可以使用`git log --decorate`顯示出該分支指向哪裡。
使用`git log --oneline --decorate --graph --all`將會指出當前分支指向的地方和歷史的分歧

## 分支的新建與合併
當想要建立新分支的同時切換到該分支的話，`git checkout -b <branch name>`  
當在切換分支時，Git 可能會因為衝突而阻止切換。在切換分支時最好保持一個乾淨的工作區域。  

可以使用`git merge`進行合併。

	$ git checkout master
	$ git merge hotfix
	Updating f42c576..3a0874c
	Fast-forward
	 README | 1 -
	 1 file changed, 1 deletion(-)

`Fast forward`：當前分支是要併入分支的直接上游，兩者間不存在需要解決的衝突，Git 只需要簡單地推進即可，這種過程稱為 Fast forward。  

當分支結束任務後，可以使用`git branch -d <branch name>`進行刪除。

在合併分支時，且兩個分支皆有自己的 commit 不能 fast forward 的合併時。Git 會自己裁決如何最佳合併，並自動合併重新創建一個 commit。

### 合併衝突
如果在不同分支中修改了同一份檔案的同一部分，則很可能就會發生了衝突。
類似以下的資訊：

	$ git merge iss53
	Auto-merging index.html
	CONFLICT (content): Merge conflict in index.html
	Automatic merge failed; fix conflicts and then commit the result.
	
在解決衝突後，要使用`git add`則可以將檔案標記為已解決。如果想用有 UI 的工具，可以使用`git mergetool`，會調用一個是覺化的合併工具並引導解決衝突。