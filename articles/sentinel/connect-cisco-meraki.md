---
title: Cisco Meraki-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Cisco Meraki-összekötőt a Cisco Meraki-naplók Azure Sentinelbe való lekéréséhez. Megtekintheti a Cisco Meraki-adatbázisait a munkafüzetekben, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: dc2d2a0724f18a02a5b667eb1004963a326ec360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745992"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>A Cisco Meraki és az Azure Sentinel összekötése

> [!IMPORTANT]
> A Cisco Meraki-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Cisco Meraki (MX/MS/MR) készülék az Azure Sentinelhez. A Cisco Meraki-adatösszekötővel könnyedén csatlakoztathatja Cisco Meraki-naplóit az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Cisco Meraki és az Azure Sentinel közötti integráció a telepített Log Analytics ügynökkel rendelkező syslog-kiszolgáló használatát teszi lehetővé. Egy Kusto függvényen alapuló, egyéni kialakítású naplózási elemzőt is használ.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedéllyel kell rendelkeznie az Azure Sentinel munkaterületen.

- A Cisco Meraki-megoldását úgy kell konfigurálni, hogy a syslog használatával exportálja a naplókat.

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Cisco Meraki-naplók küldése az Azure Sentinelnek a syslog-ügynök használatával  

Konfigurálja a Cisco Meraki-t, hogy a syslog-ügynökön keresztül továbbítsa a syslog-üzeneteket az Azure Sentinel-munkaterületre.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza ki a **Cisco Meraki (előzetes verzió)** összekötőt, majd **nyissa meg az összekötő lapot**.

1. Kövesse a **Cisco Meraki** -összekötő oldalának utasításait:

    1. A Linux-ügynök telepítése és előkészítése

        - Válasszon egy Azure Linux rendszerű virtuális gépet vagy egy nem Azure-beli Linux-gépet (fizikai vagy virtuális).

    1. A gyűjteni kívánt naplók konfigurálása

        - Válassza ki a létesítményeket és a megszakításokat a **munkaterület-ügynökök konfigurációjában**.

    1. A Cisco Meraki-eszköz (ek) konfigurálása és csatlakoztatása

        - Kövesse [ezeket az utasításokat](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) a Cisco Meraki-eszköz (ek) konfigurálásához a syslog továbbításához. A távoli kiszolgáló esetében használja a Linux-ügynököt futtató Linux-gép IP-címét.

## <a name="validate-connectivity-and-find-your-data"></a>Kapcsolat ellenőrzése és az adataik megkeresése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek az Azure Sentinelben. 

A sikeres kapcsolatok létrejötte után az adat megjelenik **a naplókban, a** *naplózás kezelése* szakaszban a *syslog* táblában.

Ez az adatösszekötő egy Kusto függvény alapján egy elemzőtől függ, hogy a várt módon működjön-e. [Kövesse az alábbi lépéseket](https://aka.ms/sentinel-ciscomeraki-parser) a **CiscoMeraki** Kusto függvény alias létrehozásához. Ezután beírhatja a `CiscoMeraki` lekérdezési ablakot az adatlekérdezéshez.

Néhány hasznos lekérdezési minta a Connector lap **következő lépések** lapján található.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakozhat a Cisco Meraki az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
