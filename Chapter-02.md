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
* ` M`：modified and unstaged
* `M `：modified and staged
* `MM`：modified, staged and then modified again

### Ignoring Files
有的時候會不希望讓 Git 追蹤某些檔案，這些通常是自動產生的檔案或是富有機密的檔案。使用者可以建立一個名為`.gitignore`檔案，列出符合這些檔案名稱的特徵。

	$ cat .gitignore
	*.[oa]
	*~

第一列告訴 Git 忽略任何檔名為`.o`或`.a`結尾的檔案，第二列告訴 Git 忽略所有檔名為`~`結尾的檔案，以此類推。

`.gitignore`的規則如下：  

* 空白列或者以`#`開頭的列會被忽略。
* 可使用標準的 Glob pattern。
* 可以`/`結尾，代表是目錄。
* 可使用`!`符號將特徵反過來使用。

Glob pattern 就像是 Shell 使用的簡化版正規運算式，`*`匹配零個或多個字元;`[abc]`匹配括弧內的任一字元(a、b、c);`?`匹配單一個字元;`[0-9]`匹配任何符合該範圍的字(0到9)。

	# 註解，會被忽略。
	# 不要追蹤檔名為 .a 結尾的檔案
	*.a
	# 但是要追蹤 lib.a，即使上方已指定忽略所有的 .a 檔案
	!lib.a
	# 只忽略根目錄下的 TODO 檔案。 不包含子目錄下的 TODO
	/TODO
	# 忽略build/目錄下所有檔案
	build/
	# 忽略doc/notes.txt但不包含doc/server/arch.txt
	doc/*.txt
	# 忽略在資料夾 doc/ 內所有以 .txt 結尾的檔案
	doc/**/*.txt

### Viewing Your Staged and Unstaged Changes
有些時候`git status`提供的資訊不夠詳細，但我們需要更詳細的資訊時可以使用`git diff`。可以顯示檔案的哪些部分被修改，和做了哪些修改。

* `git status`：  

		$ git status
		On branch master
		Changes to be committed:
		  (use "git reset HEAD <file>..." to unstage)
		
		        new file:   README
		
		Changes not staged for commit:
		  (use "git add <file>..." to update what will be committed)
		  (use "git checkout -- <file>..." to discard changes in working directory)
		
		        modified:   benchmarks.rb

* `git diff`：

		$ git diff
		diff --git a/benchmarks.rb b/benchmarks.rb
		index 3cb747f..da65585 100644
		--- a/benchmarks.rb
		+++ b/benchmarks.rb
		@@ -36,6 +36,10 @@ def main
		           @commit.parents[0].parents[0].parents[0]
		         end
		
		+        run_code(x, 'commits 1') do
		+          git.commits.size
		+        end
		+
		         run_code(x, 'commits 2') do
		           log = git.commits('master', 15)
		           log.size

此命令會比對 work directory 和 stage area，顯示尚未被存入 stage area 的變更。
若使用者比對 stage 及最後一次 commit 的差異，可用`git diff --staged`指令。

	$ git diff --staged
	diff --git a/README b/README
	new file mode 100644
	index 0000000..03902a1
	--- /dev/null
	+++ b/README
	@@ -0,0 +1 @@
	+My Project

值得注意的是`git diff`不會顯示最後一次 commit 後所有變更，只會顯示 unstaged 的變更。

### Committing Your Changes
最簡單的 commit 是執行`git commit`，所有 staged 的檔案都會被 commit。

	$ git commit -m "Story 182: Fix benchmarks for speed"
	[master 463dc4f] Story 182: Fix benchmarks for speed
	 2 files changed, 3 insertions(+)
	 create mode 100644 README

### Skipping the Staging Area
若使用者想跳過暫存區域，Git 提供了簡易的使用方式。在`git commit`後方加上`-a`參數，Git 自動將所有將所有已被追蹤且被修改的檔案送到 staged area 並開始 commit，讓使用者略過`git add`的步驟。

### Removing Files
要從 Git 刪除檔案，需要將檔案從已被追蹤檔案中移除，並且 commit。`git rm`除了完成此工作外，也會將該檔案從工作目錄移除。

### Moving Files
Git 並不像其他檔案控制系統一樣，明確地追蹤檔案的移動。因此 Git 存在`mv`指令會造成一點混淆，若想要在 Git 中更名某個檔案，可執行以下：  
`$ git mv file_from file_to`

