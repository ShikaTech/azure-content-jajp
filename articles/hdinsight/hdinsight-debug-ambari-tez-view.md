<properties
pageTitle="HDInsight で Ambari Tez ビューを使用する | Azure"
description="HDInsight で Ambari Tez ビューを使用して Tez ジョブをデバッグする方法について説明します。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="05/03/2016"
ms.author="larryfr"/>

# HDInsight で Ambari ビューを使用して Tez ジョブをデバッグする

HDInsight の Ambari Web UI には Tez ビューが含まれています。Tez ビューは、実行エンジンとして Tez を使用するジョブの確認とデバッグに使用できます。Tez ビューを使用すると、関連付けられた項目のグラフとしてジョブを可視化し、各項目をドリルダウンして、統計情報やログ情報を取得することができます。

> [AZURE.NOTE] このドキュメントの情報は、Linux ベースの HDInsight クラスターに固有のものです。Windows ベースの HDInsight を使用した Tez ジョブのデバッグの詳細については、「[Windows ベースの HDInsight で Tez UI を使用して Tez ジョブをデバッグする](hdinsight-debug-tez-ui.md)」を参照してください。

##前提条件

* Linux ベースの HDInsight クラスター。新しいクラスターの作成手順については、「[Hadoop チュートリアル: Linux 上の HDInsight で Hive と Hadoop を使用する](hdinsight-hadoop-linux-tutorial-get-started.md)」を参照してください。

* HTML5 をサポートする最新の Web ブラウザー

##Tez について

Tez は、Hadoop でデータを処理するための拡張可能なフレームワークで、処理速度が従来の MapReduce よりも向上します。Linux ベースの HDInsight クラスターでは、これが Hive の既定のエンジンになります。

Tez に処理が送信されると、ジョブで必要なアクションの実行順序を記述された有向非巡回グラフ (DAG) が作成されます。個々のアクションは頂点と呼ばれ、ジョブ全体の一部分を実行します。頂点によって表される処理を実際に実行することはタスクと呼ばれ、クラスター内の複数のノードに分散される場合があります。

###Tez ビューについて

Tez ビューでは、Tez を使用して実行されているプロセスや過去に実行されたプロセスに関する情報が表示されます。これを使用すると、Tez によって生成された DAG、DAG がクラスター間でどのように分散されているか、タスクや頂点で使用されるメモリなどのカウンター、エラー情報を表示できます。次のシナリオで役立つ情報が提供される場合があります。

* Map タスクと Reduce タスクの進行状況を表示して、実行時間の長いプロセスの監視する。

* 成功または失敗したプロセスの履歴データを分析して、処理の改善方法や失敗した理由を確認する。

##DAG の生成

Tez ビューには、Tez エンジンを使用するジョブが現在実行中か過去に実行されていた場合にのみデータが表示されます。単純な Hive クエリは通常 Tez を使用しなくても解決できますが、フィルター処理、グループ化、順序付け、結合などを実行する複雑なクエリでは、通常 Tez が必要です。

Tez を使用して実行される Hive クエリを実行するには、次の手順に従います。

1. Web ブラウザーで、https://CLUSTERNAME.azurehdinsight.net に移動します。ここでは、__CLUSTERNAME__ は HDInsight クラスターの名前です。

