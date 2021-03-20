---
title: Fürt leírása a fürterőforrás-kezelő használatával
description: Service Fabric fürt leírása a tartalék tartományok, a tartományok, a csomópont-tulajdonságok és a fürterőforrás-kezelő csomópont-kapacitásainak megadásával.
author: masnider
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d27a09f0ff38ec7422636ef0933552aa310c387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92911766"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Service Fabric-fürt leírása a fürterőforrás-kezelő használatával

Az Azure Service Fabric fürterőforrás-kezelő funkciója számos módszert kínál a fürtök leírására:

* Tartalék tartományok
* Frissítési tartományok
* Csomópont tulajdonságai
* Csomópont kapacitása

A Futtatás során a fürterőforrás-kezelő ezeket az információkat használja a fürtben futó szolgáltatások magas rendelkezésre állásának biztosításához. A fontos szabályok betartatása közben a fürtön belüli erőforrás-használat optimalizálására is törekszik.

## <a name="fault-domains"></a>Tartalék tartományok

A tartalék tartomány bármely koordinált meghibásodási terület. Egyetlen gép a tartalék tartomány. A meghibásodások különböző okok miatt sikertelenek lehetnek a hálózati ADAPTERek hibáinak elhárítása érdekében.

Az ugyanahhoz az Ethernet-kapcsolóhoz csatlakozó gépek ugyanabban a tartalék tartományban találhatók. Tehát olyan gépek, amelyek egyetlen áramforrással vagy egyetlen helyen osztoznak.

Mivel a hardveres hibák esetében természetes, hogy átfedésben vannak, a tartalék tartományok eredendően hierarchikusak. Service Fabric URI-ként jelennek meg.

Fontos, hogy a tartalék tartományok megfelelően legyenek beállítva, mivel Service Fabric ezt az információt használja a szolgáltatások biztonságos elhelyezéséhez. Service Fabric nem kíván olyan szolgáltatásokat elhelyezni, amelyek miatt a tartalék tartomány elvesztése (bizonyos összetevők meghibásodása okozta) miatt a szolgáltatás leáll.

Az Azure-környezetben a Service Fabric a környezete által biztosított tartalék tartományi információk használatával helyesen konfigurálja a fürt csomópontjait az Ön nevében. Service Fabric önálló példányai esetén a tartalék tartományok a fürt beállításának időpontjában vannak meghatározva.

> [!WARNING]
> Fontos, hogy a Service Fabrichoz megadott tartalék tartományi információk pontosak legyenek. Tegyük fel például, hogy az Service Fabric-fürt csomópontjai 10 virtuális gépen futnak, és 5 fizikai gazdagépen futnak. Ebben az esetben annak ellenére, hogy 10 virtuális gép van, csak 5 különböző (legfelső szintű) tartalék tartomány létezik. Ugyanannak a fizikai gazdagépnek a megosztása azt eredményezi, hogy a virtuális gépek ugyanazt a legfelső szintű tartalék tartományt használják, mivel a virtuális gépek a fizikai gazdagép meghibásodása esetén koordinált hibával  
>
> Service Fabric elvárja, hogy a csomópontok tartalék tartománya ne változzon meg. A virtuális gépek magas rendelkezésre állásának biztosítására szolgáló egyéb mechanizmusok, például [Ha a virtuális gépek](/previous-versions/system-center/virtual-machine-manager-2008-r2/cc967323(v=technet.10)), ütközést okozhatnak a Service Fabric. Ezek a mechanizmusok a virtuális gépek transzparens áttelepítését használják az egyik gazdagépről a másikra. Nem konfigurálja újra vagy értesíti a futó kódot a virtuális gépen. Ezért *nem támogatottak* környezetként Service Fabric fürtök futtatásához. 
>
> A Service Fabric csak magas rendelkezésre állású technológia lehet. Nem szükségesek olyan mechanizmusok, mint az élő VM-Migrálás és a SANs. Ha ezeket a mechanizmusokat a Service Fabric együtt használja, az alkalmazás rendelkezésre állását és megbízhatóságát is _csökkenti_ . Ennek az az oka, hogy további bonyolultságot jelentenek, központosított meghibásodási forrásokat biztosítanak, és olyan megbízhatósági és rendelkezésre állási stratégiákat használnak, amelyek ütköznek a Service Fabric.
>
>

Az alábbi ábrán az összes olyan entitást színezjük, amely hozzájárul a tartalék tartományokhoz, és felsorolja az összes olyan különböző tartalék tartományt, amely az eredmény. Ebben a példában az adatközpontok ("DC"), az állványok ("R") és a pengék ("B") szerepelnek. Ha minden egyes panel több virtuális gépet tartalmaz, lehet, hogy a tartalék tartomány hierarchiájában egy másik réteg található.

![Tartalék tartományokon keresztül rendezett csomópontok][Image1]

A futtatókörnyezet során Service Fabric fürterőforrás-kezelő figyelembe veszi a fürtben található tartalék tartományokat, és megtervezi az elrendezéseket. A szolgáltatás állapot-nyilvántartó replikái vagy állapot nélküli példányai szétoszthatók, hogy külön tartalék tartományokban legyenek. A szolgáltatás a tartalék tartományok között történő terjesztése biztosítja, hogy a szolgáltatás rendelkezésre állása ne veszélyeztesse, ha egy tartalék tartomány a hierarchia bármelyik szintjén meghibásodik.

A fürterőforrás-kezelő nem gondoskodik arról, hogy hány réteg van a tartalék tartományi hierarchiában. Megpróbálja biztosítani, hogy a hierarchia egy részének elvesztése ne befolyásolja a rajta futó szolgáltatásokat.

