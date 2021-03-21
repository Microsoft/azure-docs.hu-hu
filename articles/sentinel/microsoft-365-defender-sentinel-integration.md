---
title: Microsoft 365 Defender-integráció az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogy Microsoft 365 Defender és az Azure Sentinel integrációja lehetővé teszi az Azure Sentinel használatát az univerzális incidensek sorában, miközben M365D's-erősségeket biztosít a M365 biztonsági incidensek kivizsgálásához, valamint a Defender-összetevők fejlett vadászati adatainak az Azure Sentinelbe való betöltéséhez is.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745921"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Microsoft 365 Defender-integráció az Azure Sentinelrel

> [!IMPORTANT]
> A Microsoft 365 Defender-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

> [!IMPORTANT]
>
> **Microsoft 365 Defender** korábbi nevén **Microsoft Threat Protection** vagy **MTP**.
>
> **A Microsoft Defender for Endpoint** korábban a **Microsoft Defender komplex veszélyforrások elleni védelmi** vagy **MDATP** néven ismert.
>
> Előfordulhat, hogy a régi nevek még mindig használatban vannak egy adott időszakban.

## <a name="incident-integration"></a>Incidensek integrációja

Az Azure Sentinel [Microsoft 365 Defender-(M365D-)](/microsoft-365/security/mtp/microsoft-threat-protection) incidens-integrációja lehetővé teszi, hogy az összes M365D-incidenst az Azure sentinelbe továbbítsa, és mindkét portál között szinkronizálja őket. A M365D-ből (korábbi nevén Microsoft Threat Protection vagy MTP) származó incidensek tartalmazzák az összes kapcsolódó riasztást, entitást és releváns információt, így elegendő kontextust biztosítanak az osztályozás és az előzetes vizsgálat végrehajtásához az Azure Sentinelben. A Sentinelben az incidensek kétirányúan szinkronizálva maradnak a M365D, így kihasználhatja az incidensek vizsgálatának mindkét portáljának előnyeit.

Ez az integráció lehetővé teszi Microsoft 365 biztonsági incidensek számára, hogy a láthatóságot a teljes szervezeten belül az elsődleges incidensek várólistájának részeként kezelje, így láthatja és korrelálhatja az M365 incidenseket a többi Felhőbeli és helyszíni rendszerével együtt. Ugyanakkor lehetővé teszi, hogy kihasználja a M365D egyedi erősségeit és képességeit a részletes vizsgálatokhoz, valamint egy M365 élményt a M365 ökoszisztémán belül. A M365 Defender több M365-termék riasztásait gazdagítja és csoportosítja, és csökkenti a SOC incidens-várólista méretét, és lerövidíti a megoldási időt. A M365 Defender-verem részét képező Component Services a következők:

- **Microsoft Defender for Endpoint** (MDE, korábban MDATP)
- **Microsoft Defender for Identity** (MDI, korábban AATP)
- **Microsoft Defender for Office 365** (mdo, korábban O365ATP)
- **Microsoft Cloud app Security** (MCAS)

A riasztások ezen összetevőktől való gyűjtése mellett a M365 Defender saját riasztásokat hoz létre. Incidenseket hoz létre az összes riasztásból, és elküldi őket az Azure Sentinelnek.

### <a name="common-use-cases-and-scenarios"></a>Gyakori használati esetek és forgatókönyvek

- M365 Defender-incidensek egykattintásos összekapcsolásával, beleértve az M365 Defender-összetevőkből származó összes riasztást és entitást az Azure Sentinel szolgáltatásban.

- Kétirányú szinkronizálás a Sentinel és a M365D incidensek között az állapot, a tulajdonos és a záró ok miatt.

- Kihasználhatja a M365 Defender riasztás-csoportosítási és-bővítési funkcióit az Azure Sentinelben, így csökkentheti a megoldáshoz szükséges időt.

- Az Azure Sentinel-incidensek és a párhuzamos M365 Defender-incidensek közötti kontextusbeli szoros kapcsolat a mindkét portálon végzett vizsgálatok elősegítése érdekében.

### <a name="connecting-to-microsoft-365-defender"></a>Csatlakozás Microsoft 365 Defenderhez

Miután engedélyezte a Microsoft 365 Defender adatösszekötőt [incidensek és riasztások gyűjtésére](connect-microsoft-365-defender.md), a M365D incidensek megjelennek az Azure Sentinel incidensek sorában, a **terméknév** mezőben a **Microsoft 365 Defender** használatával, röviddel azután, hogy azok a M365 Defender szolgáltatásban jönnek létre.
- Akár 10 percet is igénybe vehet, amikor az incidenst a M365 Defender hozza létre az Azure Sentinel szolgáltatásban megjelenő időpontig.

- Az incidensek betöltésére és szinkronizálására külön díj nélkül kerül sor.

A M365 Defender-integráció csatlakoztatása után az összes összetevő-riasztási összekötő (MDE, MDI, MDO, MCAS) automatikusan csatlakoztatva lesz a háttérben, ha még nem voltak ilyenek. Ha bármelyik összetevő-licencet a M365 Defender csatlakoztatása után vásárolta meg, akkor az új terméktől érkező riasztások és incidensek továbbra is az Azure Sentinel szolgáltatásba kerülnek, további konfiguráció vagy díj nélkül.

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>M365 Defender-incidensek és a Microsoft incidensek létrehozási szabályai

