<properties
	pageTitle="Azure App Service での Java Web アプリの作成 | Microsoft Azure"
	description="このチュートリアルでは、Java Web アプリを Azure App Service にデプロイする方法を示します。"
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="get-started-article"
	ms.date="06/01/2016"
	ms.author="robmcm"/>

# Azure App Service での Java Web アプリの作成

[AZURE.INCLUDE [タブ](../../includes/app-service-web-get-started-nav-tabs.md)]

このチュートリアルでは、[Azure ポータル]を使用して [Azure App Service で Java Web アプリ]を作成する方法について説明します。Azure ポータルは、Azure リソースの管理に使用できる Web インターフェイスです。

> [AZURE.NOTE] このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、[Visual Studio サブスクライバーの特典を有効にする]か、[無料試用版にサインアップ]してください。
>
> Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[Azure App Service アプリケーションの作成]」にアクセスしてください。有効期間が短いスターター Web アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## Java アプリケーション オプション

複数の方法で Java アプリケーションを App Service Web アプリにセットアップできます。

1. アプリを作成して**アプリケーション設定**を構成する。

	App Service では、複数のバージョンの Tomcat と Jetty が既定の構成で提供されています。ホストするアプリケーションが組み込みバージョンの Web コンテナーで動作するようであれば、この方法で Web コンテナーをセットアップするのが最も簡単で、Web コンテナーに war ファイルをアップロードするだけなら最適です。この方法では、Azure ポータルでアプリを作成した後、アプリの **[アプリケーション設定]** ブレードに移動して、お使いの Java のバージョンと目的の Java Web コンテナーを選びます。このメソッドを使用する場合は、プログラム ファイルから Java と Web コンテナーの両方が実行されます。その他のメソッドは、Web コンテナーと場合によっては JVM をディスク領域に配置します。このモデルを使用する場合、ファイル システムのこの部分のファイルを編集するためのアクセス権はありません。つまり、*server.xml* ファイルを構成したり、*/lib* フォルダーにライブラリ ファイルを配置したりすることはできません。詳細については、このチュートリアルの後半の「[Java Web アプリの作成と構成](#appsettings)」セクションを参照してください。
	
2. Azure Marketplace からテンプレートを使用する。

	Azure Marketplace には、Java Web アプリを自動的に作成して Tomcat または Jetty Web コンテナーで管理するテンプレートが含まれています。テンプレートによって作成される Web コンテナーは、構成可能です。詳細については、このチュートリアルの「[Azure Marketplace にある Java テンプレートの使用](#marketplace)」セクションを参照してください。
  
3. アプリを作成してから構成ファイルを手動でコピーして編集する。

	App Service によって提供されるどの Web コンテナーにもデプロイしないカスタム Java アプリケーションをホストすることがあります。次に例を示します。
	
	* お使いの Java アプリケーションに必要な Tomcat または Jetty のバージョンが App Service によって直接サポートされていないか、ギャラリーで提供されていない。
	* お使いの Java アプリケーションが HTTP 要求を受け取り、WAR として既存の Web コンテナーにデプロイされない。
	* 最初から自分で Web コンテナーを構成したい。 
	* App Service でサポートされていないバージョンの Java を使用して自分でアップロードしたい。

	これらの場合は、Azure ポータルを使ってアプリを作成して、適切なランタイム ファイルを手動で指定できます。このとき、ファイルは App Service プランのストレージ領域クォータに対してカウントされます。詳細については、[Azure へのカスタム Java Web アプリのアップロード]に関するページを参照してください。

## <a name="portal"></a> Java Web アプリの作成と構成

このセクションでは、Web アプリを作成し、ポータルの **[アプリケーション設定]** ブレードを使用して Java 向けに構成する方法について説明します。

1. [Azure ポータル]にサインインします。

2. **[新規]、[Web + モバイル]、[Web アプリ]** の順にクリックします。

	![New Web App][newwebapp]

4. **[Web アプリ]** ボックスに Web アプリの名前を入力します。

	Web アプリの URL は {name}.azurewebsites.net のようになるため、この名前は azurewebsites.net ドメイン内で一意である必要があります。入力した名前が一意でない場合は、テキスト ボックスに赤色の感嘆符が表示されます。

5. **リソース グループ**を選択するか、新しく作成します。

	リソース グループの詳細については、「[Azure ポータルを使用した Azure リソースの管理]」を参照してください。

6. **App Service プラン/場所**を選択するか、新しく作成します。

	App Service プランの詳細については、[Azure App Service プランの概要]に関するページを参照してください。

7. **[作成]** をクリックします。

	![Create Web App][newwebapp2]
 
8. Web アプリが作成されたら、**[Web アプリ]、{作成した Web アプリ}** の順にクリックします。
 
	![Select Web App][selectwebapp]

9. **[Web アプリ]** ブレードで、**[設定]** をクリックします。

10. [**アプリケーションの設定**] をクリックします。

11. 目的の **[Java バージョン]** を選択します。

12. 目的の **[Java マイナー バージョン]** を選択します。**[最新]** を選択すると、お使いのアプリで、その Java メジャー バージョン向けの App Service で使用できる最新のマイナー バージョン使用することになります。**[最新]** 項目は、**[アプリケーションの設定]** で作成された Java アプリに対して一意です。ギャラリーから Java アプリを作成する場合は、ユーザーが自身のコンテナーと JVM 変更を管理する必要があります。

12. 目的の **[Web コンテナー]** を選択します。"**最新**" で始まるコンテナー名を選択すると、お使いのアプリは、App Service で使用できる Web コンテナー メジャー バージョンの最新バージョンに保たれます。

	![Web Container Versions][versions]

13. **[保存]** をクリックします。

	お使いの Web アプリはすぐに Java ベースになり、選んだ Web コンテナーを使用するように構成されます。

14. **[Web アプリ]、{作成した新しい Web アプリ}** の順にクリックします。

15. **[URL]** をクリックして新しいサイトを参照します。

	表示された Web ページで、Java ベースの Web アプリが作成されたことを確認します。

## <a name="marketplace"></a> Azure Marketplace にある Java テンプレートの使用

このセクションでは、Azure Marketplace を使用して Java Web アプリを作成する方法について説明します。Java ベースのモバイル アプリや API アプリを作成する場合にも、同様の一般的なフローを利用できます。

1. [Azure ポータル]にサインインします。

2. **[新規]、[Marketplace]** の順にクリックします。

	![New Marketplace][newmarketplace]

3. **[Web + モバイル]** をクリックします。

	左へスクロールして **[Marketplace]** ブレードを表示すると、**[Web + モバイル]** を選択できます。

4. 検索テキスト ボックスに、**Apache Tomcat** や **Jetty** などの Java アプリケーション サーバーの名前を入力して、Enter キーを押します。

5. 検索結果で、Java アプリケーション サーバーをクリックします。

	![Web Mobile Jetty][webmobilejetty]

6. 最初の **[Apache Tomcat]** ブレードまたは **\[Jetty]** ブレードで、**[作成]** をクリックします。

	![Jetty Portal Blade][jettyblade]

7. 次の **[Apache Tomcat]** ブレードまたは **\[Jetty]** ブレードで、**[Web アプリ]** ボックスに Web アプリの名前を入力します。

	Web アプリの URL は {name}.azurewebsites.net のようになるため、この名前は azurewebsites.net ドメイン内で一意である必要があります。入力した名前が一意でない場合は、テキスト ボックスに赤色の感嘆符が表示されます。

8. **リソース グループ**を選択するか、新しく作成します。

	リソース グループの詳細については、「[Azure ポータルを使用した Azure リソースの管理]」を参照してください。

9. **App Service プラン/場所**を選択するか、新しく作成します。

	App Service プランの詳細については、[Azure App Service プランの概要]に関するページを参照してください。

10. **[作成]** をクリックします。

	![Jetty Portal Create][jettyportalcreate2]

	短時間 (通常は 1 分未満) で、新しい Web アプリの作成が完了します。

11. **[Web アプリ]、{作成した新しい Web アプリ}** の順にクリックします。

12. **[URL]** をクリックして新しいサイトを参照します。

	![Jetty URL][jettyurl]

	Tomcat には既定のページ セットが用意されているため、Tomcat を選択した場合は次の例のようなページが表示されます。

	![Web app using Apache Tomcat][tomcat]

	Jetty を選択した場合は、次の例のようなページが表示されます。Jetty には既定のページ セットが用意されていないため、空の Java サイトに使用されるのと同じ JSP がここでも使用されます。

	![Web app using Jetty][jetty]

これで、Web アプリとアプリ コンテナーが作成されました。アプリケーションを Web アプリにアップロードする方法については、「[次のステップ](#next-steps)」を参照してください。

## 次のステップ

この時点で、Java アプリケーション サーバーは Azure App Service の Web アプリで動作しています。独自のコードを Web アプリにデプロイするには、[Java Web アプリへのアプリケーションまたは Web ページの追加]に関するページを参照してください。

Azure での Java アプリケーションの開発の詳細については、[Java デベロッパー センター]を参照してください。

<!-- URL List -->

[Java Web アプリへのアプリケーションまたは Web ページの追加]: ./web-sites-java-add-app.md
[Azure App Service プランの概要]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Azure ポータル]: https://portal.azure.com/
[Visual Studio サブスクライバーの特典を有効にする]: http://go.microsoft.com/fwlink/?LinkId=623901
[無料試用版にサインアップ]: http://go.microsoft.com/fwlink/?LinkId=623901
[Azure App Service アプリケーションの作成]: http://go.microsoft.com/fwlink/?LinkId=523751
[Azure App Service で Java Web アプリ]: http://go.microsoft.com/fwlink/?LinkId=529714
[Java デベロッパー センター]: /develop/java/
[Azure ポータルを使用した Azure リソースの管理]: ../azure-portal/resource-group-portal.md
[Azure へのカスタム Java Web アプリのアップロード]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png

<!---HONumber=AcomDC_0608_2016-->