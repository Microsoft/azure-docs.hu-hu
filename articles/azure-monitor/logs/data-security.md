---
title: Adatbiztonság Log Analytics | Microsoft Docs
description: Ismerje meg, hogyan védi a Log Analytics a magánélet védelmét, és gondoskodik az adatok védelméről.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/11/2020
ms.openlocfilehash: 8f193cface177e6be6343f28ef4dc27c2d6c6a7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030902"
---
# <a name="log-analytics-data-security"></a>Adatbiztonság Log Analytics
Ennek a dokumentumnak az a célja, hogy Log Analyticsra vonatkozó információt szolgáltasson, amely a Azure Monitor egyik funkciója, hogy kiegészítse a [Azure biztonsági és adatkezelési központ](https://www.microsoft.com/en-us/trust-center?rtc=1)információit.  

Ez a cikk bemutatja, hogyan gyűjti és dolgozza fel a Log Analytics az adatokat, és hogyan gondoskodik a védelmükről. Az ügynökökkel csatlakozhat a webszolgáltatáshoz, System Center Operations Manager használhatja az operatív adatok gyűjtéséhez, illetve az adatok Azure diagnosticsból való lekéréséhez Log Analytics által használható adatokat. 

A Log Analytics szolgáltatás biztonságosan kezeli a felhőalapú adatait az alábbi módszerek használatával:

* Az adatok elkülönítése
* Adatmegőrzés
* Fizikai biztonság
* Incidenskezelés
* Megfelelőség
* Biztonsági szabványok tanúsítványai

A Azure Monitorba és Log Analyticsba beépített további biztonsági funkciókat is alkalmazhat. Ezeknek a funkcióknak több rendszergazdai felügyeletre van szükségük. 
* Ügyfél által felügyelt (biztonsági) kulcsok
* Azure Private Storage
* Private Link-hálózatkezelés 
* Azure-támogatás hozzáférési korlátait az Azure kulcstárolója állítja be

Vegye fel velünk a kapcsolatot az alábbi információk bármelyikével kapcsolatos kérdésekkel, javaslatokkal vagy problémákkal, beleértve az [Azure-támogatási lehetőségek](https://azure.microsoft.com/support/options/)biztonsági szabályzatait is.

## <a name="sending-data-securely-using-tls-12"></a>Adatok biztonságos küldése a TLS 1,2 használatával 

A Log Analytics felé irányuló adatforgalom biztosításához határozottan javasoljuk, hogy az ügynököt legalább Transport Layer Security (TLS) 1,2 használatára konfigurálja. A TLS/SSL (SSL) régebbi verziói sebezhetőnek találták, miközben továbbra is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak**, és az iparág gyorsan áthelyezi a régebbi protokollok támogatását. 

A [PCI biztonsági szabványoknak szóló Tanács](https://www.pcisecuritystandards.org/) a TLS/SSL régebbi verzióinak letiltására, valamint a biztonságosabb protokollokra való frissítésre vonatkozó [határidő 2018. június 30-ig](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) érvényes. Ha az Azure elveszíti az örökölt támogatást, ha az ügynökök nem tudnak kommunikálni legalább TLS 1,2-ben, akkor nem fog tudni adatküldést küldeni Log Analyticsba. 

Nem ajánlott explicit módon beállítani az ügynököt úgy, hogy csak a TLS 1,2-et használja, kivéve, ha ez nem feltétlenül szükséges, mivel ez a platform szintű biztonsági funkciókat is lehetővé teszi, amelyekkel automatikusan észlelhetők és kihasználhatják az elérhetővé váló újabb biztonságosabb protokollokat, például a TLS 1,3. 

### <a name="platform-specific-guidance"></a>Platform-specifikus útmutató

|Platform/nyelv | Támogatás | További információ |
| --- | --- | --- |
|Linux | A Linux-disztribúciók általában az [OpenSSL](https://www.openssl.org) -t használják a TLS 1,2 támogatásához.  | Ellenőrizze az OpenSSL- [changelog](https://www.openssl.org/news/changelog.html) , hogy az OpenSSL verziója támogatott-e.|
| Windows 8,0 – 10 | Támogatott, és alapértelmezés szerint engedélyezve van. | Annak megerősítéséhez, hogy továbbra is az [alapértelmezett beállításokat](/windows-server/security/tls/tls-registry-settings)használja.  |
| Windows Server 2012 – 2016 | Támogatott, és alapértelmezés szerint engedélyezve van. | Annak ellenőrzése, hogy továbbra is az [alapértelmezett beállításokat](/windows-server/security/tls/tls-registry-settings) használja-e |
| Windows 7 SP1 és Windows Server 2008 R2 SP1 | Támogatott, de alapértelmezés szerint nincs engedélyezve. | Az engedélyezésével kapcsolatos részletekért tekintse meg a [Transport Layer Security (TLS) beállításjegyzék-beállítások](/windows-server/security/tls/tls-registry-settings) lapját.  |

## <a name="data-segregation"></a>Az adatok elkülönítése
Miután a Log Analytics szolgáltatás betöltötte az adatait, az adatai logikailag elkülönítve maradnak az egyes összetevőkön a szolgáltatáson belül. A rendszer minden adatmezőt felcímkéz a munkaterületen. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes. Az adatait a kiválasztott régióban lévő Storage-fürt egy dedikált adatbázisában tárolja a rendszer.

## <a name="data-retention"></a>Adatmegőrzés
Az indexelt naplók keresési adatai a díjszabási tervnek megfelelően tárolódnak és megmaradnak. További információ: [log Analytics díjszabása](https://azure.microsoft.com/pricing/details/log-analytics/).

Az [előfizetési szerződés](https://azure.microsoft.com/support/legal/subscription-agreement/)részeként a Microsoft megtartja az adatait a szerződés feltételei szerint.  Az ügyféladatok eltávolításakor a rendszer nem semmisít meg fizikai meghajtókat.  

Az alábbi táblázat a rendelkezésre álló megoldások némelyikét sorolja fel, és példákat tartalmaz az összegyűjtött adatok típusára.

| **Megoldás** | **Adattípusok** |
| --- | --- |
| Capacity and Performance |Teljesítményadatok és metaadatok |
| Frissítéskezelés |Metaadatok és állapotinformációkat |
| Naplózás kezelése |Felhasználó által definiált eseménynaplók, Windows-eseménynaplók és/vagy IIS-naplók |
| Változások követése |A szoftver leltározása, a Windows-szolgáltatások és a Linux-démon metaadatai, valamint a Windows/Linux-fájlok metaadatainak |
| SQL és Active Directory Assessment |A WMI-adat, a beállításjegyzék-adat, a Teljesítményadatok és a SQL Server dinamikus felügyeleti nézet eredményei |

A következő táblázat példákat mutat be az adattípusokra:

| **Adattípus** | **Mezők** |
| --- | --- |
| Riasztás |Riasztás neve, riasztás leírása, Basemanagedentityid azonosító eltérése, probléma azonosítója, IsMonitorAlert, RuleId, ResolutionState, prioritás, súlyosság, kategória, tulajdonos, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguráció |Vevőkód, ügynökazonosító, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Esemény |Napszállta, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, közzétevő neve, FullNumber, szám, kategória, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Megjegyzés:** Ha egyéni mezőkkel rendelkező eseményeket ír a Windows-eseménynaplóba, Log Analytics gyűjti őket. |
| Metaadatok |Basemanagedentityid azonosító eltérése, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, Ip_cím, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP-cím, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, egyszerű név, OffsetInMinuteFromGreenwichTime |
| Teljesítmény |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Állapot |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, Basemanagedentityid azonosító eltérése, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fizikai biztonság
A Log Analytics szolgáltatást a Microsoft munkatársai felügyelik, és minden tevékenység naplózva van és naplózható. Log Analytics Azure-szolgáltatásként működik, és megfelel az Azure megfelelőségi és biztonsági követelményeinek. Az Azure-eszközök fizikai biztonságával kapcsolatos részleteket a [Microsoft Azure biztonsági áttekintés](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)18. oldalán tekintheti meg. A biztonságos területekre vonatkozó fizikai hozzáférési jogok egy munkanapon belül megváltoznak mindazok számára, akik már nem felelnek meg a Log Analytics szolgáltatásért, beleértve az átvitelt és a megszüntetést is. A [Microsoft adatközpontokban](https://azure.microsoft.com/global-infrastructure/)használt globális fizikai infrastruktúráról olvashat.

## <a name="incident-management"></a>Incidenskezelés
Log Analytics egy incidens-felügyeleti folyamattal rendelkezik, amelyet minden Microsoft-szolgáltatás betart. Az összegzéshez:

* Olyan megosztott felelősségi modellt használjon, amelyben a biztonsági felelősség egy része a Microsofthoz tartozik, és egy rész az ügyfélhez tartozik
* Azure biztonsági incidensek kezelése:
  * Az incidens észlelése után indított vizsgálat indítása
  * Az incidensek hatásának és súlyosságának felmérése egy, a hívási incidensek csapattagjának tagja által. A bizonyítékok alapján az értékelés esetleg nem eredményezheti a biztonsági válasz csapatának további kiterjesztését.
  * A technikai vagy igazságügyi vizsgálat elvégzése érdekében biztonsági válaszok szakértői által okozott incidens diagnosztizálása, az elkülönítési, a mérséklési és a áthidaló stratégiák azonosítása. Ha a biztonsági csapat úgy véli, hogy a vásárlói adatmennyiség egy törvénytelen vagy jogosulatlan személy számára válhat elérhetővé, a felhasználói incidensek értesítési folyamatának párhuzamos végrehajtása párhuzamosan kezdődik.  
  * Az incidensből való stabilizáció és helyreállítás. Az incidens válasz csapata helyreállítási tervet hoz létre a probléma enyhítése érdekében. A válságkezelési lépések, például az érintett rendszerek karanténba helyezése azonnal és párhuzamosan, diagnózissal történhet. Az azonnali kockázat átadása után megtervezhető a hosszú távú enyhítés.  
  * Az incidens lezárása és a post mortem végrehajtása. Az incidensek válaszával foglalkozó csapat létrehoz egy levágást, amely felvázolja az incidens részleteit, és meggátolja a szabályzatok, eljárások és folyamatok felülvizsgálatát az esemény ismétlődésének megelőzése érdekében.
* A biztonsági incidensek ügyfeleinek értesítése:
  * Az érintett ügyfelek hatókörének meghatározása, valamint a lehető legrészletesebb értesítéssel rendelkező személy biztosítása
  * Hozzon létre egy értesítést, amely részletes információkat biztosít az ügyfeleknek, hogy vizsgálatot végezzenek a végfelhasználók számára, és teljesítsék a végfelhasználók által tett kötelezettségvállalásokat, miközben nem indokolatlanul késleltetik az értesítési folyamatot.
  * Szükség szerint erősítse meg és állapítsa meg az incidenst.
  * Az incidensekkel kapcsolatos értesítéseket értesítés nélkül értesíti az ügyfeleket a jogi vagy szerződéses kötelezettségvállalásnak megfelelően. A biztonsági incidensekkel kapcsolatos értesítéseket egy vagy több ügyfél rendszergazdája kapja meg a Microsoft által választott módon, beleértve az e-mailben is.
* A csapat készültségének és képzésének elvégzése:
  * A biztonsági és a tudatosságnövelő képzések elvégzéséhez a Microsoft munkatársai szükségesek, amelyek segítenek azonosítani és jelenteni a gyanús biztonsági problémákat.  
  * A Microsoft Azure szolgáltatásban dolgozó operátorok betanítási kötelezettségeket foglalnak magukban, amelyek a vásárlói adatokat üzemeltető bizalmas rendszerekhez férnek hozzá.
  * A Microsoft Security Response munkatársai speciális képzést kapnak a szerepköreik számára

Ha bármilyen vásárlói adat elvesztését tapasztalja, egy napon belül értesítjük az egyes ügyfeleket. Az ügyfél adatvesztése azonban soha nem fordult elő a szolgáltatásban. 

További információ arról, hogyan reagál a Microsoft a biztonsági incidensekre: [Microsoft Azure biztonsági válasz a felhőben](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Megfelelőség
A Log Analytics szoftverfejlesztési és szolgáltatási csapat informatikai és irányítási programja támogatja az üzleti követelményeit, és betartja a törvényeket és rendeleteket a következő témakörben leírtak szerint: [Microsoft Azure Adatvédelmi központ](https://azure.microsoft.com/support/trust-center/) és a [Microsoft megbízhatósági központ megfelelősége](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). A Log Analytics a biztonsági követelmények megállapítása, a biztonsági vezérlők, a felügyelt és a figyelési kockázatok azonosítása is megtörténik. Évente áttekintjük a házirendek, szabványok, eljárások és irányelvek áttekintését.

Minden fejlesztői csapat tagja megkapja a formális alkalmazások biztonsági képzését. Belsőleg a szoftverfejlesztés egy verziókövetés rendszerét használjuk. Minden szoftveres projektet a verziókövetés rendszer véd.

A Microsoft olyan biztonsági és megfelelőségi csapattal rendelkezik, amely felügyeli és felméri a Microsoft összes szolgáltatását. Az információs biztonsági tisztviselők alkotják a csapatot, és nincsenek társítva a Log Analyticst fejlesztő mérnöki csapatokhoz. A biztonsági tisztviselők saját felügyeleti lánctal rendelkeznek, és független értékeléseket végeznek a termékek és szolgáltatások számára a biztonság és a megfelelőség biztosítása érdekében.

A Microsoft igazgatótanácsát a Microsoft összes információs biztonsági programjáról szóló éves jelentés értesíti.

A Log Analytics szoftverfejlesztési és szolgáltatási csapat aktívan együttműködik a Microsoft jogi és megfelelőségi csapatával és más iparági partnerekkel, hogy különböző minősítéseket szerezzenek be.

## <a name="certifications-and-attestations"></a>Minősítések és igazolások
Az Azure Log Analytics megfelel a következő követelményeknek:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* A " [Payment Card Industry (PCI-kompatibilis") adatbiztonsági szabvány (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) a PCI biztonsági szabványok Tanácsának keretében.
* [Service Organization Controls (SoC) 1 Type 1 és SoC 2 Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) megfelelő
* [HIPAA és HITECH](/compliance/regulatory/offering-hipaa-hitech) a HIPAA üzleti partneri szerződéssel rendelkező vállalatok számára
* Általános Windows-mérnöki feltételek
* Microsoft megbízható számítástechnika
* Azure-szolgáltatásként az Log Analytics által használt összetevők megfelelnek az Azure megfelelőségi követelményeinek. További információk a [Microsoft adatvédelmi központ megfelelőségi](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx)szolgáltatásában olvashatók.

> [!NOTE]
> Egyes minősítések/igazolások esetén Log Analytics a *Operational Insights* korábbi neve alatt szerepel.
>
>

## <a name="cloud-computing-security-data-flow"></a>Felhő-számítástechnikai biztonsági adatfolyam
Az alábbi ábrán egy Felhőbeli biztonsági architektúra látható, amely a vállalattól származó információk áramlása, és hogy miként biztosítható, ahogy az a Log Analytics szolgáltatásba kerül, és végül a Azure Portalban látható. Az egyes lépésekről további információk a diagramot követve jelennek meg.

![Az adatgyűjtési és-biztonsági Log Analytics képe](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Regisztráljon a Log Analyticsra, és gyűjtsön adatokat
Ahhoz, hogy a szervezete adatküldést Log Analyticsba küldjön, konfigurálnia kell egy Azure-beli virtuális gépeken futó Windows-vagy Linux-ügynököt, vagy a környezetében vagy más felhőalapú szolgáltatóban található virtuális vagy fizikai számítógépeken.  Ha Operations Manager használ, a felügyeleti csoportból konfigurálja a Operations Manager ügynököt. A felhasználók (például Ön, más felhasználók vagy személyek csoportja) létrehozhatnak egy vagy több Log Analytics munkaterületet, és a következő fiókok egyikének használatával regisztrálhatják az ügynököket:

* [Szervezeti azonosító](../../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft-fiók – Outlook, Office Live, MSN](https://account.microsoft.com/account)

A Log Analytics munkaterület az adatok gyűjtését, összesítését, elemzését és bemutatását mutatja be. A munkaterületek elsődlegesen az adatparticionálási módszerként használatosak, és minden munkaterület egyedi. Előfordulhat például, hogy a termelési adatait egy munkaterülettel és egy másik munkaterülettel felügyelt tesztelési adattal szeretné kezelni. A munkaterületek segítségével a rendszergazdák vezérelhetik a felhasználók hozzáférését az adatkezeléshez. Minden munkaterülethez több felhasználói fiók is társítható, és minden egyes felhasználói fiók több Log Analytics munkaterülethez is hozzáférhet. Az adatközpont-régió alapján hozhat létre munkaterületeket.

Operations Manager esetén a Operations Manager felügyeleti csoport kapcsolatot létesít a Log Analytics szolgáltatással. Ezután beállíthatja, hogy a felügyeleti csoportban lévő ügynök által felügyelt rendszerek képesek legyenek adatokat gyűjteni és elküldeni a szolgáltatásnak. Az Ön által engedélyezett megoldástól függően a megoldásokból származó adatokat közvetlenül egy Operations Manager felügyeleti kiszolgálóról a Log Analytics szolgáltatásba küldi el, vagy az ügynök által felügyelt rendszer által gyűjtött adatok mennyisége miatt közvetlenül az ügynökről a szolgáltatásba küldi őket. A Operations Manager által nem figyelt rendszerek esetében az egyes szolgáltatások közvetlenül a Log Analytics szolgáltatáshoz csatlakoznak.

A csatlakoztatott rendszerek és a Log Analytics szolgáltatás közötti összes kommunikáció titkosítva van. A TLS (HTTPS) protokollt használja a titkosításhoz.  A Microsoft SDL eljárást követve biztosíthatja, hogy Log Analytics naprakész legyen a titkosítási protokollok legújabb fejlesztései között.

Az ügynökök mindegyike Log Analytics adatokat gyűjt. A gyűjtött adatok típusa a használt megoldások típusaitól függ. Az adatgyűjtés összegzését [a Solutions Gallery log Analytics-megoldások hozzáadása](../insights/solutions.md)című részében tekintheti meg. Emellett a legtöbb megoldás esetében részletesebb gyűjteményi információk is elérhetők. A megoldás az előre definiált nézetek, a naplózott keresési lekérdezések, az adatgyűjtési szabályok és a feldolgozási logika összessége. Megoldás importálásához csak a rendszergazdák használhatják a Log Analytics. A megoldás importálása után a rendszer áthelyezi a Operations Manager felügyeleti kiszolgálókra (ha ez használatban van), majd a kiválasztott ügynökökre. Ezután az ügynökök gyűjtik az adatokat.

## <a name="2-send-data-from-agents"></a>2. adatok küldése az ügynököktől
Regisztrálja az összes ügynököt egy regisztrációs kulccsal, és biztonságos kapcsolatot létesít az ügynök és a Log Analytics szolgáltatás között tanúsítványalapú hitelesítéssel és TLS-vel az 443-es porton keresztül. A Log Analytics titkos tárolót használ a kulcsok létrehozásához és karbantartásához. A titkos kulcsokat 90 naponta elforgatják, és az Azure-ban tárolják, és azokat az Azure-műveletek kezelik, akik szigorú szabályozási és megfelelőségi eljárásokat követnek.

A Operations Manager a Log Analytics munkaterületen regisztrált felügyeleti csoport biztonságos HTTPS-kapcsolatot létesít egy Operations Manager felügyeleti kiszolgálóval.

Az Azure-beli virtuális gépeken futó Windows-és Linux-ügynökök esetében a rendszer írásvédett tárolási kulcsot használ a diagnosztikai események olvasásához az Azure-táblákban.  

Ha a felügyeleti kiszolgáló bármilyen okból nem tud kommunikálni a szolgáltatással, akkor a Operations Manager felügyeleti csoportnak, amely a Log Analyticsba van integrálva, akkor az összegyűjtött adatokat a felügyeleti kiszolgáló egy ideiglenes gyorsítótárában tárolja helyileg.   Megpróbálják az adatküldés nyolc percenkénti újraküldését két órán keresztül.  A felügyeleti kiszolgálót megkerülő és közvetlenül a Log Analyticsnak elküldő adatműveletek esetén a viselkedés konzisztens a Windows-ügynökkel.  

A Windows vagy a felügyeleti kiszolgáló ügynökének gyorsítótárazott adatait az operációs rendszer hitelesítőadat-tárolója védi. Ha a szolgáltatás két óra elteltével nem tudja feldolgozni az adatfeldolgozást, az ügynökök várólistára helyezik az adatsorokat. Ha a várólista megtelik, az ügynök elindítja az adattípusokat, kezdve a teljesítményadatokat. Az ügynök-várólista korlátja egy beállításkulcs, amely szükség esetén módosítható. Az összegyűjtött adatokat a rendszer tömöríti és elküldi a szolgáltatásnak, megkerüli a Operations Manager felügyeleti csoport adatbázisait, így nem ad hozzá terhelést. Az összegyűjtött adatok elküldése után a rendszer eltávolítja a gyorsítótárból.

A fentiekben leírtak szerint a felügyeleti kiszolgálóról vagy a közvetlenül csatlakoztatott ügynökökről érkező adatok a TLS protokollal Microsoft Azure adatközpontokban lesznek továbbítva. Igény szerint a ExpressRoute használatával további biztonságot biztosíthat az adatvédelemhez. A ExpressRoute lehetővé teszi, hogy közvetlenül kapcsolódjon az Azure-hoz a meglévő WAN-hálózatról, például egy hálózati szolgáltató által biztosított többprotokollos feliratozási (MPLS) VPN-ről. További információ: [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. az Log Analytics szolgáltatás fogadja és dolgozza fel az adatfeldolgozást
A Log Analytics szolgáltatás biztosítja, hogy a bejövő adatok megbízható forrásból származnak a tanúsítványok és az adatok sértetlenségének ellenőrzésével az Azure-hitelesítéssel. A feldolgozatlan nyers adatok ezután egy Azure Event hub-ban tárolódnak a régióban, az adatok végül a nyugalmi állapotban lesznek tárolva. A tárolt adatok típusa az importált és az adatok gyűjtésére használt megoldások típusaitól függ. Ezután a Log Analytics szolgáltatás feldolgozza a nyers adatot, és betölti azt az adatbázisba.

Az adatbázisban tárolt összegyűjtött adatok megőrzési ideje a kiválasztott díjszabási csomagtól függ. Az *ingyenes* szinten az összegyűjtött adatok hét napig elérhetők. A *fizetős* csomag esetében a begyűjtött adatok alapértelmezés szerint 31 napig elérhetők, de 730 napig bővíthetők. Az adatok tárolása titkosított marad az Azure Storage-ban, az adatok titkosságának biztosítása érdekében, és a helyi régióban a helyileg redundáns tárolás (LRS) használatával történik az adatok replikálása. Az utolsó két hetet is tárolja SSD-alapú gyorsítótárban, és ez a gyorsítótár titkosítva van.

Az adatbázis-tárolóban tárolt adatmennyiség nem módosítható a beolvasás után, de törölhető az API- [  elérési út](personal-data-mgmt.md#delete)használatával. Bár az adat nem módosítható, bizonyos minősítésekhez szükséges, hogy az adat nem módosítható és nem törölhető a tárolóban. Az adatmódosíthatatlansági a nem módosítható [tárolóként](../../storage/blobs/storage-blob-immutability-policies-manage.md)konfigurált Storage-fiókba történő [adatexportálással](logs-data-export.md) érhető el.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. a Log Analytics használata az adateléréshez
Log Analytics munkaterület eléréséhez jelentkezzen be a Azure Portal a korábban beállított szervezeti fiókkal vagy Microsoft-fiók. A portál és a Log Analytics szolgáltatás közötti összes forgalmat biztonságos HTTPS-csatornán keresztül küldik el. A portál használatakor a rendszer egy munkamenet-azonosítót hoz létre a felhasználói ügyfélen (webböngészőn), és a rendszer a munkamenet megszakítása előtt helyi gyorsítótárban tárolja az adataikat. Ha leállt, a gyorsítótár törlődik. A személyazonosításra alkalmas adatokat nem tartalmazó ügyféloldali cookie-k nem törlődnek automatikusan. A munkamenet-cookie-k HTTPOnly vannak megjelölve, és biztonságosak. Az előre meghatározott üresjárati időszak után a Azure Portal munkamenet megszakad.


## <a name="additional-security-features"></a>További biztonsági funkciók
Ezeket a további biztonsági funkciókat a Azure Monitor/Log Analytics-környezet további biztonságossá tételéhez használhatja. Ezeknek a funkcióknak több rendszergazdai felügyeletre van szükségük. 
- [Ügyfél által felügyelt (biztonsági) kulcsok](../logs/customer-managed-keys.md) – az ügyfél által felügyelt kulcsokkal titkosíthatja az log Analytics-munkaterületek számára továbbított adatfájlokat. Azure Key Vault használatát igényli. 
- [Magánjellegű/ügyfél által felügyelt tár](./private-storage.md) – felügyelje a személyes titkosított Storage-fiókját, és mondja el, log Analytics, hogy használja a figyelési adatai tárolásához 
- [Privát kapcsolat hálózatkezelése](./private-link-security.md) – az Azure Private link lehetővé teszi, hogy privát végpontok használatával biztonságosan összekapcsolja az Azure Pásti-szolgáltatásokat (beleértve a Azure monitor) a virtuális hálózattal. 
- Az [Azure Customer kulcstároló](../../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-preview) -Ügyfélszéf for Microsoft Azure egy felületet biztosít az ügyfelek számára az ügyfél-hozzáférési kérelmek áttekintéséhez és jóváhagyásához vagy elutasításához. Olyan esetekben használatos, amikor egy Microsoft-mérnöknek egy támogatási kérelem kezelése során hozzá kell férnie az ügyfél adataihoz.


## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan gyűjthet adatokat az Azure-beli virtuális gépek Log Analyticsával az [Azure VM](../vm/quick-collect-azurevm.md)gyors üzembe helyezését követően.  

*  Ha a környezetben található fizikai vagy virtuális Windows-vagy Linux-számítógépekről szeretne adatokat gyűjteni, tekintse meg a Linux rendszerű [számítógépek](../vm/quick-collect-linux-computer.md) és a [Windows rendszerű számítógépek](../vm/quick-collect-windows-computer.md) gyors útmutatója című témakört.