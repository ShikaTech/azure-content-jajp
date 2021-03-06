<properties
   pageTitle="Azure IoT プロトコル ゲートウェイ | Microsoft Azure"
   description="Azure IoT プロトコル ゲートウェイを使用して Azure IoT Hub の機能とプロトコル サポートを拡張する方法を説明します。"
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="kdotchko"/>

# IoT Hub に対するその他のプロトコルのサポート

Azure IoT Hub は AMQP、MQTT、および HTTP/1 プロトコルを介した通信をネイティブでサポートします。場合によっては、デバイスまたはフィールド ゲートウェイでこれらの標準プロトコルをいずれも使用できず、プロトコルの適応が必要になることがあります。このような場合は、カスタム ゲートウェイを使用できます。カスタム ゲートウェイによって IoT Hub に対するトラフィックをブリッジすることにより、IoT Hub エンドポイントにプロトコルを適応できます。IoT Hub に対するプロトコルの適用を可能にするには、[Azure IoT プロトコル ゲートウェイ](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)をカスタム ゲートウェイとして使用します。

## Azure IoT プロトコル ゲートウェイ

Azure IoT プロトコル ゲートウェイは、IoT Hub との大規模双方型デバイス通信用に設計された、プロトコル適応用フレームワークです。プロトコル ゲートウェイは、特定のプロトコルを介してデバイスの接続を受け入れるパススルー コンポーネントです。AMQP 1.0 経由の IoT Hub へのトラフィックをブリッジします。IoT プロトコル ゲートウェイは、さまざまなプロトコルとプロトコルのバージョンに対するサポートを追加する柔軟性を提供するためのオープン ソース ソフトウェア プロジェクトとして使用できます。

プロトコル ゲートウェイは、Azure Cloud Services の worker ロールを使用することで、高度にスケーラブルな方法で Azure にデプロイできます。さらに、プロトコル ゲートウェイはフィールド ゲートウェイなどのオンプレミス環境にデプロイできます。

Azure IoT プロトコル ゲートウェイには、MQTT プロトコルの動作を必要に応じてカスタマイズできるようにする MQTT プロトコル アダプターが含まれています。IoT Hub では MQTT v3.1.1 プロトコルの組み込みサポートを提供しているため、プロトコルのカスタマイズが必要な場合または追加機能に対して特定の要件が必要な場合にのみ MQTT プロトコル アダプターの使用を検討してください。

MQTT アダプターは、その他のプロトコルのプロトコル アダプターを構築するためのプログラミング モデルにもなっています。さらに、IoT プロトコル ゲートウェイのプログラミング モデルでは、カスタム認証、メッセージの変換、圧縮と圧縮解除、デバイスと IoT Hub 間のトラフィックの暗号化/暗号化解除などの特殊な処理を行うために、カスタム コンポーネントを接続することができます。

柔軟性を高めるために、プロトコル ゲートウェイと MQTT の実装はオープン ソース ソフトウェア プロジェクトで提供されます。これにより、必要に応じて実装をカスタマイズできます。

## 次のステップ

Azure IoT プロトコル ゲートウェイについて、およびそれを IoT ソリューションの一部として使用およびデプロイする方法については、以下をご覧ください。

* [Azure IoT protocol gateway repository on GitHub (GitHub の Azure IoT プロトコル ゲートウェイ リポジトリ)](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Azure IoT protocol gateway developer guide (Microsoft Azure IoT プロトコル ゲートウェイ開発者ガイド)](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

IoT Hub のデプロイの計画に関する詳細については、以下をご覧ください。

- [Event Hubs との比較][lnk-compare]
- [HA と DR のスケーリング][lnk-scaling]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [開発者ガイド][lnk-devguide]
- [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->