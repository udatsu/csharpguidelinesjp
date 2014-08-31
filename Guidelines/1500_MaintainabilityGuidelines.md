<!--
NOTE: Requires Markdown Extra. See http://michelf.ca/projects/php-markdown/extra/
 --> 

#保守性ガイドライン

### メソッドは7ステートメントを超えるべきではない (AV1500) ![](images/1.png)

7個より多いステートメントを持つメソッドは、多くのことをし過ぎているか、多くの責任を持ちすぎている。これはまた、コードが何をしているかを理解するために人が正確にステートメントを分析する必要があります。内容を説明した名前を持つ、複数の小さく、フォーカスされたメソッドに分割して、それでも高いレベルでアルゴリズムがまだ明確であることを確認する。

### デフォルトでは、すべてのメンバーを`private`に、型を`internal`にする (AV1501) ![](images/1.png)

どのメンバーをほかのクラスに提供するかをより意識的に決定するために、最初はスコープを可能な限り狭める。その後慎重になにをpublicメンバーや型として公開するかを決める。

### 二重否定の条件を避ける (AV1502) ![](images/2.png)

`customer.HasNoOrders`のようなプロパティは間違っていないが、以下のように否定条件で使用するのは避ける :

	bool hasOrders = !customer.HasNoOrders;

二重否定は単純な表現よりも理解が難しく、人々は二重否定を簡単に読むことができない。

### アセンブリ名にはそれに含まれる名前空間を使用する (AV1505) ![](images/3.png)

すべてのDLLは*Company*.*Component*.dll のパターンで、*Company*は、企業名で、*Component*はひとつ以上のドットで区切られた節を含んでいる名前を使用するべきである。例えば、
`AvivaSolutions.Web.Controls.dll`

例として、アセンブリで公開しているAvivaSolutions.Web.Binding名前空間の下のグループを検討してみよう。ガイドラインによるとアセンブリは次のように呼ばれるべきである。
`AvivaSolutions.Web.Binding.dll`

**例外** 無関係な複数の名前空間からクラスを集めてひとつのアセンブリに結合する場合、アセンブリの最後にCoreをつけることを検討する。ただし、名前空間にはつけない。例えば、
AvivaSolutions.Consulting.Core.dll.

### ソースファイル名にはそれに含まれる型を使用する (AV1506) ![](images/3.png)
ファイルの名前にはPascal Caseを使用して、アンダースコアを使用しない。

### ソースコードファイルに含めるのはひとつの型に制限する (AV1507) ![](images/3.png)
**例外** ネストされた型は明らかに同じファイルの一部である必要がある。

### ソースファイル名には、partialタイプの論理機能をつける (AV1508) ![](images/3.png)

Partialタイプを使用して、ファイルごとに一部を割り当てている場合、それぞれのファイル名は論理パートの後にパートが果たしていることをつける。例えば、

	// In MyClass.cs
	public partial class MyClass
	{...}
	
	// In MyClass.Designer.cs	
	public partial class MyClass
	{...}

### 完全な型名の代わりにusing ディレクティブを使用する (AV1510) ![](images/3.png)

名前の衝突を防ぐために完全な名前を制限する。例えば、以下のように使用しない。

	var list = new System.Collections.Generic.List<string>();

代わりに以下のようにする。

	using System.Collections.Generic;

	var list = new List<string>();

名前の衝突を防ぐ必要がある場合、usingディレクティブを使って別名を割り当てる:

	using Label = System.Web.UI.WebControls.Label;

### “マジック” ナンバーを使ってはいけない (AV1515) ![](images/1.png)

記号定数以外に数値、文字列のどちらにもリテラル値を使用してはいけない。例えば:

	public class Whatever
	{
		public static readonly Color PapayaWhip = new Color(0xFFEFD5);
		public const int MaxNumberOfWheels = 18;
	}

ログやトレースのための文字列はこのルールから除外する。文脈から意味が明確な場合と将来変更される可能性がない場合にはリテラルを許可する。例えば:

	mean = (a + b) / 2;     						// OK
	WaitMilliseconds(waitTimeInSeconds * 1000); 			// 十分明確である

ある定数が他に依存している場合は、コード内で明示するようにする。

	public class SomeSpecialContainer
	{
	 	public const int MaxItems = 32;
	 	public const int HighWaterMark = 3 * MaxItems / 4; 	// at 75%
	}