A legjobb, ha azonos számú csomópont van a tartalék tartományi hierarchia minden szintjén. Ha a tartalék tartományok "Tree" értéke kiegyensúlyozatlan a fürtben, akkor nehezebb a fürterőforrás-kezelő számára a szolgáltatások legjobb kiosztását kideríteni. A kiegyensúlyozatlan tartalék tartományok elrendezése azt jelenti, hogy egyes tartományok elvesztése a többi tartománynál több szolgáltatás elérhetőségét is befolyásolja. Ennek eredményeképpen a fürterőforrás-kezelő két cél között szakad meg:

* Ezt a "nehéz" tartományhoz tartozó gépeket szeretné használni a szolgáltatások elhelyezésével. 
* A szolgáltatásokat más tartományokban kívánja elhelyezni, így a tartományok elvesztése nem okoz problémát.

Hogyan néznek ki a kiegyensúlyozatlan tartományok? Az alábbi ábrán két különböző fürtkonfiguráció látható. Az első példában a csomópontok egyenletesen oszlanak el a tartalék tartományok között. A második példában az egyik tartalék tartomány sokkal több csomóponttal rendelkezik, mint a többi tartalék tartomány.

![Két különböző fürtkonfiguráció][Image2]

Az Azure-ban a kiválasztható, hogy melyik tartalék tartomány tartalmaz egy csomópontot, a rendszer felügyeli. Azonban a kiépített csomópontok számától függően továbbra is előfordulhat, hogy a tartalék tartományok több csomóponttal rendelkeznek, mint másokban.

Tegyük fel például, hogy öt tartalék tartomány van a fürtben, de hét csomópontot helyez üzembe a csomópont típusa (**NodeType**) számára. Ebben az esetben az első két tartalék tartomány több csomóponttal fejeződik be. Ha továbbra is több **NodeType** -példányt telepít, és csak néhány példánya van, a probléma rosszabb lesz. Ezért azt javasoljuk, hogy a csomópontok száma az egyes csomópont-típusokban a tartalék tartományok számának többszöröse legyen.

## <a name="upgrade-domains"></a>Frissítési tartományok

A frissítési tartományok egy másik funkció, amely segít Service Fabric fürterőforrás-kezelőnek megérteni a fürt elrendezését. A frissítési tartományok olyan csomópont-készleteket határoznak meg, amelyek egy időben frissülnek. A frissítési tartományok segítséget nyújtanak a fürterőforrás-kezelőnek a felügyeleti műveletek, például a frissítések megismerésében és összehangolásában.

A frissítési tartományok nagyon hasonlóak a tartalék tartományokhoz, de néhány fő különbséggel. Először is, a koordinált hardveres hibák területei határozzák meg a tartalék tartományokat. A frissítési tartományokat a házirend határozza meg. Eldöntheti, hogy hányat szeretne, ahelyett, hogy a környezet diktálja a számot. Több frissítési tartományt is használhat a csomópontok végrehajtásához. A tartalék tartományok és a frissítési tartományok között egy másik különbség, hogy a frissítési tartományok nem hierarchikusak. Ehelyett inkább egy egyszerű címkéhez hasonlítanak.

A következő ábra három tartalék tartományba tartozó frissítési tartományt mutat be. Emellett az állapot-nyilvántartó szolgáltatás három különböző replikájának egyik lehetséges elhelyezését is megjeleníti, ahol mindegyik a különböző hibák és frissítési tartományokba végződik. Ez az elhelyezés lehetővé teszi a tartalék tartomány elvesztését egy szolgáltatás frissítése közben, és továbbra is rendelkezik a kód és az adatmennyiség egy másolatával.  

![Elhelyezés a hibák és a frissítési tartományok között][Image3]

Vannak olyan előnyök és hátrányok, amelyek nagy számú frissítési tartománnyal rendelkeznek. A további frissítési tartományok a frissítés minden egyes lépése részletesebbek, és kisebb számú csomópontot vagy szolgáltatást érint. A rendszernek kevesebb szolgáltatást kell áthelyeznie. Ez általában növeli a megbízhatóságot, mivel a frissítés során bevezetett egyik probléma nem érinti a szolgáltatás kisebb részét. A további frissítési tartományok azt is jelenti, hogy a frissítés hatásának kezeléséhez kevesebb rendelkezésre állású pufferre van szükség más csomópontokon.

Ha például öt frissítési tartománnyal rendelkezik, az egyes csomópontok nagyjából 20%-át kezelik a forgalmat. Ha le kell állítania a frissítési tartományt a frissítéshez, a terhelésnek általában valahova kell mennie. Mivel négy további frissítési tartománnyal rendelkezik, mindegyiknek a teljes forgalom 25%-ának megfelelő helyet kell tartalmaznia. A további frissítési tartományok azt jelentik, hogy a fürt csomópontjain kevesebb pufferre van szükség.

Vegye figyelembe, hogy ehelyett 10 frissítési tartománnyal rendelkezett. Ebben az esetben minden frissítési tartomány csak a teljes forgalom 10 százalékát fogja kezelni. A fürtön keresztüli frissítési lépések esetén minden tartománynak a teljes forgalom 11%-ának megfelelő helyet kell biztosítania. A további frissítési tartományok általában lehetővé teszik, hogy magasabb kihasználtságú csomópontokat futtasson, mert kevesebb fenntartott kapacitásra van szüksége. Ugyanez érvényes a tartalék tartományokra is.  

Számos frissítési tartomány hátránya, hogy a frissítések általában hosszabb ideig tarthatnak. Service Fabric egy rövid időszakot vár a frissítési tartomány befejezése után, és ellenőrzi a következő frissítés megkezdése előtt. Ezek a késések lehetővé teszik a frissítés által bevezetett problémák észlelését a frissítés előtt. A kompromisszum elfogadható, mert megakadályozza, hogy a helytelen változások a szolgáltatás egyszerre túl nagy részét befolyásolják.

