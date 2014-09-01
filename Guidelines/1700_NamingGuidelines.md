<!--
NOTE: Requires Markdown Extra. See http://michelf.ca/projects/php-markdown/extra/
 --> 

#命名ガイドライン

### アメリカ英語を使用する (AV1701) ![](images/1.png)

英語を使用する場合、すべての型メンバー、パラメータ、変数はアメリカ英語の単語を使用するべきである。

- 簡単で読みやすく、文法として正しい名前を選択する。例えば、`HorizontalAlignment`は`AlignmentHorizontal`よりも読みやすい
- 短さよりも読みやすさを選ぶ。`CanScrollHorizontally`というプロパティ名は`ScrollableX`よりも優れている（X軸はあいまいな参照である）
- プログラム言語で広く使われているキーワードとの衝突を避ける

**例外** ほとんどのプロジェクトでは、ドメインや企業に固有の単語やフレーズを使用する。Visual Studioの静的コード分析は、すべてのコードにスペルチェックを実行するため、[カスタムコード分析辞書](http://blogs.msdn.com/fxcop/archive/2007/08/20/new-for-visual-studio-2008-custom-dictionaries.aspx)に用語を追加する必要がある。

### 言語要素に適したケースを使用する (AV1702) ![](images/1.png) 
言語要素&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|ケース&nbsp;&nbsp;&nbsp;&nbsp;|例
--------------------|----------|:-----------
クラス、構造体|Pascal|`AppDomain`
インターフェイス | Pascal | `IBusinessService`
列挙型 | Pascal | `ErrorLevel` |
列挙値 | Pascal | `FatalError` |
イベント | Pascal | `Click` |
Privateフィールド | Camel | `listItem` |
Protectedフィールド | Pascal | `MainPanel` |
Constフィールド | Pascal | `MaximumItems` |
Const変数 | Camel | `maximumItems` |
Read-only 静的フィールド | Pascal | `RedValue` |
変数 | Camel | `listOfValues` |
メソッド | Pascal | `ToString` |
名前空間 | Pascal | `System.Drawing` |
パラメータ | Camel | `typeName` |
型パラメータ | Pascal | `TView` |
プロパティ | Pascal | `BackColor` |

### 変数、パラメータ、型メンバーに数字を含めない  (AV1704) ![](images/3.png)
ほとんどのケースで明確に意図を示さない名前をつけているのは怠けているだけである。

### フィールドにプレフィックスを使用しない  (AV1705) ![](images/1.png)

例えば、静的フィールドかどうかを区別するために`g_`や`s_`を使用してはいけない。一般的にローカル変数かメンバー変数かの区別がつかない場合、大きすぎることが多い。正しくない識別子名には次のようなものがある: `_currentUser`, `mUserName`, `m_loginTime`

### 略称を使用しない (AV1706) ![](images/2.png)

例えば、`OnBtnClick`ではなく`OnButtonClick`を使用する。一文字の変数名を避け、`i`や`q`ではなく、`index`や`query`を使用する。

**例外** あなたの領域（ドメイン）で広く受け入れられて、よく知られている略称。例えば、`UserInterface`ではなく`UI`など。 

### メンバー、パラメータ、変数には、型ではなく意味に応じた名前をつける (AV1707) ![](images/2.png)

- 機能名を使用する。例えば、`GetInt`ではなく`GetLength`を使用する。
- 名前に`Enum`、`Class`、 `Struct`のような用語を使用しない。
- コレクション型の識別子には、複数形の名前をつける。

### 型名には、名詞、名詞句、形容詞句を使用する (AV1708) ![](images/2.png)

悪い例として`SearchExamination`（検査するため検索ページ）、`Common`（名詞で終わらない、目的を説明していない) 、`SiteSecurity` （名前は技術的にはOKだが、目的についてなにも説明していない)があげられる。よい例としては、`BusinessBinder`、`SmartTextBox`、`EditableSingleCustomer`がある。

クラスに`Utility`や`Helper`などの用語は使用しない。このようなクラスは通常、静的クラスであり、オブジェクト指向の原則(AV1008も参照)を考慮していない。

### ジェネリック型パラメータには説明的な名前を使用する (AV1709) ![](images/2.png)

- 常にパラメータ名のプレフィックスに`T`を使用する
- 一文字の名前が完全にそれを説明しない限り常に説明的な名前を使用し、長い名前に価値がない場合は、ひともじ`T`を型パラメータとして使用する。
- 型パラメータの名前には、設定された制約を示す名前を検討する。例えば、`ISession`の制約があるパラメータは`TSession`と呼ばれる。

### クラスや列挙型のメンバーは、名前を繰り返えさない  (AV1710) ![](images/1.png)

	class Employee
	{
		// 誤り!
		static GetEmployee() {}
		DeleteEmployee() {}
		
		// 正しい
		static Get() {...}
		Delete() {...}
		
		// これも正しい
		AddNewJob() {...}
		RegisterForMeeting() {...}
	}

