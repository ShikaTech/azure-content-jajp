<properties 
   pageTitle="VPN ゲートウェイの計画と設計 | Microsoft Azure"
   description="クロスプレミス、ハイブリッド、および VNet 間接続のための VPN ゲートウェイの計画と設計について説明します。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc"/>

# VPN ゲートウェイの計画と設計

クロスプレミス構成および VNet 間構成の計画と設計は、ネットワークのニーズに応じて、ごく単純になる場合もあれば、かなり複雑になる場合もあります。この記事では、計画と設計に関する基本的な考慮事項を説明します。

## 計画


### <a name="compare"></a>クロスプレミス接続オプション

オンプレミスのサイトを仮想ネットワークに安全に接続するには、サイト間、ポイント対サイト、および ExpressRoute の 3 つのオプションがあります。利用可能なさまざまなクロスプレミス接続を比較します。選択するオプションは、次のようなさまざまな考慮事項によって異なります。


- ソリューションにどのようなスループットが必要か。
- セキュリティで保護された VPN 経由のパブリックなインターネットで通信するか、またはプライベートな接続で通信するか。
- 使用できるパブリック IP アドレスを持っているか。
- VPN デバイスを使用する予定があるか。 その場合、互換性があるか。
- 接続するコンピューターは数台か、サイトとの永続的な接続が必要か。
- 作成するソリューションにはどのような種類の VPN Gateway が必要か。
- どのゲートウェイ SKU を使用するか。


次の表は、ソリューションに最適な接続オプションを決定するのに役立ちます。


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>VPN の種類と SKU 別のゲートウェイの要件


VPN ゲートウェイを作成するときは、使用するゲートウェイの SKU を指定する必要があります。次の 3 つの VPN Gateway の SKU があります。

- Basic
- Standard
- 高性能

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]



### <a name="aggthroughput"></a>ゲートウェイの種類と合計スループットの見積もり

