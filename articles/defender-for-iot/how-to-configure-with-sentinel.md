---
title: Az Azure Sentinel for Defender konfigurálása a IoT-hez
description: Ez a cikk azt ismerteti, hogyan konfigurálható az Azure Sentinel a Defender for IoT megoldás adatainak fogadására.
ms.topic: how-to
ms.date: 12/28/2020
ms.openlocfilehash: b481dd31b73e741d265a569076f1ddc076ad4a45
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778950"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel"></a>Az adatok összekötése a Defendertől az Azure Sentinel IoT 

Az IoT-összekötő Defender használatával továbbíthatja az összes Defender IoT-eseményeit az Azure Sentinelbe. 

Ez az integráció lehetővé teszi a szervezetek számára, hogy gyorsan azonosítsák a gyakran határokon átnyúló, illetve a határokon átívelő támadásokat. Emellett az Azure Sentinel biztonsági előkészítési, automatizálási és reagálási (SZÁRNYALó) képességeinek IoT való integrációja lehetővé teszi az automatikus válaszadást és a megelőzést a beépített, nem optimalizált forgatókönyvek használatával. 

## <a name="prerequisites"></a>Előfeltételek

- **Olvasási** és **írási** engedélyek azon a munkaterületen, amelyen az Azure Sentinel üzembe lett helyezve
- A **IoT Defender számára** engedélyezni kell a megfelelő IoT hub (ka) **t**
- **Közreműködői** engedélyekkel kell rendelkeznie a csatlakozni kívánt **előfizetéshez**

## <a name="connect-to-defender-for-iot"></a>Kapcsolódás a Defenderhez a IoT-hez

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd válassza ki a **IoT Defendert** Azure Security Center (IoT) a katalógusból.

1. A jobb oldali ablaktábla alján kattintson az **összekötő lap megnyitása** lehetőségre.

1. Kattintson a **Kapcsolódás** lehetőségre minden olyan IoT hub-előfizetés mellett, amelynek a riasztásait és az eszközök riasztásait az Azure Sentinel szolgáltatásba kívánja továbbítani.
    - Hibaüzenet jelenik meg, ha a Defender for IoT nincs engedélyezve legalább egy IoT Hub egy előfizetésen belül. Engedélyezze a Defender számára a IoT belüli IoT Hub a hiba eltávolításához.

1. Eldöntheti, hogy szeretné-e, hogy a Defender IoT az incidensek automatikus előállítását az Azure Sentinelben. Az **incidensek létrehozása** területen válassza az **Engedélyezés** lehetőséget az alapértelmezett elemzési szabály engedélyezéséhez, hogy a generált riasztásokból automatikusan hozzon létre incidenseket. Ez a szabály az **Analytics**  >  **aktív szabályok** területén módosítható vagy módosítható.

> [!NOTE]
> A kapcsolódási módosítások végrehajtása után az **előfizetések** listájának frissítése 10 másodpercig is eltarthat. 

## <a name="log-analytics-alert-view"></a>Log Analytics riasztás nézet

A Log Analytics vonatkozó sémájának használata a IoT riasztások megjelenítéséhez:

1. Nyissa meg a **naplók**  >  **SecurityInsights**  >  **SecurityAlert**, vagy keressen rá a **SecurityAlert** kifejezésre.

1. A szűréssel csak a IoT által generált riasztások jelennek meg a következő kql szűrő használatával:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Szolgáltatási megjegyzések

Az **előfizetés** csatlakoztatása után körülbelül 15 perccel később elérhetővé válik a hub-adatbázis az Azure sentinelben.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Defender a IoT az Azure Sentinelhez. A veszélyforrások észlelésével és a biztonsági adathozzáféréssel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan használhatja az Azure Sentinelt az [adatai és a potenciális fenyegetések](../sentinel/quickstart-get-visibility.md)megismerésére.
- Ismerje meg, hogyan [férhet hozzá a IoT biztonsági adataihoz](how-to-security-data-access.md)