<properties
   pageTitle="Azure Functions のテスト |Microsoft Azure"
   description="Postman、cURL、Node.js を使用して Azure Functions をテストします。"
   services="functions"
   documentationCenter="na"
   authors="wesmc7777"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure Functions, 関数, イベント処理, webhook, 動的コンピューティング, サーバーレス アーキテクチャ, テスト"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="03/30/2016"
   ms.author="wesmc"/>

# Azure Functions のテスト

## 概要

このチュートリアルでは、関数をテストするさまざまな方法について説明します。この記事では、クエリ文字列パラメーターまたは要求本文を通じて入力を受け取る HTTP トリガー関数を定義します。既定の **HttpTrigger Node.js 関数**テンプレート コードでは、`name` クエリ文字列パラメーターがサポートされています。このパラメーターを、要求本文内のユーザーの `address` 情報と共にサポートするコードも追加します。

## テスト用の関数を作成する

このチュートリアルの大部分で、**HttpTrigger Nodejs 関数**テンプレートに少し変更を加えたバージョンを使用します。このテンプレートは、新しい関数を作成する際に入手できます。新しい関数の作成に関する詳細については、チュートリアル「[Create your first Azure Function(初めての Azure 関数の作成)](functions-create-first-azure-function.md)」を参照してください。[Azure ポータル]でテスト関数を作成する際に、**HttpTrigger Nodejs 関数**テンプレートをクリックするだけで入手できます。

この既定の関数テンプレートは、本質的には hello world 関数で、要求本文またはクエリ文字列パラメーター `name=<your name>` から名前を取得して返します。記事では、要求本文で JSON コンテンツとして名前と住所を指定できるようにするためのコードの更新も行います。これにより、関数は、これらが取得可能であれば、クライアントに返すようになります。

テストに使用する次のコードで関数を更新します。

	module.exports = function(context, req) {
	    context.log("Node.js HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
	    context.log("Request Headers = " + JSON.stringify(req.headers));    
	
	    if (req.query.name || (req.body && req.body.name)) {
	        if (typeof req.query.name != "undefined") {
	            context.log("Name was provided as a query string param..."); 
	            ProcessNewUserInformation(context, req.query.name);
	        }
	        else {
	            context.log("Processing user info from request body..."); 
	            ProcessNewUserInformation(context, req.body.name, req.body.address);
	        }
	    }
	    else {
	        context.res = {
	            status: 400,
	            body: "Please pass a name on the query string or in the request body"
	        };
	    }
	    context.done();
	};
	
	function ProcessNewUserInformation(context, name, address)
	{    
	    context.log("Processing User Information...");            
	    context.log("name = " + name);            
	    echoString = "Hello " + name;
	    
	    if (typeof address != "undefined")
	    {
	        echoString += "\n" + "The address you provided is " + address;
	        context.log("address = " + address);            
	    }
	    
	    context.res = {
	            // status: 200, /* Defaults to 200 */
	            body: echoString
	        };
	}


## ツールを使用した関数のテスト

### cURL を使用してテストする

ソフトウェアのテストでは、アプリケーションをデバッグする際にコマンド ライン以外の詳しい調査が必要になることはめったにありませんが、これは関数の場合も同じです。

上記の関数をテストするには、ポータルから**関数の URL** をコピーします。関数の URL の形式は次のとおりです。

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
これは、関数をトリガーするための URL です。関数をテストするには、次のように、コマンド ラインで cURL コマンドを使用して、関数に対して Get (`-G` または `--get`) 要求を実行します。

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
上記の例の場合は、次のように、cURL コマンドでデータ (`-d`) として渡すことができるクエリ文字列パラメーターが必要です。

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>
    
Enter キーを押すと、コマンド ラインに関数の出力が表示されます。

![](./media/functions-test-a-function/curl-test.png)

関数を実行すると、ポータルの **[ログ]** ウィンドウに、次のような出力が記録されます。

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### ブラウザーを使用してテストする

パラメーターを必要としない関数、またはクエリ文字列パラメーターのみを必要とする関数は、ブラウザーを使用してテストできます。

上で定義した関数をテストするには、ポータルから**関数の URL** をコピーします。関数の URL の形式は次のとおりです。

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

次のように `name` クエリ文字列パラメーターを追加します。`<Enter a name here>` プレースホルダーには実際の名前を使用します。

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

ブラウザーに URL を貼り付けると、次のような応答が取得されます。

![](./media/functions-test-a-function/browser-test.png)

関数を実行すると、ポータルの **[ログ]** ウィンドウに、次のような出力が記録されます。

	2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
	2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
	2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Wes from a browser
	2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"ja-JP"}
	2016-03-23T07:35:10.338 Name was provided as a query string param.
	2016-03-23T07:35:10.338 Processing User Information...
	2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### Postman を使用してテストする