### メンバー名は、関連する.NET Frameworkクラスのメンバーと同様にする (AV1711) ![](images/3.png)

.NET開発者はすでにフレームワークユーザーのパターンになれているため、同じパターンのものはあなたのクラスの助けになるだろう。例えば、コレクションのように振る舞うクラスを定義した場合、`AddItem`、`Delete`、`NumberOfItems`ではなく、`Add`、`Remove`、`Count`のようなメンバーを提供する。

### 短い名前や他の名前と間違えられることがある名前は避ける (AV1712) ![](images/1.png)

以下のステートメントは、技術的には正しいが混乱する可能性が非常に高い

	bool b001 = (lo == l0) ? (I1 == 11) : (lOl != 101);

### プロパティ名を適切につける (AV1715) ![](images/2.png)

- プロパティ名には、名詞、名詞句、ときどき形容詞句を使用する
- Booleanプロパティには肯定的なフレーズを使用する。例えば`CantSeek`ではなく`CanSeek`
- Booeanプロパティには、`Is`、 `Has`、 `Can`、 `Allows`、`Supports`などのプレフィックスを検討する
- 型と同じ名前のプロパティを検討する。列挙型に強い型付けされているプロパティは、列挙型と同じ名前を使用することができる。例えば、`CacheLevel`という名前の列挙型がある場合、その中のひとつの値を返すプロパティ名も`CacheLevel`にすることができる

### 動詞と目的語をペアにしたメソッド名を使用する (AV1720) ![](images/2.png)

`ShowDialog`のように動詞-目的語をペアにしたメソッド名を使用する。よい名前は、メンバーを「どうする(_What_)」かのヒントを与えるべきであり、可能であれば、「なぜ(_Why_)」も含める。また、メソッド名には`And`を含めてはいけない。これは、メソッドがひとつ以上のことをしていることを暗示しており、AV1115で説明されている単一責任の原則に違反している。

### 名前空間名には、名前、レイヤ、動詞、機能を使用する (AV1725) ![](images/3.png)

例えば、以下の名前空間はこのガイドラインのよい例である。

	AvivaSolutions.Commerce.Web
	NHibernate.Extensibility
	Microsoft.ServiceModel.WebApi
	Microsoft.VisualStudio.Debugging
	FluentAssertion.Primitives
	CaliburnMicro.Extensions

**Note** 名前空間には型の名前を含めてはいけないが、複数形の名詞、例えば `Collections`は通常OKである。

### イベント名には、動詞か動詞句を使用する (AV1735) ![](images/2.png)

イベント名には、動詞か動詞句を使用する。例えば: `Click`、 `Deleted`、 `Closing`、 `Minimizing`、`Arriving`。例えば、`Search`イベントは以下のように宣言される: 

	public event EventHandler<SearchArgs> Search;

### 事前イベントと事後イベントには、`-ing` と `-ed` を使用する (AV1737) ![](images/3.png)

例えば、ウィンドウが閉じる前に発生する`close`イベントは、`Closing`と呼ばれ、ウィンドウが閉じた後で発生するイベントは、`Closed`と呼ばれる。事前、または事後イベントを示すために`Before`や`After`プレフィックスやサフィックスを使用してはいけない。
オブジェクトを削除するプロセスに関するイベントを定義したいとする。`Deleting`と`Deleted`イベントを`BeginDelete`と`EndDelete`としてイベントを定義するのは避ける。

- `Deleting`: オブジェクトが削除される前に発生する
- `Delete`:オブジェクトがイベントハンドラで削除される必要がある時に発生する
- `Deleted`: オブジェクトがすでに削除されたときに発生する

### イベントハンドラのプレフィックスとして`On`を使用する (AV1738) ![](images/3.png)

イベントを処理するメソッドには`On`をプレフィックスにするとよい。例えば、`Closing`イベントを処理するメソッド名は`OnClosing`にする。

### 重要でないラムダパラメータにはアンダースコアを使用する (AV1739) ![](images/3.png)

例えば、イベントをサブスクライブするのにラムダステートメントを使用する場合、イベントの実際のパラメータは重要でないことをより明確にするため、以下の規則を使用する。

	button.Click += (\_, \_\_) => HandleClick();

### クラス内の拡張メソッドのグループは、`Extensions`サフィックスを使用する (AV1745) ![](images/3.png)

拡張メソッドの名前が、他のメンバーや拡張メソッドと競合する場合、クラス名にプレフィックスを付ける必要がある。`Extensions`サフィックスを持つクラスは可読性を向上させる。

### 非同期メソッドの接尾辞には`TaskAsync`または`Async`を使用する (AV1755) ![](images/2.png)

一般的な規約では、`Task`や`Task<TResult>`を返すメソッドは、接尾辞に`Async`を使用するが、そのようなメソッドがすでに存在している場合、代わりに`TaskAsync`を使用する。
