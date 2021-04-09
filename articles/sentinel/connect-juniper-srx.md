---
title: Juniper Networks SRX-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogy a Juniper SRX adatösszekötővel hogyan hívhat le Juniper SRX-naplókat az Azure Sentinel szolgáltatásba. Megtekintheti a SRX-ben a munkafüzetek, a riasztások létrehozása és a vizsgálat javítása érdekében.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530637"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>A Juniper SRX-tűzfal összekapcsolása az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A Juniper SRX-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Juniper SRX tűzfala készülék az Azure Sentinelhez. A Juniper SRX adatösszekötővel egyszerűen csatlakoztathatja a SRX-naplókat az Azure Sentinel segítségével, így megtekintheti a munkafüzetek adatait, felhasználhatja egyéni riasztások létrehozására, és a vizsgálat javítására is felépítheti azt. A Juniper SRX és az Azure Sentinel közötti integráció a syslog használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedéllyel kell rendelkeznie az Azure Sentinel munkaterületen.

- A Juniper SRX-megoldást úgy kell konfigurálni, hogy a syslog használatával exportálja a naplókat.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Juniper SRX-naplók továbbítása a syslog-ügynökhöz  

Konfigurálja a Juniper SRX-t a syslog-üzenetek Azure Sentinel-munkaterületre történő továbbításához a syslog-ügynök használatával.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza ki a **Juniper SRX (előzetes verzió)** összekötőt, majd **nyissa meg az összekötő lapot**.

1. Kövesse a **JUNIPER SRX** -összekötő oldalának utasításait:

    1. A Linux-ügynök telepítése és előkészítése

        - Válasszon egy Azure Linux rendszerű virtuális gépet vagy egy nem Azure-beli Linux-gépet (fizikai vagy virtuális).

    1. A gyűjteni kívánt naplók konfigurálása

        - Válassza ki a létesítményeket és a megszakításokat a munkaterület-ügynökök konfigurációjában.

    1. A Juniper SRX konfigurálása és összekapcsolása

        - Kövesse az alábbi utasításokat a Juniper SRX a syslog továbbításához való konfigurálásához.
            - [Forgalmi naplók (biztonsági házirendek naplói)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [Rendszernaplók](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - A távoli kiszolgáló esetében használja a Linux-ügynököt futtató Linux-gép IP-címét.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat Log Analytics a syslog alatt jelenik meg.

Néhány hasznos minta lekérdezéshez tekintse meg az összekötő lap **következő lépések** lapját.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudta, hogyan csatlakoztatható a Juniper SRX az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
