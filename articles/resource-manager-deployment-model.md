<properties
   pageTitle="Resource Manager とクラシック デプロイ | Microsoft Azure"
   description="Resource Manager のデプロイ モデルと従来 (あるいはサービス管理) のデプロイ モデルの違いについて説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="tomfitz"/>

# Azure Resource Manager とクラシック デプロイ: デプロイ モデルとリソースの状態について

このトピックでは、Azure Resource Manager と従来のデプロイ モデルについて、また、リソースの状態や、各リソースのデプロイにどちらの方法を使用したかについて説明します。Resource Manager デプロイ モデルには従来のデプロイとの重要な違いがあり、2 つのモデルは互いにまったく互換性を持ちません。リソースのデプロイと管理を簡単にするために、新しいリソースには Resource Manager を利用し、可能であれば、Resource Manager で既存のリソースを再デプロイすることが推奨されています。

ほぼすべてのリソースで、問題なく Resource Manager に移行できます。ただし、いくつかのリソース プロバイダーはモデル間の構造の違いに起因して 2 つのバージョンのリソースを提供します (従来のモデルに 1 つ、リソース マネージャーに 1 つ)。2 つのモデルを区別するリソース プロバイダー:

- **Compute** - 仮想マシンとオプションの可用性セットのインスタンスをサポートします。
- **Storage** - オペレーティング システム ディスク、追加のデータ ディスクを含む、仮想マシンの VHD の保存に必要なストレージ アカウントをサポートします。
- **Network** - 必要とされる NIC、仮想マシンの IP アドレス、仮想ネットワークとオプションのロード バランサー内のサブネット、ロード バランサー IP アドレス、およびネットワーク セキュリティ グループをサポートします。

これらの書類のリソースの場合、サポートされる操作が異なるため、使用しているバージョンに注意してください。クラシック デプロイメントから Resource Manager のデプロイにリソースを移行する準備ができたら、「[Platform supported migration of IaaS resources from Classic to Azure Resource Manager (プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行)](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)」を参照してください。

リソースのデプロイにどちらのモデルを使用したかを把握するために、2 つのモデルを詳しく見てみましょう。

## Resource Manager の特性

Resource Manager で作成したリソースには次の特性があります。