次の表は、ゲートウェイの種類、および予測される合計スループットを示したものです。見積もられた合計スループットは、設計の決定要因になる可能性があります。料金はゲートウェイの SKU によって異なります。料金については、[VPN Gateway の料金](https://azure.microsoft.com/pricing/details/vpn-gateway/)のページをご確認ください。この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]



### <a name="wf"></a>ワークフロー

次のリストは、クラウド接続の一般的なワークフローを示しています。

1.	接続トポロジを設計および計画し、接続するすべてのネットワークのアドレス空間の一覧を作成します。
2.	Azure 仮想ネットワークを作成します。 
3.	仮想ネットワークの VPN ゲートウェイを作成します。
4.	オンプレミス ネットワークやその他の仮想ネットワークへの接続を作成および構成します (必要な場合)。
5.	Azure VPN ゲートウェイのポイント対サイト接続を作成および構成します (必要な場合)。
 

## 設計

### <a name="topologies"></a>接続トポロジ

まず、[接続トポロジ](vpn-gateway-topology.md)に関する記事の図を確認します。この記事には、基本的なダイアグラム、各トポロジのデプロイメント モデル (Resource Manager またはクラシック)、および構成のデプロイに使用できるデプロイ ツールが記載されています。

### <a name="designbasics"></a>デザインの基礎

以降のセクションでは、VPN ゲートウェイの基本について説明します。また、[ネットワーク サービスの制限事項](../articles/azure-subscription-service-limits.md#networking-limits)についても考慮する必要があります。


#### <a name="subnets"></a>サブネットについて

環境に最適な接続を計画および設計する際に、使用できる IP アドレス範囲とサブネットについて考慮することは非常に重要です。

VPN ゲートウェイを構成するには、VNet のゲートウェイ サブネットを作成する必要があります。すべてのゲートウェイ サブネットを正常に動作させるには、GatewaySubnet という名前を付ける必要があります。ゲートウェイ サブネットに別の名前を付けず、ゲートウェイ サブネットに VM やその他のものをデプロイしないでください。ゲートウェイ サブネットの詳細については、VPN ゲートウェイに関する記事の[ゲートウェイ サブネット](vpn-gateway-about-vpngateways.md#gwsub)のセクションを参照してください。

接続を作成しているときは、多くの場合、接続間でのサブネット アドレス範囲の重複ついて注意する必要があります。サブネットの重複とは、1 つの仮想ネットワークまたはオンプレミスの場所が、他の場所に含まれているアドレス空間と同じアドレス空間を含んでいることです。つまり、ローカルのオンプレミス ネットワークのネットワーク エンジニアに、Azure IP アドレス空間/サブネットで使用する範囲を除外しておくように依頼する必要があります。ローカル オンプレミス ネットワークで使用されていないアドレス空間が必要です。

VNet 間接続の操作でも、サブネットの重複を避けることが重要です。サブネットが重複し、IP アドレスが送信側と受信側の両方の VNet に存在する場合、VNet 間接続の作成は失敗します。この場合、送信先アドレスが送信側 VNet の一部であるため、Azure はもう一方の VNet にデータをルーティングできません。



#### <a name="local"></a>ローカル ネットワーク ゲートウェイについて

ローカル ネットワーク ゲートウェイは通常、オンプレミスの場所を指します。クラシック デプロイ モデルでは、ローカル ネットワーク ゲートウェイはローカル サイトとして参照されます。ローカル ネットワーク ゲートウェイに名前とオンプレミス VPN デバイスのパブリック IP アドレスを指定し、オンプレミスの場所に存在するアドレスのプレフィックスを指定します。Azure は、ネットワーク トラフィックの宛先アドレスのプレフィックスを参照して、ローカル ネットワーク ゲートウェイに指定された構成を確認し、それに応じてパケットをルーティングします。これらのアドレス プレフィックスは、必要に応じて変更できます。ローカル ネットワーク ゲートウェイの詳細については、VPN ゲートウェイに関する記事の[ローカル ネットワーク ゲートウェイ](vpn-gateway-about-vpngateways.md#lng)のセクションを参照してください。


#### <a name="gwtype"></a>ゲートウェイの種類について

トポロジに適したゲートウェイの種類を選択することは重要です。間違った種類を選択すると、ゲートウェイは正しく動作しません。ゲートウェイの種類は、ゲートウェイ自体の接続方法を指定するものであり、Resource Manager のデプロイ モデルに必要な構成設定です。

ゲートウェイには、次の種類があります。

- Vpn
- ExpressRoute

#### <a name="connectiontype"></a>接続の種類について

各構成には、特定の接続の種類が必要です。接続には、次の種類があります。

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>VPN の種類について

各構成を適切に機能させるためには、特定の VPN の種類が必要です。同じ VNet に対してサイト間接続とポイント対サイト接続を作成するなど、2 つの構成を組み合わせる場合は、両方の接続の要件を満たす VPN の種類を使用する必要があります。ポイント対サイト接続とサイト間接続を共存させるときは、Azure Resource Manager のデプロイ モデルの場合はルート ベースの VPN の種類、クラシック デプロイ モデルの場合は動的ゲートウェイを使用する必要があります。

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

次の表は、VPN の種類と各接続構成の組み合わせを示しています。ゲートウェイの VPN の種類が、作成する構成と対応していることを確認してください。


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>サイト間接続用の VPN デバイス

サイト間接続を構成するには、デプロイメント モデルに関係なく、以下の項目が必要です。

- Azure VPN ゲートウェイと互換性のある VPN デバイス
- NAT の内側にない、公開された IPv4 IP アドレス

VPN デバイスの構成経験が必要です。VPN デバイスの詳細については、[VPN デバイス](vpn-gateway-about-vpn-devices.md)に関するページを参照してください。VPN デバイスの記事には、検証済みのデバイスと、未検証デバイスの要件に関する情報、および各デバイスのデバイス構成ドキュメントへのリンク (ドキュメントがある場合) が含まれています。

### <a name="forcedtunnel"></a>強制トンネル ルーティングを検討する

ほとんどの構成では、強制トンネリングを構成できます。強制トンネリングを使用すると、検査および監査のために、サイト間の VPN トンネルを介して、インターネットへのすべてのトラフィックをオンプレミスの場所に戻すようにリダイレクトする (つまり、"強制する") ことができます。これは、ほとんどの企業 IT ポリシーの重要なセキュリティ要件です。

強制トンネリングを使用しない場合、Azure の VM からインターネットへのトラフィックは、トラフィックを検査または監査できるオプションを使用せずに、常に Azure ネットワーク インフラストラクチャからインターネットへ直接トラバースします。認証されていないインターネット アクセスは、情報の漏えいまたは他の種類のセキュリティ侵害を招く可能性があります。

強制トンネリングの構成の詳細については、[クラシック デプロイメント モデルのための強制トンネリング](vpn-gateway-about-forced-tunneling.md)に関するページと、[Resource Manager デプロイメント モデルのための強制トンネリング](vpn-gateway-about-forced-tunneling.md)に関するページを参照してください。

**強制トンネリングの図**

![Forced Tunneling connection](./media/vpn-gateway-plan-design/forced-tunnel.png "forced tunneling")

両方のデプロイ モデルで、さまざまなツールを使用して強制トンネリング接続を構成できます。詳細については、下の表を参照してください。この表は、この構成について新しい記事、新しいデプロイメント モデル、追加のツールが利用できるようになったら更新されるものです。記事が利用できるようになったら、表から直接リンクできるようにします。

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]



## 次のステップ

設計に役立つ詳しい情報については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)」と「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」の記事を参照してください。

接続トポロジの詳細については、[接続トポロジ](vpn-gateway-topology.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0518_2016-->