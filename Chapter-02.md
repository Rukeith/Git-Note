# Git Basics
## 取得 Git Repository
可使用兩種方式取得 Git Repository

* 將現有的專案或者目錄匯入 Git
若要開始使用 Git 追蹤現有的專案，只需要進入該專案的目錄並執行：  q
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

## 復原
### 更動最後一筆 commit
最常見的復原發生在太早提交更新，也許忘了加入某些檔案、或者搞砸了提交的訊息。 若想要試著重新提交，可試著加上`--amend`選項：  
`$ git commit --amend`

此命令取出 stage area 資料並用來做本次的提交。只要在最後一次提交後沒有做過任何修改（例如：在上一次提交後，馬上執行此命令），那麼整個 snapshot 看起來會與上次提交的一模一樣，唯一有更動的是提交時的訊息。使用者可像往常一樣編輯這些訊息，差別在於它們會覆蓋上一次提交。

如下例，若提交了更新後發現忘了一併提交某些檔案，可執行最後一個命令：

	$ git commit -m 'initial commit'
	$ git add forgotten_file
	$ git commit --amend

### 取消已被暫存的檔案
修改兩個檔案，並想要以兩個不同的更新提交它們，不過不小心執行`git add *`將它們同時都加入暫存區。 應該如何將其中一個移出暫存區？`git status`命令已附上相關的提示：

	$ git add .
	$ git status
	On branch master
	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)
	
	        modified:   README.txt
	        modified:   benchmarks.rb

在 “Changes to be commited” 文字下方，註明著使用 `“git reset HEAD <file>...，將 file 移出暫存區”`。 因此，讓我們依循該建議將`benchmarks.rb`檔案移出暫存區：

	$ git reset HEAD benchmarks.rb
	Unstaged changes after reset:
	M       benchmarks.rb

>`git reset`如果你使用了`--hard`，則可能是相當危險的命令，則可能會更動到全部的工作目錄。

### 復原已被更動的檔案
如何做才能很容易的復原為最後一次提交的狀態（或者最初複製儲存庫時、或放到工作目錄時的版本）  
`git checkout -- <file>`

> 需要特別注意的是，在 Git 中的提交都是可以復原的。即使是分支中的 commit 被刪除或被`--amend`複寫，只有未被提交的則幾乎無法救回。

## Working with Remotes
### 與遠端協同工作
想要在任何Git控管的專案協同作業，需要瞭解如何管理遠端的儲存庫。 遠端儲存庫是置放在網際網路或網路其它地方中的專案版本。 讀者可設定多個遠端儲存庫，具備唯讀或可讀寫的權限。 與他人協同作業時，需要管理這些遠端儲存庫，並在需要分享工作時上傳或下載資料。 管理遠端儲存庫包含瞭解如何新增遠端儲存庫、移除已失效的儲存庫、管理許多分支及定義是否要追蹤它們等等。 本節包含如何遠端管理的技巧。

### Showing Your Remotes
如果想要知道目前所有的 remote repository，可執行`git remote`命令。它會列出當初加入 remote repository 時指定的名稱。若目前所在 repository 是從其它 repository 複製過來的，至少應該看到 origin，也就是 Git 複製儲存庫時預設名字：

	$ git clone git://github.com/schacon/ticgit.git
	Cloning into 'ticgit'...
	remote: Reusing existing pack: 1857, done.
	remote: Total 1857 (delta 0), reused 0 (delta 0)
	Receiving objects: 100% (1857/1857), 374.35 KiB | 193.00 KiB/s, done.
	Resolving deltas: 100% (772/772), done.
	Checking connectivity... done.
	$ cd ticgit
	$ git remote
	origin


再加上`-v`參數，將會在名稱後方顯示其URL：

	$ git remote -v
	origin  git://github.com/schacon/ticgit.git (fetch)
	origin  git://github.com/schacon/ticgit.git (push)

