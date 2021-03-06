<properties 
	pageTitle="Data Factory - 名前付け規則 | Microsoft Azure" 
	description="Data Factory エンティティの名前付け規則について説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/27/2016" 
	ms.author="spelluru"/>

# Azure Data Factory - 名前付け規則 
次の表に、Data Factory アーティファクトの名前付け規則を示します。



名前 | 名前の一意性 | 検証チェック
:--- | :-------------- | :----------------
Data Factory | Microsoft Azure 全体で一意です。名前の大文字と小文字を区別されません。つまり、MyDF と mydf は同じデータ ファクトリを表します。 |<ul><li>各データ ファクトリは、厳密に 1 つの Azure サブスクリプションに関連付けられます。</li><li>オブジェクト名は文字または数字で始まる必要があり、文字、数字、またはダッシュ (-) 文字のみを含めることができます。</li><li>すべてのダッシュ (-) 文字は、その直前または直後に文字または数字が使用されている必要があります。連続するダッシュはコンテナー名では使用できません。</li><li>名前は 3 ～ 63 文字にすることができます。</li></ul>
リンクされたサービス/テーブル/パイプライン | データ ファクトリ内で一意です。名前の大文字と小文字は区別されません。 | <ul><li>テーブル名の最大文字数: 260。</li><li>オブジェクトの名前は、文字、数字、またはアンダースコア (_) で始まる必要があります。</li><li>次の文字は使用できません。"."、"+"、"?"、"/"、"<"、">"、"*"、"%"、"&"、":"、"\"</li></ul>
リソース グループ | Microsoft Azure 全体で一意です。名前の大文字と小文字は区別されません。 | <ul><li>最大文字数: 1,000。</li><li>名前には、文字、数字、および、次の文字を含めることができます。"-"、"\_"、"," および "."。</li></ul>

<!---HONumber=AcomDC_0629_2016-->