A túl kevés frissítési tartomány jelenléte sok negatív mellékhatással rendelkezik. Az egyes frissítési tartományok nem állnak le és nem frissülnek, a teljes kapacitás nagy része nem érhető el. Ha például csak három frissítési tartománnyal rendelkezik, a teljes szolgáltatás vagy a fürt kapacitásának egyharmadát veszi igénybe. Ha nem szeretné, hogy a szolgáltatás egyáltalán ne legyen elérhető, mert a fürt többi részén elegendő kapacitásra van szüksége a munkaterhelés kezeléséhez. A puffer fenntartása azt jelenti, hogy a normál működés során ezek a csomópontok kevésbé töltődnek be, mint egyébként. Ez növeli a szolgáltatás futtatásának költségeit.

Nincs valós korlátja egy adott környezetben a hibák vagy frissítési tartományok teljes számának, illetve az átfedések megkötésének. Vannak azonban gyakori minták:

* A tartalék tartományok és a frissítési tartományok leképezve 1:1
* Egy frissítési tartomány/csomópont (fizikai vagy virtuális operációsrendszer-példány)
* Egy "szalagos" vagy "Matrix" modell, ahol a tartalék tartományok és a frissítési tartományok olyan mátrixot alkotnak, amelyben a gépek általában az átlókat futtatják

![A hibák és a frissítési tartományok elrendezése][Image4]

Nincs legjobb válasz a választott elrendezéshez. Mindegyiknek vannak előnyei és hátrányai. Például a 1FD: 1UD Model egyszerűen beállítható. A Node-modellekben egy frissítési tartomány modellje a legtöbbet a leggyakrabban használt felhasználókhoz hasonlít. A frissítések során az egyes csomópontok egymástól függetlenül frissülnek. Ez hasonló ahhoz, ahogyan a gépek kis készleteit korábban manuálisan frissítették.

A leggyakoribb modell az FD/UD mátrix, ahol a tartalék tartományok és a frissítési tartományok alkotnak egy táblát, és a csomópontok az átlós vonalon kezdődnek. Ez az Azure-beli Service Fabric-fürtökben alapértelmezés szerint használt modell. A sok csomóponttal rendelkező fürtök esetében minden véget ér, mint egy sűrű mátrixos minta.

> [!NOTE]
> Service Fabric az Azure-ban üzemeltetett fürtök nem támogatják az alapértelmezett stratégia módosítását. Csak az önálló fürtök nyújtanak ilyen testreszabást.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>A hibák és a frissítési tartományok korlátai és az eredményül kapott viselkedés
### <a name="default-approach"></a>Alapértelmezett megközelítés

Alapértelmezés szerint a fürterőforrás-kezelő a hibák és a frissítési tartományok között egyensúlyban tartja a szolgáltatásokat. Ez [megkötésként](service-fabric-cluster-resource-manager-management-integration.md)van modellezve. A hiba és a frissítési tartományok állapotának korlátozásai: "az adott szolgáltatás partíciója esetében soha nem lehet nagyobb a különbség, mint a szolgáltatási objektumok (állapot nélküli szolgáltatási példányok vagy állapot-nyilvántartó szolgáltatások replikái) száma az azonos szintű hierarchia két tartománya között."

Tegyük fel, hogy ez a korlátozás "maximális különbség" garanciát biztosít. A hiba-és frissítési tartományok megkötése megakadályozza a szabályt sértő bizonyos lépéseket vagy intézkedéseket.

Tegyük fel például, hogy van egy hat csomóponttal rendelkező fürt, amely öt tartalék tartománnyal és öt frissítési tartománnyal van konfigurálva.

|  | FD0 | FD1 | FD2 ÁRAMLÁSMEGOSZTÓ | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Most tegyük fel, hogy létrehozunk egy szolgáltatást egy **TargetReplicaSetSize** (vagy egy állapot nélküli szolgáltatáshoz, **InstanceCount**), öt értékkel. A replikák az N1-N5. Valójában az N6-ot soha nem használták, hogy hány szolgáltatást hoz létre. De miért? Nézzük meg, mi a különbség az aktuális elrendezés és a mi történne, ha az N6-ot választja.

Itt látható az elrendezés, valamint a replikák száma a hibák és a frissítési tartományok esetében:

|  | FD0 | FD1 | FD2 ÁRAMLÁSMEGOSZTÓ | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Ez az elrendezés a csomópontok és a frissítési tartomány csomópontjainak esetében is egyensúlyban van. A hibák és a frissítési tartományok replikáinak száma is egyensúlyban van. Mindegyik tartomány azonos számú csomóponttal és azonos számú replikával rendelkezik.

Most nézzük meg, mi történne, ha az N2 helyett az N6-ot használta. Hogyan történik a replikák terjesztése?

|  | FD0 | FD1 | FD2 ÁRAMLÁSMEGOSZTÓ | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Ez az elrendezés sérti a "maximális különbség" garanciát a tartalék tartományi korlátozásra vonatkozóan. A FD0 két replikával rendelkezik, míg a FD1 értéke nulla. A FD0 és a FD1 közötti különbség összesen kettő, ami nagyobb, mint a maximális különbség. Mivel a korlátozást megsértették, a fürterőforrás-kezelő nem engedélyezi ezt a megállapodást.

Hasonlóképpen, ha az N2-t és az N6-ot (N1 és N2 helyett) választottuk, a következőt fogjuk kapni:

|  | FD0 | FD1 | FD2 ÁRAMLÁSMEGOSZTÓ | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Ez az elrendezés a tartalék tartományok szempontjából egyensúlyban van. Most azonban megsértjük a frissítési tartomány korlátozását, mivel a UD0 nulla replikával rendelkezik, és a UD1 két van. Ez az elrendezés szintén érvénytelen, és a fürterőforrás-kezelő nem fogja kiválasztani.