若有一個以上遠端儲存庫，此命令會全部列出：
 
	$ git remote -v
	bakkdoor  git://github.com/bakkdoor/grit.git
	cho45     git://github.com/cho45/grit.git
	defunkt   git://github.com/defunkt/grit.git
	koke      git://github.com/koke/grit.git
	origin    git@github.com:mojombo/grit.git

### Adding Remote Repositories
欲新增遠端儲存庫並取一個簡短的名字，執行`git remote add [shortname] [url]`：

	$ git remote
	origin
	$ git remote add pb git://github.com/paulboone/ticgit.git
	$ git remote -v
	origin  git://github.com/schacon/ticgit.git
	pb  git://github.com/paulboone/ticgit.git

現在可看到命令列中的`pb`字串取代了整個 URL。 例如，若想取得 Paul 上傳的且本地端儲存庫沒有的更新，可執行`git fetch pb`：

	$ git fetch pb
	remote: Counting objects: 58, done.
	remote: Compressing objects: 100% (41/41), done.
	remote: Total 44 (delta 24), reused 1 (delta 0)
	Unpacking objects: 100% (44/44), done.
	From git://github.com/paulboone/ticgit
	 * [new branch]      master     -> pb/master
	 * [new branch]      ticgit     -> pb/ticgit

現在可在本地端使用`pb/master`存取 Paul 的 master 分支。 

### Fetching and Pulling from Your Remotes
欲從遠端擷取資料，可執行：  
`$ git fetch [remote-name]`

此命令到該遠端專案將所有本地端沒有的資料拉下來。若複製了一個儲存庫，會自動將該遠端儲存庫命令為 *origin*。 因此`git fetch origin`取出所有在複製或最後一下擷取後被上傳到該儲存庫的更新。 需留意的是`fetch`命令僅僅將資料拉到本地端的儲存庫，並未自動將它合併進來，也沒有修改任何目前工作的項目。 使用者得在必要時將它們手動合併進來。

若使用者設定一個會追蹤遠端分支的分支，可使用`git pull`命令自動擷取及合併遠端分支到目錄的分支。這或許是較合適的工作流程。而且`git clone`命令預設情況下會自動設定本地端的 master 分支追蹤被複製的遠端儲存庫的 master 分支。（假設該儲存庫有 master 分支）執行`git pull`一般來說會從當初複製時的來源儲存庫擷取資料並自動試著合併到目前工作的版本。

### Pushing to Your Remotes
`git push [remote-name] [branch-name]`

若想要上傳 master 分支到 origin 伺服器
`$ git push origin master`

此命令只有在被複製的伺服器開放寫入權限給使用者，而且同一時間內沒有其它人在上傳。 若讀者在其它同樣複製該伺服器的使用者上傳一些更新後上傳到上游，該上傳動作將會被拒絕。 讀者必須先將其它使用者上傳的資料拉下來並整合進來後才能上傳。 

### Inspecting a Remote
若讀者想取得遠端儲存庫某部份更詳盡的資料，可執行`git remote show [remote-name]`。若執行此命令時加上特定的遠端名字，比如說：`origin`。 會看到類似以下輸出：

	$ git remote show origin
	* remote origin
	  URL: git://github.com/schacon/ticgit.git
	  Remote branch merged with 'git pull' while on branch master
	    master
	  Tracked remote branches
	    master
	    ticgit

它將同時列出遠端儲存庫的 URL 位置和追蹤分支資訊。特別是告訴你如果你在 master 分支時用`git pull`時，會去自動抓取數據合併到本地的 master 分支。它也列出所有曾經被抓取過的遠端分支。

	$ git remote show origin
	* remote origin
	  URL: git@github.com:defunkt/github.git
	  Remote branch merged with 'git pull' while on branch issues
	    issues
	  Remote branch merged with 'git pull' while on branch master
	    master
	  New remote branches (next fetch will store in remotes/origin)
	    caching
	  Stale tracking branches (use 'git remote prune')
	    libwalker
	    walker2
	  Tracked remote branches
	    acl
	    apiv2
	    dashboard2
	    issues
	    master
	    postgres
	  Local branch pushed with 'git push'
	    master:master

