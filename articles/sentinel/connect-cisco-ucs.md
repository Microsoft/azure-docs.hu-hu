---
title: A Cisco Unified Computing System (FKR)-eszközök összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Cisco FKR-adatösszekötőt a Cisco FKR-naplók Azure Sentinelbe való lekéréséhez. Megtekintheti a Cisco FKR-beli adattáblákat, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
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
ms.openlocfilehash: 15e31b8dc5ac6db5861e3ea0fb5390ddf0a8c945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530671"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>A Cisco Unified Computing System (FKR) összekötése az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A Cisco FKR-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Cisco Unified Computing System (FKR) készülék az Azure Sentinelhez. A Cisco FKR adatösszekötővel egyszerűen csatlakoztathatja az FKR-naplókat az Azure Sentinel szolgáltatáshoz, így megtekintheti a munkafüzetek adatait, felhasználhatja egyéni riasztások létrehozásához és a vizsgálat javítására. A Cisco FKR és az Azure Sentinel közötti integráció a syslog használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedéllyel kell rendelkeznie az Azure Sentinel munkaterületen.

- A Cisco FKR-megoldást úgy kell konfigurálni, hogy a syslog használatával exportálja a naplókat.

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>Cisco FKR-naplók továbbítása a syslog-ügynöknek  

Konfigurálja a Cisco FKR-t, hogy a syslog-ügynökön keresztül továbbítsa a syslog-üzeneteket az Azure Sentinel-munkaterületre.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza ki a **Cisco FKR (előzetes verzió)** összekötőt, majd **nyissa meg az összekötő lapot**.

1. Kövesse a **Cisco FKR** -összekötő oldalának utasításait:

    1. A Linux-ügynök telepítése és előkészítése

        - Válasszon egy Azure Linux rendszerű virtuális gépet vagy egy nem Azure-beli Linux-gépet (fizikai vagy virtuális).

    1. A gyűjteni kívánt naplók konfigurálása

        - Válassza ki a létesítményeket és a megszakításokat a munkaterület-ügynökök konfigurációjában.

    1. A Cisco FKR konfigurálása és összekapcsolása

        - [Ezeket az utasításokat](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) követve konfigurálja a Cisco FKR-t a syslog továbbítására. A távoli kiszolgáló esetében használja a Linux-ügynököt futtató Linux-gép IP-címét.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat Log Analytics a syslog alatt jelenik meg.

Néhány hasznos minta lekérdezéshez tekintse meg az összekötő lap **következő lépések** lapját.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Cisco FKR az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