Az állapot-nyilvántartó replikák vagy állapot nélküli példányok eloszlásának megközelítése a lehető legjobb hibatűrést biztosítja. Ha egy tartomány leáll, a replikák/példányok minimális száma elvész.

Másfelől ez a megközelítés túl szigorú lehet, és nem teszi lehetővé, hogy a fürt az összes erőforrást kihasználja. Bizonyos fürtcsomópontok esetében bizonyos csomópontok nem használhatók. Ennek hatására előfordulhat, hogy Service Fabric a szolgáltatások nem helyezhetők el, ami figyelmeztető üzeneteket eredményez. Az előző példában az egyes fürtcsomópontok nem használhatók (a példában N6). Még akkor is, ha csomópontokat adott hozzá a fürthöz (N7-N10), a replikák/példányok csak az N1-N5 lesznek elhelyezve a hibák és a frissítési tartományok korlátozásai miatt.

|  | FD0 | FD1 | FD2 ÁRAMLÁSMEGOSZTÓ | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

### <a name="alternative-approach"></a>Alternatív megközelítés

A fürterőforrás-kezelő a hiba-és frissítési tartományok korlátozásának egy másik verzióját támogatja. Lehetővé teszi az elhelyezést, miközben továbbra is garantálja a minimális biztonsági szintet. Az alternatív megkötést a következőképpen lehet megállapítani: "egy adott szolgáltatás partíciója esetében a replika eloszlása tartományokon belül biztosítania kell, hogy a partíció ne érje el a kvórum elvesztését." Tegyük fel, hogy ez a korlátozás "kvórum biztonságos" garanciát biztosít.

> [!NOTE]
> Állapot-nyilvántartó szolgáltatás esetén a *kvórum elvesztését* olyan helyzetben adjuk meg, amikor a partíció replikáinak többsége nem áll le egyszerre. Ha például a **TargetReplicaSetSize** értéke öt, a három replika egy halmaza a kvórumot jelöli. Hasonlóképpen, ha a **TargetReplicaSetSize** hat, négy replikára van szükség a kvórumhoz. Mindkét esetben nem lehet kettőnél több replikát leállítani, ha a partíció szokásos módon kívánja folytatni a működést.
>
> Állapot nélküli szolgáltatás esetén nincs olyan dolog, mint a *kvórum elvesztése*. Az állapot nélküli szolgáltatások általában akkor is működnek, ha a példányok többsége egy időben leáll. Ezért az állapot-nyilvántartó szolgáltatásokra koncentrálunk a cikk további részében.
>

Térjünk vissza az előző példához. A megkötés "kvórum biztonságos" verziójában mindhárom elrendezés érvényes lesz. Még ha a FD0 sikertelen volt a második elrendezésben, vagy a UD1 nem sikerült a harmadik elrendezésben, a partíció továbbra is kvórumot eredményezne. (A replikák többsége továbbra is fennáll.) A megkötés ezen verziójában az N6-ot szinte mindig ki lehet használni.

A "kvórum biztonságos" megközelítés nagyobb rugalmasságot biztosít, mint a "maximális különbség" megközelítés. Ennek az az oka, hogy egyszerűbb olyan replika-eloszlásokat találni, amelyek szinte bármilyen fürtbeli topológiában érvényesek. Ez a megközelítés azonban nem garantálja a legjobb hibatűrési jellemzőket, mert néhány hiba rosszabb, mint a többi.

A legrosszabb esetben a replikák többsége elvész egy tartomány és egy további replika meghibásodásával. Például, ha a kvórumot öt replikával vagy példánnyal szeretné elveszíteni, akkor már csak két hibával veszítheti el a legtöbb hibát.

### <a name="adaptive-approach"></a>Adaptív megközelítés

Mivel mindkét megközelítés rendelkezik erősségekkel és gyengeségekkel, egy adaptív megközelítést vezettünk be, amely kombinálja ezt a két stratégiát.

> [!NOTE]
> Ez az alapértelmezett viselkedés a Service Fabric 6,2-es verziótól kezdődően.
>
> Az adaptív módszer alapértelmezés szerint a "maximális különbség" logikát használja, és a "kvórum biztonságos" logikára vált, ha szükséges. A fürterőforrás-kezelő automatikusan kiértékeli, hogy milyen stratégiát kell megkeresni a fürt és a szolgáltatások konfigurálásának módjával.
>
> A fürterőforrás-kezelőnek a "kvórum alapú" logikát kell használnia egy szolgáltatáshoz, mindkét feltétel igaz:
>
> * A szolgáltatás **TargetReplicaSetSize** a tartalék tartományok számával és a frissítési tartományok számával egyenlően osztható.
> * A csomópontok száma kisebb vagy egyenlő, mint a tartalék tartományok száma, szorozva a frissítési tartományok számával.
>
> Vegye figyelembe, hogy a fürterőforrás-kezelő ezt a megközelítést fogja használni az állapot nélküli és az állapot-nyilvántartó szolgáltatásokhoz is, bár a kvórum elvesztése nem vonatkozik az állapot nélküli szolgáltatások esetében.

Térjünk vissza az előző példához, és tegyük fel, hogy egy fürt most már nyolc csomóponttal rendelkezik. A fürt továbbra is öt tartalék tartománnyal és öt frissítési tartománnyal van konfigurálva, és a fürtben üzemeltetett szolgáltatás **TargetReplicaSetSize** értéke öt marad.

|  | FD0 | FD1 | FD2 ÁRAMLÁSMEGOSZTÓ | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Mivel az összes szükséges feltétel teljesül, a fürterőforrás-kezelő a "kvórum-alapú" logikát fogja használni a szolgáltatás terjesztésekor. Ez az N6-N8 használatát teszi lehetővé. Ebben az esetben az egyik lehetséges szolgáltatás a következőhöz hasonló lehet:

