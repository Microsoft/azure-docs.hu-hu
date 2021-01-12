---
title: A Azure Security Center díjszabása
description: Azure Security Center két üzemmódban érhető el, az Azure Defender és anélkül.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2020
ms.author: memildin
ms.openlocfilehash: 99f47df86d89e9daf2bc8878b868b04b7038ffd5
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071204"
---
# <a name="pricing-of-azure-security-center"></a>A Azure Security Center díjszabása
Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít az Azure-ban, a helyszínen és egyéb felhőszolgáltatásokban futó számítási feladatokhoz. Lehetővé teszi a hibrid Felhőbeli számítási feladatok, az aktív védelem és a fenyegetések kialakulásának csökkentését, valamint az intelligens észlelést, amely megkönnyíti a gyors fejlődést jelentő számítógépes kockázatok megtartását.


## <a name="free-option-vs-azure-defender-enabled"></a>Ingyenes lehetőség és Azure Defender engedélyezve

Security Center két üzemmódban érhető el:

- **Azure DEFENDER off** (ingyenes) – az Azure defender nélküli Security Center ingyenes az összes Azure-előfizetésben, amikor első alkalommal látogatják meg az Azure Security Center irányítópultot a Azure Portal, vagy ha az API-n keresztül engedélyezte a programozott módon. Az ingyenes mód használatával biztonsági házirendet, folyamatos biztonsági értékelést és gyakorlati biztonsági ajánlásokat biztosít az Azure-erőforrások védelméhez.