這個指令顯示當你執行`git push`會自動推送的哪個分支(最後兩行)。它也顯示哪些遠端分支還沒被同步到本地端(在這個例子是caching)，哪些已同步到本地的遠端分支在遠端已被刪除(libwalker和walker2)，以及當執行`git pull`時會自動被合併的分支。

### Removing and Renaming Remotes
可以用`git remote rename`命令修改某個遠端儲存庫在本地的簡稱，舉例而言，想把 pb 改成 paul，可以執行下列指令：

	$ git remote rename pb paul
	$ git remote
	origin
	paul
	
若你想要移除某個 remote repository，則可以使用`git remote rm`：

	$ git remote rm paul
	$ git remote
	origin

## Tag
Git具備在特定時間點加入標籤去註明其重要性的功能。

### 列出 Tag
在 Git 中列出既有的 tag，可使用`git tag`，會以字母排序列出：

	$ git tag
	v0.1
	v1.3

可以針對特定的字串規則去搜尋 tag。例如只會 1.4.2 的 tag 感興趣，可以使用`-l`

	$ git tag -l 'v1.4.2.*'
	v1.4.2.1
	v1.4.2.2
	v1.4.2.3
	v1.4.2.4

### 建立 Tag
Git 使用兩大類的標籤：輕量級(lightweight)和含附註(annotated)。輕量級標籤就像是**沒有更動的分支**，實際上它僅是指到特定 commit 的指標。然而，含附註的標籤則是實際存在 Git 資料庫上的完整物件。它具備檢查碼、e-mail 和日期，也包含標籤訊息，並可以被GNU Privacy Guard (GPG)簽署和驗證。一般而言，我們都建議使用含附註的標籤以便保留相關訊息；但如果只是臨時加註標籤或不需要保留其他訊息，就是使用輕量級標籤的時機。

### 含附註 Tag
建立一個含附註的標籤很簡單。最容易的方法是加入`-a`到`tag`指令上：

	$ git tag -a v1.4 -m 'my version 1.4'
	$ git tag
	v0.1
	v1.3
	v1.4

`-m`選項用來設定標籤訊息。如果你沒有設定該訊息，Git 會啟動文字編輯器讓你輸入。
透過`git show`可看到指定標籤的資料與對應的 commit。

	$ git show v1.4
	tag v1.4
	Tagger: Scott Chacon <schacon@gee-mail.com>
	Date:   Mon Feb 9 14:45:11 2009 -0800
	
	my version 1.4
	
	commit 15027957951b64cf874c3557a0f3547bd83b3ff6
	Merge: 4a447f7... a6b4c97...
	Author: Scott Chacon <schacon@gee-mail.com>
	Date:   Sun Feb 8 19:02:46 2009 -0800
	
	    Merge branch 'experiment'
	    
### 簽署 Tag
假設你有私鑰(private key)，你也可以用GPG簽署在標籤上。只要用`-s`取代`-a`：

	$ git tag -s v1.5 -m 'my signed 1.5 tag'
	You need a passphrase to unlock the secret key for
	user: "Scott Chacon <schacon@gee-mail.com>"
	1024-bit DSA key, ID F721C45A, created 2009-02-09

### 輕量級 Tag
基本上就是只保存 commit 檢查碼的文件。要建立這樣的標籤，不必下任何選項，直接設定標籤名稱即可。

	$ git tag v1.4-lw
	$ git tag
	v0.1
	v1.3
	v1.4
	v1.4-lw
	v1.5

### 驗證 Tag
想要驗證已簽署的標籤，需要使用`git tag -v [tag-name]`。這個指令透過 GPG 去驗證簽章。而且在你的 keyring 中需要有簽署者的公鑰才能進行驗證：

	$ git tag -v v1.4.2.1
	object 883653babd8ee7ea23e6a5c392bb739348b1eb61
	type commit
	tag v1.4.2.1
	tagger Junio C Hamano <junkio@cox.net> 1158138501 -0700
	
	GIT 1.4.2.1
	
	Minor fixes since 1.4.2, including git-mv and git-http with alternates.
	gpg: Signature made Wed Sep 13 02:08:25 2006 PDT using DSA key ID F3119B9A
	gpg: Good signature from "Junio C Hamano <junkio@cox.net>"
	gpg:                 aka "[jpeg image of size 1513]"
	Primary key fingerprint: 3565 2A26 2040 E066 C9A7  4A7D C0C6 D9A4 F311 9B9A

