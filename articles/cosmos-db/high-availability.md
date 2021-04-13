---
title: Magas rendelkezésre állás a Azure Cosmos DB
description: Ez a cikk azt ismerteti, Azure Cosmos DB hogyan biztosít magas rendelkezésre állást
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ac1e77d99707cdaa34ef42eb9b327a62f4e864c0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365365"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Hogyan biztosítja Azure Cosmos DB a magas rendelkezésre állást?
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB két elsődleges módon biztosít magas rendelkezésre állást. Először is Azure Cosmos DB Cosmos-fiókban konfigurált régiók között replikálja az adatokat. Másodszor, Azure Cosmos DB 4 adatreplikát tart fenn egy régión belül.

Azure Cosmos DB egy globálisan elosztott adatbázis-szolgáltatás, és egy alapszintű szolgáltatás, amely minden olyan régióban elérhető, ahol az [Azure elérhető.](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all) Az Azure Cosmos-fiókjához bármilyen számú Azure-régiót társíthat, és a rendszer automatikusan és transzparens módon replikálja az adatokat. Az Azure Cosmos-fiókjához bármikor hozzáadhat vagy eltávolíthat egy régiót. Cosmos DB az ügyfelek számára elérhető öt különböző Azure-felhőkörnyezetben érhető el:

* **Az Azure nyilvános** felhő, amely globálisan elérhető.

* **Azure China 21Vianet** a Microsoft és a 21Vianet közötti egyedi partnerség révén érhető el, amely az ország egyik legnagyobb internetszolgáltatója Kínában.

* **Az Azure Germany** az adat-megbízotti modell keretében nyújt szolgáltatásokat, ami biztosítja, hogy az ügyféladatok Németországban maradnak a T-Systems International GmbH leányvállalata, a Of Telekom leányvállalata, amely németországi adatkezelési megbízottként szolgál.

* **Azure Government** az USA kormányzati szervek és partnereik Egyesült Államok régióban érhető el.

* **Azure Government Védelmi Minisztérium (DoD)** két régióban érhető el a Egyesült Államok Egyesült Államok Védelmi Minisztériuma számára.

Egy régión belül a Azure Cosmos DB az adatok négy másolatát tartja fenn replikákként a fizikai partíciókon belül, az alábbi képen látható módon:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Fizikai particionálás" border="false":::

* Az Azure Cosmos-tárolókban található adatok [horizontálisan vannak particionálva.](partitioning-overview.md)

* A partíciókészlet több replikakészlet gyűjteménye. Az egyes régiókon belül minden partíciót egy replikakészlet véd, amely az összes írási adatokat replikálja és tartósan lekötötte a replikák többsége által. A replikák akár 10–20 tartalék tartományban is el vannak osztva.

* A rendszer az összes régió minden partícióját replikálja. Minden régió egy Azure Cosmos-tároló összes adatpartícióját tartalmazza, és olvasási és írási szolgáltatásokat is kiszolgálhat, ha a többrépontos írások engedélyezve vannak.  

Ha az Azure Cosmos-fiók  N Azure-régióban van elosztva, akkor az összes adatnak legalább *N* x 4 másolata lesz. Ha egy Azure Cosmos-fiók több mint 2 régióban van, az javítja az alkalmazás rendelkezésre állását, és alacsony késést biztosít a társított régiókban.

## <a name="slas-for-availability"></a>A rendelkezésre állásra vonatkozó STA-k

Azure Cosmos DB olyan átfogó SZOLGÁLTATÁSSZINT-eket biztosít, amelyek magukban foglalják az átviteli sebességet, a késést a 99. percentilisnél, a konzisztenciát és a magas rendelkezésre állást. Az alábbi táblázat az egy- és Azure Cosmos DB fiókok magas rendelkezésre állásának garanciáit mutatja be. A magasabb írási rendelkezésre állás érdekében konfigurálja Azure Cosmos-fiókját több írási régióra.

