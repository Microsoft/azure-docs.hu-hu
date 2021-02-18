---
title: Az Azure Security Center által nyújtott adatbiztonság | Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 0c21c916d152188031212b47704dfb41d3f64a82
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596282"
---
# <a name="azure-security-center-data-security"></a>Adatbiztonság Azure Security Center

A fenyegetések megelőzése, észlelése és elhárítása érdekében a Azure Security Center gyűjti és dolgozza fel a biztonsággal kapcsolatos adatokat, beleértve a konfigurációs információkat, a metaadatokat, az eseménynaplókat és egyebeket. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.

Ez a cikk azt ismerteti, hogyan történik az adatkezelés és a védelem a Security Centerban.

## <a name="data-sources"></a>Adatforrások
Security Center a következő forrásokból származó adatok elemzésével biztosítja a biztonsági állapot láthatóságát, a biztonsági rések azonosítását és a javasolt megoldások használatát, valamint az aktív fenyegetések észlelését:

- **Azure-szolgáltatások**: az adott szolgáltatás erőforrás-szolgáltatójának használatával folytatott kommunikációval üzembe helyezett Azure-szolgáltatások konfigurációjával kapcsolatos információkat használja.
- **Hálózati forgalom**: a Microsoft infrastruktúrájának mintául szolgáló hálózati forgalmi metaadatokat használ, például a forrás/cél IP-cím/port, a csomag mérete és a hálózati protokoll.
- **Partneri megoldások**: az integrált partneri megoldásokból, például tűzfalakból és antimalware-megoldásokból származó biztonsági riasztásokat használ.
- **A gépek**: konfigurációs adatokat és információkat használ a biztonsági eseményekről, például a Windows-eseményekről és a naplókról, valamint a számítógépek syslog-üzeneteiről.


## <a name="data-protection"></a>Adatvédelem

### <a name="data-segregation"></a>Az adatok elkülönítése
Az adat a szolgáltatás minden összetevőjénél logikailag elkülönítve marad. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes.

### <a name="data-access"></a>Az adatok elérése
A biztonsági javaslatok és a lehetséges biztonsági fenyegetések kivizsgálása érdekében a Microsoft munkatársai hozzáférhetnek az Azure-szolgáltatások által összegyűjtött vagy elemzett információkhoz, beleértve a folyamat-létrehozási eseményeket és egyéb összetevőket, amelyek véletlenül magukban foglalhatják a gépek vásárlói adatait vagy személyes adatait. 

Betartjuk a [Microsoft Online Services adatvédelmi](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880)mellékletét, amely azt jelzi, hogy a Microsoft nem használja fel az ügyféladatokat, és nem ad hozzá információt semmilyen hirdetési vagy hasonló kereskedelmi célra. Az ügyféladatokat szükség esetén csak arra használjuk, hogy biztosítsuk Önnek az Azure-szolgáltatásokat, beleértve a szolgáltatások nyújtásának megfelelő célokat is. Minden, az ügyféladatokhoz fűződő jog az Ön tulajdonában marad.

### <a name="data-use"></a>Adathasználat
A Microsoft a különböző bérlők által látott minták és fenyegetések felderítésével fokozza a megelőzési és észlelési képességeiket; ezt a jelen [adatvédelmi nyilatkozatban](https://privacy.microsoft.com/privacystatement)ismertetett adatvédelmi kötelezettségvállalásoknak megfelelően tesszük.

## <a name="manage-data-collection-from-machines"></a>Az adatgyűjtés kezelése a gépekről
Ha bekapcsolja a Security Centert az Azure-ban, az adatgyűjtés bekapcsolódik minden előfizetésénél. Az előfizetések adatgyűjtését Security Center is engedélyezheti. Ha az adatgyűjtés engedélyezve van, Security Center kiépíti az Log Analytics ügynököt az összes meglévő támogatott Azure-beli virtuális gépen és a létrehozott újakon.

A Log Analytics ügynök különböző biztonsággal kapcsolatos konfigurációkat vizsgál, és azokat az eseményeket [Windows esemény-nyomkövetés](/windows/win32/etw/event-tracing-portal) (ETW) nyomkövetésbe. Az operációs rendszer emellett az eseménynaplóba írandó eseményeket hoz létre a gép futtatása során. A gyűjtött adatok például a következők: az operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek, a bejelentkezett felhasználó és a bérlő azonosítója. A Log Analytics ügynök beolvassa az Eseménynapló-bejegyzéseket és a ETW-nyomkövetéseket, és átmásolja őket a munkaterületre (ok) elemzésre. A Log Analytics ügynök a folyamat-létrehozási eseményeket és a parancssori naplózást is lehetővé teszi.

Ha nem használja az Azure Defendert, akkor a biztonsági házirendben letilthatja a virtuális gépekről történő adatgyűjtést is. Az Azure Defender által védett előfizetések esetében az adatgyűjtés szükséges. A virtuálisgép-lemez pillanatképeinek és összetevőinek gyűjtése akkor is engedélyezve lesz, ha letiltotta az adatgyűjtést.

Megadhatja azt a munkaterületet és régiót, ahol a gépekről gyűjtött adatokat tárolják. Az alapértelmezett érték a gépekről gyűjtött adatok tárolása a legközelebbi munkaterületen az alábbi táblázatban látható módon:

| Virtuális gép geo                                      | Munkaterület geo  |
|---------------------------------------------|----------------|
| Egyesült Államok, Brazília, Dél-Afrika         | Egyesült Államok  |
| Kanada                                      | Kanada         |
| Európa (kivéve az Egyesült Királyságot)           | Európa         |
| Egyesült Királyság                              | Egyesült Királyság |
| Ázsia (kivéve India, Japán, Korea, Kína) | Ázsia és a Csendes-óceáni térség   |
| Dél-Korea                                       | Ázsia és a Csendes-óceáni térség   |
| India                                       | India          |
| Japán                                       | Japán          |
| Kína                                       | Kína          |
| Ausztrália                                   | Ausztrália      |
|                                             |                |

> [!NOTE]
> Az **Azure Defender a Storage számára** a kapcsolódó Azure-erőforrás helye szerint regionálisan tárolja az összetevőket. További információ: az [Azure Defender for Storage bemutatása](defender-for-storage-introduction.md).


## <a name="data-consumption"></a>Adathasználat

Az ügyfelek a következő adatfolyamokból érhetik el Security Center kapcsolódó adatait:


| Adatfolyam                                                                                | Adattípusok                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure-tevékenység naplója](../azure-monitor/essentials/activity-log.md)                       | Minden biztonsági riasztás, [jóváhagyott Security Center igény](security-center-just-in-time.md) szerinti hozzáférési kérés, valamint az [adaptív alkalmazások](security-center-adaptive-application.md)által létrehozott összes riasztás.|
| [Azure Monitor-naplók](../azure-monitor/data-platform.md)                      | Minden biztonsági riasztás.                                                                                                                                                                                                |
| [Azure Resource Graph](../governance/resource-graph/overview.md)                      | A biztonsági riasztások, a biztonsági javaslatok, a sebezhetőségi felmérés eredményei, a biztonságos pontszám adatai, a megfelelőségi ellenőrzések állapota és egyebek.                                                                       |
| [Azure Security Center – REST API](/rest/api/securitycenter/) | Biztonsági riasztások, biztonsági javaslatok és egyebek.                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudta, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik azok védelméről. 

További információ a Azure Security Centerről: [Mi az Azure Security Center?](security-center-introduction.md)