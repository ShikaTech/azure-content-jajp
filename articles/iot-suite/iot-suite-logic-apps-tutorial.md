<properties
  pageTitle="Azure IoT Suite と Logic Apps | Microsoft Azure"
  description="ビジネス プロセスのための Azure IoT Suite への Logic Apps の接続方法に関するチュートリアルです。"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="05/20/2016"
  ms.author="araguila"/>
  
# チュートリアル: ロジック アプリを Azure IoT Suite リモート監視構成済みソリューションに接続する

[Microsoft Azure IoT Suite][lnk-internetofthings] リモート監視構成済みソリューションは、IoT ソリューションを例示するエンド ツー エンド機能をすぐに使い始められる優れた方法です。このチュートリアルでは、Logic App を Microsoft Azure IoT Suite リモート監視構成済みソリューションに追加する方法を説明します。これは、IoT ソリューションをビジネス プロセスに接続してさらに活用できる方法を示すものです。

_リモート監視構成済みソリューションのプロビジョニング方法のチュートリアルについては、「[チュートリアル: IoT 事前構成済みソリューションの使用][lnk-getstarted]」を参照してください。_

このチュートリアルを開始する前に、Azure サブスクリプションでリモート監視構成済みソリューションをプロビジョニングするだけでなく、ビジネス プロセスをトリガーするメールを送信できるように SendGrid アカウントを作成する必要があります。[SendGrid](https://sendgrid.com/) で**[無料で試す]** をクリックし、無料試用版アカウントにサインアップすることができます。無料試用版アカウントに登録したら、SendGrid でメールを送信するアクセス許可を付与する [API キー](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html)を作成する必要があります。この API キーはチュートリアルの後半で必要になります。

リモート監視構成済みソリューションを既にプロビジョニングしていることを前提とし、[Azure ポータル][lnk-azureportal]でそのソリューションのリソース グループに移動します。リソース グループの名前は、リモート監視ソリューションのプロビジョニング時に選択したソリューション名と同じです。リソース グループには、ソリューション用に事前にプロビジョニングされている Azure リソース (Azure クラシック ポータルにある Azure Active Directory アプリケーションを除く) がすべて表示されます。次のスクリーンショットには、リモート監視構成済みソリューションの **[リソース グループ]** ブレードの例が示されています。

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

作業を開始するには、構成済みソリューションで使用するロジック アプリをセットアップします。

## ロジック アプリをセットアップする

1. Azure ポータルで、リソース グループ ブレードの上部にある __[追加]__ をクリックします。

2. __ロジック アプリ__を検索し、選択してから **[作成]** をクリックします。

3. __名前__を入力し、リモート監視ソリューションのプロビジョニング時に使用したものと同じ**サブスクリプション**、**リソース グループ**、および **App Service プラン**を使用します。__[作成]__ をクリックします。

    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)

4. デプロイが完了すると、ロジック アプリがリソース グループにリリースとしてリストされていることがわかります。

5. ロジック アプリをクリックしてロジック アプリ ブレードに移動するとすぐに **Logic Apps デザイナー**が開きます。

    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)

6. __[手動 - HTTP 要求を受信したとき]__ を選択します。これで、特定の JSON 形式のペイロードを含む受信 HTTP 要求がトリガーとして機能するようになります。