|  | FD0 | FD1 | FD2 ÁRAMLÁSMEGOSZTÓ | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Ha a szolgáltatás **TargetReplicaSetSize** értéke négyre van csökkentve (például), a fürterőforrás-kezelő ezt a változást fogja megfigyelni. A "maximális különbség" logikát fogja használni, mivel a **TargetReplicaSetSize** nem osztható meg a tartalék tartományok és a frissítési tartományok számával. Ennek eredményeképpen bizonyos replika-mozgások történnek a fennmaradó négy replikának az N1-N5 csomópontokon való elosztása során. Így a tartalék tartomány és a frissítési tartomány logikájának "maximális különbség" verziója nem sérül.

Az előző elrendezésben, ha a **TargetReplicaSetSize** értéke öt, és a rendszer eltávolítja az N1-t a fürtből, a frissítési tartományok száma négy értékkel egyenlő lesz. A fürterőforrás-kezelő a "maximális különbség" logikát használja, mivel a frissítési tartományok száma többé nem osztja el a szolgáltatás **TargetReplicaSetSize** értékét. Ennek eredményeképpen a (z)-replika R1-es verziójának újraépítése az N4-es porton keresztül történik, hogy a hiba-és frissítési tartományra vonatkozó megkötés ne legyen megsértve.

|  | FD0 | FD1 | FD2 ÁRAMLÁSMEGOSZTÓ | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N.A. |N.A. |N.A. |N.A. |N.A. |N.A. |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>A hibák és a frissítési tartományok konfigurálása

Az Azure által üzemeltetett Service Fabric üzemelő példányok esetében a tartalék tartományok és a frissítési tartományok automatikusan definiálva vannak. Service Fabric felveszi és felhasználja a környezeti információkat az Azure-ból.

Ha saját fürtöt hoz létre (vagy egy adott topológiát szeretne futtatni a fejlesztésben), megadhatja a tartalék tartományt, és saját kezűleg is frissítheti a tartományi adatokat. Ebben a példában egy kilenc csomópontos helyi fejlesztési fürtöt adunk meg, amely három adatközpontra terjed ki (mindkettő három racktel). Ezen a fürtön három frissítési tartomány is szerepel a három adatközpontban. A ClusterManifest.xml konfigurációjának példája:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

Ez a példa ClusterConfig.jst használ a különálló központi telepítésekhez:

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Ha Azure Resource Manageron keresztül definiál fürtöket, az Azure a tartalék tartományokat és a frissítési tartományokat rendeli hozzá. Így az Azure Resource Manager-sablonban szereplő csomópontok és virtuálisgép-méretezési csoportok definíciója nem tartalmaz információkat a tartalék tartományról vagy a frissítési tartományról.
>

## <a name="node-properties-and-placement-constraints"></a>Csomópont tulajdonságai és elhelyezési megkötések

Néha (valójában az idő nagy részében) érdemes biztosítani, hogy bizonyos munkaterhelések csak bizonyos típusú csomópontokon fussanak a fürtben. Előfordulhat például, hogy egyes munkaterhelésekhez GPU vagy SSD szükséges, mások pedig nem.

Nagyszerű példa arra, hogy a hardverek konkrét számítási feladatokhoz való célzása szinte minden n szintű architektúra. Bizonyos gépek az alkalmazás előtér-vagy API-kiszolgálóként szolgálnak, és elérhetők az ügyfelek vagy az Internet számára. A különböző gépek – gyakran különböző hardveres erőforrásokkal – kezelik a számítási vagy tárolási rétegek munkáját. Ezeket általában _nem_ közvetlenül az ügyfelek vagy az Internet teszi elérhetővé.

Service Fabric bizonyos esetekben várhatóan bizonyos számítási feladatoknak bizonyos hardveres konfigurációkon is futniuk kell. Például:

* Egy meglévő n szintű alkalmazás "felemelve és áthelyezve" lett egy Service Fabric környezetbe.
* A munkaterhelést meghatározott hardveren kell futtatni teljesítmény-, méretezési vagy biztonsági elkülönítési okokból.
* A munkaterhelést más számítási feladatokból is el kell különíteni a házirend-vagy erőforrás-felhasználási okokból.

Az ilyen típusú konfigurációk támogatásához Service Fabric tartalmaz olyan címkéket, amelyek a csomópontokra alkalmazhatók. Ezeket a címkéket *csomópont-tulajdonságokat* nevezzük. Az *elhelyezési megkötések* az egyes szolgáltatásokhoz csatolt utasítások, amelyek egy vagy több csomópont-tulajdonsághoz vannak kiválasztva. Az elhelyezési megkötések határozzák meg a szolgáltatások futtatásának helyét. A megkötések halmaza bővíthető. Bármely kulcs/érték pár működhet.

![Különböző számítási feladatok a fürt elrendezéséhez][Image5]

### <a name="built-in-node-properties"></a>Beépített csomópont tulajdonságai

Service Fabric definiál néhány alapértelmezett csomópont-tulajdonságot, amelyet automatikusan használhat, így nem kell megadnia azokat. Az egyes csomópontokon definiált alapértelmezett tulajdonságok a **NodeType** és a **csomópontnév**.

Például megadhat egy elhelyezési korlátozást a következőként: `"(NodeType == NodeType03)"` . A **NodeType** egy általánosan használt tulajdonság. Ez azért hasznos, mert megfelel a 1:1-nek a gép egy típusával. Minden típusú gép egy hagyományos n szintű alkalmazásban a számítási feladatok típusának felel meg.

![Elhelyezési megkötések és csomópont tulajdonságai][Image6]

## <a name="placement-constraints-and-node-property-syntax"></a>Elhelyezési korlátozások és Node tulajdonság szintaxisa