**Note** 記号定数を明らかにするために列挙型を使用することができる

### 型が確実に明確なときにだけvarを使用する (AV1520) ![](images/1.png)

LINQクエリの結果やステートメントからの型が確実に明確なときには、`var`を使うことによって可読性が向上する。そのため、以下のようには使用しない。

	var i = 3;		// 型はなに？ int? uint? float?
	var myfoo = MyFactoryMethod.Create("arg");	// なにを期待しているのかが明確じゃない。 
												// ベースクラス? インターフェイス?
												// また、クラスを探すことができないと
												// リファクタリングが難しくなる

`var`は、以下のように使用する。

	var q = from order in orders where order.Items > 10 and order.TotalValue > 1000;
	var repository = new RepositoryFactory.Get<IOrderRepository>();
	var list = new ReadOnlyCollection<string>();

上記3つの例では、期待している型が明確である。より詳細な `var`によるメリット・デメリットは、Eric Lippert氏の[型推論を使うかどうか](http://blogs.msdn.com/b/ericlippert/archive/2011/04/20/uses-and-misuses-of-implicit-typing.aspx)を参照して欲しい。

### 変数の宣言と初期化は可能な限り遅らせる (AV1521) ![](images/2.png)

CとVisual Basicのようにすべての変数をブロックの最初に宣言するスタイルは避け、各変数が必要になった時点で定義して初期化する。

### 各変数は分離して割り当てる (AV1522)
以下のように紛らわしい構築をしてはならない。

	var result = someField = GetSomeMethod();

### ステートメントをわけるよりもオブジェクトやコレクションの初期化子を使用する (AV1523) ![](images/?.png)
以下のようにする代わりに

	var startInfo = new ProcessStartInfo("myapp.exe");	
	startInfo.StandardOutput = Console.Output;
	startInfo.UseShellExecute = true;

[オブジェクト初期化子](http://msdn.microsoft.com/ja-jp/library/bb384062.aspx)を使用する。

	var startInfo = new ProcessStartInfo("myapp.exe")  
	{
		StandardOutput = Console.Output,
		UseShellExecute = true  
	};

同様に、以下の代わりに

	var countries = new List();
	countries.Add("Netherlands");
	countries.Add("United States");

コレクションや[ディクショナリ初期化子](http://msdn.microsoft.com/ja-jp/library/bb531208.aspx)を使用する。

	var countries = new List { "Netherlands", "United States" };

### `true` や`false` を明示的に比較しない (AV1525) ![](images/1.png)

通常は`bool`型の式で`true`か`false`かを比較するためことはよくないスタイルである。例えば:

	while (condition == false)// 間違えた、よくないスタイル 
	while (condition != true)// これも誤り  
	while (((condition == true) == true) == true)// どこまでいくの？
	while (condition)// OK

### ループ変数を`for`や`foreach`ループの中で変更しない (AV1530) ![](images/2.png)

ループ変数を一か所だけでなく、ループ処理の中で変更すると通常は混乱をきたすことになる。このルールは`foreach`ループにも適用されるが、通常は列挙子がコレクションの変更を検出する。

	for (int index = 0; index < 10; ++index)  
	{  
		if (_some condition_)
		{
			index = 11; //誤り! 代わりに‘break’ か‘continue’を使用する  
		}
	}

### ループのネストを避ける (AV1532) ![](images/2.png)

ネストされたループを持つメソッドは単一のループのものより理解しづらい。実際のところ、ネストされたループを持つほとんどのケースは、データを結合するために`from`キーワードを2回以上使用してLINQクエリに置き換えることで大幅にシンプルにできる。

### `if`、`else`、`while`、`for`、`foreach`、`case`などのキーワードの後に常にブロックを追加する (AV1535) ![](images/2.png)

これはフォーム内のステートメントの混乱も防ぐこともできる:

	if (b1) if (b2) Foo(); else Bar(); // どちらの ‘if’ が ‘else’に進むのか?
	
	// 正しくは:
	if (b1)  
	{  
		if (b2)  
		{  
			Foo();  
		}  
		else  
		{  
			Bar();  
		}  
	}

### 常に`switch`ステートメントの最後の`case`の後に`default`ブロックを追加する (AV1536) ![](images/1.png)
`default`ブロックが空の場合は、説明するコメントを追加する。また、そのブロックに到達することがない場合は、将来の変更で既存の`case`を通り抜けてしまったことを検出するために`InvalidOperationException`をスローする。これはすべてのパスを通るように考えられたいいコードと言える。

	void Foo(string answer)  
	{  
		switch (answer)  
		{  
			case "no":  
			  Console.WriteLine("You answered with No");  
			  break;  
			  
			case "yes":  
			  Console.WriteLine("You answered with Yes");  
			  break;
			
			default:  
			  // ここで終わることはない。  
			  throw new InvalidOperationException("Unexpected answer " + answer);  
		}  
	}

### すべてのif-else-ifステートメントはelseで終わる (AV1537) ![](images/2.png)

例えば、
   
	void Foo(string answer)  
	{  
		if (answer == "no")  
		{  
			Console.WriteLine("You answered with No");  
		}  
		else if (answer == "yes")  
		{  
			Console.WriteLine("You answered with Yes");  
		}  
		else  
		{  
			// ここに到達したときになにが起こるのか？無視？そうじゃない場合は、   
			// throw an InvalidOperationException.  
		}  
	}

### 複数の`return` ステートメントを控える (AV1540) ![](images/2.png)

ひとつの入り口、ひとつの出口は可読性の高いフロー制御を維持する原則である。ただし、メソッドが非常に小さく、ガイドラインAV1500に準拠している場合、複数のreturnステートメントは、何カ所かで更新されるbooleanフラグを管理するよりも可読性を向上させる。

### 単純な(条件付きの)割り当ての代わりにif-else ステートメントを使用しない (AV1545) ![](images/2.png)
直接の意図を表現する。例えば、以下よりも

	bool pos;
	
	if (val > 0)  
	{  
		pos = true;  
	}  
	else  
	{  
		pos = false;  
	}

以下のように書く。

	bool pos = (val > 0);// 初期化

または、以下の代わりに

	string result;
	
	if (someString != null)
	{  
		result = someString;  
	}
	else
	{
		result = "Unavailable";
	}

	return result;

以下のように書く

	return someString ?? "Unavailable";

### メソッドやプロパティの複雑な表現をカプセル化する (AV1547) ![](images/1.png)

以下の例について考えてみよう

	if (member.HidesBaseClassMember && (member.NodeType != NodeType.InstanceInitializer))
	{
		// なにかをする
	}

この表現を理解するためには、詳細とすべての結果で、なにをしているのかを分析する必要がある。明らかに、上にコメントを追加することができるが、それよりも明確な名前のメソッド名でこの複雑な表現を起きえる方がよいだろう:

	if (NonConstructorMemberUsesNewKeyword(member))
	{
		// なにかをする
	}

	private bool NonConstructorMemberUsesNewKeyword(Member member)
	{
		return 
			(member.HidesBaseClassMember && 
			(member.NodeType != NodeType.InstanceInitializer)
	}
これでも、これを編集する時には理解する必要があるが、こちらの方が理解するのが簡単である。

### 他のオーバーロードからもっともパラメータの多いメソッドを呼び出す (AV1551) ![](images/2.png)
このガイドラインは、オプショナル引数を持つオーバーロードにのみ適用される。例えば以下のコードを見てみよう:

	public class MyString  
	{
		private string someText;
		
		public MyString(string text)  
		{  
			this.someText = text;  
		}  
		  
		public int IndexOf(string phrase)  
		{  
			return IndexOf(phrase, 0, someText.Length);  
		}
		
		public int IndexOf(string phrase, int startIndex)  
		{  
			return IndexOf(phrase, startIndex, someText.Length - startIndex );  
		}
		
		public virtual int IndexOf(string phrase, int startIndex, int count)  
		{  
			return someText.IndexOf(phrase, startIndex, count);  
		}  
	}

`MyString`クラスは`IndexOf`の3つのオーバーロードを提供するが、そのうち2つはもっともパラメータの多いものを呼び出す。同じルールがクラスのコンストラクターに適用される; もっとも完全なオーバーロードを実装して、他のオーバーロードから`this()`初期化子を使って呼び出す。また、同じ名前のパラメータは同じすべてのオーバーロードで同じ位置に現れるようにするべきである。

**重要** 派生クラスでこれらのメソッドのオーバーロードを可能にしたい場合、もっとも完全なオーバーロードを`protected virtual`にして、すべてのオーバーロードから呼ばれるようにする。

### オプショナル引数は、オーバーロードを置き換えるためにだけ使用する (AV1553) ![](images/1.png)
C# 4.0でオプショナル引数を使用する唯一の妥当な理由は、ひとつのメソッドで以下のようにルールAV1551 の例を置き換えることである:

	public virtual int IndexOf(string phrase, int startIndex = 0, int count = 0)
	{
		return someText.IndexOf(phrase, startIndex, count);
	}

オプショナルパラメータが参照型の場合、デフォルト値は`null`しか与えられない。しかしここで、string、リスト、コレクションの場合は、ルールAV1235により`null`であってはならないため、代わりにオーバーロードされたメソッドを使用する必要がある。

**Note** オプショナルパラメータのデフォルト値は、呼び出し側に保存されている。呼び出し側のコードを再コンパイルしないでデフォルト値を変更すると、新しいデフォルト値プロパティは適用されない。

**Note** インターフェイスメソッドがオプショナルパラメータを定義している場合、インターフェイスの参照を通じてクラスの実態を呼び出さない限り、デフォルト値はオーバーロードの解決時に考慮されない。詳細は、Eric Lippert氏の[この投稿](http://blogs.msdn.com/b/ericlippert/archive/2011/05/09/optional-argument-corner-cases-part-one.aspx)を参照。

### 名前付き引数の使用を避ける (AV1555) ![](images/1.png)
C# 4.0の名前付き引数は、大量のオプショナルパラメータを提供するCOMコンポーネントの呼び出しを簡単にするために導入された。もしメソッド呼び出しの可読性を改善するために名前付き引数が必要なら、そのメソッドはおそらく多くのことをやり過ぎているか、リファクタリングすべきである。

**例外**
名前付き引数で可読性が改善する唯一の例外は、`bool`パラメータを持つコントロールしないいくつかのコード ベース メソッドを呼び出す以下のような時である:

	object[] myAttributes = type.GetCustomAttributes(typeof(MyAttribute), inherit: false);

### 3つより多いパラメータを受け取るメソッドやコンストラクターを許可しない (AV1561) ![](images/1.png)
メソッドが3つより多いパラメータを必要とする場合、[Specification](http://en.wikipedia.org/wiki/Specification_pattern)デザインパターンで説明されているように、複数の引数を渡すための構造体かクラスを使用する。一般的にパラメータ数が少ないほうがメソッドを理解しやすい。さらに、多くのパラメータが必要なメソッドのユニットテストには多くのシナリオが必要になる。

### `ref`や`out`パラメータを使用しない (AV1562) ![](images/1.png)
これらはコードを理解しづらくして、バグを引き起こすことがある。代わりに複合オブジェクトを返すようにする。

### bool フラグを受け取るメソッドを避ける (AV1564) ![](images/2.png)
以下のようなシグニチャのメソッドについて考えてみよう:

	public Customer CreateCustomer(bool platinumLevel) {}

一見するとこのシグニチャは問題ないように見えるが、このメソッドを呼び出すと、完全に目的を見失うことになる:

	Customer customer = CreateCustomer(true);

フラグを受け取るメソッドは2つ以上のことをやっていることが多く、2つ以上のメソッドにリファクタリングする必要がある。代替のソリューションとしてはフラグを列挙に置き換えることである。

### パラメータを一時変数として使用しない (AV1568) ![](images/3.png)
一時的な状態を保持する便利な変数としてパラメータを使用してはならない。一時的な変数と型が同じであったとしても、一時変数としての目的を反映していない。

### 常に`as`操作の結果をチェックする (AV1570) ![](images/1.png)
オブジェクトからインターフェイスの参照を取得するために`as`を使っている場合、その操作が`null`を返さないかを常に確認する。オブジェクトがインターフェイスを実装していないときにこれを怠ると、ずっと後の段階で`NullReferenceException`が発生することがある。

### コードをコメントアウトしない (AV1575) ![](images/1.png)
コメントアウトされたコードをチェックインするのではなく、やるべき作業を管理できる作業アイテムをトラックできるシステムを使用する。コメントアウトされたコードをみつけたときになにをするべきかを誰もわからない。テストのために一時的に無効化している？サンプルをコピーした？そんなものは削除するべきだ。
