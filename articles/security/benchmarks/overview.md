---
title: Az Azure Security teljesítményteszt v2 áttekintése
description: Azure Security teljesítményteszt V2 – Áttekintés
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3bc97a932e622838315f717cc7b036a559787805
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369157"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Az Azure biztonsági teljesítményteszt áttekintése (v2)

Az Azure Security benchmark (ASB) az Azure-ban a munkaterhelések, az adatok és a szolgáltatások biztonságának javítását segítő ajánlott eljárásokat és javaslatokat tartalmaz.

Ez a teljesítményteszt egy átfogó biztonsági útmutató részét képezi, amely a következőket is tartalmazza:

- **Felhőalapú bevezetési keretrendszer** – útmutatás a biztonsághoz, beleértve a [stratégiát](/azure/cloud-adoption-framework/strategy/define-security-strategy), a [szerepköröket és a felelősségeket](/azure/cloud-adoption-framework/organize/cloud-security), az [Azure Top 10 ajánlott biztonsági eljárásait](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)és a [hivatkozások megvalósítását](/azure/cloud-adoption-framework/ready/enterprise-scale/).
- **Azure Well-Architected Framework** – útmutató az Azure-beli számítási [feladatok biztonságossá](/assessments/?mode=pre-assessment&session=local) tételéhez.
- **Microsoft Security – ajánlott eljárások** – az Azure-beli példákkal. [](/security/compass/microsoft-security-compass-introduction)

 Az Azure biztonsági teljesítményteszt a felhő-központú ellenőrzési területekre koncentrál. Ezek a vezérlők konzisztensek a jól ismert biztonsági referenciaértékekkel, például a Center for Internet Security (CIS) vezérli az 7,1-es és a National Institute of Standards and Technology (NIST) SP 800-53-es verzióját.
Az Azure biztonsági teljesítményteszt a következő vezérlőket tartalmazza:

| ASB-vezérlési tartományok | Description 
|--|--|
| [Hálózati &nbsp; Biztonság &nbsp; (NS)](security-controls-v2-network-security.md) | A hálózati biztonság magában foglalja az Azure-hálózatok biztonságossá tételének és védelmének, beleértve a virtuális hálózatok biztonságossá tételét, a magánhálózati kapcsolatok létrehozását, a külső támadások megelőzését és enyhítését, valamint a DNS biztonságossá tételét. |
| [Identitáskezelés &nbsp; &nbsp; (IM)](security-controls-v2-identity-management.md) | Az Identitáskezelés kiterjed a biztonságos identitás-és hozzáférés-vezérlések létrehozására Azure Active Directory használatával, beleértve az egyszeri bejelentkezést, az erős hitelesítéseket, a felügyelt identitásokat (és a szolgáltatási alapelveket) az alkalmazásokhoz, a feltételes hozzáféréshez és a fiókok rendellenességének figyeléséhez. |
| [Emelt szintű &nbsp; hozzáférés &nbsp; (PA)](security-controls-v2-privileged-access.md) | Az emelt szintű hozzáférés kiterjed az Azure-bérlőhöz és-erőforrásokhoz való rendszerjogosultságú hozzáférések elleni védelemre, többek között a felügyeleti modell, a rendszergazdai fiókok és a privilegizált hozzáférésű munkaállomások szándékos és véletlen kockázattal való ellátására szolgáló vezérlők körére. |
| [Adatvédelem &nbsp; &nbsp; (DP)](security-controls-v2-data-protection.md) | Az adatvédelem magában foglalja az adatvédelem inaktív állapotban való felügyeletét, az átvitelt és a jogosult hozzáférési mechanizmusok útján történő védelmet, beleértve a bizalmas adategységek felderítését, osztályozását, védelmét és figyelését az Azure hozzáférés-vezérlési, titkosítási és naplózási funkciójával. |
| [Eszközkezelés &nbsp; &nbsp; (am)](security-controls-v2-asset-management.md) | Az eszközkezelés magában foglalja az Azure-erőforrások biztonságának láthatóságát és irányítását, beleértve a biztonsági személyzet engedélyeivel kapcsolatos javaslatokat, az eszközök leltárba való biztonsági hozzáférését, valamint a szolgáltatások és erőforrások jóváhagyásának kezelését (leltár, nyomon követés és helyes). |
| [Naplózás &nbsp; és &nbsp; fenyegetések &nbsp; észlelése (lt)](security-controls-v2-logging-threat-detection.md) | A naplózás és a veszélyforrások észlelése kiterjed az Azure-beli fenyegetések észlelésére, valamint az Azure-szolgáltatások naplófájljainak engedélyezésére, gyűjtésére és tárolására, beleértve az észlelési, a nyomozási és a Szervizelési folyamatokat, amelyekkel magas színvonalú riasztások állíthatók elő az Azure-szolgáltatások natív veszélyforrások észlelésével. magában foglalja a naplók gyűjtését is Azure Monitor, központosítva a biztonsági elemzést az Azure Sentinel, az időszinkronizálás és a naplózás megőrzése mellett. |
| [Incidens &nbsp; válasza &nbsp; (IR)](security-controls-v2-incident-response.md) | Az incidensek válasza az incidensek életciklusának előkészítését, észlelését és elemzését, a tárolást és az incidens utáni tevékenységeket is magában foglalja, beleértve az Azure-szolgáltatások, például a Azure Security Center és a Sentinel használatát az incidensek megválaszolásának automatizálásához. |
| [Testtartás &nbsp; és &nbsp; sebezhetőségi &nbsp; kezelés &nbsp; (PV)](security-controls-v2-posture-vulnerability-management.md) | A testhelyzet és a biztonsági rések kezelése az Azure biztonsági helyzetek felmérésére és fejlesztésére, például a sebezhetőségi vizsgálatra, a behatolás tesztelésére és szervizelésére, valamint a biztonsági konfiguráció nyomon követésére, jelentéskészítésre és az Azure-erőforrások javítására koncentrál. |
| [Endpoint &nbsp; Security &nbsp; (es)](security-controls-v2-endpoint-security.md) | A végpontok biztonsága kiterjed a végpontok észlelésének és válaszának szabályozására, beleértve a végpontok észlelését és válaszát (EDR) és a kártevők elleni szolgáltatást az Azure-környezetekben található végpontokhoz. |
| [Biztonsági mentés &nbsp; és &nbsp; helyreállítás &nbsp; (br)](security-controls-v2-backup-recovery.md) | A biztonsági mentés és helyreállítás kiterjed a vezérlőkre, így biztosítva, hogy az adatokat és a konfigurációkat a különböző szolgáltatási rétegek végzik, ellenőrzik és védik. |
| [Irányítási &nbsp; és &nbsp; stratégiai &nbsp; (GS)](security-controls-v2-governance-strategy.md) | A szabályozás és a stratégia útmutatást nyújt egy koherens biztonsági stratégia és dokumentált irányítási megközelítés biztosításához a biztonsági garanciák kialakításához és fenntartásához, beleértve a szerepkörök és felelősségek létrehozását a Felhőbeli biztonsági funkciók, az egységes műszaki stratégia és a támogatási szabályzatok és szabványok tekintetében. |

