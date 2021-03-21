---
title: NXLog-LinuxAudit-adatbázis összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogy a NXLog LinuxAudit-adatösszekötővel hogyan hívhat le LinuxAudit-naplókat az Azure Sentinel szolgáltatásba. Megtekintheti a munkafüzetek LinuxAudit, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 2010b21a3cdb81f2b2aa4180f87857862cd02bf5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700877"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>A NXLog-LinuxAudit összekötése az Azure Sentinel használatával

> [!IMPORTANT]
> A NXLog LinuxAudit-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

A [NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) -összekötő lehetővé teszi, hogy valós időben egyszerűen exportálja a linuxos biztonsági eseményeket az Azure Sentinel szolgáltatásba, így betekintést nyerhet a tartománynév-kiszolgáló tevékenységeibe a szervezeten belül. A NXLog LinuxAudit modul támogatja az egyéni naplózási szabályokat, és naplózott vagy bármely más felhasználói felületi szoftver nélkül gyűjt naplókat. Az IP-címek és a csoportok/felhasználói azonosítók a megfelelő nevük szerint vannak feloldva, így a [Linux](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) -naplók jobban érthetővé teszik a biztonsági elemzőket. A NXLog LinuxAudit és az Azure Sentinel közötti integrációt REST APIon keresztül könnyíti meg.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>NXLog-LinuxAudit konfigurálása és összekapcsolása

A NXLog beállítható úgy, hogy JSON formátumban küldje el az eseményeket közvetlenül az Azure Sentinelnek.

1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** elemre, és válassza a **NXLog LinuxAudit** -összekötő lehetőséget.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse a *NXLog felhasználói útmutató* integrációs témakörének részletes utasításait, [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) segítségével konfigurálja a továbbítást a REST API használatával.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplókban** az  **egyéni naplók** szakaszban, a *LinuxAudit_CL* táblában.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan használhatja a NXLog-LinuxAudit a linuxos biztonsági események Azure Sentinelbe való betöltésére. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