|Művelettípus  | Egy régió |Többrépontos (egyrépontos írások)|Többrépontos (többrépontú írások) |
|---------|---------|---------|-------|
|Írások    | 99,99    |99,99   |99,999|
|Olvasások     | 99,99    |99,999  |99,999|

> [!NOTE]
> A gyakorlatban a korlátozott elavultság, munkamenet, konzisztens előtag és konzisztenciamodellek tényleges írási rendelkezésre állása jelentősen magasabb, mint a közzétett ATA-k. A konzisztenciaszintek tényleges olvasási rendelkezésre állása jelentősen magasabb, mint a közzétett SZOLGÁLTATÁSSZINT-k.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Magas rendelkezésre állás Azure Cosmos DB regionális kimaradás esetén

A regionális kimaradás ritka esetekben a Azure Cosmos DB, hogy az adatbázis mindig magas rendelkezésre áll. Az alábbi részletek az Azure Cosmos DB viselkedését rögzítik az Azure Cosmos-fiók konfigurációjának függvényében:

* A Azure Cosmos DB az írási műveletek ügyfél számára való nyugtázása előtt az adatokat tartósan le kell mondania a régión belüli replikák kvóruma, amely elfogadja az írási műveleteket. További részletekért lásd: [Konzisztenciaszintek és átviteli sebesség](./consistency-levels.md#consistency-levels-and-throughput)

* A több írási régióval konfigurált többrégió-fiókok magas rendelkezésre állásban lesznek az íráshoz és az olvasáshoz is. A regionális feladatátvételek észlelése és kezelése a Azure Cosmos DB ügyfélen. Emellett azonnaliak is, és nem igényelnek módosításokat az alkalmazásból.

* Regionális kimaradás esetén az egy régióban található fiókok rendelkezésre állása elveszhet. Mindig ajánlott legalább két **régiót** (lehetőleg legalább két írási régiót) beállítani az Azure Cosmos-fiókkal a magas rendelkezésre állás biztosítása érdekében.

> [!IMPORTANT]
> SQL API-k használatakor úgy kell konfigurálni a Cosmos DB SDK-t, hogy az összes megadott olvasási régiót használja a magasabb rendelkezésre állás kihasználja. További információt [ebben a](troubleshoot-sdk-availability.md) cikkben talál.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Többrépontos fiókok egy írási régióval (írási régió kimaradása)

* Írási régió kimaradása esetén az Azure Cosmos-fiók automatikusan előléptet  egy másodlagos régiót új elsődleges írási régióként, ha az automatikus feladatátvétel engedélyezése konfigurálva van az Azure Cosmos-fiókban. Ha engedélyezve van, a feladatátvétel egy másik régióba fog lekövetkezni a megadott régiós prioritás szerint.

* Vegye figyelembe, hogy a manuális feladatátvétel nem indítható el, és nem fog sikerülni a forrás- vagy cél-régió kimaradása esetén. Ennek oka a feladatátvételi eljárás által megkövetelt konzisztencia-ellenőrzés, amely a régiók közötti kapcsolatot igényli.

* Ha a korábban érintett régió újra online állapotba áll, minden olyan írási adat elérhetővé válik az ütközéscsatornán keresztül, amely nem lett replikálva, amikor a régió [meghibásodott.](how-to-manage-conflicts.md#read-from-conflict-feed) Az alkalmazások olvashatják az ütközéscsatornát, feloldják az ütközéseket az alkalmazásspecifikus logika alapján, és szükség szerint visszaírni a frissített adatokat az Azure Cosmos-tárolóba.

* A korábban érintett írási régió helyreállítása után az olvasási régió automatikusan elérhetővé válik. Az írási régióként vissza lehet váltani a helyreállított régióra. A régiókat a [PowerShell,](how-to-manage-database-account.md#manual-failover)az Azure CLI vagy a Azure Portal. Az **írási régió** váltása előtt, közben vagy után nem áll le adat- vagy rendelkezésre állási adatvesztés, és az alkalmazás továbbra is magas rendelkezésre állású marad.

> [!IMPORTANT]
> Erősen ajánlott az éles számítási feladatokhoz használt Azure Cosmos-fiókokat konfigurálni az **automatikus feladatátvétel engedélyezéséhez.** Ez lehetővé Cosmos DB a fiókadatbázisok feladatátvételét, így automatikusan elérhetővé tehetik a régiókat. E konfiguráció hiányában a fiók írási rendelkezésre állása az írási régió teljes kimaradásának idejére megszakad, mivel a manuális feladatátvétel a régiókapcsolat hiánya miatt nem fog sikerülni.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Többrépontú fiókok egy írási régióval (olvasási régió kimaradása)

* Az olvasási régiók kimaradása esetén az Azure Cosmos-fiókok, amelyek bármilyen konzisztenciaszintet vagy erős konzisztenciát használnak három vagy több olvasási régióval, magas rendelkezésre áll állás marad az olvasáshoz és az íráshoz.

* A három régióval (egy írás, két olvasás) erős konzisztenciát használó Azure Cosmos-fiókok fenntartják az írási rendelkezésre állást az olvasási régió kimaradása során. A két régióval és engedélyezett automatikus feladatátvételsel rendelkező fiókok esetében a fiók nem fogad írásokat, amíg a régió meg nem hibásként van megjelölve, és automatikus feladatátvétel meg nem történik.

* Az érintett régió automatikusan megszakad, és offline állapotúként lesz megjelölve. A [Azure Cosmos DB az olvasási](sql-api-sdk-dotnet.md) hívások az előnyben részesített régiólistában következő elérhető régióba lesznek átirányítva.

* Ha a régiók rangsorolt listáján egyetlen régió sem érhető el, a beérkező kérések automatikusan az aktuális írási régióra váltanak vissza.

* Az olvasási régió kimaradásának kezeléséhez nincs szükség módosításra az alkalmazáskódban. Amikor az érintett olvasási régió ismét online állapotba kerül, az automatikusan szinkronizál az aktuális írási régióval, és ismét elérhető lesz az olvasási kérések kiszolgálásához.

* A további olvasásokat a szolgáltatás a helyreállt régiókhoz irányítja át anélkül, hogy módosítania kellene az alkalmazáskódot. A feladatátvétel és a korábban meghibásodott régiók újracsatlakozása során az olvasási konzisztenciagaranciák továbbra is Azure Cosmos DB.

* Még egy ritka és gyakori eseményben is, amikor az Azure-régió véglegesen nem állítható vissza, nem történik adatvesztés, ha a többrétű Azure Cosmos-fiók erős konzisztenciával *van konfigurálva.* Ha egy tartósan visszavonhatatlan írási régió, egy többrépontú Azure Cosmos-fiók korlátozottan elavult konzisztenciával van konfigurálva, a lehetséges adatvesztési idő az elavultságú *(K* vagy *T)* ablakra korlátozódik, ahol a K=100 000 frissítés vagy a T=5 perc az első, ami először bekövetkezik. A munkamenetek, konzisztens előtagok és a konzisztenciaszintek esetében a lehetséges adatvesztési időtartam legfeljebb 15 percre van korlátozva. Az RTO- és RPO-célokkal kapcsolatos további Azure Cosmos DB: Konzisztenciaszintek és adat [tartóssága](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Rendelkezésre állási zónák támogatása

A régiók közötti rugalmasság mellett a Azure Cosmos DB  támogatja a zónaredundaniát is a támogatott régiókban az Azure Cosmos-fiókhoz társítani kívánt régió kiválasztásakor.

A rendelkezésre állási zóna (AZ) támogatásával a Azure Cosmos DB biztosítja, hogy a replikák egy adott régión belül több zónában is el vannak helyezve, így magas rendelkezésre állást és rugalmasságot biztosítanak a zónahibák esetén. Availability Zones 99,995%-os rendelkezésre állási SLA-t biztosít a késés változásai nélkül. Egyetlen zóna meghibásodása esetén a zónaredundania RPO=0 teljes adatmegőrzést, RTO=0 rendelkezésre állást biztosít. A zónaredundanitás a regionális replikáció kiegészítő képessége. A zónaredundanitás önmagában nem használható a regionális rugalmasság eléréséhez.

A zónaredundans csak akkor konfigurálható, ha új régiót ad hozzá egy Azure Cosmos-fiókhoz. Meglévő régiók esetében a zónaredundania úgy engedélyezhető, hogy eltávolítja a régiót, majd újra hozzáadja, ha engedélyezve van a zónaredundania. Egyetlen régiós fiók esetén ehhez egy további régiót kell hozzáadnia, amely ideiglenesen feladatátvételt biztosít a számára, majd eltávolítja és hozzáadja a kívánt régiót, ahol engedélyezve van a zónaredundania.

Ha többrétű írásokat konfigurál az Azure Cosmos-fiókhoz, további költségek nélkül választhatja a zónaredundaniát. Ellenkező esetben tekintse meg az alábbi táblázatot a zónaredundania-támogatás díjszabásával kapcsolatban. Azon régiók listáját, ahol rendelkezésre állási zónák érhetők el, lásd: [Rendelkezésre állási zónák.](../availability-zones/az-region.md)

Az alábbi táblázat összefoglalja a különböző fiókkonfigurációk magas rendelkezésre állási képességét:

|KPI|Egy régió AZ nélkül|Egy régió AZS-ekkel|Többrépontos, egyrépontos írások AZs-ekkel|Többrédű, többrépontos írások AZs-ekkel|
|---------|---------|---------|---------|---------|
|Írási rendelkezésre állási SLA | 99.99% | 99.995% | 99.995% | 99.999% |
|Olvasási rendelkezésre állási SLA  | 99.99% | 99.995% | 99.995% | 99.999% |
|Zónahibák – adatvesztés | Adatvesztés | Nincs adatvesztés | Nincs adatvesztés | Nincs adatvesztés |
|Zónahibák – rendelkezésre állás | Rendelkezésre állási veszteség | Nincs rendelkezésre állási veszteség | Nincs rendelkezésre állási veszteség | Nincs rendelkezésre állási veszteség |
|Regionális kimaradás – adatvesztés | Adatvesztés |  Adatvesztés | A konzisztenciaszinttől függ. További [információkért](./consistency-levels.md) lásd: Konzisztencia, rendelkezésre állás és teljesítmény – a konzisztencia és a teljesítmény. | A konzisztenciaszinttől függ. További [információkért](./consistency-levels.md) lásd: Konzisztencia, rendelkezésre állás és teljesítmény – a konzisztencia és a teljesítmény.
|Regionális kimaradás – rendelkezésre állás | Rendelkezésre állási veszteség | Rendelkezésre állási veszteség | Nincs rendelkezésre állási veszteség az olvasási régió hibája esetén, ideiglenes írási régióhiba esetén | Nincs rendelkezésre állási veszteség |
|Ár (***1** _) | N/A | Kiépített RU/s x 1,25 sebesség | Kiépített RU/s x 1,25 sebesség (_*_2_**) | Többrépontos írási sebesség |

***1*** A kiszolgáló nélküli fiókok kérelemegységei (RU) szorozva vannak egy 1,25-ös tényezővel.

***2*** 1,25 arány csak olyan régiókban érvényes, amelyekben az AZ engedélyezve van.

Availability Zones a következőn keresztül engedélyezhetők:

* [Azure Portalra](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](manage-with-cli.md#add-or-remove-regions)

* [Azure Resource Manager-sablonok](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Magas rendelkezésre álló alkalmazások kiépítése

* Tekintse át az [Azure Cosmos SDK-k](troubleshoot-sdk-availability.md) várt viselkedését az események során, és hogy melyek azok a konfigurációk, amelyek hatással vannak rá.

* A magas írási és olvasási rendelkezésre állás biztosításához konfigurálja azure Cosmos-fiókját úgy, hogy legalább két, több írási régióval rendelkező régióra is kitérjon. Ez a konfiguráció biztosítja a legmagasabb rendelkezésre állást, a legkisebb késést és a legjobb méretezhetőséget az OLVASÁSI és írási szolgáltatások által is. További információ: Azure [Cosmos-fiók konfigurálása több írási régióval.](tutorial-global-distribution-sql-api.md)

* Az egy írási régióval konfigurált többrédű Azure Cosmos-fiókok esetében engedélyezze az automatikus feladatátvételt az Azure CLI vagy a [Azure Portal.](how-to-manage-database-account.md#automatic-failover) Az automatikus feladatátvétel engedélyezése után regionális katasztrófa esetén a Cosmos DB automatikusan átveszi a fiókját.  

* Még ha az Azure Cosmos-fiókja is magas rendelkezésre áll, előfordulhat, hogy az alkalmazást nem úgy tervezték, hogy magas rendelkezésre állás maradjon. Az alkalmazás végpontok között magas rendelkezésre állásának tesztelésére az alkalmazás tesztelése vagy vészhelyreállítási (DR) próbák részeként ideiglenesen tiltsa le a fiók automatikus feladatátvételét, hívja meg a manuális feladatátvételt a [PowerShell,](how-to-manage-database-account.md#manual-failover)az Azure CLI vagy a Azure Portal használatával, majd figyelje az alkalmazás feladatátvételét. Ha elkészült, visszaveheti a feladatátvételt az elsődleges régióba, és visszaállíthatja a fiók automatikus feladatátvételét.

> [!IMPORTANT]
> Ne hívja meg a manuális feladatátvételt a Cosmos DB vagy a célrégió kimaradása során, mivel az adatkonzisztencia fenntartásához régiókra van szükség, és nem fog sikerülni.

* Egy globálisan elosztott adatbázis-környezetben közvetlen kapcsolat van a konzisztenciaszint és az adatok tartóssága között a régióra kiterjedő kimaradás esetén. Az üzletmenet-folytonossági terv kidolgozása során meg kell értenie a maximális elfogadható időt, mielőtt az alkalmazás teljesen helyreáll egy zavaró esemény után. Az alkalmazás teljes helyreállításához szükséges időt helyreállítási időre vonatkozó célkitűzésnek (RTO) nevezik. Emellett tisztában kell lenni a legutóbbi adatfrissítések maximális időtartamával, amely az alkalmazás számára a megszakítást okozó események utáni helyreállítás esetén elfogadható. Az adatfrissítés-vesztés megengedhető időkorlátja a helyreállítási időkorlát (RPO). Az RPO és az RTO Azure Cosmos DB lásd: Konzisztenciaszintek [és adat tartóssága](./consistency-levels.md#rto)

## <a name="what-to-expect-during-a-cosmos-db-region-outage"></a>Mire számítsunk egy Cosmos DB során?

Az egy régióban található fiókok esetében az ügyfelek olvasási és írási rendelkezésre állásuk elvesztését tapasztalják.

A többrépontos fiókok működése az alábbi táblázattól függően eltérő lesz.

| Régiók írása | Automatikus feladatátvétel | Amire számíthat | Teendő |
| -- | -- | -- | -- |
| Egyetlen írási régió | Nincs engedélyezve | Olvasási régióban való kimaradás esetén minden ügyfél más régiókba lesz átirányítva. Nincs olvasási vagy írási rendelkezésre állási veszteség. Nincs adatvesztés. <p/> Az írási régióban kimaradás esetén az ügyfelek írási rendelkezésre állási veszteséget tapasztalnak. Ha nincs erős konzisztenciaszint kiválasztva, előfordulhat, hogy egyes adatok nem replikálódnak a fennmaradó aktív régiókba. Ez a jelen szakaszban leírtak szerint kiválasztott konzúgószinttől [függ.](consistency-levels.md#rto) Ha az érintett régió állandó adatvesztést okoz, a nem példányos adatok elveszhetnek. <p/> Cosmos DB a szolgáltatáskimaradás után automatikusan helyreállítja az írási rendelkezésre állást. | A szolgáltatáskimaradás során győződjön meg arról, hogy a fennmaradó régiókban elegendő kiépített RU van az olvasási forgalom támogatásához. <p/> Ne *indítsa* el a manuális feladatátvételt a kimaradás során, mert az sikertelen lesz. <p/> A szolgáltatáskimaradás után szükség szerint módosítsa újra a kiépített ru-okat. |
| Egyetlen írási régió | Engedélyezve | Olvasási régióban való kimaradás esetén minden ügyfél más régiókba lesz átirányítva. Nincs olvasási vagy írási rendelkezésre állási veszteség. Nincs adatvesztés. <p/> Írási régió kimaradása esetén az ügyfelek írási rendelkezésre állási veszteséggel fognak tapasztalni, amíg az Cosmos DB automatikusan új régiót nem választ új írási régióként a beállításoknak megfelelően. Ha nincs erős konzisztenciaszint kiválasztva, előfordulhat, hogy egyes adatok nem replikálódnak a többi aktív régióba. Ez az ebben a szakaszban leírt konzúgószinttől [függ.](consistency-levels.md#rto) Ha az érintett régió állandó adatvesztést okoz, a nem példányos adatok elveszhetnek. | A szolgáltatáskimaradás során győződjön meg arról, hogy a fennmaradó régiókban elegendő kiépített RU van az olvasási forgalom támogatásához. <p/> Ne *indítsa* el a manuális feladatátvételt a kimaradás során, mert az sikertelen lesz. <p/> Ha a szolgáltatáskimaradás véget ért, vissza lehet állítani az írási régiót az eredeti régióba, és szükség szerint újra lehet állítani a kiépített ru-okat. Az SQL API-kat használó fiókok a sikertelen régióban található nem replikált adatokat is helyreállíthatja az [ütközéscsatornából.](how-to-manage-conflicts.md#read-from-conflict-feed) |
| Több írási régió | Nem alkalmazható | Nincs olvasási vagy írási rendelkezésre állási veszteség. <p/> Előfordulhat, hogy a legutóbb frissített adatok a sikertelen régióban nem lesznek kezelhetők a fennmaradó aktív régiókban. A tartós, konzisztens előtag- és munkamenet-konzisztenciaszintek 15 <garantálják. A kötött elavultság a konfigurációtól függően k-s vagy T másodpercnél rövidebb frissítéseket garantál. Ha az érintett régió állandó adatvesztést okoz, a nem példányos adatok elveszhetnek. | A szolgáltatáskimaradás során győződjön meg arról, hogy a fennmaradó régiókban elegendő kiépített RU van a további forgalom támogatásához. <p/> A szolgáltatáskimaradás után szükség szerint módosíthatja a kiépített ru-k beállítását. Ha lehetséges, a Cosmos DB automatikusan helyreállítják a sikertelen régióban található nem replikált adatokat az SQL API-fiókok konfigurált ütközésfeloldási módszerével, és a Last Write Wins használatával a más API-kat használó fiókok esetében. |

## <a name="next-steps"></a>Következő lépések

Ezután elolvashatja a következő cikkeket:

* [A különböző konzisztenciaszintek rendelkezésre állása és teljesítménye](./consistency-levels.md)

* [Kiosztott átviteli sebesség globális méretezése](./request-units.md)

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)

* [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md)

* [Cosmos-fiók konfigurálása több írási régióval](how-to-multi-master.md)

* [Az SDK viselkedése többrépontú környezetekben](troubleshoot-sdk-availability.md)