Postman は、ほとんどの関数でテスト用に推奨されるツールです。Postman をインストールする方法については、[Postman の取得](https://www.getpostman.com/)に関するサイトを参照してください。Postman を使用すると、HTTP 要求の多くの属性を制御できます。

> [AZURE.TIP] 使い慣れた REST クライアントを使用できます。次のクライアントが Postman の代わりに使用できます。
> 
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Paw](https://luckymarmot.com/paw)  

Postman で要求本文を使用して関数をテストするには、次の手順に従います。

1. Chrome ブラウザー ウィンドウの左上隅にある **[アプリ]** ボタンをクリックして、Postman を起動します。
2. **関数の URL** をコピーして Postman に貼り付けます。URL には、アクセス コード クエリ文字列パラメーターが含まれています。
3. HTTP メソッドを **POST** に変更します。
4. **[Body]** (本文)、**[raw]** (未加工) の順にクリックし、次のような JSON 要求本文を追加します。

		{
		    "name" : "Wes testing with Postman",
		    "address" : "Seattle, W.A. 98101"
		}

5. [**送信**] をクリックします。

次の図は、このチュートリアルの単純なサンプル echo 関数のテストを示しています。

![](./media/functions-test-a-function/postman-test.png)

関数を実行すると、ポータルの **[ログ]** ウィンドウに、次のような出力が記録されます。

	2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
	2016-03-23T08:04:57.763 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
	2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"ja-JP"}
	2016-03-23T08:04:57.763 Processing user info from request body...
	2016-03-23T08:04:57.763 Processing User Information...
	2016-03-23T08:04:57.763 name = Wes testing with Postman
	2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
	2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    
### ストレージ エクスプローラーを使用して BLOB トリガーをテストする

[Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com/)を使用して BLOB トリガー関数をテストできます。

1. 関数アプリの [Azure ポータル]で、C# または Node の BLOB トリガー関数を新規作成します。監視するパスを BLOB コンテナーの名前に設定します。次に例を示します。

		files

2. **[+]** ボタンをクリックし、使用するストレージ アカウントを選択または作成します。**[Create]** をクリックします。

3. 次のテキストが含まれたテキスト ファイルを作成して保存します。

		A text file for blob trigger function testing.

4. [Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com/)を実行し、監視対象のストレージ アカウントの BLOB コンテナーに接続します。

5. **[アップロード]** ボタンをクリックし、テキスト ファイルをアップロードします。

	![](./media/functions-test-a-function/azure-storage-explorer-test.png)


	既定の BLOB トリガー関数コードによって、ログに BLOB の処理がレポートされます。

		2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
		2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
		2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
		2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## 関数内での関数のテスト

### 関数ポータルの実行ボタンを使用してテストする

ポータルにある **[実行]** ボタンをクリックすると、限定的なテストを実施できます。[実行] ボタンを使用する場合、要求本文は指定できますが、クエリ文字列パラメーターを指定したり、要求ヘッダーを更新したりすることはできません。

前に作成した HTTP トリガー関数をテストするには、次のような JSON 文字列を **[要求本文]** フィールドに追加し、**[実行]** ボタンをクリックします。

	{
		"name" : "Wes testing Run button",
		"address" : "USA"
	} 

