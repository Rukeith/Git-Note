# Git Basics
## 取得 Git Repository
可使用兩種方式取得 Git Repository

* 將現有的專案或者目錄匯入 Git
若要開始使用 Git 追蹤現有的專案，只需要進入該專案的目錄並執行：  
`$ git init`

此指令會建立名為`.git`的子目錄，該目錄包含一個 Git Repository 必要的所有檔案。目前來說，專案內任何檔案都還沒有被追蹤。使用者能以少數的`git add`命令指定要追蹤的檔案，並提交：

	$ git add *.c
	$ git add README
	$ git commit -m 'initial project version'

* 從其他伺服器複製一份已存在的 Git Repository
若想要取得現有的 Git Repository 的複本，那需要使用的是`git clone`。此方式可以取得該專案歷史中所有檔案的所有版本。  
也可以使用`git clone [url]`，複制單一個 Repository 。例如：
`$ git clone git://github.com/schacon/grit.git`

接下來會有個名為`grit`的目錄被建立，並在旗下初始化名為`.git`的目錄。複製所以存在該  Repository 的所有資料，並取得最新版本為工作複本。不過也可以指定目錄的名字。
`$ git clone git://github.com/schacon/grit.git mygrit`

## Recording Changes to the Repository
在工作目錄內的每個檔案可能為兩種狀態的任一種： tracked 或 untracked。tracked 是最近的 snapshot ;可以被復原、修改或者暫存。untracked 則是其他未在最近 snapshot 也未被暫存的任何檔案。第一次複製 repository 食，所有檔案都是被追蹤且未被修改的。只要編輯任何已被 tracked 的檔案，Git 將視為被更動的。

![The lifecycle of the status of your files.](lifecycle.png)

### Checking the Status of Your Files
使用`git status`可以知道每個檔案是屬於哪種狀態。如果是在 clone 之後執行此指令，你應該會看到以下：

	$ git status
	On branch master
	nothing to commit, working directory clean
	
Wokring directory clean 意味著目前的工作目錄沒有未被追蹤或已被修改的檔案。這個指令告訴使用者在哪一個 branch 上。

### Tracking New Files
要追蹤新增的檔案，我們可以使用`git add`。例如：  
`$ git add README`

之後將可看到 README 檔案被列入追蹤且被暫存(tracked and staged to be commited)：

	$ git status
	On branch master
	Changes to be committed:
	  (use "git reset HEAD &lt;file&gt;..." to unstage)
	
	    new file:   README

因為它被放在 Changes to be commited 下方，可得知已被暫存起來。若此時提交更新，剛才執行`git add`加進來的檔案就會被記錄在歷史的 snapshot。可回想一下先前執行`git init`後也有執行過`git add`，開始追蹤目錄內的檔案。`git add`命令可接受檔名或者目錄名。 若是目錄名，Git 會以遞迴(recursive)的方式會將整個目錄下所有檔案及子目錄都加進來。

### Staging Modified Files
若是修改先前已被追蹤的檔案，名為`benchmarks.rb`。並且檢查狀態，會看到類似以下文字：

	$ git status
	On branch master
	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)
	
	        new file:   README
	
	Changes not staged for commit:
	  (use "git add <file>..." to update what will be committed)
	  (use "git checkout -- <file>..." to discard changes in working directory)
	
	        modified:   benchmarks.rb

這兩個檔案目前都被暫存起來，而且會進入下一次的提交。 假設仍需要對 benchmarks.rb 做一點修改後才要提交，可再度開啟並編輯該檔案。然而，當我們再度執行git status：

	$ vim benchmarks.rb
	$ git status
	On branch master
	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)
	
	        new file:   README
	        modified:   benchmarks.rb
	
	Changes not staged for commit:
	  (use "git add <file>..." to update what will be committed)
	  (use "git checkout -- <file>..." to discard changes in working directory)
	
	        modified:   benchmarks.rb

現在 benchmarks.rb 同時被列在 staged 及 unstaged。這表示 Git 的確在讀者執行`git add`命令後，將檔案暫存起來。若現在提交更新，最近一次執行`git add`命令時暫存的`benchmarks.rb`會被提交。若在`git add`後修改檔案，需要再度執行`git add`將最新版的檔案暫存起來：

	$ git add benchmarks.rb
	$ git status
	On branch master
	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)
	
	        new file:   README
	        modified:   benchmarks.rb

### Short Status
Git 提供簡短的狀態標籤，可以用更簡短的方式來查看狀態。行`git status -s`或`git status --short`

	$ git status -s
	 M README
	MM Rakefile
	A  lib/git.rb
	M  lib/simplegit.rb
	?? LICENSE.txt

* `??`：untracked
* `A`：staged
* `M`：modified and staged
* `MM`：modified, staged and then modified again

### Ignoring Files