- Az M365 Defender által létrehozott incidensek a M365 biztonsági termékektől érkező riasztások alapján jönnek létre egyéni M365-logikával.

- Az Azure Sentinel szolgáltatásban a Microsoft incidensek létrehozási szabályai ugyanazon riasztásokból származó incidenseket is létrehoznak, a (más) egyéni Azure Sentinel Logic használatával.

- A két mechanizmus együttes használata teljes mértékben támogatott, és ez a konfiguráció megkönnyíti az új M365 Defender incidens-létrehozási logikára való áttérést. Ez azonban **ismétlődő incidenseket** hoz létre ugyanahhoz a riasztásokhoz.

- Ha nem szeretne ismétlődő incidenseket létrehozni ugyanahhoz a riasztásokhoz, javasoljuk, hogy az ügyfelek kikapcsolják az összes **Microsoft incidens-létrehozási szabályt** a M365 termékekhez (MDE, MDI és mdo – lásd az alábbi MCAS) a M365 Defender csatlakoztatásakor. Ezt úgy teheti meg, hogy megjelöli a megfelelő jelölőnégyzetet az összekötő lapon. Ne feledje, hogy ha ezt teszi, az incidens-létrehozási szabályok által alkalmazott szűrők nem lesznek alkalmazva a M365 Defender incidens-integrációra.

- A Microsoft Cloud App Security-(MCAS-) riasztások esetében nem minden riasztási típus van előkészítve a M365 Defender számára. Annak ellenőrzéséhez, hogy továbbra is incidensek vannak-e az összes MCAS-riasztáshoz, meg kell őriznie vagy létre kell hoznia a **Microsoft incidens-létrehozási szabályait** a M365D *nem beépített* riasztási típusokhoz.

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>M365 Defender-incidensek használata az Azure Sentinelben és a kétirányú szinkronizálásban

A M365 Defender-incidensek az Azure Sentinel incidensek várólistájában jelennek meg **Microsoft 365 Defender** termék nevével, és a többi Sentinel-incidenshez hasonló részletekkel és funkciókkal. Minden incidens a M365 Defender portálon található párhuzamos incidensre mutató hivatkozást tartalmaz.

Mivel az incidens a M365 Defenderben fejlődik, és további riasztásokat vagy entitásokat ad hozzá, az Azure Sentinel incidens ennek megfelelően frissülni fog.

A M365 incidens állapotának, zárásának vagy hozzárendelésének módosítása a M365D vagy az Azure Sentinel szolgáltatásban hasonlóan a másik incidensek várólistáján is megfelelően frissül. A szinkronizálás mindkét portálon közvetlenül az incidens módosítása után történik, és nem jár késéssel. Előfordulhat, hogy a legutóbbi módosítások megtekintéséhez frissítésre van szükség.

Az M365 Defender az egyik incidensből származó összes riasztást átviheti egy másikba, ami az incidensek egyesítését eredményezi. Ha ez történik, az Azure Sentinel-incidensek megjelennek a változásokon. Az egyik incidens tartalmazni fogja az eredeti incidensek összes riasztását, a másik incidens pedig automatikusan bezáródik, az "átirányított" címkével kiegészítve.

> [!NOTE]
> Az Azure Sentinel-incidensek legfeljebb 150 riasztást tartalmazhatnak. A M365D incidensek ennél többhöz is tartozhatnak. Ha a több mint 150 riasztást tartalmazó M365D incidens szinkronizálva van az Azure Sentinel szolgáltatással, a Sentinel-incidens "150 +" riasztást jelenít meg, és a M365D párhuzamos incidensre mutató hivatkozást biztosít, ahol a riasztások teljes készlete látható lesz.

## <a name="advanced-hunting-event-collection"></a>Speciális vadászati események gyűjtése

A Microsoft 365 Defender-összekötő lehetővé teszi a **speciális vadászati** események – a nyers események típusú adatok – Microsoft 365 Defender és annak összetevő-szolgáltatásainak az Azure sentinelbe való továbbítását is. Jelenleg összegyűjtheti a [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) [speciális vadászati](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) eseményeit, és közvetlenül az Azure Sentinel-munkaterületen, a célra létrehozott táblákba továbbíthatja őket. Ezek a táblák a Microsoft 365 Defender portálon használt sémára épülnek, így teljes körű hozzáférést biztosít a speciális vadászati események teljes készletéhez, és lehetővé teszi a következőket:

- Egyszerűen másolhatja a meglévő Microsoft Defender-végpontok speciális vadászati lekérdezéseit az Azure Sentinelbe.

- A nyers eseménynaplók segítségével további elemzéseket biztosíthat a riasztásokról, a vadászatokról és a vizsgálatokról, és összekapcsolhatja ezeket az eseményeket másokkal az Azure Sentinel további adatforrásaival.

- A naplókat a Microsoft Defender for Endpoint vagy a Microsoft 365 Defender alapértelmezett megőrzési idejének megtartásával tárolja. Ezt úgy teheti meg, hogy beállítja a munkaterület megőrzését, vagy a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan veheti igénybe a Microsoft 365 Defender és az Azure Sentinel együttes használatát a Microsoft 365 Defender-összekötő használatával.

- Utasításokat kaphat [a Microsoft 365 Defender-összekötő engedélyezéséhez](connect-microsoft-365-defender.md).
- Hozzon létre [Egyéni riasztásokat](tutorial-detect-threats-custom.md) , és vizsgálja meg az [incidenseket](tutorial-investigate-cases.md).