関数を実行すると、ポータルの **[ログ]** ウィンドウに、次のような出力が記録されます。

	2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
	2016-03-23T08:03:18.697 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
	2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"ja-JP"}
	2016-03-23T08:03:18.697 Processing user info from request body...
	2016-03-23T08:03:18.697 Processing User Information...
	2016-03-23T08:03:18.697 name = Wes testing Run button
	2016-03-23T08:03:18.697 address = USA
	2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### タイマー トリガーを使用してテストする

機能の中には、ここまでに説明したツールでは十分にテストできないものがあります。たとえば、メッセージが [Azure Queue Storage ](../storage/storage-dotnet-how-to-use-queues.md)にドロップされたときに実行されるキュー トリガー関数などがこれにあたります。キューにメッセージをドロップするコードはいつでも記述することができます。コンソール プロジェクトにおけるこの例を下に示します。ただし、関数で直接テストするための別の方法もあります。

キュー出力バインドを用いて構成されたタイマー トリガーを使用できます。このタイマー トリガー コードで、テスト メッセージをキューに書き込むことができます。このセクションでは、例を用いて説明します。

Azure Functions でのバインドの使用に関する詳細については、「[Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md)」を参照してください。


#### テスト用のキュー トリガーの作成

この方法を実演するために、まず、`queue-newusers` という名前のキューに対してテストするキュー トリガー関数を作成します。この関数は、Azure Queue Storage にドロップされた新しいユーザーの名前と住所の情報を処理します。

