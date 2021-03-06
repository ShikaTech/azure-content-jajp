サブネットまたは仮想マシン (VM) ネットワークポ インターフェイス上の選択したポートへのトラフィックを許可するネットワーク フィルターを作成して、Azure でポートを開くかエンドポイントを作成します。着信および発信の両方のトラフィックを制御するこれらのフィルターは、ネットワーク セキュリティ グループに配置され、トラフィックを受信するリソースに接続されます。

ポート 80 での Web トラフィックの一般的な例を使用して説明します。標準の TCP ポート 80 の Web 要求を処理するように構成されている仮想マシンがある場合 (適切なサービスを開始し、仮想マシン上の OS ファイアウォール規則を開いてください) の手順は、以下のとおりとなります。

1. ネットワーク セキュリティ グループを作成します。
2. 以下によってトラフィックを許可する受信の規則を作成します。
  - 宛先ポート範囲 "80"
  - 発信元ポート範囲"*"(任意のポートを許可)
  - 65,500 より小さな優先順位値 (既定の包括的な受信拒否規則より優先されるように設定)
3. ネットワーク セキュリティ グループと仮想マシンのネットワーク インターフェイスまたはサブネットを関連付けます。
    
HTTP トラフィックやリモート管理を許可する 1 つまたは 2 つの規則のみではなく、ネットワーク セキュリティ グループと規則を使用して、非常に複雑なネットワーク構成を作成して環境を保護することができます。詳細については、後述の「[詳細情報](#more-information-on-network-security-groups)」、または「[ネットワーク セキュリティ グループについて](../articles/virtual-network/virtual-networks-nsg.md)」を参照してください。

<!---HONumber=AcomDC_0601_2016-->