A Node tulajdonságban megadott érték karakterlánc, logikai vagy hosszú lehet. A szolgáltatás utasításait elhelyezési *korlátozásnak* nevezzük, mert korlátozza, hogy a szolgáltatás hogyan futhat a fürtben. A korlátozás bármely olyan logikai utasítás lehet, amely a fürt csomópont-tulajdonságain működik. A logikai utasítások érvényes választói a következők:

* Feltételes ellenőrzések adott utasítások létrehozásához:

  | Utasítás | Syntax |
  | --- |:---:|
  | "egyenlő" | "==" |
  | "nem egyenlő" | "!=" |
  | "nagyobb, mint" | ">" |
  | "nagyobb vagy egyenlő" | ">=" |
  | "kisebb, mint" | "<" |
  | "kisebb vagy egyenlő" | "<=" |

* Logikai utasítások csoportosításhoz és logikai műveletekhez:

  | Utasítás | Syntax |
  | --- |:---:|
  | és | "&&" |
  | vagy | "&#124;&#124;" |
  | nem | "!" |
  | "csoport mint egyetlen utasítás" | "()" |

Íme néhány példa az alapszintű korlátozási utasításokra:

* `"Value >= 5"`
* `"NodeColor != green"`
* `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Csak azok a csomópontok kerülhetnek rá a szolgáltatásba, ahol az általános elhelyezési megkötés utasítása az "igaz" értékre van kiértékelve. A tulajdonsággal nem rendelkező csomópontok nem egyeznek meg a tulajdonságot tartalmazó elhelyezési korlátozásokkal.

Tegyük fel, hogy a következő csomópont-tulajdonságokat definiálták egy csomópont-típushoz a ClusterManifest.xmlban:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Az alábbi példa a ClusterConfig.json keresztül meghatározott csomópont-tulajdonságokat jeleníti meg az önálló telepítések esetén, vagy az Azure által üzemeltetett fürtökön Template.js.

> [!NOTE]
> A Azure Resource Manager-sablonban a csomópont típusa általában paraméteres. A NodeType01 helyett a következőképpen fog kinézni `"[parameters('vmNodeType1Name')]"` .
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

A szolgáltatáshoz a következő módon hozhat létre szolgáltatási elhelyezési *korlátozásokat* :

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Ha a NodeType01 összes csomópontja érvényes, akkor a csomópont típusát is kiválaszthatja a megkötéssel `"(NodeType == NodeType01)"` .

A szolgáltatás elhelyezési korlátozásai dinamikusan frissíthetők a futtatókörnyezet során. Ha szükséges, áthelyezheti a szolgáltatást a fürtön, a követelmények hozzáadásával és eltávolításával stb. Service Fabric biztosítja, hogy a szolgáltatás az ilyen típusú változások után is rendelkezésre álljon.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Az elhelyezési megkötések minden elnevezett Service-példányhoz meg vannak adva. A frissítések mindig a korábban megadott helyen (felülírva) lépnek.

A fürt definíciója a csomópont tulajdonságait határozza meg. A csomópont tulajdonságainak módosítása a fürtkonfiguráció frissítését igényli. A csomópont tulajdonságainak frissítéséhez minden érintett csomópontnak újra kell indítania az új tulajdonságainak jelentését. A Service Fabric kezeli ezeket a működés közbeni frissítéseket.

## <a name="describing-and-managing-cluster-resources"></a>A fürterőforrások leírása és kezelése

Bármelyik Orchestrator egyik legfontosabb feladata, hogy segítsen kezelni a fürt erőforrás-felhasználását. A fürt erőforrásainak kezelése több különböző dolgot is jelenthet.

Először is gondoskodni kell arról, hogy a gépek ne legyenek túlterhelve. Ez azt jelenti, hogy a gépek nem futnak több szolgáltatással, mint amennyit képesek kezelni.

Másodszor, a szolgáltatások hatékony futtatásához elengedhetetlen a kiegyensúlyozás és az optimalizálás. A költséghatékony vagy a teljesítményre érzékeny szolgáltatási ajánlatok nem teszik lehetővé, hogy egyes csomópontok ne legyenek melegek, míg mások is hidegek. A gyors csomópontok erőforrás-tartalomhoz és gyenge teljesítményhez vezetnek. A hideg csomópontok az elpazarolt erőforrásokat és a megnövekedett költségeket jelentik.

Service Fabric az erőforrásokat *metrikaként* jeleníti meg. A metrikák bármilyen logikai vagy fizikai erőforrás, amelyet le szeretne írni a Service Fabric. Mérőszámok például a következők: "WorkQueueDepth" vagy "MemoryInMb". További információ a csomópontokon Service Fabric által szabályozható fizikai erőforrásokról: [erőforrás-szabályozás](service-fabric-resource-governance.md). A fürterőforrás-kezelő által használt alapértelmezett metrikákkal és az egyéni metrikák konfigurálásával kapcsolatos információkért tekintse meg [ezt a cikket](service-fabric-cluster-resource-manager-metrics.md).

A metrikák eltérnek az elhelyezési megkötések és a csomópont tulajdonságaitól. A csomópont tulajdonságai maguk a csomópontok statikus leírói. A metrikák leírják, hogy a csomópontok milyen erőforrásokat használnak, és hogy a szolgáltatások mikor futnak a csomópontokon. Lehetséges, hogy a Node tulajdonság **HasSSD** , és igaz vagy hamis értékre van állítva. Az SSD-lemezen rendelkezésre álló szabad terület nagysága és a szolgáltatások által felhasznált mennyiség (például "DriveSpaceInMb") mérőszáma.

Az elhelyezési korlátozásokhoz és a csomópontok tulajdonságaihoz hasonlóan a Service Fabric fürterőforrás-kezelő nem érti, hogy mit jelent a metrikák neve. A metrikák nevei csak karakterláncok. Érdemes bejelenteni az egységeket a létrehozott metrikai nevek részeként, ha azok nem egyértelműek.

## <a name="capacity"></a>Kapacitás

Ha kikapcsolta az összes erőforrás- *kiegyenlítést*, Service Fabric a fürterőforrás-kezelő továbbra is gondoskodik arról, hogy egyetlen csomópont sem haladja meg a kapacitását. A kapacitás-túllépések kezelése csak akkor lehetséges, ha a fürt túl teljes, vagy a munkaterhelés nagyobb, mint bármely csomópont. A kapacitás egy másik *korlátozás* , amelyet a fürterőforrás-kezelő használ annak megértéséhez, hogy egy adott erőforrás mekkora része a csomópontnak. A fennmaradó kapacitást is nyomon követheti a fürt teljes egészében.

A szolgáltatás szintjén a kapacitás és a felhasználás is mérőszámok alapján van kifejezve. Előfordulhat például, hogy a metrika "ClientConnections", és egy csomópont 32 768-as "ClientConnections" képességgel rendelkezik. Más csomópontok rendelkezhetnek más korlátozásokkal. A csomóponton futó szolgáltatás azt is megteheti, hogy jelenleg a (z) "ClientConnections" metrika 32 256.

A Futtatás során a fürterőforrás-kezelő nyomon követi a fürt és a csomópontok fennmaradó kapacitását. A kapacitás nyomon követéséhez a fürterőforrás-kezelő kivonja az egyes szolgáltatások használatát egy csomópont azon kapacitása alapján, ahol a szolgáltatás fut. Ezen információk alapján a fürterőforrás-kezelő kiderítheti, hogy hová helyezheti vagy helyezheti át a replikákat, hogy a csomópontok ne lépjék túl a kapacitást.

![Fürtcsomópontok és kapacitás][Image7]

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Láthatja a fürt jegyzékfájljában definiált kapacitásokat. Íme egy példa a ClusterManifest.xmlra:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Az alábbi példa az Azure által üzemeltetett fürtökre való ClusterConfig.json, az önálló üzembe helyezésekhez vagy az Template.js-on keresztül definiált kapacitásokra mutat példát:

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

A szolgáltatás terhelése gyakran dinamikusan változik. Tegyük fel, hogy a replika "ClientConnections" terhelése 1 024 – 2 048 értékre változott. A csomóponton, amelyen a fut, csak 512 maradt a metrikához. Most, hogy a replika vagy a példány elhelyezése érvénytelen, mert nincs elég hely a csomóponton. A fürterőforrás-kezelőnek vissza kell kérnie a csomópontot a kapacitás alatt. Ez csökkenti a terhelést a kapacitás feletti csomóponton, ha egy vagy több replikát vagy példányt helyez át a csomópontról más csomópontokra.

A fürterőforrás-kezelő megpróbálja csökkenteni a replikák áthelyezésének költségeit. További információ a [mozgási díjakról](service-fabric-cluster-resource-manager-movement-cost.md) és a [stratégiák és szabályok újraelosztásáról](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Fürt kapacitása

Hogyan működik a Service Fabric fürterőforrás-kezelője, hogy a teljes fürt túl teljes legyen? Dinamikus betöltéssel nem sokat tehet. A szolgáltatások a fürt erőforrás-kezelője által végrehajtott műveletektől függetlenül is rendelkezhetnek a terheléssel. Ennek eredményeképpen a fürt sok belmagassággal rendelkezik, ha holnap van egy tüske.

A fürterőforrás-kezelőben a vezérlők segítenek a problémák megelőzésében. Az első dolog, ami meggátolja az új munkaterhelések létrehozását, amelyek miatt a fürt megtelik lesznek.

Tegyük fel, hogy létrehoz egy állapot nélküli szolgáltatást, és a hozzá tartozó terheléssel rendelkezik. A szolgáltatás a "DiskSpaceInMb" metrikával foglalkozik. A szolgáltatás a szolgáltatás minden példánya esetében a "DiskSpaceInMb" öt egységét fogja használni. A szolgáltatás három példányát kívánja létrehozni. Ez azt jelenti, hogy a fürtben a "DiskSpaceInMb" 15 egységnek kell lennie ahhoz, hogy még létre lehessen hozni ezeket a szolgáltatási példányokat.

A fürterőforrás-kezelő folyamatosan kiszámítja az egyes mérőszámok kapacitását és felhasználását, hogy az képes legyen a fürt fennmaradó kapacitásának meghatározására. Ha nincs elég hely, a fürterőforrás-kezelő elutasítja a szolgáltatás létrehozásának hívását.

Mivel a követelmény csak a 15 egység lesz elérhető, számos különböző módon lefoglalhatja ezt a területet. Lehet például, hogy egy fennmaradó egység kapacitása 15 különböző csomóponton, vagy három további, öt különböző csomóponton fennmaradó egység kapacitása. Ha a fürterőforrás-kezelő átrendezi a dolgokat, hogy a három csomóponton öt egység legyen elérhető, akkor a szolgáltatás elhelyezi a szolgáltatást. A fürt átrendezése általában csak akkor lehetséges, ha a fürt csaknem megtelt, vagy a meglévő szolgáltatásokat valamilyen okból nem lehet összevonni.

## <a name="node-buffer-and-overbooking-capacity"></a>Csomópont-puffer és a túlfoglalási kapacitás

Ha meg van adva egy metrika csomópont-kapacitása, a fürterőforrás-kezelő soha nem helyezi át vagy helyezi át a replikákat egy csomópontba, ha a teljes terhelés a megadott csomópont-kapacitás fölé kerül. Ez esetenként megakadályozhatja az új replikák elhelyezését vagy a sikertelen replikák cseréjét, ha a fürt közel teljes kapacitású, és egy nagy terhelésű replikát kell elhelyezni, cserélni vagy áthelyezni.

A nagyobb rugalmasság érdekében megadhatja a csomópont-puffert vagy a túlfoglalási kapacitást. Ha egy metrika esetében meg van adva a csomópont-puffer vagy a túlfoglalási kapacitás, a fürterőforrás-kezelő megkísérli a replikák elhelyezését vagy áthelyezését oly módon, hogy a puffer vagy a túlfoglalási kapacitás ne legyen használatban, de lehetővé teszi a puffer vagy a túlfoglalási kapacitás használatát, ha olyan műveletek szükségesek, amelyek növelik a szolgáltatás rendelkezésre állását, például:

* Új replika elhelyezése vagy a sikertelen replikák cseréje
* Elhelyezés a frissítések során
* A Soft és a Hard korlátozás megsértésének javítása
* Töredezettségmentesítése

A csomópont-puffer kapacitása a megadott csomópont-kapacitás alatti kapacitás fenntartott részét jelöli, a túlfoglalási kapacitás pedig a megadott csomópont kapacitása feletti extra kapacitás egy részét jelöli. Mindkét esetben a fürterőforrás-kezelő megkísérli megtartani ezt a kapacitást.

Ha például egy csomópont a 100 metrikus *CpuUtilization* megadott kapacitással rendelkezik, és az adott metrika esetében a csomópont-puffer százaléka 20%-ra van állítva, akkor a teljes és a nem pufferelt kapacitások 100 és 80 lesznek, és a fürterőforrás-kezelő a normál körülmények között nem 80 helyezi el a csomópontot a maximálisan kifelé haladó terheléssel.

![A teljes kapacitás megegyezik a csomópont kapacitásával (csomópont-puffer + nem pufferelt)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity.png)

A csomópont-puffert akkor kell használni, ha a csomópont-kapacitás egy részét le szeretné foglalni, amely csak a fent említett szolgáltatás rendelkezésre állását növelő műveletekhez használható.

Másfelől, ha a csomópont-túlfoglalási százalékot használja, és a értéke 20%, akkor a teljes és a nem pufferelt kapacitás 120 és 100 lesz.

![A teljes kapacitás egyenlő a túlfoglalási kapacitással és a csomópont kapacitásával (túlfoglalás + nem pufferelt)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity-with-overbooking.png)

A túlfoglalási kapacitást akkor kell használni, ha engedélyezni szeretné a fürterőforrás-kezelő számára, hogy replikákat helyezzen el egy csomóponton, még akkor is, ha a teljes erőforrás-használat meghaladja a kapacitást. Ennek segítségével további rendelkezésre állást biztosíthat a szolgáltatásoknak a teljesítmény rovására. Ha a rendszer túlfoglalást használ, a felhasználói alkalmazás logikájának képesnek kell lennie a kevesebb fizikai erőforrással való működésre, mint amennyire szüksége lehet.

Ha meg van adva a csomópont-puffer vagy a túlfoglalási kapacitás, a fürterőforrás-kezelő nem helyezi át és nem helyezi át a replikákat, ha a célként megadott csomópont teljes terhelése a teljes kapacitás fölé kerülne (a csomópont-puffer és a csomópont kapacitása, valamint a túlterhelés esetén a túlfoglalási kapacitás

A túlfoglalási kapacitás is megadható a végtelen értékre. Ebben az esetben a fürterőforrás-kezelő megkísérli megőrizni a csomópont teljes terhelését a megadott csomópont-kapacitás alatt, de a csomóponton sokkal nagyobb terhelést eredményezhet, ami súlyos teljesítményt okozhat.

Egy metrika nem rendelkezhet egyszerre a csomópont-pufferrel és a hozzájuk megadott túlfoglalási kapacitással.

Az alábbi példa bemutatja, hogyan határozhatja meg a csomópontok pufferét vagy a túlfoglalási kapacitásokat a *ClusterManifest.xmlban*:

```xml
<Section Name="NodeBufferPercentage">
    <Parameter Name="SomeMetric" Value="0.15" />