2. ページの上部にあるメニューの __[Views]__ アイコンを選択します。このアイコンは、四角形が並んだ形をしています。表示されたドロップダウン リストで __[Hive View]__ を選択します。

    ![Selecting Hive View](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Hive ビューが読み込まれたら、クエリ エディターに次の内容を貼り付けて、__[Execute]__ をクリックします。

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
    
    ジョブが完了すると、__[Query Process Results]__ セクションに出力が表示されることがわかります。結果は次のようになります。
    
        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        
4. __[Log]__ タブを選択します。次のような情報が表示されます。
    
        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    __App id__ 値を保存します。この値は次のセクションで使用します。

##Tez ビューの使用

1. ページの上部にあるメニューの __[Views]__ アイコンを選択します。表示されたドロップダウン リストで __[Tez View]__ を選択します。

    ![Selecting Tez view](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Tez ビューが読み込まれると、クラスターで現在実行されている DAG や実行されていた DAG の一覧が表示されます。既定のビューには、DAG 名、ID、送信者、状態、開始時刻、終了時刻、期間、アプリケーション ID、キューが表示されます。ページの右側にある歯車アイコンを使用すると、列をさらに追加できます。

    ![All DAGS](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. エントリが 1 つしかない場合、それは、前のセクションで実行したクエリのエントリになります。エントリが複数ある場合は、__[Application ID]__ フィールドにアプリケーション ID を入力して検索してから、Enter キーを押します。

4. __DAG 名__を選択します。選択すると、その DAG に関する情報のほか、DAG に関する情報が含まれる JSON ファイルの zip ファイルをダウンロードするオプションが表示されます。

    ![DAG Details](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. __[DAG Details]__ の上にはいくつかのリンクがあります。これらのリンクを使用すると、DAG に関する情報を表示できます。

    * __[DAG Counters]__ をクリックすると、この DAG のカウンターの情報が表示されます。
    
    * __[Graphical View]__ をクリックすると、この DAG がグラフィカルに表示されます。
    
    * __[All Vertices]__ をクリックすると、この DAG 内の頂点の一覧が表示されます。
    
    * __[All Tasks]__ をクリックすると、この DAG 内のすべての頂点のタスクの一覧が表示されます。
    
    * __[All TaskAttempts]__ をクリックすると、この DAG のタスクの実行に関する情報が表示されます。
    
    > [AZURE.NOTE] [Vertices]、[Tasks]、[TaskAttempts] が表示されるように列をスクロールする場合は、行ごとに__カウンター__を表示するためのリンクと__ログを表示またはダウンロードする__ためのリンクがあることに注意してください。

    ジョブでエラーが発生した場合、[DAG Details] には、[FAILED] という状態と、失敗したタスクに関する情報へのリンクが表示されます。診断情報は、DAG の詳細の下に表示されます。
    
    ![A DAG Details screen detailing a failure](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

7. __[Graphical View]__ を選択します。これにより、DAG がグラフィカルに表示されます。ビューの各頂点にマウス ポインターを置くと、その項目に関する情報を表示できます。

    ![Graphical view](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

8. 頂点をクリックすると、その項目の __[Vertex Details]__ が読み込まれます。__Map 1__ の頂点をクリックして、この項目の詳細を表示します。

    ![Vertex details](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

9. これで、ページの上部に頂点とタスクに関連するリンクが表示されます。

    > [AZURE.NOTE] __[DAG Details]__ に戻り、__[Vertex Details]__ を選択して、__Map 1__ の頂点を選択することで、このページに移動することもできます。

    * __[Vertex Counters]__ をクリックすると、この頂点のカウンター情報が表示されます。
    
    * __[Tasks]__ をクリックすると、この頂点のタスクが表示されます。
    
    * __[Task Attempts]__ をクリックすると、この頂点のタスクの実行に関する情報が表示されます。
    
    * __[Sources & Sinks]__ をクリックすると、この頂点のデータ ソースとシンクが表示されます。

    > [AZURE.NOTE] 前のメニューと同様に、[Tasks]、[Task Attempts]、[Sources & Sinks\_\_] が表示されるように列をスクロールすると、各項目の詳細情報へのリンクを表示できます。

10. __[Tasks]__ を選択した後、__00\_000000__ という名前の項目をクリックします。このタスクの __[Task Details]__ が表示されます。この画面から、__[Task Counters]__ と __[Task Attempts]__ を表示できます。

    ![タスクの詳細](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

##次のステップ

ここでは、Tez ビューの使用方法を学習できました。詳細については、「[HDInsight で Hadoop と共に Hive と HiveQL を使用して Apache log4j サンプル ファイルを分析する](hdinsight-use-hive.md)」を参照してください。

Tez に関する技術的な情報の詳細については、[Hortonworks の Tez に関するページ](http://hortonworks.com/hadoop/tez/)を参照してください。

HDInsight での Ambari の使用の詳細については、「[Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

<!---HONumber=AcomDC_0504_2016-->