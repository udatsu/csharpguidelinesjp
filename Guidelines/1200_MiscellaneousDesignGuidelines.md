<!--
NOTE: Requires Markdown Extra. See http://michelf.ca/projects/php-markdown/extra/
 --> 

#その他のデザインガイドライン

### いくつかの種類のステータスで返すよりも例外をスローする (AV1200) ![](images/2.png)

成功か失敗かを報告する値を返すコードベースでは、ネストされたifステートメントですべてのコードで振り分けする傾向にある。呼び出し元はよく戻り値のチェックを忘れる。構造化例外ハンドリングは、例外のスロー、高いレイヤでのキャッチや例外の置き換えを許可している。ほとんどのシステムで、予期しない状況での例外のスローはとても一般的である。

### リッチで意味のある例外メッセージテキストを提供する (AV1202) ![](images/2.png)

メッセージは、例外が発生した理由を説明して、例外を避けるためにはどうする必要があるかを明確に説明する必要がある。

### もっとも適した例外をスローする (AV1205) ![](images/3.png)

例えば、メソッドが引数に`null`を受け取った場合、基本型の`ArgumentException`ではなく、`ArgumentNullException`をスローするべきである。

### 汎用的な例外キャッチでエラーを飲み込まない (AV1210) ![](images/1.png)

アプリケーションのコードでException、SystemExceptionなど明示的でない例外をキャッチしてエラーを飲み込むことは避ける。例外を処理する最後のチャンスであるトップレベルのコードでは、ロギングの目的で明示的でない例外をキャッチして、きちんとアプリケーションを終了させるべきである。

### 非同期コード内の例外をきちんと処理する (AV1215) ![](images/2.png)

`async`/`await`や`Task`のコードで例外をスローまたは処理するときは、以下の2つのルールを忘れてはいけない。

- `async`/`await` ブロックと`Task`のアクション で例外が発生したときは、`await`しているものに伝搬される
- 非同期ブロックの前のコードで発生した例外は、呼び出し元に伝搬される

### 常にイベントハンドラの`null`をチェックする (AV1220) ![](images/1.png)

イベントのサブスクライバが存在しない場合`null`になるため、呼び出す前にデリゲートリストであるイベント変数が`null`でないことを確認する。さらに同時実行スレッドからの変更が競合することを防ぐために、一時変数を使用する。

	event EventHandler<NotifyEventArgs> Notify;

	void RaiseNotifyEvent(NotifyEventArgs args)
	{
		EventHandler<NotifyEventArgs> handlers = Notify;
	 	if (handlers != null)
 		{
	   	   handlers(this, args);   
	  	}
	}

**Tip** 完全に空のデリゲートリストを防ぐために、このように空のデリゲートを割り当ててもよい:

	event EventHandler<NotifyEventArgs> Notify = delegate {};

### 各イベントを発生させるためにprotected virtualメソッドを使用する (AV1225) ![](images/2.png)

このガイドラインを遵守すると、`protected`メソッドをオーバーライドすることで、派生クラスからベースクラスのイベントを処理することができる。`protected virtual`メソッドの名前は、イベントと同じ名前に`On`をつけた名前にするべきである。たとえば、`TimeChanged`というイベントの`protected virtual`メソッドは`OnTimeChanged`という名前にする。

**Note** `protected virtual`メソッドをオーバーライドした派生クラスは、ベースクラスの実装を呼び出す必要はない。ベースクラスは、実装が呼び出されなかったとしても正しく動作できなくてはならない。

### プロパティ変更イベントの提供を検討する (AV1230) ![](images/3.png)

プロパティが変更された時に発生するイベントの提供を検討する。`PropertyChanged`という名前のイベントを付けるべきで、`Property`はこのイベントに関連づけられたプロパティの名前に置き換えられるべきである。

**Note** クラスが、対応するイベントが必要な多くのプロパティを持っている場合、`INotifyPropertyChanged`インターフェイスの実装を検討する。これは[プレゼンテーションモデル](http://martinfowler.com/eaaDev/PresentationModel.html)と[Model-View-ViewModel](http://msdn.microsoft.com/en-us/magazine/dd419663.aspx)パターンでよく使用されている。

### イベントを発生させる時sender 引数としてnullを渡すべきではない (AV1235) ![](images/1.png)

たびたび、複数の送信者からの同じようなイベントをひとつのイベントハンドラで処理することがある。sender引数は、イベントのソースを取得するために使われる。イベントを発生させるときは、常にソースの参照(通常はthis) を渡すようにする。イベントを派生させるときにイベントデータパラメータに`null`を渡してはならない。もしイベントデータが存在しない場合は、`null`ではなく、`EventArgs.Empty`を渡すようにする。

**例外** 静的イベントでは、sender引数は`null`でなくてはならない。

### 該当する場合は、ジェネリック制約を使用する (AV1240) ![](images/2.png)

ジェネリック型かメソッドのobject型からキャストする代わりに`where`制約や`as`演算子をジェネリックパラメータの正確な特性を指定するために使用する。例えば:

	class SomeClass
	{}

	// Don't
	class MyClass<T>
	{
		void SomeMethod(T t)
	 	{
  			object temp = t;
  			SomeClass obj = (SomeClass) temp;
 		}
	}
	
	// Do
	class MyClass<T> where T : SomeClass
	{
		void SomeMethod(T t)
	 	{
	  		SomeClass obj = t;
	 	}
	}

### LINQ式を戻り値として返す前に評価する  (AV1250) ![](images/1.png)

以下のコードスニペットを考えてみよう

	public IEnumerable GetGoldMemberCustomers()
	{
		const decimal GoldMemberThresholdInEuro = 1000000;
		
		var query = 
			from customer in db.Customers
			where customer.Balance > GoldMemberThresholdInEuro
			select new GoldMember(customer.Name, customer.Balance);
		
		return query;  
	}

LINQクエリは遅延実行を使用しているため、実際には`query`は上記のクエリを表現する式木を返す。呼び出し元は、`foreach`などを使って毎回この結果を評価し、クエリ全体が再実行された結果`GoldMember`の新しいインスタンスが返される。そのため、複数の`GoldMember`インスタンスを比較するために`==`演算子を使用することはできない。その代わり、`ToList()`や`ToArray()`などのメソッドを使ってLINQクエリの結果を明示的に評価する。
