<!--
NOTE: Requires Markdown Extra. See http://michelf.ca/projects/php-markdown/extra/
 --> 

#レイアウトガイドライン

### 一般的なレイアウトを使用する (AV2400) ![](images/1.png)

- 1行を130文字以内に抑える
- 4文字の空白インデントを使用して、タブを使用しない
- ifと式の間のようにキーワード間に空白をひとつ入れるが、`if (condition == null)` のようにその前後には空白を追加しない
- `+`、`-`、`==`など、演算子の周りに空白を追加する
- 言語的に必要なかったとしても、`if`、 `else`、 `do`、 `while`、 `for`、 `foreach`などに開始と終了の括弧を置いて、常にキーワードを成功させる。
- 開始と終了の括弧は常に新しい行に置く
- オブジェクト初期化子と各プロパティの初期化は、以下のようにインデントせず、新しい行に書く:

		var dto = new ConsumerDto()
		{  
			Id = 123,  
			Name = "Microsoft",  
			PartnerShip = PartnerShip.Gold
		}



- ブロックを持つラムダ式は、インデントせず、以下のように書く:


		methodThatTakesAnAction.Do(x =>
		{ 
			// なにかする
		}

- クエリ式は、以下のように全体を1行にするか、各キーワードを同じインデントにする:

		var query = from product in products where product.Price > 10 select product;

  	または、
	
		var query =  from product in products  where product.Price > 10  select product;

- LINQステートメントは必ず、`from` 句から始めて、`where`句でそれを混ぜ込まない
- すべての比較条件の周りに括弧を追加するが、単数の条件の周りには括弧を追加しない。例えば、
 `if (!string.IsNullOrEmpty(str) && (str != "new"))`
- 複数行のステートメントの間、メンバーの間、閉じ括弧の後、無関係のコードブロック、`#region`キーワードの周り、異なる会社の`using`ステートメントの間には空行を追加する。

### 企業ごとの名前空間の並び順とグループ (AV2402) ![](images/3.png)

	// Microsoft 名前空間を最初にする
	using System;
	using System.Collections;
	using System.XML;
	
	// その後、他の名前空間をアルファベット順に
	using AvivaSolutions.Business;
	using AvivaSolutions.Standard;
	using Telerik.WebControls;
	using Telerik.Ajax;

### メンバーを適切に定義された並び順にする (AV2406) ![](images/1.png)
共通の並び順を維持することで、他のチームメンバーが簡単にコードで目的のものを見つけることができる。一般的にソースファイルは、本を読むように上から下に読むことができるべきである。これは読者がコードファイルで上下に行き来しなければいけない状況を防ぐ。

1.	privateフィールドと（領域の）定数
2.	public定数
3.	public readonly static フィールド
4.	ファクトリメソッド
5.	コンストラクターとファイナライザー
6.	イベント
7.	publicプロパティ
8.	呼び出す順で、その他のメソッドとprivateプロパティ

### `#region`を控える (AV2407) ![](images/1.png)
Regionは便利なときもあるが、クラスの主な目的を隠すこともできてしまう。従って#regionは、以下の目的でのみ使用する:

- `private`フィールドと定数（できればprivate定義region）
- ネストされたクラス
- インターフェイスの実装（インターフェイスがそのクラスの主な目的でない場合のみ）