如果沒有簽署者的公鑰，則會看到下列訊息：

	gpg: Signature made Wed Sep 13 02:08:25 2006 PDT using DSA key ID F3119B9A
	gpg: Can't check signature: public key not found
	error: could not verify the tag 'v1.4.2.1'

### 追加 Tag
你也可以對過去的commit上加入標籤。假設你的commit歷史如下：

	$ git log --pretty=oneline
	15027957951b64cf874c3557a0f3547bd83b3ff6 Merge branch 'experiment'
	a6b4c97498bd301d84096da251c98a07c7723e65 beginning write support
	0d52aaab4479697da7686c15f77a3d64d9165190 one more thing
	6d52a271eda8725415634dd79daabbc4d9b6008e Merge branch 'experiment'
	0b7434d86859cc7b8c3d5e1dddfed66ff742fcbc added a commit function
	4682c3261057305bdd616e23b64b0857d832627b added a todo file
	166ae0c4d3f420721acbb115cc33848dfcc2121a started write support
	9fceb02d0ae598e95dc970b74767f19372d61af8 updated rakefile
	964f16d36dfccde844893cac5b347e7b3d44abbc commit the todo
	8a5cbc430f1a9c3d00faaeffd07798508422908a updated readme

如果你之前忘了將 "updated rakefile" 這個 commit 加入 v1.2 標籤。仍然可在事後設定。要完成這個動作，你必須加入該次 commit 的檢查碼(或前幾碼即可)到以下指令：  
`$ git tag -a v1.2 9fceb02`

你可以看到標籤已經補上：

	$ git tag
	v0.1
	v1.2
	v1.3
	v1.4
	v1.4-lw
	v1.5
	
	$ git show v1.2
	tag v1.2
	Tagger: Scott Chacon <schacon@gee-mail.com>
	Date:   Mon Feb 9 15:32:16 2009 -0800
	
	version 1.2
	commit 9fceb02d0ae598e95dc970b74767f19372d61af8
	Author: Magnus Chacon <mchacon@gee-mail.com>
	Date:   Sun Apr 27 20:43:35 2008 -0700
	
	    updated rakefile
	...

### 分享 Tag
在預設的情況下，`git push`指令並不會將標籤傳到遠端伺服器上。當建立新標籤後，你必須特別下指令才會將它推送到遠端儲存庫上。類似將分支推送到遠端的過程，透過`git push origin [tagname]`指令。

	$ git push origin v1.5
	Counting objects: 50, done.
	Compressing objects: 100% (38/38), done.
	Writing objects: 100% (44/44), 4.56 KiB, done.
	Total 44 (delta 18), reused 8 (delta 1)
	To git@github.com:schacon/simplegit.git
	* [new tag]         v1.5 -> v1.5

若有許多 tag 要一次 push，可以加入`--tags`。當其他使用者clone或pull你的儲存庫時，他們也同時會取得所有你的標籤。

### 切換 Tag
You can’t really check out a tag in Git, since they can’t be moved around. If you want to put a version of your repository in your working directory that looks like a specific tag, you can create a new branch at a specific tag with `git checkout -b [branchname] [tagname]`:

	$ git checkout -b version2 v2.0.0
	Switched to a new branch 'version2'

Of course if you do this and do a commit, your `version2` branch will be slightly different than your `v2.0.0` tag since it will move forward with your new changes, so do be careful.

## Git alias
	$ git config --global alias.co checkout
	$ git config --global alias.br branch
	$ git config --global alias.ci commit
	$ git config --global alias.st status

某些情況下，會想要執行外部的命令，在要執行的命令前加上`!`。  
`$ git config --global alias.visual '!gitk'`