檢視一下狀態，可看到 Git 認為該檔案被更名：

	$ git mv README.txt README
	$ git status
	On branch master
	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)
	
	        renamed:    README.txt -> README

相當於以下指令：

	$ mv README.txt README
	$ git rm README.txt
	$ git add README
	
## Viewing the Commit History
在 commit 數個更新後，或許會有需要查看之前所執行的 commit，這時就可以使用`git log`指令。

	$ git log
	commit ca82a6dff817ec66f44342007202690a93763949
	Author: Scott Chacon <schacon@gee-mail.com>
	Date:   Mon Mar 17 21:52:11 2008 -0700
	
	    changed the version number
	
	commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
	Author: Scott Chacon <schacon@gee-mail.com>
	Date:   Sat Mar 15 16:40:33 2008 -0700
	
	    removed unnecessary test code
	
	commit a11bef06a3f659402fe7563abf99ad00de2209e6
	Author: Scott Chacon <schacon@gee-mail.com>
	Date:   Sat Mar 15 10:31:28 2008 -0700
	
	    first commit

預設情況下，`git log`會以新到舊的順序列出 repository 提交的歷史記錄。 也就是說最新的更新會先被列出來。 同時也會列出每個更新的 SHA-1 查核值、作者大名及電子郵件地址、及提交時輸入的訊息。  

**常用參數：**  

* `-p`，用來顯示每個更新之間差別的內容。
* `-2(數字)`，限制只輸出最後兩個更新。
* `--word-diff`，用 word level 的方式取代 line level。變動的地方用內嵌的方式顯示，新增的自備包括在`{+ +}`，而刪除的則包括在`[- -]`內。若再加上`-U1`參數，則可以減少顯示的資訊到只顯示變動的那行。
* `--stat`，檢視每個更新的簡略統計資訊。
* `--pretty`，這個參數改變原本預設輸出的格式。有數個內建的選項供使用者選用。其中`online`選項將每一個更新印到單獨一行。其他還有`short`,`full`,`fuller`,`format`，輸出的格式大致相同，但會有些許的資訊差異。其中`format`允許使用者自訂輸出格式。

|選項|說明|
|---|---|
|`%H`|該更新的SHA1雜湊值|
|`%h`|該更新的簡短SHA1雜湊值|
|`%T`|存放該更新的根目錄的Tree物件的SHA1雜湊值|
|`%t`|存放該更新的根目錄的Tree物件的簡短SHA1雜湊值|
|`%P`|該更新的父更新的SHA1雜湊值|
|`%p`|該更新的父更新的簡短SHA1雜湊值|
|`%an`|作者名字|
|`%ae`|作者電子郵件|
|`%ad`|作者的日期 (格式依據 date 選項而不同)|
|`%ar`|相對於目前時間的作者的日期|
|`%cn`|提交者的名字|
|`%ce`|提交者的電子郵件|
|`%cd`|提交的日期|
|`%cr`|相對於目前時間的提交的日期|
|`%s`|標題|

* `--graph`，以 ASCII 畫出分支的分歧及合併的歷史，配合`online`或`format`時特別有用。
* `--shortstat`，僅顯示--stat提供的的訊息中關於更動、插入、刪除的文字。
* `--name-only`，在更新的訊息後方顯示更動的檔案列表。
* `--name-status`，顯示新增、更動、刪除的檔案列表。
* `--abbrev-commit`，僅顯示SHA1查核值的前幾位數，而不是顯示全部的40位數。
* `--relative-date`，以相對於目前時間方式顯示日期（例如：“2 weeks ago”），而不是完整的日期格式。
* `--oneline`，`--pretty=oneline --abbrev-commit`的簡短用法。

### Limiting Log Output

|選項|選項的說明文字|
|---|---|
|`-(n)`|僅顯示最後 n 個更新|
|`--since`,`--after`|列出特定日期後的更新。|
|`--until`,`--before`|列出特定日期前的更新。|
|`--author`|列出作者名稱符合指定字串的更新。|
|`--committer`|列出提交者名稱符合指定字串的更新。|
|`--grep`|列出以關鍵字搜尋提交的訊息(若要對比多個字串，需加上`--all-match`，否則只會列出符合任一條件的更新)|
|`-S`|Only show commits adding or removing code matching the string|