- 次のいずれかのメソッドで作成:

  - [Azure ポータル](https://portal.azure.com/)。

        ![Azure portal](./media/resource-manager-deployment-model/preview-portal.png)

        Compute、Storage、ネットワークのリソースについては、リソース マネージャーと従来のデプロイのどちらかを使用できます。 **[リソース マネージャー]** を選択します。

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - Azure PowerShell では、Resource Manager 版のコマンドを使用してください。これらのコマンドは、次に示すように *Verb-AzureRmNoun* 形式となります。

            Get-AzureRmResourceGroupDeployment

  - [Azure リソース マネージャー REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx) (REST 操作の場合)。
  - Azure CLI コマンドは **arm** モードで実行されます。

            azure config mode arm

- リソース タイプの名前には **(classic)** は含まれません。下の画像ではタイプとして "**ストレージ アカウント**" が表示されています。

    ![Web アプリ](./media/resource-manager-deployment-model/resource-manager-type.png)

次の図のアプリケーションは、Resource Manager でデプロイしたリソースを 1 つのリソース グループに含める方法を示しています。

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

さらに、リソース プロバイダー内にはリソース間の関係があります。

- 仮想マシンは BLOB ストレージにそのディスクを保存するための SRP で定義されている特定のストレージ アカウントに依存します。
- 仮想マシンは、NRP (必須) と、CRP で定義されている可用性セット (オプション) で定義されている、特定の NIC を参照します。
- NIC は、仮想マシンに割り当てられた IP アドレス (必須)、仮想ネットワークのサブネット(必須)、ネットワークのセキュリティ グループ (オプション) を参照します。
- 仮想ネットワーク内のサブネットは、ネットワークセキュリティ グループ (オプション) を参照します。
- Load Balancer のインスタンスは仮想マシンの NIC を含め(オプション)、Load Balancer パブリックまたはプライベート IP アドレス (オプション) を参照している IP アドレスのバックエンドプールを参照します。

## 従来のデプロイの特性

従来のデプロイ モデルはサービス管理モデルとしても知られています。

Azure サービス管理では、仮想マシンをホストするためのコンピューティング、ストレージ、またはネットワークは次によって提供されています:

- 仮想マシンをホストするためのコンテナーとして機能する必須のクラウド サービス (コンピューティング) 仮想マシンにはネットワーク インターフェイス カード (NIC) が自動的に提供され、IP アドレスは Azure によって割り当てられます。さらに、クラウド サービスには、外部のロード バランサーのインスタンス、パブリック IP アドレス、および Windows ベースのバーチャル マシンのリモート デスクトップとリモート PowerShell トラフィックと Linux ベースのバーチャル マシン用の Secure Shell (SSH) トラフィックを許可する既定のエンドポイントが含まれています。
- オペレーティング システム、一時、および追加のデータ ディスク (ストレージ) を含む、仮想マシンの VHD を格納するのに必要なストレージ アカウント。
- サブネット化された構造を作成できる、また仮想マシンが配置されているサブネットを指定することができる、追加のコンテナーとして機能する、省略可能な仮想ネットワーク (ネットワーク)。

従来のデプロイで作成されたリソースには次の特性があります。

- 次のいずれかのメソッドで作成:

  - [クラシック ポータル](https://manage.windowsazure.com)

        ![Classic portal](./media/resource-manager-deployment-model/azure-portal.png)

        または、プレビュー ポータルを使い、**クラシック** デプロイを指定 (Compute、Storage、ネットワーク).

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - Azure PowerShell では、サービス管理版のコマンドを使用してください。これらのコマンド名は、次に示すように *Verb-AzureNoun* 形式となります。

            Get-AzureDeployment

  - REST 操作のための[サービス管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)
  - Azure CLI コマンドは **asm** または既定のモードで実行されます。
- リソース タイプの名前に **(classic)** が含まれます。下の画像ではタイプとして "**ストレージ アカウント (クラシック)**" が表示されています。

    ![従来のタイプ](./media/resource-manager-deployment-model/classic-type.png)

Azure ポータルを利用し、従来のデプロイで作成されたリソースを引き続き管理できます。

コンポーネントと Azure サービス管理のための関係を示します。

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Resource Manager とリソース グループを使用する利点

Resource Manager はリソース グループの概念を加えます。Resource Manager で作成したリソースはすべてリソース グループ内に存在します。Resource Manager のデプロイ モデルにはいくつかの利点があります。

- ソリューションのサービスを個別に処理するのではなく、すべてのサービスをデプロイ、管理、監視できます。
- アプリケーションをアプリのライフサイクルを通して繰り返しデプロイできます。常にリソースが一貫した状態でデプロイされます。
- 宣言型のテンプレートを利用し、デプロイを定義できます。
- 正しい順序でデプロイされるようにリソース間の依存性を定義できます。
- ロールベースの Access Control (RBAC) が管理プラットフォームにネイティブ統合されるため、リソース グループのすべてのリソースにアクセス制御を適用できます。
- タグをリソースに適用し、サブスクリプションのすべてのリソースを論理的に整理できます。


Resource Manager の導入前は、従来のデプロイで作成されたリソースはリソース グループ内に存在しませんでした。Resource Manager が追加されたとき、すべてのリソースが遡及的に既定のリソース グループに追加されました。今、従来のデプロイでリソースを作成すると、デプロイ時にリソース グループを指定していなくても、リソースはそのサービスの既定のリソース グループ内に自動的に作成されます。ただし、リソース グループ内に存在するだけでは、リソースが Resource Manager モデルに変換されたことになりません。Virtual Machines、Storage、および Virtual Networks の場合、リソースが従来のデプロイで作成された場合、引き続き従来の操作でそれを操作する必要があります。

リソースを異なるリソース グループに移動したり、新しいリソースを既存のリソース グループに追加したりできます。そのため、リソース グループにはResource Manager で作成されたリソースと従来のデプロイで作成されたリソースが混在します。リソースが同じ操作に対応しないため、そのような混在が予想外の結果を生むことがあります。

場合によっては、宣言型のテンプレートを利用し、デプロイのためにスクリプトを簡略化できます。サービス管理から Resource Manager に既存のスクリプトを変換する代わりに、デプロイ戦略を作り直し、テンプレートのインフラストラクチャと構成を定義することの利点を生かすことを検討してください。

## タグの使用

タグを利用すれば、リソースを論理的に整理できます。Resource Manager で作成したリソースだけがタグに対応しています。従来のリソースにタグを適用することはできません。

リソース マネージャーのタグの利用に関する詳細については、「[タグを利用して Azure リソースを整理する](resource-group-using-tags.md)」を参照してください。

## デプロイ モデルで対応している操作

従来のデプロイ モデルで作成したリソースは Resource Manager の操作に対応していません。場合によっては、Resource Manager コマンドで従来のデプロイで作成したリソースに関する情報を取得できます。あるいは、従来のリソースを別のリソース グループに移動するなどの管理タスクを実行できますが、この種類が Resource Manager の操作に対応するという印象を与えるべきではありません。たとえば、クラシック デプロイで作成された仮想マシンを含むリソース グループがあるものとします。例として、次の PowerShell コマンドを実行します。

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

仮想マシンが返されます。
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

ただし、**Get-AzureRmVM** コマンドレットでは、Resource Manager でデプロイされた Virtual Machines のみが返されます。次のコマンドでは、クラシック デプロイで作成された仮想マシンは返されません。

    Get-AzureRmVM -ResourceGroupName ExampleGroup

一般的に、従来のデプロイで作成したリソースは Resource Manager のコマンドと連動しないと考えてください。

Resource Manager で作成したリソースを使用するとき、Resource Manager の操作を利用し、サービス管理の操作には切り替えないでください。

## Virtual Machines に関する考慮事項

Virtual Machines を使用するときには、重要な考慮事項がいくつかあります。

- 従来のデプロイ モデルで作成された仮想マシンは Resource Manager で作成された仮想ネットワークに含めることができません。
- Resource Manager のデプロイ モデルでデプロイされた仮想マシンは仮想ネットワークに含める必要があります。
- 従来のデプロイ モデルでデプロイされた仮想マシンは仮想ネットワークに含める必要がありません。

クラシック デプロイメントから Resource Manager のデプロイにリソースを移行する準備ができたら、「[Platform supported migration of IaaS resources from Classic to Azure Resource Manager (プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行)](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)」を参照してください。

Compute、Storage、Networking のリソースの移行に関する詳細については、「[Azure リソース マネージャーの Azure Compute、Network および Storage プロバイダー](./virtual-machines/virtual-machines-windows-compare-deployment-models.md)」を参照してください。

さまざまなデプロイ モデルから仮想ネットワークを接続する方法の詳細については、「[従来の Vnet を新しい Vnet に接続する](./virtual-network/virtual-networks-arm-asm-s2s.md)」を参照してください。

## 次のステップ

- 仮想マシン、ストレージ アカウント、仮想ネットワークを定義するテンプレートの作成に関するチュートリアルは、「[Resource Manager テンプレートのチュートリアル](resource-manager-template-walkthrough.md)」を参照してください。
- Resource Manager のテンプレートの構造について詳しくは、「[Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
- テンプレートをデプロイするためのコマンドについては、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。

<!---HONumber=AcomDC_0525_2016-->