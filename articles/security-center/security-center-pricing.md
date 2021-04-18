---
title: Azure Security Center ingyenes és engedélyezett Azure Defender
description: Ismerje meg, milyen előnyökkel jár, ha Azure Defender felhőbeli számítási feladatok védelmére a Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/23/2021
ms.openlocfilehash: aa65989953f761ff915383fcb59da7f36ea98dab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600482"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure Security Center ingyenes és engedélyezett Azure Defender
Azure Defender első 30 napban ingyenes. A 30 nap végén, ha a szolgáltatás további használata mellett dönt, automatikusan elkezdjük a használat díjszabását.

A Díjszabási beállítások & **frissíthet** a Rövid [útmutató:](enable-azure-defender.md)Az Azure Defender. Az árképzéssel kapcsolatos részleteket a választott pénznemben és a régiónak megfelelően a [díjszabást Security Center meg.](https://azure.microsoft.com/pricing/details/security-center/)

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Milyen előnyökkel jár a Azure Defender?

Security Center kétféle módban létezik:

- **Azure Defender KI** (ingyenes) – Security Center Azure Defender nélküli előfizetés ingyenes az összes Azure-előfizetésben, amikor először látogatja meg az Azure Security Center-irányítópultot az Azure Portal-ban, vagy ha programozott módon, API-n keresztül engedélyezi. Ennek az ingyenes módnak a használata biztonsági szabályzatokat, folyamatos biztonsági értékelést és beavatkozást segítő biztonsági javaslatokat biztosít az Azure-erőforrások védelméhez.

- **Azure Defender ON** – Az Azure Defender engedélyezése kiterjeszti az ingyenes mód képességeit a magán- és más nyilvános felhőkben futó számítási feladatokra, így egységes biztonsági felügyeleti és fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. Az Azure Defender főbb Azure Defender:

    - **Microsoft Defender végponthoz** – Azure Defender kiszolgálókhoz a [Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) átfogó végpontészlelést és -választ (EDR) nyújt. További információ a Microsoft Defender végponthoz való használatának előnyeiről és Azure Defender az Security Center integrált [EDR-megoldásának használatával kapcsolatos cikkből.](security-center-wdatp.md)
    - **Virtuális gépek és** tárolóregisztrálók biztonsági rések vizsgálata – Könnyedén üzembe helyezhet egy szkennert az összes virtuális gépen, amely az iparág legfejlettebb biztonsági réskezelési megoldását biztosítja. Az eredmények megtekintése, kivizsgálása és orvoslása közvetlenül a Security Center. 
    - **Hibrid biztonság** – Egységes biztonsági nézetet kap az összes helyszíni és felhőbeli számítási feladatról. Biztonsági szabályzatok alkalmazása és a hibrid felhőbeli számítási feladatok biztonságának folyamatos értékelése a biztonsági szabványoknak való megfelelés biztosítása érdekében. Biztonsági adatok gyűjtése, keresése és elemzése több forrásból, beleértve a tűzfalakat és más partnermegoldásokat.
    - **Fenyegetésvédelmi riasztások** – A fejlett viselkedéselemzés és a Microsoft Intelligens biztonsági gráf a kibertámadások kiszélesedő veszélyforrását biztosítják. A beépített viselkedéselemzés és gépi tanulás képes azonosítani a támadásokat és a napi biztonsági réseket. Hálózatok, gépek és felhőszolgáltatások figyelése bejövő támadások és biztonsági incidens utáni tevékenységek figyelése. Interaktív eszközökkel és környezetfüggő fenyegetési intelligenciával egyszerűsítheti a vizsgálatot.
    - **Hozzáférés- és alkalmazásvezérlők** (AAC) – Letilthatja a kártevőket és más nemkívánatos alkalmazásokat az adott számítási feladatokhoz igazított gépi tanulási javaslatok alkalmazásával az engedélyező és megtagadási listák létrehozásához. Csökkentse a hálózati támadási felületet az Azure-beli virtuális gépek felügyeleti portjaihoz való, megfelelő időben elérhető, szabályozott hozzáféréssel. Az AAC jelentősen csökkenti a találgatásos és más hálózati támadásoknak való kitettséget.
    - **Tárolóbiztonsági funkciók** – Kihasználható a biztonsági rések kezelése és a valós idejű fenyegetésvédelem a tárolóba ított környezetekben. A **tárolóregisztr Azure Defender** engedélyezése akár 12 óra is lehet az összes funkció engedélyezéséig. A díjak a csatlakoztatott regisztrációs adatbázisba lekért egyedi tárolóképek számán alapulnak. A kép egyszeri vizsgálata után a rendszer csak akkor számít fel díjat, ha módosítják és továbbkiktálják.
    -  Az Azure-környezethez csatlakoztatott erőforrások veszélyforrások elleni szélessége – az Azure Defender natív veszélyforrások elleni védelmet nyújt az azure-beli fenyegetéseknek az összes erőforrása számára: Azure Resource Manager, Azure DNS, Azure hálózati réteg és Azure Key Vault. Azure Defender azure felügyeleti réteg és a Azure DNS réteg egyedi rálátással rendelkezik, és így képes megvédeni az ezekhez a rétegekhez kapcsolódó felhőbeli erőforrásokat.


## <a name="faq---pricing-and-billing"></a>Gyakori kérdések – Díjszabás és számlázás 

- [Hogyan követem nyomon, hogy a cégen belül ki engedélyezte Azure Defender módosításokat a Security Center?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Milyen csomagokat kínálnak Security Center?](#what-are-the-plans-offered-by-security-center)
- [Hogyan engedélyezhetem az Azure Defendert az előfizetéshez?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Engedélyezhetem az Azure Defendert kiszolgálók részhalmazában lévő kiszolgálókhoz az előfizetésben?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Ha már rendelkezik Microsoft Defender-licenccel a végponthoz, kaphatok kedvezményt a Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Az előfizetésem Azure Defender a kiszolgálókért, fizetnem kell a nem futó kiszolgálókért?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [A Telepített Log Analytics-ügynök nélküli gépekért díjat számítunk fel?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Ha egy Log Analytics-ügynök több munkaterületnek is jelentést készít, kétszer kell fizetni?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Ha egy Log Analytics-ügynök több munkaterületnek is jelentéseket készít, mindegyiken elérhető az 500 MB-os ingyenes adatbetelés?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [Az 500 MB-os ingyenes adatbetelés számítása egy teljes munkaterületre, vagy szigorúan gépenként történik?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [Milyen adattípusokat tartalmaz a napi 500 MB-os adatmennyiség?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Hogyan követem nyomon, hogy a cégen belül ki engedélyezte Azure Defender módosításokat a Security Center?
Az Azure-előfizetések több, a díjszabási beállítások módosítására vonatkozó engedéllyel rendelkező rendszergazdával is rendelkezniük kell. Annak kiderítése, hogy melyik felhasználó végzett változtatást, használja az Azure-tevékenységnaplót.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Díjszabásváltozási eseményt megjelenítő Azure-tevékenységnapló":::

Ha a felhasználó adatai nem szerepelnek  az esemény által kezdeményezett esemény oszlopban, a vonatkozó részletekért tekintse meg az esemény JSON-ját.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Azure-tevékenységnapló JSON Explorer":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Milyen csomagokat kínálnak Security Center? 
Security Center két ajánlata van: 

- Az Azure Security Center ingyenes verziója 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Hogyan engedélyezhetem az Azure Defendert az előfizetéshez? 
Az alábbi módszerek bármelyikével engedélyezheti a Azure Defender számára: 

| Metódus                                          | Utasítások                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Security Center oldalának Azure Portal | [Az Azure Defender engedélyezése](enable-azure-defender.md)                                                                                                  |
| REST API                                        | [Pricings API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Csomag díjszabása](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Engedélyezhetem az Azure Defendert kiszolgálók részhalmazában lévő kiszolgálókhoz az előfizetésben?
Nem. Ha engedélyezi [az Azure Defender](defender-for-servers-introduction.md) kiszolgálókra vonatkozó adatokat, az előfizetésben található összes kiszolgálót az Azure Defender. 

Alternatív megoldásként engedélyezheti a Azure Defender a Log Analytics-munkaterület szintjén. Ha így történik, csak az erre a munkaterületre jelentéseket készítő kiszolgálók lesznek védve és kiszámlázva. Számos képesség azonban nem érhető el. Ezek közé tartozik többek között a virtuális gépekhez való időben történő hozzáférés, a hálózatészlelés, a jogszabályi megfelelőség, az adaptív hálózat-ellenőrzés, az adaptív alkalmazásvezérlés. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Ha már van Microsoft Defender-licencem a végponthoz, kaphatok kedvezményt a Azure Defender?
Ha már rendelkezik Microsoft Defender-licenccel a végponthoz, nem kell fizetnie a microsoftos licenc ezen Azure Defender ért.

A kedvezmény megerősítéséhez lépjen kapcsolatba Security Center ügyfélszolgálatával, és adja meg a megfelelő munkaterület-azonosítót, régiót és licencinformációt az egyes kapcsolódó licencekhez.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Az előfizetésem Azure Defender engedélyezett kiszolgálókért, fizetnem kell a nem futó kiszolgálókért? 
Nem. Ha engedélyezi [az Azure Defender-kiszolgálókra](defender-for-servers-introduction.md) vonatkozó adatokat, nem számítunk fel díjat az olyan gépekért, amelyek felszabadított energiaállapotban vannak, amíg az adott állapotban vannak. A gépek energiaállapotának megfelelően vannak számlázva, ahogy az az alábbi táblázatban látható:

| Állam        | Leírás                                                                                                                                      | Példányhasználat számlázása |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Indítás     | A virtuális gép elindul.                                                                                                                               | Nincs kiszámlázva            |
| Futó      | Virtuális gép normál munkaállapota                                                                                                                    | Számlázott                |
| Leállítás     | Ez átmeneti állapot. Ha elkészült, Leállítva ként fog mutatni.                                                                           | Számlázott                |
| Leállítva      | A virtuális gép a vendég operációs rendszerből vagy a PowerOff API-k használatával lett leállítani. A hardver továbbra is ki van osztva a virtuális géphez, és a gazdagépen marad. | Számlázott                |
| Felszabadítás | Átmeneti állapot. Ha elkészült, a virtuális gép Felszabadítva ként fog mutatni.                                                                             | Nincs kiszámlázva            |
| Felszabadítva  | A virtuális gép sikeresen le lett állítva, és el lett távolítva a gazdagépről.                                                                                  | Nincs kiszámlázva            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Az Azure Virtual Machines felszabadított gép megjelenítése":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>A Log Analytics-ügynök nélkül telepített gépekért díjat számítunk fel?
Igen. Ha engedélyezi [a Azure Defender](defender-for-servers-introduction.md) kiszolgálókhoz egy előfizetésben, az előfizetésben található gépek számos védelmet kapnak, még akkor is, ha még nem telepítette a Log Analytics-ügynököt.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Ha egy Log Analytics-ügynök több munkaterületnek is jelentést készít, kétszer kell fizetni? 
Igen. Ha úgy konfigurálta a Log Analytics-ügynököt, hogy adatokat küldjön két vagy több különböző Log Analytics-munkaterületre (több kiszolgálóra), akkor minden olyan munkaterületért fizetnie kell, amely rendelkezik telepített "Security" vagy "AntiMalware" megoldással. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Ha egy Log Analytics-ügynök több munkaterületnek is jelentéseket készít, mindegyiken elérhető az 500 MB-os ingyenes adatbetelés?
Igen. Ha úgy konfigurálta a Log Analytics-ügynököt, hogy két vagy több különböző Log Analytics-munkaterületre küldjön adatokat (több kiszolgálóra), akkor 500 MB-os ingyenes adatbetolást kap. A számítás csomópontonként, jelentett munkaterületenként és naponta történik, és minden olyan munkaterülethez elérhető, amely rendelkezik telepített "Biztonság" vagy "Kártevőirtó" megoldással. Az 500 MB-nál nagyobb mennyiségben betöltött adatokért díjat számítunk fel.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>Az 500 MB-os ingyenes adatbe akkor számítható ki, ha egy teljes munkaterületre vonatkozik, vagy szigorúan gépenként?
Naponta 500 MB ingyenes adatbe kapok a munkaterülethez csatlakozó összes géphez. Kifejezetten a közvetlenül a biztonsági adatok által gyűjtött Azure Security Center.

Ez az adatmennyiség az összes csomópont napi átlaga. Így ha egyes gépek 100 MB-ot, mások pedig 800 MB-ot küldenek, ha az összeg nem haladja meg a **[gépek száma] x 500 MB ingyenes** korlátot, akkor nem számítunk fel további díjakat.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>Milyen adattípusokat tartalmaz a napi 500 MB-os adatmennyiség?

Security Center számlázása szorosan kötődik a Log Analytics számlázásához. Security Center 500 MB/csomópont/nap kiosztást biztosít a biztonsági adattípusok alábbi [részéhez:](/azure/azure-monitor/reference/tables/tables-category#security)
- WindowsEvent
- SecurityAlert (Biztonsági beállítások)
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection (Biztonság)
- Biztonsági esemény
- WindowsFirewall
- MaliciousIPCommunication
- LinuxAuditLog
- SysmonEvent (SysmonEvent)
- ProtectionStatus (Védelemállapot)
- Adattípusok frissítése és frissítéseSummary Update Management amikor a Update Management megoldás nem fut a munkaterületen vagy a megoldás céljának beállítása engedélyezve van

Ha a munkaterület az örökölt csomópontonkénti tarifacsomagban van, a rendszer kombinálja a Security Center és Log Analytics-foglalásokat, és közösen alkalmazza őket az összes számlázható betöltött adatra.

## <a name="next-steps"></a>Következő lépések
Ez a cikk Security Center a díjszabási lehetőségeket. Kapcsolódó anyagok:

- [Az Azure-beli számítási feladatok költségeinek optimalizálása](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Díjszabási részletek a választott pénznemben és az Ön régiójának megfelelően](https://azure.microsoft.com/pricing/details/security-center/)
- Előfordulhat, hogy szeretné kezelni a költségeket, és korlátozni a megoldáshoz gyűjtött adatok mennyiségét azáltal, hogy az ügynökök egy adott készletét korlátozza. [A megoldáscélzás](../azure-monitor/insights/solution-targeting.md) lehetővé teszi, hogy hatókört alkalmaz a megoldásra, és a munkaterületen lévő számítógépek egy részkészletét célozza meg. Ha megoldáscélzást használ, a Security Center a munkaterületet nem megoldásként listázza.
