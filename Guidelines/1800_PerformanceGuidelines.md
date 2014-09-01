<!--
NOTE: Requires Markdown Extra. See http://michelf.ca/projects/php-markdown/extra/
 --> 

#パフォーマンス ガイドライン
	
### `IEnumerable<T>`が空かどうかを確認するために`Any()`の使用を検討する (AV1800) ![](images/3.png)

メソッドや他のメンバーが`Count`プロパティを公開しない`IEnumerable<T>`や他のコレクションクラスを返す場合、コレクションにアイテムが含まれているかどうかを判定するために`Count()`ではなく`Any()`拡張メソッドを使用する。`Count()`を使用する場合、コレクション全体を反復する重大なインパクト（それが実際に永続ストアへの`IQueryable<T>`など）を与えるリスクがある。

**Note** `IEnumerable<T>`を返す場合、AV1130 で説明されているようにオーナーが外部からの編集を防ぎ、.NET 4.5以上で開発している場合は、新しい読み取り専用クラスを検討する。

### `async`は、低負荷で長期実行されるアクティビティにのみ使用する (AV1820)

`async`の使用は、`Task.Run`のように自動的にワーカースレッドでなにかを実行するためではない。これは単に、現在のスレッドを解放して、長期実行されている非同期操作が完了したときに同じスレッドで結果をマーシャリングするのに必要なロジックを追加するだけである。言い換えれば、`async`は、I/Oに張り付く操作にのみ使用する。

### CPUの負荷が高いアクティビティのために`Task.Run`を使用する (AV1825)

CPUに張り付く作業が必要な場合、スレッドプールからスレッドの作業負荷を軽減する`Task.Run`を使用する。メインスレッドに返された結果を手動でマーシャリングする必要があることを忘れてはいけない。

### `await`/`async`と`Task.Wait`の混合に注意する (AV1830)

`await`は現在のスレッドからブロックせず、単にコンパイラがステートマシンを生成するように指示するだけである。しかし`Task.Wait`は、スレッドをブロックしてデッドロックが発生する可能性がある（AV1835を参照）

### シングルスレッド環境で`async`/`await`のデッドロックに注意する (AV1835)

以下の非同期メソッドを見てみよう: 

	private async Task GetDataAsync()
	{
		var result = await MyWebService.GetDataAsync();
		return result.ToString();
	}

ASP.NET MVCコントローラの操作は以下のようになっている:

	public ActionResult ActionAsync()
	{
		var data = GetDataAsync().Result;
		
		return View(data);  
	}

これはデッドロックになってしまう。なぜか？なぜなら`Result`プロパティの取得は、`async`操作が終わるまでブロックされるが、`async`メソッドは元のスレッドに結果が返されると自動的にマーシャリングされるので、ASP.NETは、シングルスレッド同期コンテキストを使用してお互いを待ってしまう。同じような問題は、WPF、SilverlightやWindows Store C#/XAMLアプリでも発生する。これに関する詳細は[ここ](http://blogs.msdn.com/b/pfxteam/archive/2011/01/13/10115163.aspx)を読んで欲しい。