7. 要求本文の JSON スキーマには、以下を貼り付けます。

    ```
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
    
    注: ロジック アプリの保存後に HTTP ポストの URL をコピーできますが、まず、アクションを追加する必要があります。

8. 手動トリガーで __(+)__ をクリックします。次に、**[アクションの追加]** をクリックします。

    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)

9. **[SendGrid - メールを送信]** を見つけてクリックします。

    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)

10. **SendGridConnection** などの接続名を入力し、SendGrid アカウントのセットアップ時に作成した **SendGrid API キー**を入力して **[接続の作成]** をクリックします。

    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)

11. **[送信元]** と **[宛先]** の両方のフィールドに所有しているメール アドレスを追加します。**[件名]** フィールドに「**リモート監視アラート [DeviceId]**」と入力します。**[電子メールの本文]** フィールドには、「**デバイス [DeviceId] が [measuredValue] という値の [measurementName] をレポートしました**」と入力します。 **[DeviceId]**、**[measurementName]**、および **[measuredValue]** は、**[前のステップで使用したデータを挿入できます]** セクションでクリックして追加することができます。

    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)

12. トップ メニューの __[保存]__ をクリックします。

13. **[HTTP 要求を受信したとき]** のトリガーをクリックして、__[この URL に対する HTTP POST]__ の値をコピーします。この URL はこのチュートリアルの後半で必要になります。

> [AZURE.NOTE] Logic Apps では、Office 365 でのアクションを含め、[さまざまな種類のアクション][lnk-logic-apps-actions]を実行できます。

## EventProcessor Web ジョブをセットアップする

このセクションでは、デバイス センサー値がしきい値を超えた場合に行われるアクションに Logic App をトリガーする URL を追加して作成した Logic App に構成済みソリューションを接続します。

1. git クライアントを使用して、最新バージョンの [azure-iot-remote-monitoring github リポジトリ][lnk-rmgithub]を複製します。次に例を示します。

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. Visual Studio で、リポジトリのローカル コピーの __RemoteMonitoring.sln__ を開きます。

3. **Infrastructure\\Repository** フォルダーの __ActionRepository.cs__ ファイルを開きます。

4. 以下のように、Logic App からメモした __[この URL に対する HTTP POST]__ を使用して **actionIds** ディクショナリを更新します。

    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this UR>" },
        { "Raise Alarm", "<Http Post to this UR> }
    };
    ```

5. ソリューションの変更内容を保存して、Visual Studio を終了します。

## コマンドラインからデプロイする

このセクションでは、更新されたバージョンのリモート監視ソリューションをデプロイして、Azure で現在実行中のバージョンを置き換えます。

1. [デプロイのセットアップ][lnk-devsetup]手順に従って、デプロイ用に環境をセットアップします。

2.  ローカルでデプロイする場合は、[ローカル デプロイ][lnk-localdeploy]の手順に従ってください。

3.  クラウドにデプロイし、既存のクラウド デプロイを更新する場合は、[クラウド デプロイ][lnk-clouddeploy]の手順に従ってください。デプロイ名として、元のデプロイの名前を使用します。たとえば、元のデプロイ名が **demologicapp** の場合は、以下のコマンドを使用します。

    ``
    build.cmd cloud release demologicapp
    ``
    
    ビルド スクリプトを実行する場合は、最初のソリューションのプロビジョニング時に使用したものと同じ Azure アカウント、サブスクリプション、リージョン、および Active Directory インスタンスを必ず使用してください。

## Logic App の動作を確認する

リモート監視構成済みソリューションには、最初のソリューションのプロビジョニング時に既定で 2 つのルールが設定されます。以下のルールは両方とも **SampleDevice001** デバイスに対するものです。

* 気温 > 38.00
* 湿度 > 48.00

気温ルールは **Raise Alarm** アクションをトリガーし、湿度ルールは **SendMessage** アクションをトリガーします。両方のアクション (**ActionRepository** クラス) で同じ URL を使用したものと仮定した場合、ロジック アプリはいずれかのルールをトリガーし、SendGrid を使用して**宛先**アドレスにアラートの詳細を含むメールを送信します。

> [AZURE.NOTE] Logic App はしきい値が満たされるたびにトリガーし続けます。したがって、不要なメールが送信されないようにするために、ソリューション ポータルでルールを無効にするか、[Azure ポータル][lnk-azureportal]で Logic App を無効にすることができます。

メールの受信だけでなく、ポータルでは次のように Logic App の実行時間を確認することもできます。

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## 次のステップ

これで Logic App を使用して構成済みソリューションをビジネス プロセスに接続しました。次は、[構成済みソリューションのカスタマイズ][lnk-customize]または[ソリューションへの物理デバイスの追加方法][lnk-connect]について、さらに理解を深めます。

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-connect]: iot-suite-connecting-devices.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md

<!---HONumber=AcomDC_0525_2016-->