- Az **Azure Defender** engedélyezése az Azure Defender számára lehetővé teszi az ingyenes mód képességeit a magán-és más nyilvános felhőkben futó számítási feladatokra, így egységes biztonsági felügyeletet és veszélyforrások elleni védelmet biztosít a hibrid Felhőbeli számítási feladatokban. Az Azure Defender főbb funkciói:

    - **Microsoft Defender for Endpoint** – az Azure Defender for Servers tartalmazza a [Microsoft Defender for](https://www.microsoft.com/microsoft-365/security/endpoint-defender) végpontot a végpontok átfogó észleléséhez és válaszához (EDR). További információ a Microsoft Defender és a végpont együttes használatának előnyeiről az Azure Defender [használata Security Center integrált EDR megoldásával](security-center-wdatp.md).
    - **Biztonsági rések keresése a virtuális gépek és a tárolók** beállításjegyzékében – egyszerűen üzembe helyezhet egy képolvasót az összes olyan virtuális gépen, amely az iparág legfejlettebb megoldást nyújt a biztonsági rések kezelésére. Megtekintheti, megvizsgálhatja és elháríthatja az eredményeket közvetlenül a Security Centeron belül. 
    - **Hibrid biztonság** – az összes helyszíni és Felhőbeli számítási feladatra kiterjedő egységes biztonsági nézet. Alkalmazza a biztonsági házirendeket, és folyamatosan mérje fel a hibrid Felhőbeli számítási feladatok biztonságát a biztonsági normáknak való megfelelés biztosítása érdekében. Több forrásból származó biztonsági adatok gyűjtése, keresése és elemzése, beleértve a tűzfalakat és más partneri megoldásokat.
    - **Veszélyforrások elleni védelem – riasztások** – a fejlett viselkedési elemzések és a Microsoft intelligens biztonsági gráf a fejlődő Cyber-támadásokat biztosítanak. A beépített viselkedési elemzések és gépi tanulás képes azonosítani a támadásokat és a napi kihasználat. Figyelheti a hálózatokat, a gépeket és a Felhőbeli szolgáltatásokat a beérkező támadásokhoz és a szabálysértés utáni tevékenységekhez. Egyszerűsítse a nyomozást az interaktív eszközökkel és a kontextusbeli fenyegetések intelligenciával.
    - **Hozzáférés és alkalmazás-vezérlőelemek** (AAC) – a kártevők és más nemkívánatos alkalmazások blokkolása gépi tanulásra alkalmas, az engedélyezési és letiltási listát létrehozó munkaterhelésekhez igazított gépi tanulási javaslatokat alkalmazva. Csökkentse a hálózati támadási felületet az Azure-beli virtuális gépeken található felügyeleti portokra vonatkozó, igény szerinti, vezérelt hozzáféréssel. Az AAC drasztikusan csökkenti a találgatásos támadásokat és más hálózati támadások kockázatát.
    - **Tároló-biztonsági funkciók** – a biztonsági rések kezelése és a valós idejű veszélyforrások elleni védelem kihasználása a tároló környezetekben. Ha engedélyezi az **Azure Defender számára a tároló**-beállításjegyzékek használatát, akár 12 óráig is eltarthat, amíg az összes funkció be nem fejeződik. A díjak a csatlakoztatott beállításjegyzékbe leküldett egyedi tároló-lemezképek számától függenek. Ha egyszer már megvizsgálta a képet, a rendszer nem számítja fel újra, hacsak nem módosítják, és még egyszer leküldik.
    - **A veszélyforrások védelme az Azure-környezethez kapcsolódó erőforrások esetében** – az Azure Defender az Azure-beli natív, az Azure-szolgáltatások általános fenyegetés elleni védelmét is biztosítja az összes erőforráshoz: Azure Resource Manager, Azure DNS, Azure hálózati réteg és Azure Key Vault. Az Azure Defender egyedi láthatósággal rendelkezik az Azure felügyeleti rétegében és a Azure DNS rétegben, és így biztosíthatja a rétegekhez kapcsolódó Felhőbeli erőforrások védelméhez.


## <a name="try-azure-defender-free-for-30-days"></a>Próbálja ki az Azure Defendert ingyen 30 napig
Az Azure Defender az első 30 napon belül díjmentes. 30 nap elteltével a szolgáltatás használatának folytatásához automatikusan elindul a használati díj.

## <a name="enable-azure-defender"></a>Az Azure Defender engedélyezése
Az Azure Defender teljes Azure-előfizetését védelemmel láthatja el, és az előfizetésben lévő összes erőforrás örökli a védelmet.

Az Azure Defender engedélyezése:

1. A Security Center főmenüjében válassza a **díjszabás & beállítások** lehetőséget.
1. Válassza ki a frissíteni kívánt előfizetést.
1. A frissítéshez válassza az **Azure Defender** lehetőséget.
1. Válassza a **Mentés** lehetőséget.

Az alábbi példa egy előfizetésre vonatkozó díjszabási oldalt mutat be. Megfigyelheti, hogy az Azure Defender minden csomagja külön díjszabású, és egyénileg be-és kikapcsolható.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="A Security Center díjszabási oldala a portálon":::

> [!NOTE]
> Az összes Security Center funkció engedélyezéséhez, beleértve a veszélyforrások elleni védelmi képességeket is, engedélyeznie kell az Azure Defender szolgáltatást a megfelelő munkaterheléseket tartalmazó előfizetésben. A munkaterület szintjének engedélyezése nem teszi lehetővé az Azure-erőforrások igény szerinti elérését, az adaptív alkalmazások vezérlését és a hálózati észleléseket. Emellett az egyetlen Azure Defender-csomag is elérhető a munkaterület szintjén az Azure Defender for Servers és az Azure Defender a gépeken futó SQL-kiszolgálókhoz.
>
> Az **Azure Defendert** engedélyezheti a Storage-fiókokhoz az előfizetés szintjén vagy az erőforrás szintjén.
> Az **Azure Defender for SQL** engedélyezhető az előfizetés szintjén vagy az erőforrás szintjén is.
> Az **Azure Database for MariaDB/MySQL/PostgreSQL** veszélyforrások elleni védelmét csak az erőforrás szintjén engedélyezheti.


## <a name="faq---pricing-and-billing"></a>Gyakori kérdések – díjszabás és számlázás 

- [Hogyan követhetem nyomon a szervezetem, hogy az Azure Defender milyen változásokat Azure Security Center?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Mik a Security Center által kínált csomagok?](#what-are-the-plans-offered-by-security-center)
- [Hogyan engedélyezhetem az Azure Defendert az előfizetéshez?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Engedélyezhetem az Azure Defendert kiszolgálók részhalmazában lévő kiszolgálókhoz az előfizetésben?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Ha már van licencem a Microsoft Defender for Endpoint szolgáltatáshoz, kedvezményt kaphatok az Azure Defender számára?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Engedélyezve van-e az Azure Defender for Servers előfizetése, ha nem futó kiszolgálókat fizetnem?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [A Log Analytics ügynök nélküli gépek után kell díjat fizetni?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Ha egy Log Analytics ügynök több munkaterületre jelent jelentést, akkor kétszer kell fizetnem?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Ha egy Log Analytics-ügynök több munkaterületre is jelentést készít, a 500 MB-os ingyenes adatfeldolgozás az összes rajtuk elérhető?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [A teljes munkaterülethez vagy a gépenként kizárólag a 500 MB-os ingyenes adatfeldolgozást számítjuk ki?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)

### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Hogyan követhetem nyomon a szervezetem, hogy az Azure Defender milyen változásokat Security Center?
Az Azure-előfizetések több rendszergazdai jogosultsággal rendelkezhetnek a díjszabási beállítások módosításához. Ha szeretné megtudni, hogy melyik felhasználó módosította a változást, használja az Azure-tevékenység naplóját.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Az Azure-tevékenység naplója a díjszabás változási eseményét mutatja":::

Ha a felhasználó adatai nem szerepelnek az oszlop **által kezdeményezett eseményben** , tekintse át az esemény JSON-fájlját a vonatkozó részletekért.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Azure-Tevékenységnaplók JSON-kezelője":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Mik a Security Center által kínált csomagok? 
Security Center két ajánlattal rendelkezik: 

- Az Azure Security Center ingyenes verziója 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Hogyan engedélyezhetem az Azure Defendert az előfizetéshez? 
Az előfizetéshez az alábbi módokon engedélyezheti az Azure Defender használatát: 

|Metódus  |Utasítások  |
|---------|---------|
|A Azure Portal Azure Security Center lapja|[Az Azure Defender engedélyezése](#enable-azure-defender)|
|REST API|[Díjszabási API](/rest/api/securitycenter/pricings)|
|Azure CLI|[az Security díjszabása](/cli/azure/security/pricing)|
|PowerShell|[Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)|
|Azure Policy|[Csomagok díjszabása](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json)|
|||

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Engedélyezhetem az Azure Defendert kiszolgálók részhalmazában lévő kiszolgálókhoz az előfizetésben?
Nem. Ha engedélyezi az [Azure Defender számára](defender-for-servers-introduction.md) az előfizetéshez tartozó kiszolgálókat, az előfizetésben lévő összes kiszolgálót az Azure Defender fogja védeni. 

Egy másik lehetőség az Azure Defender engedélyezése a kiszolgálók számára a Log Analytics munkaterület szintjén. Ha ezt teszi, csak az adott munkaterületre jelentett kiszolgálók lesznek védve és számlázva. Számos funkció azonban elérhetetlenné válik. Ezek közé tartozik az igény szerinti virtuális gépekhez való hozzáférés, a hálózati észlelések, a szabályozások megfelelősége, az adaptív hálózat megerősítése, az adaptív alkalmazások vezérlése és egyebek. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Ha már van licencem a Microsoft Defender for Endpoint szolgáltatáshoz, kedvezményt kaphatok az Azure Defender számára?
Ha már rendelkezik Microsoft Defender-licenccel a végponthoz, nem kell fizetnie az Azure Defender-licenc adott részének.

A kedvezmény megerősítéséhez lépjen kapcsolatba Security Center támogatási csapatával, és adja meg a megfelelő munkaterület-azonosítót, régiót és licenc-információkat.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Engedélyezve van-e az Azure Defender for Servers előfizetése, ha nem futó kiszolgálókat fizetnem? 
Nem. Ha engedélyezi [Az Azure Defender számára az előfizetéshez tartozó kiszolgálókat](defender-for-servers-introduction.md) , óradíjat kell fizetnie a kiszolgálók futtatásához. A kikapcsolt kiszolgálók után nem számítunk fel díjat. 

> [!TIP]
> Ez a Security Center által védett egyéb erőforrástípusok esetében is érvényes. 

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>A Log Analytics ügynök nélküli gépek után kell díjat fizetni?
Igen. Ha engedélyezi az [Azure Defender számára az előfizetéshez tartozó kiszolgálókat](defender-for-servers-introduction.md) , az adott előfizetésben lévő gépek akkor is különböző védelmet kapnak, ha még nem telepítette a log Analytics ügynököt.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Ha egy Log Analytics ügynök több munkaterületre jelent jelentést, akkor kétszer kell fizetnem? 
Igen. Ha úgy konfigurálta a Log Analytics-ügynököt, hogy két vagy több különböző Log Analytics-munkaterületre küldje el az adatküldés adatait, akkor minden olyan munkaterületért fizetnie kell, amelyhez telepítve van a "biztonság" vagy az "antimalware" megoldás. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Ha egy Log Analytics-ügynök több munkaterületre is jelentést készít, a 500 MB-os ingyenes adatfeldolgozás az összes rajtuk elérhető?
Igen. Ha úgy konfigurálta a Log Analytics-ügynököt, hogy két vagy több különböző Log Analytics-munkaterületre küldje el az adatküldés adatait, 500 MB szabad adatfeldolgozást fog kapni. A rendszer minden olyan munkaterületre kiszámítja, amelyet jelentettek, naponta, és minden olyan munkaterület számára elérhető, amelyeken telepítve van a "biztonság" vagy az "antimalware" megoldás. A 500 MB-nál nagyobb mennyiségű adatot kell fizetnie.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>A teljes munkaterülethez vagy a gépenként kizárólag a 500 MB-os ingyenes adatfeldolgozást számítjuk ki?
Napi 500 MB-nyi ingyenes adatfeldolgozást biztosít minden, a munkaterülethez csatlakozó gépen. Kifejezetten a Azure Security Center által közvetlenül gyűjtött biztonsági adattípusokhoz.

Ez az adatmennyiség az összes csomópont napi átlaga. Így még akkor is, ha egyes gépek 100-MB-ot küldenek, mások pedig 800-MB-ot küldenek, ha az összeg nem lépi túl a **[gépek számát] x 500-MB** szabad korlátot, nem számítunk fel külön díjat.

## <a name="next-steps"></a>Következő lépések
Ez a cikk a Security Center díjszabási lehetőségeit ismerteti. Kapcsolódó anyagok esetében lásd:

- [Az Azure számítási feladatok költségeinek optimalizálása](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [A választott pénznem díjszabása és a régiójuk szerint](https://azure.microsoft.com/pricing/details/security-center/)
- Érdemes lehet felügyelni a költségeket, és korlátozni a megoldáshoz összegyűjtött adatok mennyiségét, ha korlátozza azt egy adott ügynökre. A [megoldás célcsoportja](../azure-monitor/insights/solution-targeting.md) lehetővé teszi, hogy hatókört alkalmazzon a megoldásra, és a munkaterületen lévő számítógépek egy részhalmazát célozza meg. Ha megoldás-célcsoportot használ, Security Center listázza a munkaterületet úgy, hogy ne legyen megoldás.