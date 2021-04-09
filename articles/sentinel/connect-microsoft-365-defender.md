---
title: Microsoft 365 Defender-adatbázis összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan foglalhat be Microsoft 365 Defenderből származó incidenseket, riasztásokat és nyers eseményeket az Azure Sentinel szolgáltatásba.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 6500805a4dc7e26f5e1bc601df9ea78279ae17e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709342"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Adatok összekötése Microsoft 365 Defender-ből az Azure Sentinelbe

> [!IMPORTANT]
>
> **Microsoft 365 Defender** korábbi nevén **Microsoft Threat Protection** vagy **MTP**.
>
> **A Microsoft Defender for Endpoint** korábban a **Microsoft Defender komplex veszélyforrások elleni védelmi** vagy **MDATP** néven ismert.
>
> Előfordulhat, hogy a régi nevek még mindig használatban vannak egy adott időszakban.

## <a name="background"></a>Háttér

Az Azure Sentinel [Microsoft 365 Defender-(M365D-)](/microsoft-365/security/mtp/microsoft-threat-protection) összekötője az incidensek integrálásával lehetővé teszi, hogy az összes M365D-incidenst és riasztást az Azure sentinelbe továbbítsa, és megtartja a két portál között szinkronizált incidenseket. A M365D incidensek közé tartoznak a riasztások, az entitások és az egyéb kapcsolódó információk, amelyeket a M365D's Component Services **Microsoft Defender for Endpoint**, a Microsoft Defender for **Identity**, a **Microsoft Defender for Office 365** és a **Microsoft Cloud app Security**.

Az összekötő lehetővé teszi, hogy a Microsoft Defender által az Azure Sentinelbe **irányított speciális vadászati** eseményeket továbbítsa, így lehetővé teszi az MDE fejlett vadászati lekérdezéseknek az Azure sentinelbe való másolását, a Sentinel-riasztások és az MDE nyers események adatait, hogy további elemzéseket nyújtson, és a naplókat nagyobb adatmegőrzéssel tárolja a log Analytics.

További információ az incidensek integrálásáról és a speciális vadászati eseményekről: [Microsoft 365 Defender-integráció az Azure sentinelrel](microsoft-365-defender-sentinel-integration.md).

> [!IMPORTANT]
>
> A Microsoft 365 Defender-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

## <a name="prerequisites"></a>Előfeltételek

- Microsoft 365 Defender számára érvényes licenccel kell rendelkeznie, a következő témakörben leírtak szerint: [Microsoft 365 Defender előfeltételei](/microsoft-365/security/mtp/prerequisites). 

- Azure Active Directory-ben **globális rendszergazdának** vagy **biztonsági rendszergazdának** kell lennie.

## <a name="connect-to-microsoft-365-defender"></a>Kapcsolódás Microsoft 365 Defenderhez

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, válassza **Microsoft 365 Defender (előzetes verzió)** elemet a katalógusból, és válassza az **összekötő lap megnyitása** lehetőséget.

1. A **csatlakozási incidensek & riasztások** szakaszban a **konfiguráció** alatt kattintson a **csatlakozási incidensek & riasztások** gombra.

1. Az incidensek ismétlődésének elkerülése érdekében ajánlott bejelölni az **összes Microsoft incidens-létrehozási szabályt az adott termékeknél** jelölőnégyzetet.

    > [!NOTE]
    > Ha engedélyezi a Microsoft 365 Defender-összekötőt, az összes M365D-összetevő összekötője (a cikk elején említettek) automatikusan a háttérben csatlakozik. Az egyik összetevő összekötő leválasztásához először le kell választania a Microsoft 365 Defender-összekötőt.

1. Az M365 Defender-incidensek lekérdezéséhez használja a következő utasítást a lekérdezési ablakban:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Ha szeretné összegyűjteni a Microsoft Defender által a végpontnak szánt speciális vadászati eseményeket, a következő típusú események gyűjthetők össze a kapcsolódó speciális vadászati táblázatokból.

    1. A táblák jelölőnégyzetének megjelölése a gyűjteni kívánt események típusai alapján:

       | Table name (Táblázat neve) | Események típusa |
       |-|-|
       | DeviceInfo | Számítógép adatai (beleértve az operációs rendszer adatait) |
       | DeviceNetworkInfo | Gépek hálózati tulajdonságai |
       | DeviceProcessEvents | Folyamat létrehozása és kapcsolódó események |
       | DeviceNetworkEvents | Hálózati kapcsolat és kapcsolódó események |
       | DeviceFileEvents | Fájlok létrehozása, módosítása és más fájlrendszerbeli események |
       | DeviceRegistryEvents | Beállításjegyzékbeli bejegyzések létrehozása és módosítása |
       | DeviceLogonEvents | Bejelentkezések és egyéb hitelesítési események |
       | DeviceImageLoadEvents | DLL-betöltési események |
       | DeviceEvents | További események típusai |
       | DeviceFileCertificateInfo | Az aláírt fájlok tanúsítványának adatai |
       |

    1. Kattintson a **módosítások alkalmazása** lehetőségre.

    1. A Log Analytics speciális vadászati tábláinak lekérdezéséhez írja be a tábla nevét a fenti listából a lekérdezési ablakban.

## <a name="verify-data-ingestion"></a>Adatfeldolgozás ellenőrzése

Az összekötő oldal adatdiagramja azt jelzi, hogy adatfeldolgozást végez. Láthatja, hogy az incidensek, riasztások és események esetében egy sort mutat, az események sorban pedig az összes engedélyezett tábla eseményeinek összesítése. Ha engedélyezte az összekötőt, az alábbi KQL-lekérdezésekkel adhatók meg konkrétabb diagramok.

Használja a következő KQL-lekérdezést a bejövő M365 Defender-incidensek gráfához:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

A következő KQL-lekérdezéssel hozhatja elő az esemény-kötet gráfját egyetlen táblához (módosítsa a *DeviceEvents* táblát a választott táblázatra):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

A **következő lépések** lapon néhány hasznos munkafüzetet, példákat és elemzési szabályt is talál. Ezeket a helyszínen futtathatja, vagy módosíthatja és mentheti.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan integrálhatja Microsoft 365 Defender-incidenseket, és hogyan teheti elérhetővé a Microsoft Defender for Endpoint, az Azure Sentinel, a Microsoft 365 Defender Connector használatával történő fejlett események adatait. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](./tutorial-detect-threats-built-in.md).
