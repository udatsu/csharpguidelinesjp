<!--
NOTE: Requires Markdown Extra. See http://michelf.ca/projects/php-markdown/extra/
 --> 

#フレームワーク ガイドライン

### `System`名前空間の型ではなく、C#の型エイリアスを使用する (AV2201) ![](images/1.png)

例えば、`Object`の代わりに`object`、`String`の代わりに`string`、`Int32`の代わりに`int`。これらのエイリアスはプリミティブ型としてC#言語の一級市民として提供されているため、これらを使用するべきである。

**例外** これらの型の静的メンバーを参照するときは、完全なCLS名を使用する。例えば、`int.Parse()`の代わりに`Int32.Parse()`。

### ローカライズされたリソースを参照するプロパティ、変数、フィールドに適切な名前をつける (AV2205) ![](images/3.png)

このトピックのガイドラインは、エラーメッセージやメニューテキストのようにローカライズされたリソースに適用される。

- リソースキーにPascal caseを使用する
- 短い識別子よりも説明的なものを提供する。可能であれば簡潔にするが、読みやすさを重視する。
- リソースの名前には、英数字のみを使用する。

### デプロイメントで変更される文字列をハードコードしない (AV2207) ![](images/3.png)

例えば、接続文字列やサーバーアドレスなど。`ConfigurationManager`クラスの`ConnectionStrings`プロパティやVisual Studioによって生成された`Settings`クラスなどの`Resources`を使用する。実際の値は、`app.config`や`web.config`（と間違えなくカスタム構成ストア）で維持する。

### 最も高い警告レベルでビルドする (AV2210) ![](images/1.png)

開発環境で、C#コンパイラの**警告レベル 4**で構成し、**警告をエラーとして扱う**オプションを有効にする。これはコンパイラに可能な限り最高のコード品質を強要することができる。

### `AssemblyInfo.cs`ファイルの属性を正しく埋める  (AV2215) ![](images/3.png)

会社名、説明、著作権、バージョンなどの属性が埋められていることを確認する。すべてのアセンブリで、バージョンなどのフィールドに同じ値がきちんと埋められていることを確認するひとつの方法は、`AssemblyInfo.cs`の対応する属性をソリューション内のプロジェクトで共有される`SolutionInfo.cs`に移動することである。

### シンプルな表現へのLINQ（クエリ式）は避ける (AV2220) ![](images/3.png)

以下の記述よりも

	var query = from item in items where item.Length > 0;

`System.Linq`名前空間の拡張メソッドを使用する。

	var query = items.Where(i => i.Length > 0);

LINQクエリは可読性のために複数行で書くべきなので、2番目の例の方がもう少し可読性が高い。

### 匿名メソッドではなくラムダ式を使用する (AV2221) ![](images/?.png)

ラムダ式は、匿名メソッドよりもはるかにエレガントである。なので、以下の代わりに

Customer c = Array.Find(customers, delegate(Customer c)

{

return c.Name == "Tom";

});

ラムダ式を使用する。

	Customer c = Array.Find(customers, c => c.Name == "Tom");

また、以下の方がさらによい。

	var customer = customers.Where(c => c.Name == "Tom");

### `dynamic`キーワードは動的オブジェクトを受け取るときだけ使用する  (AV2230) ![](images/1.png)

`dynamic`キーワードは、動的言語で作業するために導入された。コンパイラがいくつかの複雑なリフレクションコードを生成する必要があるため、これを使うことで深刻なパフォーマンスのボトルネックになる可能性がある。

これは、(`Activator`を使って)動的に生成されたインスタンスのメソッドやメンバーを呼び出すときだけ、`Type.GetProperty()`、`Type.GetMethod()`、COM互換型として操作する代わりに使用する。

### `Task`よりも`async`/`await`を好む (AV2235)

新しいC# 5.0キーワードを使うと、複数の非同期操作をつなげる必要がある場合でも、コードをシーケンシャルに読むことができ、保守性が大幅に向上する。
以下のように定義するよりも:

	public Task GetDataAsync()
	{
	  return MyWebService.FetchDataAsync()
	    .ContinueWith(t => new Data(t.Result));
	}

以下のように定義する:

	public async Task GetDataAsync()
	{
	  var result = await MyWebService.FetchDataAsync();
	  return new Data (result);
	}