> [AZURE.NOTE] 別のキュー名を使用する場合は、必ず、[キューおよびメタデータの名前付け](https://msdn.microsoft.com/library/dd179349.aspx)の規則に準拠した名前を使用してください。準拠していない場合、"HTTP 状態コード 400: 正しくない要求" が表示されます。

1. 関数アプリの [Azure ポータル]で、**[New Function]** (新しい関数)、**[QueueTrigger - c#]** (QueueTrigger - C#) の順にクリックします。
2. キュー関数で監視するキューの名前を入力します。 

		queue-newusers 

3. **[+]** (追加) ボタンをクリックし、使用するストレージ アカウントを選択または作成します。**[Create]** をクリックします。
4. 既定のキュー関数テンプレート コードのログ エントリを監視できるように、このポータルのブラウザー ウィンドウを開いたままにします。


#### キューにメッセージをドロップするタイマー トリガーの作成

1. 新しいブラウザー ウィンドウで [Azure ポータル]を開き、関数アプリに移動します。
2. **[New Function]** (新しい関数)、**[TimerTrigger - C#]** (TimerTrigger - C#) の順にクリックします。タイマー コードがキュー関数のテストを実行する頻度を設定する CRON 式を入力します。**[Create]** をクリックします。テストを 30 秒ごとに実行する場合は、次の [CRON 式](https://wikipedia.org/wiki/Cron#CRON_expression)を使用できます。

		*/30 * * * * *


2. 新しいタイマー トリガーの **[統合]** タブをクリックします。
3. **[出力]** にある **[+ 新規出力]** ボタンをクリックします。**[キュー]**、**[選択]** ボタンの順にクリックします。
4. **キュー メッセージ オブジェクト**に使用している名前をメモします。この名前は、タイマー関数コードで使用します。

		myQueue

4. メッセージの送信先キューの名前を入力します。

		queue-newusers 

3. **[+]** (追加) ボタンをクリックし、前にキュー トリガーで使用したストレージ アカウントを選択します。その後、**[保存]** をクリックします。
4. タイマー トリガーの **[開発]** タブをクリックします。
5. 上に示したのと同じキュー メッセージ オブジェクト名を使用している場合は、C# タイマー関数に次のコードを使用できます。その後、**[保存]** をクリックします。

		using System;
		
		public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
		{
		    String newUser = 
		    "{"name":"User testing from C# timer function","address":"XYZ"}";
		
		    log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
		    log.Verbose($"{newUser}");   
		    
		    myQueue = newUser;
		}

これで、上の CRON 式の例を使用している場合は、C# タイマー関数が 30 秒ごとに実行されるようになります。タイマー関数のログに、各実行がレポートされます。

	2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
	2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
	2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

キュー関数のブラウザー ウィンドウに、処理されている各メッセージが表示されます。

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	
## コードを使用した関数のテスト

### コードを使用して HTTP トリガー関数をテストする: Node.js

Node.js コードを使用して、Azure Functions をテストする HTTP 要求を実行できます。

次のように設定します。

- 要求オプションの `host` を関数アプリのホストに設定する。
- `path` に関数の名前を設定する。
- `path` にアクセス コード (`<your code>`) を設定する。

コード例:

	var http = require("http");
	
	var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";
	
	var nameBodyJSON = {
	    name : "Wes testing with Node.JS code",
	    address : "Dallas, T.X. 75201"
	};
	
	var bodyString = JSON.stringify(nameBodyJSON);
	
	var options = {
	  host: "functions841def78.azurewebsites.net",
	  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
	  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
	  method: "POST",
	  headers : {
	      "Content-Type":"application/json",
	      "Content-Length": Buffer.byteLength(bodyString)
	    }    
	};
	
	callback = function(response) {
	  var str = ""
	  response.on("data", function (chunk) {
	    str += chunk;
	  });
	
	  response.on("end", function () {
	    console.log(str);
	  });
	}
	
	var req = http.request(options, callback);
	console.log("*** Sending name and address in body ***");
	console.log(bodyString);
	req.end(bodyString);



出力:

	C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
	*** Sending name and address in body ***
	{"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
	Hello Wes testing with Node.JS code
	The address you provided is Dallas, T.X. 75201
		
関数を実行すると、ポータルの **[ログ]** ウィンドウに、次のような出力が記録されます。

	2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
	2016-03-23T08:09:01.153 Node.js HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
	2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
	2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
	2016-03-23T08:09:01.153 Request Body Type = object
	2016-03-23T08:09:01.153 Request Body = [object Object]
	2016-03-23T08:09:01.153 Processing User Information...
	2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    

### コードを使用してキュー トリガー関数をテストする: C# #

キューにメッセージをドロップするコードを使用してキュー トリガーをテストできることは既に説明しました。次のコード例は、[Azure Queue Storage の概要](../storage/storage-dotnet-how-to-use-queues.md)に関するチュートリアルに記載されている C# コードをベースにしています。他の言語のコードも、こちらのリンク先から入手できます。

コンソール アプリでこのコードをテストするには、次の手順を実行する必要があります。

- [app.config ファイルにストレージ接続文字列を構成します](../storage/storage-dotnet-how-to-use-queues.md#setup-a-storage-connection-string)。
- このコードは、実行時に新しいユーザーの名前と住所をコマンド ライン引数として受け取ります。`name` と `address` をパラメーターとしてアプリに渡します。たとえば、`C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"` のように指定します。


C# のコード例:

    static void Main(string[] args)
    {
        string name = null;
        string address = null;
        string queueName = "queue-newusers";
        string JSON = null;

        if (args.Length > 0)
        {
            name = args[0];
        }
        if (args.Length > 1)
        {
            address = args[1];
        }

        // Retrieve storage account from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

        // Create the queue client
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

        // Retrieve a reference to a queue
        CloudQueue queue = queueClient.GetQueueReference(queueName);

        // Create the queue if it doesn't already exist
        queue.CreateIfNotExists();

        // Create a message and add it to the queue.
        if (name != null)
        {
            if (address != null)
                JSON = String.Format("{{"name":"{0}","address":"{1}"}}", name, address);
            else
                JSON = String.Format("{{"name":"{0}"}}", name);
        }

        Console.WriteLine("Adding message to " + queueName + "...");
        Console.WriteLine(JSON);

        CloudQueueMessage message = new CloudQueueMessage(JSON);
        queue.AddMessage(message);
    }

キュー関数のブラウザー ウィンドウに、処理されている各メッセージが表示されます。

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[Azure ポータル]: https://portal.azure.com

<!---HONumber=AcomDC_0427_2016-->