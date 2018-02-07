---
layout: tutorial_hands_on
topic_name: introduction
tutorial_name: galaxy-intro-peaks2genes
---

# イントロダクション
{:.no_toc}

先日、Cell Stem Cellに2012年にLi氏らが投稿した論文で、マウスにある興味深いタンパク質の標的遺伝子を解析することに成功したという内容を読みました[(Li et al., Cell Stem Cell 2012)](https://www.ncbi.nlm.nih.gov/pubmed/22862943)。この標的遺伝子の解析にはChIP-seqが用いられていて、結果のデータは [GEO](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE37268)にて手に入れることができるのですが、この入手できるデータは論文の補足でも、GEOに提出された形にもなっておらず、シグナルが顕著な部分を表した（いわゆるピークの）データでした。

1 | 3660676 | 3661050 | 375 | 210 | 62.0876250438913 | -2.00329386666667
1 | 3661326 | 3661500 | 175 | 102 | 28.2950833625942 | -0.695557142857143
1 | 3661976 | 3662325 | 350 | 275 | 48.3062708406486 | -1.29391285714286
1 | 3984926 | 3985075 | 150 | 93 | 34.1879823073944 | -0.816992
1 | 4424801 | 4424900 | 100 | 70 | 26.8023246007435 | -0.66282

**表1** 入手できるデータのサブサンプル

というわけで、今回は**このゲノム領域のリストを、標的遺伝子が何か分かるような形に変える**ことを目標としてやっていきたいと思います。

> ### テーマ
>
> 今回のチュートリアルでは以下のものを扱います。
>
> 1. TOC
> {:toc}
>
{: .agenda}

# はじめに

まずはGalaxyを開いて、ログインしましょう（まだの方は登録しましょう）。

Galaxyのインタフェースは以下のような3つの部分によって主に構成されていて、左側が使用出来るツールを並べたリストで、右側が解析した結果がヒストリーとして記録される場所、そして中央がツールやデータセットを操作する場所となっています。

![Galaxy interface](../../images/galaxy_interface.png)

それでは、新しいヒストリーを使ってチュートリアルを始めましょう。

> ### {% icon hands_on %} ハンズオン:ヒストリーを作成する
>
> 1. 何も解析していない空のヒストリーがあることを確認する
>
>    > ### {% icon tip %} Starting a new history
>    >
>    > * Click the **Gear** icon at the top of the history panel
>    > * Select the option **Create New** from the menu
>    {: .tip}
>
> 2. ヒストリーの名前を分かりやすいものに変える
>
>    > ### {% icon tip %} Rename a history
>    >
>    > * Click on the title of the history (by default the title is *Unnamed history*)
>    >
>    >   ![Renaming history](../../../../shared/images/rename_history.png)
>    >
>    > * Type **Galaxy Introduction** as the name
>    >
>    {: .tip}
>
{: .hands_on}

## データをアップロードする

> ### {% icon hands_on %} ハンズオン:データをアップロードする
>
> 1.  [GEO](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE37268)からピーク領域のリスト(ファイルはこちら [`GSE37268_mof3.out.hpeak.txt.gz`](https://www.ncbi.nlm.nih.gov/geo/download/?acc=GSE37268&format=file&file=GSE37268%5Fmof3%2Eout%2Ehpeak%2Etxt%2Egz))をPCにダウンロードする 
> 2. インタフェースの左上にあるアップロードボタンをクリックする
>
>    ![Upload icon](../../images/upload_button.png)
>
>
> 3. **Choose local file** を選択し、ダウンロードしたファイルを探す
>
> 4. **Type** を `interval`にする
>
> 5. **Start** を押し、アップロードが完了するのを待つ    
>    
>     Galaxy が自動でファイルを展開する。
>
>     > ### {% icon comment %} Comment
>     > After this you will see your first history item in Galaxy’s right pane. It will go through
>     > the gray (preparing/queued) and yellow (running) states to become green (success):
>     >
>     > ![History section](../../images/intro_01.png)
>     {: .comment}
>
>    > ### {% icon tip %} Tip: Importing data via links
>    >
>    > * Copy the link location
>    > * Open the Galaxy Upload Manager
>    > * Select **Paste/Fetch Data**
>    > * Paste the link into the text field
>    > * As **Type** select `interval`
>    > * Press **Start**
>    {: .tip}
>
>    > ### {% icon tip %} Tip: Changing the file type once the data file is in your history
>    >
>    > * Click on the pencil button displayed in your dataset in the history
>    > * Choose **Datatype** on the top
>    > * Select `interval` in this case
>    > * Press **Save**
>    {: .tip}
>
>    デフォルトでは、Galaxyはリンクを名前にします。また、データセットをデータベースや参照ゲノムにリンクしません。
>
>    > ### {% icon comment %} Comments
>    > - Check that the database of your uploaded dataset is mm9. If not, click on the pencil icon and modify the Database/Build: field to Mouse July 2007 (NCBI37/mm9) (mm9).
>    > - Rename the datasets according to the samples
>    {: .comment}
>
{: .hands_on}

ピーク領域に関連する遺伝子を見つけるためには、
UCSCから手に入る、マウスの遺伝子のリストが他に必要となります。

> ### {% icon hands_on %} ハンズオン: UCSCからデータをアップロードする
>
> 1. ツールのメニューから、 `Get Data -> UCSC Main - table browser` へと移動する。
>
>     ![UCSC Main tool in tools section](../../images/101_01.png)
>
>     You will be taken to the **UCSC table browser**, which looks something like this:
>
>     ![UCSC table browser interface](../../images/intro_02.png)
>
> 2. 以下のようにオプションを設定する:
>     - **clade** to `Mammal`
>     - **genome** to `Mouse`
>     - **assembly** to `July 2007 (NCBI37/mm9)`
>     - **group** to `Genes and Gene Predictions`
>     - **track** to `RefSeq Genes`
>     - **table** to `refGene`
>     - **region** to `genome`
>     - **output format** to `BED - browser extensible data`
>     - **Send output to** to `Galaxy` checked
>
> 3. **get output** ボタンをクリックする
>
>    ボタンをクリックすると以下のようなスクリーンが表示される:
>
>    ![Output settings](../../images/intro_03.png)
>
> 4. **Create one BED record per** の欄を見て、 `Whole Gene` がチェックされているかを確認した後、 **Send Query to Galaxy** ボタンをクリックする
>
> 5. Rename our dataset to something more recognizable
>    - Click on the **pencil icon** to edit a file's attributes.
>      ![Pencil icon](../../images/edit_icon.png)
>    - In the next screen change the name of the dataset to `Genes`.
>    - Click the **Save** button at the bottom of the screen.
>
{: .hands_on}

> ### {% icon comment %} BEDファイル形式について
> **BED - Browser Extensible Data** 形式は遺伝子をコード化する領域を上手く表示する形式です。BEDラインには以下の3つの位置情報が必要です。:
> - 染色体のID
> - 染色体や足場での塩基の開始地点 (最初の塩基を0とする)
> - 終了地点 (最後の塩基を除く)
>
> この3つの必須な位置情報に加えて最大9つのオプションの位置情報がありますが、1行あたりの位置情報の数は1つのデータセット全体を通して統一しなければなりません。
>
> オプションの位置情報の内容も含めたBEDのより詳しい情報は[UCSC](https://genome.ucsc.edu/FAQ/FAQformat#format1) で得ることができます。
{: .comment}

これで、解析を開始するために必要なすべてのデータを揃えることができました。

# Part 1: 基本的なやり方

## ファイルの準備

それでは、実際にどのような内容のデータを持っているか見るためにファイルを見てみましょう。

> ### {% icon hands_on %} ハンズオン: ファイルの内容を表示する
>
> 1. ピーク領域のファイルを表示するには、 **目のアイコン**をクリックする。クリックすると以下の図が現れる。:
>
>    ![Contents of the peak file](../../images/intro_04.png)
>
> 2. UCSCから得た遺伝子領域のデータの内容が表示される。
>
>    ![Contents of UCSC file](../../images/intro_05.png)
>
{: .hands_on}

> ### {% icon question %} 問題
>
> UCSCのファイルには列のラベルが付いていますが、ピークのファイルにはラベルが付いてありません。どうすればラベルなしの状態でそれぞれの列が何の列か推測できるでしょうか。
>
{: .question}

このピークのファイルは一般的な形式ではなく、このファイルを見るだけではそれぞれの列が何を表しているか判断できません。今回挙げた論文の著者は [HPeak](https://www.ncbi.nlm.nih.gov/pubmed/20598134) と呼ばれるピークを用いていると述べています。

HPeakのマニュアルを見ると、列に以下のような情報が含まれていることがわかります。:

 - 番号で表されている染色体の名前
 - 開始座標
 - 終了座標
 - 染色体の長さ
 - 最も高い仮説的なDNAフラグメント（頂上）の範囲を含むピーク内の位置。

2つのファイルを比べるには、染色体の名前が同じ形式で表されていることを確認する必要があります。
見てわかるように、ピークのファイルでは染色体番号の前に `chr` が欠けています。しかし、20番染色体と21番染色体ではどのように判断すればよいでしょうか。またはX染色体とY染色体では？確認してみましょう。:

> ### {% icon hands_on %} ハンズオン: ファイルの末尾を表示する
>
> 1. **末尾を選択する** {% icon tool %}: 以下の設定を行った上で **Select last lines from a dataset (tail)** を走らせる:
>     - **Text file** to our peak file `GSE37268_mof3.out.hpeak.txt`
>     - **Operation**: `Keep last lines`
>     - **Number of lines**: Choose a value, e.g. `100`
> 2. **Execute** をクリックする
> 3. 作業が終了するまで待機する
> 4. **目のアイコン** をクリックしてファイルを見る
>
>    > ### {% icon question %} 問題
>    >
>    > 1. Are the chromosomes 20 and 21 named X and Y?
>    >
>    >    <details>
>    >    <summary>Click to view answers</summary>
>    >    <ol type="1">
>    >    <li>Not at all. One more thing to fix.</li>
>    >    </ol>
>    >    </details>
>    {: .question}
{: .hands_on}

したがって、染色体の名前を変換するには以下の2つの工程を踏む必要があります。:

 - `chr` を加える
 - 20 と 21 を X と Y に変える

> ### {% icon hands_on %} ハンズオン: 染色体の名前を調整する
>
> 1. **テキストを置き換える** {% icon tool %}: 以下の設定を行った上で **Replace Text in a specific column** を走らせる:
>     - **File to process** to our peak file `GSE37268_mof3.out.hpeak.txt`
>     - **in column**: `Column:1`
>     - **Find pattern**: `[0-9]+` (this will look for numerical digits)
>     - **Replace with**: `chr&` (`&` is a placeholder for the find result)
> 3. **テキストを置き換える** {% icon tool %}: 上で使ったツールをもう一度走らせてみよう
>    - **File to process** to the output from the last run, e.g. something like `Replace Text on data ...`
>    - **in column**: `Column:1`
>    - **Find pattern**: `chr20`
>    - **Replace with**: `chrX`
>
>    > ### {% icon tip %} Tip: Rerunning a tool
>    >
>    > * Press the **rerun icon** in the history
>    {: .tip}
>
> 4. **Replace Text** {% icon tool %}: Rerun this tool accordingly for chromosome Y
> 5. Inspect the latest file through the **eye icon**
>
>    Have we been successful?
>
{: .hands_on}

現段階でGalaxyに沢山のファイルがあるため、それぞれのファイルが区別できなくならないように注意しなければなりません。なので、最新の結果のファイルを例えば `Peak regions` などといった分かりやすい名前に変えておきましょう。


## 解析

今回の目標は、2つの遺伝子領域のファイル(遺伝子のファイルと出版物のファイル)を比較して、どのピークがどの遺伝子に関連しているかを知ることです。
もしあなたが、遺伝子**内**にどのようなピークがあるのかを知りたいだけであれば、このステップをスキップすることができます。
もしそうでなければ、ChIP-seqの実験で転写因子を入れる必要があるなどの理由があるため、このステップを通してプロモーター領域を比較に加えた方が良いでしょう。

> ### {% icon hands_on %} ハンズオン: 遺伝子レコードにプロモーター領域を加える
>
> 1. **Get Flanks** {% icon tool %}: 以下の設定を行った上で**Get flanks returns flanking region/s for every gene** を走らせる:
>     - **Select data** to the file from UCSC
>     - **Region** to `Around Start`
>     - **Location of the flanking region/s** to `Upstream`
>     - **Offset** to `10000`
>     - **Length of the flanking region(s)** to `12000`
>
>     This tool returns flanking regions for every gene
>
> 2. BEDファイルの結果の行とインプットを比べて、開始地点と終了地点の変更方法を見つける
>
>    > ### {% icon tip %} Tip: Inspecting several files using the scratchbook
>    >
>    > * Click **Enable/Disable Scratchbook** on the top panel
>    >
>    >    ![Enable/Disable Scratchbook](../../images/intro_scratchbook_enable.png)
>    >
>    > * Click on the **eye** icon of the files to inspect
>    > * Click on **Show/Hide Scratchbook**
>    >
>    >    ![Show/Hide Scratchbook](../../images/intro_scratchbook_show_hide.png)
>    {: .tip}
>
> 3. データセットの名前を変更して結果を反映させる
{: .hands_on}

そろそろUCSCのファイルが `BED` 形式であり、それに関連したデータベースを持っていることに気付いたかもしれません。このファイルこそがピークファイルのために必要になっているのです。

> ### {% icon hands_on %} ハンズオン: 形式とデータベースを変更する
>
> 1. ピーク領域のファイルにある **鉛筆アイコン** をクリックする:
>      ![Pencil icon](../../images/edit_icon.png)
> 2. Switch to the `Convert Format` tab
> 3. Select `Convert Genomic Intervals To BED` and press **Convert**
> 4. Edit the "Database/Build" to select "mm9", the database build for mice used in the paper
{: .hands_on}

これで重複している部分を探す段階に来ました (ついに!)。そのためには、ピークと重複している遺伝子や交差している遺伝子を抽出する必要があります。

> ### {% icon hands_on %} ハンズオン: 重複部分を探す
>
> 1. **Intersect** {% icon tool %}: 以下の設定を行った上で **Intersect the intervals of two datasets** を走らせる:
>     - **Return** to `Overlapping Intervals`
>     - **of**: the UCSC file with promoter regions
>     - **that intersect**: our converted peak region file
>     - **for at least**: `1`
>
>    > ### {% icon comment %} Comments
>    > 入力する順番はとても大事です！今回の目標としては遺伝子のリストで終わりたいので、対応するデータセットを最初に入力する必要があります。
>    {: .comment}
{: .hands_on}

We now have the list of genes (column 4) overlapping with the peak regions.
To get a better overview of the genes we obtained, we want to look at their distribution across the different chromosomes.
We will regroup the table by chromosome and count the number of genes with peaks on each chromosome

> ### {% icon hands_on %} Hands-on: Count genes on different chromosomes
>
> 1. **Group** {% icon tool %}: Run **Group data by a column and perform aggregate operation on other columns** with the following settings:
>     - **Select data** to the result of the intersection
>     - **Group by column**:`Column 1`
>     - Press **Insert Operation** and choose:
>     - **Type**: `Count`
>     - **On column**: `Column 1`
>     - **Round result to nearest integer?**: `No`
>
>    > ### {% icon question %} Questions
>    >
>    > Which chromosome contained the highest number of target genes?
>    >
>    >    <details>
>    >    <summary>Click to view answers</summary>
>    >    <ol type="1">
>    >    <li>The result varies with different settings. If you followed step by step, it should be chromosome 7 with 1675 genes.</li>
>    >    </ol>
>    >    </details>
>    {: .question}
>
{: .hands_on}

## Visualization

Since we have some nice data, let's draw a barchart out of it!

> ### {% icon hands_on %} Hands-on: Draw barchart
>
> 1. Select the **Visualize icon** at the latest history item and select `Charts`
> 2. Choose a title at **Provide a title**, e.g. `Gene counts per chromosome`
> 3. Switch to the **Select data** tab and play around with the settings
> 4. Press **Visualize** and the top right to inspect your result
> 5. Click on **Editor** and repeat with different settings
>
{: .hands_on}

## Extracting workflow

When you look carefully at your history, you can see that it contains all steps of our analysis, from the beginning to the end. By building this history we have actually built a complete record of our analysis with Galaxy preserving all parameter settings applied at every step.
Wouldn't it be nice to just convert this history into a workflow that we'll be able to execute again and again?

Galaxy makes this very simple with the `Extract workflow` option. This means that any time you want to build a workflow, you can just perform it manually once, and then convert it to a workflow, so that next time it will be a lot less work to do the same analysis. It also allows you to easily share or publish your analysis.

> ### {% icon hands_on %} Hands-on: Extract workflow
>
> 1. **Clean up** your history
>
>    If you had any failed jobs (red), please remove those datasets from your history by clicking on the `x` button. This will make the creation of a workflow easier.
>
> 2. Go to the history **Options menu** (gear symbol) and select the `Extract Workflow` option.
>
>    ![Extracting workflow in history menu](../../images/history_menu_extract_workflow.png)
>
>    The center panel will change and you will be able to choose which steps to include/exclude and how to name the newly created workflow.
>
> 3. **Uncheck** any steps that shouldn't be included in the workflow
>
>    Since we did some steps which where specific to our custom peak file, we might want to exclude:
>    - **Select last**
>    - all **Replace Text** steps
>    - **Convert Genomic Intervals to strict BED**
>    - **Get flanks**
>
> 4. Rename the workflow to something descriptive, e.g. `From peaks to genes`
>
> 5. Click on the **Create Workflow** button near the top.
>
>    You will get a message that the workflow was created. But where did it go?
>
> 6. Click on **Workflow** in the top menu of Galaxy
>
>    Here you have a list of all your workflows
>
> 7. Select the newly generated workflow and click on **Edit**
>
>    You should see something similar to this:
>
>    ![Editing workflow interface](../../images/intro_06.png)
>
>    > ### {% icon comment %} The workflow editor
>    > We can examine the workflow in Galaxy's workflow editor. Here you can view/change the parameter settings of each step, add and remove tools, and connect an output from one tool to the input of another, all in an easy and graphical manner. You can also use this editor to build workflows from scratch.
>    {: .comment}
>
>     Although we have our two inputs in the workflow they are missing their connection to the first tool (Intersect), because we didn't carry over some of the intermediate steps.
>
> 8. Connect each input dataset to the **Intersect** tool by dragging the arrow pointing outwards on the right of its box (which denotes an output) to an arrow on the left of the **Intersect** box pointing inwards (which denotes an input)
> 9. Rename the input datasets to `Reference regions` and `Peak regions`
> 10. Click on the **gear icon** at the top right and press **Auto Re-layout** to clean up our view:
>    ![Auto re-layouting](../../images/intro_07.png)
> 11. Click on the **gear icon** at the top right and press **Save** to save your changes
>
>    > ### {% icon tip %} Tip: Hiding intermediate steps
>    > When a workflow is executed, the user is usually primarily interested in the final product and not in all intermediate steps. By default all the outputs of a workflow will be shown, but we can explicitly tell Galaxy which output to show and which to hide for a given workflow. This behaviour is controlled by the little asterisk next to every output dataset:
>    >
>    > ![Workflow editor mark output](../../../../shared/images/workflow_editor_mark_output.png)
>    >
>    > If you click on this asterisk for any of the output datasets, then *only* files with an asterisk will be shown, and all outputs without an asterisk will be hidden (Note that clicking *all* outputs has the same effect as clicking *none* of the outputs, in both cases all the datasets will be shown).
>    {: .tip}
>
{: .hands_on}

Now it's time to reuse our workflow for a more sophisticated approach.

# Part 2: More sophisticated approach

In part 1 we used an overlap definition of 1 bp (default setting). In order to get a more meaningful definition, we now want to use the information of the position of the peak summit and check for overlap of the summits with genes.

## Preparation

Create a new history and name it. If you forgot how to do that, you can have a look at the beginning of this tutorial.
The history is now empty, but we need our peak file again. Before we upload it twice, we can copy it from our former history:

> ### {% icon hands_on %} Hands-on: Copy history items
>
> 1. Click on the **View all histories icon** at the top right of your history
>
>       You should see both of your histories side-by-side now
>
> 2. Use drag-and-drop with your mouse to copy the edited peak file (after the replace steps) but still in interval format, which contains the summit information, to your new history.
> 3. Press **Done** in the top left to go back to your analysis window
>
{: .hands_on}

## Create peak summit file

We need to generate a new BED file from the original peak file that contains the positions of the peak summits. The start of the summit is the start of the peak (column 2) plus the location within the peak that has the highest hypothetical DNA fragment coverage (column 5). As the end we simply define `start + 1`.

> ### {% icon hands_on %} Hands-on: Create peak summit file
>
> 1. **Compute** {% icon tool %}: Run **Compute an expression on every row** with the following settings:
>   - **Add expression**: `c2+c5`
>   - **as a new column to**: our peak file
>   - **Round result?**: `YES`
> 2. **Compute an expression on every row** {% icon tool %}: rerun this tool on the last result with:
>   - **Add expression**: `c8+1`
>   - **as a new column to**: the result from step 1
>   - **Round result?**: `YES`
>
{: .hands_on}

Now we cut out just the chromosome plus the start and end of the summit:

> ### {% icon hands_on %} Hands-on: Cut out columns
> 1. **Cut** {% icon tool %}: Run **Cut columns from a table** with the following settings:
>   - **Cut columns**: `c1,c8,c9`
>   - **Delimited by Tab**: `Tab`
>   - **From**: our latest history item
>
>    The output from **Cut** will be in `tabular` format.
>
> 2. Change the format to `interval` since that's what the tool **Intersect** expects.
{: .hands_on}

## Get gene names

The RefSeq genes we downloaded from UCSC did only contain the RefSeq identifiers, but not the gene names. To get a list of gene names in the end, we use another BED file from the Data Libraries.

> ### {% icon comment %} Comments
> There are several ways to get the gene names in, if you need to do it yourself. One way is to retrieve a mapping through Biomart and then join the two files (**Join two Datasets side by side on a specified field** {% icon tool %}). Another is to get the full RefSeq table from UCSC and manually convert it to BED format.
{: .comment}

> ### {% icon hands_on %} Hands-on: Data upload
>
> 1. Import from [Zenodo](https://zenodo.org/record/1025586) or from the data library (in "Introduction - From peaks to genes") the file
>    - `mm9.RefSeq_genes_from_UCSC.bed`
>
>    > ### {% icon tip %} Tip: Importing data via links
>    >
>    > * Copy the link location
>    > * Open the Galaxy Upload Manager
>    > * Select **Paste/Fetch Data**
>    > * Paste the link into the text field
>    > * Press **Start**
>    {: .tip}
>
>    > ### {% icon tip %} Tip: Importing data from a data library
>    >
>    > * Go into "Shared data" (top panel) then "Data libraries"
>    > * Click on "Training data" and then "Introduction - From peaks to genes"
>    > * Select interesting file
>    > * Click on "Import selected datasets into history"
>    > * Import in a new history
>    {: .tip}
>
>    As default, Galaxy takes the link as name, so rename them.
>
> 2. Inspect the file content to check if it contains gene names
>
{: .hands_on}

## Repeat workflow

It's time to reuse the workflow we created earlier.

> ### {% icon hands_on %} Hands-on: Run a workflow
> 1. Open the workflow menu (top menu bar)
> 2. Find the workflow you made in the previous section, and select the option **Run**
> 3. Choose as inputs our imported gene BED file and the result of the **Cut** tool
> 4. Click **Run workflow**
>
>    The outputs should appear in the history but it might take some time until they are finished.
>
{: .hands_on}

We used our workflow to rerun our analysis with the peak summits. The **Group** tool again produced a list containing the amount of genes found in each chromosome.
But woudln't it be more interesting to know about the amount of peaks in each unique gene? Let's rerun the workflow with different settings!

> ### {% icon hands_on %} Hands-on: Run a workflow with changed settings
> 1. Open the workflow menu (top menu bar)
> 2. Find the workflow you made in the previous section, and select the option **Run**
> 2. Choose as inputs our imported gene BED file and the result of the **Cut** tool
> 3. Click on the title of the Group tool to expand the options.
> 4. Change the following settings by clicking at the **edit icon** on the left:
>   - **Group by column**: `7`
>   - **Operation -> On column**: `7`
> 5. Click **Run workflow**
{: .hands_on}

Congratulations! You should have a file with all the unique gene names and a count on how many peaks they contained.

> ### {% icon question %} Questions
>
> The list of unique genes is not sorted. Try to sort it on your own!
>
>    <details>
>    <summary>Click to view answers</summary>
>    You can use the tool "Sort data in ascending or descending order" on column 2 and a numerical sort.
>    </details>
{: .question}


# Share your work

One of the most important features of Galaxy comes at the end of an analysis. When you have published striking findings, it is important that other researchers are able to reproduce your in-silico experiment. Galaxy enables users to easily share their workflows and histories with others.

To share a history, click on the gear symbol in the history pane and select `Share or Publish`. On this page you can do 3 things:

1. **Make accessible via Link**

    This generates a link that you can give out to others. Anybody with this link will be able to view your history.

2. **Publish History**

    This will not only create a link, but will also publish your history. This means your history will be listed under `Shared Data → Published Histories` in the top menu.

3. **Share with Individual Users**

    This will share the history only with specific users on the Galaxy instance.


> ### {% icon hands_on %} Hands-on: Share history and workflow
>
> 1. Share one of your histories with your neighbour.
> 2. See if you can do the same with your workflow!
> 3. Find the history and/or workflow shared by your neighbour
>
>    Histories shared with specific users can be accessed by those users in their history menu (gear icon) under `Histories shared with me`.
>
{: .hands_on}

# Conclusion
{:.no_toc}

{% icon trophy %} You have just performed your first analysis in Galaxy. You also created a workflow from your analysis so you can easily repeat the exact same analysis on other datasets. Additionally you shared your results and methods with others.