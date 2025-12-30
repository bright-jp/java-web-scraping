# Java-web-scraping
<h2>Javaを使用したWebスクレイピングの方法をコード例付きで素早く紹介するガイド</h2>

![java web scraping header](https://github.com/luminati-io/java-web-scraping/blob/main/Web%20scraping%20with%20Java%20-%20Ultimate%20guide.png "java scraping guide banner")

Pythonの使用を好む人もいますが、もう1つの人気のある選択肢は、**WebスクレイピングにJavaを活用すること**です。ここでは、これを簡単に実現するためのステップバイステップガイドをご紹介します。

開始する前に、Webスクレイピングに最適な環境になるよう、コンピューターに以下がセットアップされていることを確認してください。

[Java11](https://www.java.com/en/download/help/download_options.html) -より高度なバージョンもありますが、開発者の間ではこれが圧倒的に最も人気です。

[Maven](https://maven.apache.org/) – 依存関係管理のためのビルド自動化ツールです

[IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) – IntelliJ IDEAは、Javaで書かれたコンピューターソフトウェアを開発するための統合開発環境です。

[HtmlUnit](https://htmlunit.sourceforge.io/) – これはブラウザー操作のシミュレーター（例：フォーム送信のシミュレーション）です。

これらのコマンドでインストール状況を確認できます。

* ```java -version```
* ```mvn -v```

## Alternative Solution

[Bright Data's Web Scraping API](https://brightdata.jp/products/web-scraper) は、データ収集のための完全自動化ソリューションを提供します。スクレイパーのセットアップやメンテナンスの複雑さを省き、ターゲットサイト、必要なデータセット、出力形式を定義するだけで済みます。リアルタイムでの構造化データが必要な場合でも、スケジュール配信が必要な場合でも、Bright Dataの堅牢なツールにより、正確性、スケーラビリティ、使いやすさが保証されます。データ運用において効率性と信頼性を重視するプロフェッショナルに最適です。

それでは、Javaスクレイパーを続けましょう。

<h3>Step One: ターゲットページを検証する</h3>
データを収集したいターゲットサイトにアクセスし、任意の場所を右クリックして「inspect element」を押して「Developer Console」を開きます。これによりWebページのHTMLへアクセスできます。
<h3>Step Two: HTMLのスクレイピングを開始する</h3>
IntelliJ IDEAを開き、Mavenプロジェクトを作成します。

![intellij IDEA Maven project](https://github.com/luminati-io/java-web-scraping/blob/main/Java2%20intellij.png "intellij maven project")

Mavenプロジェクトにはpom.xmlファイルがあります。pom.xmlファイルに移動し、まずプロジェクトのJDKバージョンを設定します。

```
<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<maven.compiler.source>11</maven.compiler.source>
		<maven.compiler.target>11</maven.compiler.target>
	</properties>
  ```
  次に、以下のとおり **HtmlUnit** の依存関係を **pom.xml** ファイルに追加します。
  
  ```
  <dependencies>
		<dependency>
			<groupId>net.sourceforge.htmlunit</groupId>
			<artifactId>htmlunit</artifactId>
			<version>2.63.0</version>
		</dependency>
	</dependencies>
  ```
  これで、最初のJavaクラスを書き始める準備が整いました。次のように新しいJavaソースファイルを作成することから始めます。
  
  ![open new java source](https://github.com/luminati-io/java-web-scraping/blob/main/Java3%20intellij.png "new java class")
  
  アプリケーションを開始するためにmainメソッドを作成する必要があります。次のようにmainメソッドを作成してください。
  
  ```
  public static void main(String[] args) throws IOException {
   }
  ``` 
  アプリはこのメソッドから開始されます。これはアプリケーションのエントリーポイントです。次のように **HtmlUnit** のimportを使用して **HTTP request** を送信できるようになります。
  
  ```   
  import com.gargoylesoftware.htmlunit.*;
  import com.gargoylesoftware.htmlunit.html.*;
  import java.io.IOException;
  import java.util.List;
  ```  
  次に、以下のようにオプションを設定して ```WebClient``` を作成します。
  
  ``` 
  private static WebClient createWebClient() {
		WebClient webClient = new WebClient(BrowserVersion.CHROME);
		webClient.getOptions().setThrowExceptionOnScriptError(false);
		webClient.getOptions().setCssEnabled(false);
	  webClient.getOptions().setJavaScriptEnabled(false);
		return webClient;
	}
  ``` 
  
<h3>Step Three: HTMLからデータを抽出/解析する</h3>
それでは、関心のあるターゲットの価格データを抽出しましょう。
これを実現するために、次の **HtmlUnit** の組み込みコマンドを使用します。**product price** に関するデータポイントの場合の例は以下のとおりです。

```
WebClient webClient = createWebClient();
	    
		try {
			String link = "https://www.ebay.com/itm/332852436920?epid=108867251&hash=item4d7f8d1fb8:g:cvYAAOSwOIlb0NGY";
			HtmlPage page = webClient.getPage(link);
			
			System.out.println(page.getTitleText());
			
			String xpath = "//*[@id=\"mm-saleDscPrc\"]";			
			HtmlSpan priceDiv = (HtmlSpan) page.getByXPath(xpath).get(0);			
			System.out.println(priceDiv.asNormalizedText());
			
			CsvWriter.writeCsvFile(link, priceDiv.asNormalizedText());
			
		} catch (FailingHttpStatusCodeException | IOException e) {
			e.printStackTrace();
		} finally {
			webClient.close();
		}	
  ```
目的の要素の **XPath** を取得するには、Developer Consoleを使用します。Developer Consoleで選択したセクションを右クリックし、「Copy XPath」をクリックします。このコマンドにより、選択したセクションがXPath式としてコピーされます。

![xpath of element](https://github.com/luminati-io/java-web-scraping/blob/main/Java4.png "get xpath")

Webページにはリンク、テキスト、画像、表が含まれています。表のXPathを選択した場合は、CSVにエクスポートして、Microsoft Excelなどのプログラムで追加の計算や分析を行えます。次のステップでは、表をCSVファイルとしてエクスポートする方法を確認します。

<h3>Step Four: データをエクスポートする</h3>
データが解析できたので、さらに分析できるようCSV形式でエクスポートできます。この形式は、Microsoft Excelで簡単に開いて表示できるため、他の形式より好むプロフェッショナルもいます。これを実現するためのコマンドラインは次のとおりです。

```
public static void writeCsvFile(String link, String price) throws IOException {
		
		FileWriter recipesFile = new FileWriter("export.csv", true);

		recipesFile.write("link, price\n");

		recipesFile.write(link + ", " + price);

		recipesFile.close();
	}
  ```

----

## Conclusion

Javaはさまざまな分野のプロフェッショナルが必要なデータを抽出するのに役立ちますが、Webスクレイピングのプロセスは非常に時間がかかる場合があります。データ収集の運用を完全に自動化するには、Bright Dataの [Web Scraping API](https://brightdata.jp/products/web-scraper) のようなツールを活用できます。必要なのは、ターゲットサイトと出力データセットを選択し、希望するスケジュール、ファイル形式、配信方法を選ぶだけです。