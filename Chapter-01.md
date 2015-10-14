# How to use Git
　Git is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency.  
　Git is easy to learn and has a tiny footprint with lightning fast performance. It outclasses SCM tools like Subversion, CVS, Perforce, and ClearCase with features like cheap local branching, convenient staging areas, and multiple workflows.  

  對於版本控制，有幾種方式  

* Local Version Control Systems
* Centralized Version Control Systems
* Distributed Version Control Systems

## Short History of Git
> 此段擷取於 [Git offical website](https://git-scm.com/book/zh-tw/v2/%E9%96%8B%E5%A7%8B-Git-%E7%9A%84%E7%B0%A1%E5%8F%B2)

如同許多生命中偉大的事物一樣，Git 伴隨著一點點創造性破壞和熱烈的討論而生。  

Linux kernel 是規模相當大的開放原始碼軟體專案。 Linux kernel 在 1991 年到 2002 年間的維護工作，幾乎都是透過補丁和壓縮檔來完成的。 在 2002 年時，Linux kernel 開始採用名為 BitKeeper 的商業分散式版本控制系統。

在 2005 年時，開發 Linux kernel 的社群與開發 BitKeeper 的商業公司的合作關係結束，也就無法再免費使用該工具。 這就迫使了 Linux 社群（特別是 Linux 之父 Linus Torvalds）基於使用 BitKeeper 所學到的經驗，來開發自有的工具。 這個系統必須達成下列目標：

* 快速
* 簡潔的設計
* 完整支援非線性的開發（上千個同時進行的分支）
* 完全的分散式系統
* 能夠有效地處理像 Linux kernel 規模的專案（速度及資料大小）

從 Git 在 2005 年誕生後，現在的 Git 已相當成熟，也能很容易上手，並保持著最一開始的要求的品質。 它不可思議的快速、處理大型專案非常有效率、也具備相當優秀足以應付非線性開發的分支系統。

## Git basics
Git 運作上與其他版本控制系統主要差異在於。一般系統是記錄檔案更改的資訊，儲存一組基本的檔案和這些檔案隨時間遞增的更動資料。但是 Git 不是用這種方式儲存及看待這些資料， 而是將其視為小型檔案系統的一組快照（Snapshot）。 每當你提交（commit）（註：在 Git 儲存目前專案的狀態）時，Git 會紀錄下你所有目前檔案的樣子，並且參照到這次快照中。 為了講求效率，只要檔案沒有變更，Git 不會再度儲存該檔案，而是直接將上一次相同的檔案參照到這次快照中。 Git 把它的資料視為一連串的快照。

## Git 能檢查完整性
在 Git 中所有的物件在儲存前都會被計算校驗碼（checksum）並以校驗碼參照物件。 這意謂著你不可能瞞著 Git 對任何檔案或目錄進行修改。 此功能內建在 Git 底層並整合到它的設計哲學。 Git 更能夠馬上察覺傳輸時的遺失或是檔案的毀損。

Git 用來計算校驗碼的機制稱為 SHA-1 雜湊演算法。 一個校驗碼是由 40 個 16 進位的字母（0–9 和 a–f）所組成，Git 會根據檔案的內容和資料夾的結構來計算。 一個 SHA-1 校驗碼看起來如下所示：

`24b9da6552252987aa493b52f8696cd6d3b00373`
你會 Git 中到處都看到校驗碼，因為校驗碼被 Git 到處使用。 事實上在 Git 的資料庫內，每個檔案都是用其內容的校驗碼來儲存，而不是使用檔名。

## 三種狀態
現在，請特別注意。 若你希望接下來的學習過程順利些，請務必記住以下這些關於 Git 的知識。 Git 會把你的檔案標記為三種主要的狀態：已提交（committed）、已修改（modified）及已暫存（staged）。 已提交代表這檔案己安全地存在你的本地端資料庫。 己修改代表這檔案已被修改但尚未提交到本地端資料庫。 已暫存代表這檔案將會被存到下次你提交的快照中。

這帶領我們到 Git 專案的三個主要區域：Git 資料夾、工作目錄（working directory）以及暫存區（staging area）。

## 設定 Git
在安裝了 git 後，Git 附帶一個名為`git config`的工具，能夠取得和設定 git。這些設定允許你控制 git 各方面的外觀和行為。這些參數被存放在下列三個地方：

1. `/etc/gitconfig`：包含該系統所有使用者和使用者 repositories 的預設設定。如果傳遞`--system`參數給 git config，會明確地從這個檔案讀取或寫入設定。
2. `~/.gitconfig`,`~/.config/git/config`：個人使用者專用的設定。傳遞`--global`，就可以讀取或寫入設定。
3. 任何 repositories 中的 Git 資料夾的`config`檔案（位於`.git/config`）：這個 repository 的專用設定。

每個層級的設定皆覆蓋先前的設定，所以在`.git/config`的設定優先權高於在`/etc/gitconfig`裡的設定。

### 設定識別資料
在安裝 Git 後首先要做的事是設定使用者名稱和 email 地址。因為每次 Git 的提交會使用這些資訊，而且提交後不能再被修改。

	$ git config --global user.name "John Doe"
	$ git config --global user.email johndoe@example.com

若想指定不同的名字或電子郵件給特定的專案，只需要在該專案目錄內執行此命令，並確定未加上`--global`參數。

### 設定編輯器
現在你的識別資料已設定完畢，讀者可設定預設的文書編輯器，當 Git 需要你輸入訊息時會使用它。 預設情況下，Git 會使用系統預設的編輯器，一般來說是 Vim。 若你想指定不同的編輯器，例如：Emacs，可以執行下列指令：

`$ git config --global core.editor emacs`

### 檢查使用者設定
若你想檢查設定值，可使用`git config --list`命令列出所有 Git 在目前位置能找到的設定值：

	$ git config --list
	user.name=John Doe
	user.email=johndoe@example.com
	color.status=auto
	color.branch=auto
	color.interactive=auto
	color.diff=auto
	...
你可能會看到同一個設定名稱出現多次，因為 Git 從不同的檔案讀到同一個設定名稱（例如：`/etc/gitconfig`及`~/.gitconfig`）。 在這情況下，Git 會使用最後一個設定名稱的設定值。  
你也可以輸入`git config <key>`來檢視某個設定目前的值：

	$ git config user.name
	John Doe

### 取得 Git 說明文件
有三種取得 Git 命令說明文件的方法：

	$ git help <verb>
	$ git <verb> --help
	$ man git-<verb>