</Section>
<Section Name="NodeOverbookingPercentage">
    <Parameter Name="SomeOtherMetric" Value="0.2" />
    <Parameter Name=”MetricWithInfiniteOverbooking” Value=”-1.0” />
</Section>
```

Az alábbi példa bemutatja, hogyan határozhatja meg a csomópontok pufferét vagy a kapacitások túlfoglalását *ClusterConfig.json* keresztül a különálló központi telepítések esetén, vagy *Template.js* az Azure által üzemeltetett fürtökhöz:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      }
    ]
  },
  {
    "name": "NodeOverbookingPercentage",
    "parameters": [
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      },
      {
          "name": "MetricWithInfiniteOverbooking",
          "value": "-1.0"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Következő lépések

* A fürterőforrás-kezelő architektúrájának és információinak folyamatáról további információt a [fürterőforrás-kezelő architektúrájának áttekintése](service-fabric-cluster-resource-manager-architecture.md)című témakörben talál.
* A Lemeztöredezettség-mentesítő mérőszámok meghatározása az egyik módszer a csomópontok terhelésének konszolidálására a kiterjedésük helyett. A töredezettségmentesítés konfigurálásával kapcsolatos további információkért lásd: [a metrikák és a betöltések töredezettségmentesítése Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Kezdje a kezdetektől, és [Ismerkedjen meg Service Fabric fürterőforrás-kezelővel](service-fabric-cluster-resource-manager-introduction.md).
* Annak megismeréséhez, hogy a fürterőforrás-kezelő hogyan kezeli és kiegyenlíti a fürt terhelését, tekintse meg a [Service Fabric-fürt kiegyensúlyozását](service-fabric-cluster-resource-manager-balancing.md)ismertető témakört.

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