## <a name="azure-security-benchmark-recommendations"></a>Az Azure biztonsági teljesítménytesztekkel kapcsolatos javaslatok

Minden javaslat a következő információkat tartalmazza:

- **Azure ID**: a javaslatnak megfelelő Azure biztonsági TELJESÍTMÉNYTESZT-azonosító.
- **CIS Controls v 7.1 azonosító (k)**: az ehhez a javaslathoz tartozó CIS Controls v 7.1 vezérlő (k).
- **NIST sp 800-53 R4 azonosító (k)**: az ehhez a javaslathoz tartozó nist SP 800-53 R4 (mérsékelt) vezérlő (k).
- **Részletek**: a javaslat indoklása és a megvalósításával kapcsolatos útmutatásra mutató hivatkozások. Ha Azure Security Center támogatja az ajánlást, a rendszer ezeket az információkat is felsorolja.
- **Felelősség**: a javaslat végrehajtásáért az ügyfél, a szolgáltató vagy mindkettő felelős. A biztonsági kötelezettségek a nyilvános felhőben vannak megosztva. Bizonyos biztonsági vezérlők csak a felhőalapú szolgáltató számára érhetők el, ezért a szolgáltató felelős az ilyen problémák kezeléséhez. Ezek általános megfigyelések – egyes szolgáltatások esetében a felelősség nem fog megjelenni az Azure biztonsági Teljesítménytesztben felsoroltak közül. Ezeket a különbségeket az egyes szolgáltatásokra vonatkozó alapkövetelmények írják le.
- **Ügyfelek biztonsági résztvevői**: az ügyfél szervezetének azon [biztonsági funkciói](/azure/cloud-adoption-framework/organize/cloud-security#security-functions) , akik felelősséggel vonhatók, felelősek vagy megtekinthetők a megfelelő felügyelethez. A vállalat biztonsági szervezetének struktúrájától, illetve az Azure-biztonsággal kapcsolatos szerepköröktől és felelősségtől függően a szervezettől eltérő lehet.

> [!NOTE]
> A ASB és az iparági referenciaértékek (például a NIST és a CIS) közötti vezérlési leképezések csak azt jelzik, hogy egy adott Azure-szolgáltatás teljes mértékben vagy részben a NIST-ben vagy a CIS-ben meghatározott ellenőrzési követelményt is felhasználható. Tisztában kell lennie azzal, hogy az ilyen implementáció nem feltétlenül a VIR-ben vagy a NIST-ben lévő megfelelő ellenőrzés teljes megfelelőségét jelenti.

Örömmel vesszük a részletes visszajelzéseket és az aktív részvételt az Azure biztonsági teljesítményteszttel kapcsolatos erőfeszítésekben. Ha meg szeretné adni az Azure Security teljesítményteszt-csoport közvetlen bemenetét, töltse ki az űrlapot a következő címen: https://aka.ms/AzSecBenchmark

## <a name="download"></a>Letöltés

Az Azure biztonsági teljesítménytesztet [táblázatos formátumban](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark)töltheti le.

## <a name="next-steps"></a>Következő lépések 
- Tekintse meg az első biztonsági ellenőrzést: [hálózati biztonság](security-control-network-security.md)
- Olvassa el az [Azure Security teljesítményteszt bevezetését](introduction.md)
- Ismerje meg az [Azure biztonsági alapjait](../fundamentals/index.yml)