---
title: Az Azure Security teljesítményteszt v1 áttekintése
description: Azure Security teljesítményteszt v1 – áttekintés
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 39b6a5452c4bc0cba1e1153c266b83e87f6209ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90059312"
---
# <a name="overview-of-the-azure-security-benchmark-v1"></a>Az Azure biztonsági Teljesítménytesztének áttekintése (v1)

> [!NOTE]
> A legfrissebb Azure-os biztonsági teljesítményteszt [itt](overview.md)érhető el.

Az Azure biztonsági teljesítménytesztje olyan javaslatokat tartalmaz, amelyekkel nagyobb biztonságban tudhatja alkalmazásait és adatait az Azure-ban.

Ez a teljesítményteszt a felhő-központú ellenőrzési területekre koncentrál. Ezek a vezérlők összhangban vannak a jól ismert biztonsági összehasonlításokkal, például a Center for Internet Security (CIS) által a 7,1-es verzióban leírt szabályokkal.

Az Azure biztonsági Teljesítménytesztben a következő vezérlők használhatók: 

- [Hálózati biztonság](security-control-network-security.md)
- [Naplózás és figyelés](security-control-logging-monitoring.md)
- [Identitás- és hozzáférés-vezérlés](security-control-identity-access-control.md)
- [Adatvédelem](security-control-data-protection.md)
- [Biztonságirés-kezelés](security-control-vulnerability-management.md)
- [Leltár- és eszközfelügyelet](security-control-inventory-asset-management.md)
- [Biztonságos konfiguráció](security-control-secure-configuration.md)
- [Kártevők elleni védelem](security-control-malware-defense.md)
- [Adathelyreállítás](security-control-data-recovery.md)
- [Incidensmegoldás](security-control-incident-response.md)
- [Behatolási tesztek és Red Team-gyakorlatok](security-control-penetration-tests-red-team-exercises.md)

Letöltheti az [Azure Security teljesítményteszt v1 Excel-táblázatát](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)is.

## <a name="azure-security-benchmark-recommendations"></a>Az Azure biztonsági teljesítménytesztekkel kapcsolatos javaslatok 

Minden javaslat a következő információkat tartalmazza: 

- **Azure ID**: a javaslatnak megfelelő Azure biztonsági TELJESÍTMÉNYTESZT-azonosító. 
- **CIS-azonosító (k)**: a VIR-viszonyítási javaslat (ok), amelyek megfelelnek a javaslatnak.  
- **Felelősség**: azt, hogy az ügyfél vagy a szolgáltató (vagy mindkettő) felelős-e a javaslat végrehajtásáért. A biztonsági kötelezettségek a nyilvános felhőben vannak megosztva. Bizonyos biztonsági vezérlők csak a felhőalapú szolgáltató számára érhetők el, ezért a szolgáltató felelős az ilyen problémák kezeléséhez. Ezek általános megfigyelések – egyes szolgáltatások esetében a felelősség nem fog megjelenni, mint az Azure biztonsági Teljesítménytesztben. Ezeket a különbségeket az egyes szolgáltatásokra vonatkozó alapkövetelmények írják le. 
- **Részletek**: a javaslat indoklása és a megvalósításával kapcsolatos útmutatásra mutató hivatkozások. Ha Azure Security Center támogatja az ajánlást, a rendszer ezeket az információkat is felsorolja.

Örömmel vesszük a részletes visszajelzéseket és az aktív részvételt az Azure biztonsági teljesítményteszttel kapcsolatos erőfeszítésekben. Ha meg szeretné adni az Azure Security teljesítményteszt csapatának közvetlen bemenetét, töltse ki az űrlapot a következő címen: [https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark) .

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az első biztonsági ellenőrzést: [hálózati biztonság](security-control-network-security.md)
- Olvassa el az [Azure Security teljesítményteszt bevezetését](introduction.md)
- Az [Azure Security teljesítményteszt v1 Excel-táblázatának](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets) letöltése
