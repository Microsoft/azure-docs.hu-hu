---
title: Azure Service Fabric vész-helyreállítás
description: Az Azure Service Fabric a katasztrófák kezelésére szolgáló képességeket kínál. Ez a cikk az esetlegesen felmerülő katasztrófák típusait és azok kezelését ismerteti.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8d99b4d1fbf227d850de387b7ca24dcd3fd40646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791155"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Vész-helyreállítás az Azure Service Fabric
A magas rendelkezésre állás megvalósításának kritikus része annak biztosítása, hogy a szolgáltatások képesek legyenek túlélni az összes különböző típusú hibát. Ez különösen fontos a nem tervezett és a vezérlőn kívüli hibák esetén. 

Ez a cikk néhány olyan gyakori meghibásodási módot ismertet, amely akkor fordulhat elő, ha nem megfelelően lett modellezve és felügyelve. Emellett azt is ismerteti, hogy milyen enyhítések és műveletek történnek, ha a katasztrófa amúgy is előfordul. A cél az állásidő vagy az adatvesztés kockázatának korlátozása vagy megszüntetése, ha a hiba a tervezett vagy egyéb módon történik.

## <a name="avoiding-disaster"></a>Katasztrófák elkerülése
Az Azure Service Fabric fő célja, hogy segítse a környezet és a szolgáltatások modellezését oly módon, hogy a gyakori meghibásodási típusok ne legyenek katasztrófák. 

Általánosságban elmondható, hogy a vész-és meghibásodási forgatókönyvek két típusa létezik:
- Hardver-és szoftver-hibák
- Működési hibák

### <a name="hardware-and-software-faults"></a>Hardver-és szoftver-hibák
A hardver-és szoftver-hibák kiszámíthatatlanok. A hibák túlélésének legegyszerűbb módja a szolgáltatás több példányának futtatása hardveres vagy szoftveres hibák határain keresztül. 

Ha például a szolgáltatás csak egy gépen fut, akkor az adott gép meghibásodása az adott szolgáltatáshoz tartozó katasztrófa. A katasztrófa elkerülésének egyszerű módja annak biztosítása, hogy a szolgáltatás több gépen is fusson. Tesztelésre is szükség van annak biztosításához, hogy az egyik gép meghibásodása ne zavarja meg a futó szolgáltatást. A kapacitás megtervezése biztosítja, hogy a helyettesítő példányok máshol is létrehozhatók, és a kapacitás csökkentése ne terhelje túl a fennmaradó szolgáltatásokat. 

Ugyanez a minta attól függetlenül működik, hogy milyen hibát próbál elkerülni. Ha például egy SAN-kiszolgáló meghibásodása miatt aggódik, akkor több, mint egy tartományon belül fut. Ha aggódik a kiszolgálók rack elvesztése miatt, több állványon is futtathatja őket. Ha aggódik az adatközpontok elvesztése miatt, a szolgáltatásnak több Azure-régióban, több Azure Availability Zones vagy a saját adatközpontjai között kell futnia. 

Ha egy szolgáltatás több fizikai példányra (gépekre, állványokra, adatközpontokra, régiókra) terjed ki, akkor továbbra is bizonyos típusú egyidejű hibákra van szüksége. Egy adott típus (például egyetlen virtuális gép vagy hálózati kapcsolat meghibásodása) esetén a rendszer automatikusan kezeli az adatokat, és így már nem "vész". 

A Service Fabric mechanizmusokat biztosít a fürt bővítéséhez és a sikertelen csomópontok és szolgáltatások visszaállításához. A Service Fabric a szolgáltatások számos példányának futtatását is lehetővé teszi, hogy megakadályozza a nem tervezett hibák valós katasztrófák felé való bekapcsolását.

Előfordulhat, hogy az üzembe helyezés elég nagy méretűre állítása nem valósítható meg. Előfordulhat például, hogy több hardveres erőforrást is igénybe vehet, mint amennyit a meghibásodás valószínűségéhez képest szeretne fizetni. Ha elosztott alkalmazásokat használ, a további kommunikációs ugrások vagy az állami replikálási költségek nem elfogadható késést okozhatnak. Ha ez a sor eltér az egyes alkalmazásokhoz. 

A szoftveres hibák esetében előfordulhat, hogy a hiba a méretezni kívánt szolgáltatásban van. Ebben az esetben a több példány nem akadályozza meg a katasztrófát, mert a meghibásodási feltétel összefügg az összes példány között.

### <a name="operational-faults"></a>Működési hibák
Még akkor is, ha a szolgáltatás számos redundanciával rendelkezik a világ különböző részeit illetően, továbbra is katasztrofális eseményekkel találkozhat. Előfordulhat például, hogy valaki véletlenül újrakonfigurálja a szolgáltatás DNS-nevét, vagy törli azt. 

Tegyük fel például, hogy van egy állapot-nyilvántartó Service Fabric szolgáltatás, és valaki törölte a szolgáltatást véletlenül. Hacsak nincs valamilyen más megoldás, akkor ez a szolgáltatás és az összes olyan állam, amely már elfogyott. Az ilyen típusú működési katasztrófák ("Hoppá") különböző kockázatcsökkentő és helyreállítási lépéseket igényelnek, mint a normál, nem tervezett hibák. 

Az ilyen típusú működési hibák elkerülésének legjobb módja a következő:
- A környezethez való operatív hozzáférés korlátozása.
- A veszélyes műveletek szigorú ellenőrzése.
- Automatizálja az automatizálást, meggátolja a manuális vagy sávon kívüli változásokat, és érvényesítse a környezet adott módosításait, mielőtt azok életbe lépnek.
- Győződjön meg arról, hogy a roncsolásos műveletek "Soft". A lágy műveletek nem lépnek azonnal érvénybe, vagy egy időablakon belül visszavonhatók.

A Service Fabric mechanizmusokat biztosít a működési hibák megelőzésére, például [szerepköralapú hozzáférés-](service-fabric-cluster-security-roles.md) vezérlés biztosítására a fürt műveleteihez. Azonban a legtöbb működési hiba a szervezeti erőfeszítésekhez és más rendszerekhez szükséges. A Service Fabric a megmaradó működési hibákra vonatkozó mechanizmusokat biztosít, amelyek többsége az [állapot-nyilvántartó szolgáltatások biztonsági mentésére és visszaállítására](service-fabric-backuprestoreservice-quickstart-azurecluster.md)szolgál.

## <a name="managing-failures"></a>Hibák kezelése
A Service Fabric célja a hibák automatikus kezelése. Bizonyos típusú hibák kezeléséhez azonban a szolgáltatásoknak további kóddal kell rendelkezniük. A biztonsági és üzletmenet-folytonossági okok miatt más típusú hibák _nem_ kezelhetők automatikusan. 

### <a name="handling-single-failures"></a>Egyszeri hibák kezelése
Az egyes gépek bármilyen okból sikertelenek lehetnek. Előfordulhat, hogy a hardver oka, például a tápegységek és a hálózati hardver meghibásodása. Más hibák a szoftverben vannak. Ilyenek például az operációs rendszer és a szolgáltatás hibái. Service Fabric automatikusan észleli az ilyen típusú hibákat, beleértve azokat az eseteket is, amikor a gép hálózati problémák miatt el lesz különítve más gépektől.

A szolgáltatás típusától függetlenül az adott példány futtatása az adott szolgáltatáshoz tartozó állásidőt eredményezi, ha a kód egyetlen példánya valamilyen okból meghiúsul. 

Egyetlen hiba kezeléséhez a legegyszerűbb dolog, ha a szolgáltatások alapértelmezés szerint egynél több csomóponton futnak. Az állapot nélküli szolgáltatások esetében ügyeljen arra, hogy `InstanceCount` az nagyobb legyen, mint 1. Az állapot-nyilvántartó szolgáltatások esetében a minimális javaslat az, hogy `TargetReplicaSetSize` `MinReplicaSetSize` mindkét érték 3. A szolgáltatási kód több példányának futtatásával biztosítható, hogy a szolgáltatás automatikusan kezeljen egyetlen hibát. 

### <a name="handling-coordinated-failures"></a>Koordinált hibák kezelése
A fürtben található koordinált hibák oka lehet tervezett vagy nem tervezett infrastruktúra-meghibásodás és-változás, illetve a tervezett szoftverek módosítása. Az olyan infrastruktúra-zónák Service Fabric, amelyek az összehangolt hibákat a tartalék *tartományokban* tapasztalják. Az összehangolt szoftveres változásokat tapasztaló területek *frissítési tartományként* lesznek modellezve. További információ a tartalék tartományokról, a frissítési tartományokról és a fürt topológiáról: [Service Fabric-fürt leírása a fürterőforrás-kezelő használatával](service-fabric-cluster-resource-manager-cluster-description.md).

Alapértelmezés szerint a Service Fabric a szolgáltatások futtatásának megtervezése során a hiba-és frissítési tartományokat tekinti át. Alapértelmezés szerint a Service Fabric megkísérli biztosítani, hogy a szolgáltatások több hibatűrési és frissítési tartományon keresztül fussanak, hogy ha a tervezett vagy nem tervezett módosítások történnek, a szolgáltatások továbbra is elérhetők maradnak. 

Tegyük fel például, hogy egy áramforrás meghibásodása egy állványon lévő összes gép egyidejű feladatátvételét okozza. Ha a szolgáltatás több példányban fut, akkor a tartalék tartományba tartozó sok gép elvesztése csupán egy újabb példát mutat be egy szolgáltatás egyetlen meghibásodása esetén. Ezért fontos a szolgáltatások magas rendelkezésre állásának biztosítása a hibák és a frissítési tartományok kezelésében. 

Ha az Azure-ban Service Fabric futtat, a tartalék tartományok és a frissítési tartományok automatikusan kezelhetők. Más környezetekben előfordulhat, hogy nem. Ha saját fürtöket hoz létre a helyszínen, ügyeljen arra, hogy megfelelően képezze le és tervezze meg a tartalék tartomány elrendezését.

A frissítési tartományok olyan modellezési területek esetében hasznosak, ahol a szoftverek frissítése egyszerre történik. Emiatt a frissítési tartományok gyakran meghatározzák azokat a határokat, ahol a szoftverek a tervezett frissítések során le vannak véve. A Service Fabric és a szolgáltatásainak frissítései ugyanazt a modellt követik. További információ a működés közbeni frissítésekről, a frissítési tartományokról és a Service Fabric Health modellről, amely segít megakadályozni a fürt és a szolgáltatás nem kívánt módosításainak működését. lásd:

 - [Alkalmazás frissítése](service-fabric-application-upgrade.md)
 - [Alkalmazás-verziófrissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric Health Model](service-fabric-health-introduction.md)

A fürt elrendezését a [Service Fabric Explorerban](service-fabric-visualizing-your-cluster.md)megadott fürtös Térkép használatával jelenítheti meg:

<center>

![Service Fabric Explorerban található tartalék tartományok közötti csomópontok][sfx-cluster-map]
</center>

> [!NOTE]
> A hibák modellezése, a működés közbeni frissítés, a szolgáltatási kód és az állapot számos példányának futtatása, elhelyezési szabályok, amelyek biztosítják, hogy a szolgáltatások a hibák és a frissítési tartományok között futnak, és a beépített állapot-figyelés csupán *néhány* olyan funkció, amelyet a Service Fabric biztosít a normál működési problémák és hibák megtartása érdekében. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Egyidejű hardveres vagy szoftveres hibák kezelése
Egyetlen hibáról beszélünk. Amint láthatja, egyszerűen kezelhetik az állapot nélküli és az állapot-nyilvántartó szolgáltatásokat, csupán a kód (és az állapot) több példányának megtartásával, amely a hibák és a frissítési tartományok között fut. 

Több egyidejű véletlenszerű hiba is előfordulhat. Ezek nagyobb valószínűséggel eredményeznek állásidőt vagy tényleges katasztrófát.


#### <a name="stateless-services"></a>Állapot nélküli szolgáltatások
Az állapot nélküli szolgáltatás példányszáma azt jelzi, hogy a kívánt számú példányt kell futtatni. Ha a példányok bármelyike (vagy mindegyike) meghibásodik, Service Fabric válaszol a más csomópontokon lévő helyettesítő példányok automatikus létrehozásával. Service Fabric továbbra is létrehozhatja a kihelyezéseket, amíg a szolgáltatás vissza nem áll a kívánt példányszámra.

Tegyük fel például, hogy az állapot nélküli szolgáltatás `InstanceCount` értéke-1. Ez az érték azt jelenti, hogy az egyik példánynak futnia kell a fürt mindegyik csomópontján. Ha a példányok némelyike meghibásodik, Service Fabric felismeri, hogy a szolgáltatás nem a kívánt állapotban van, és megpróbálja létrehozni a példányokat azokon a csomópontokon, amelyeken hiányoznak.

#### <a name="stateful-services"></a>Állapot-nyilvántartó szolgáltatások
Kétféle állapot-nyilvántartó szolgáltatás létezik:
- Állapot-nyilvántartó megőrzött állapottal.
- Nem megőrzött állapottal rendelkező állapotú. (A rendszer a memóriában tárolja az állapotot.)

Az állapot-nyilvántartó szolgáltatás meghibásodása miatti helyreállítás az állapot-nyilvántartó szolgáltatás típusától, a szolgáltatás replikáinak számától és a replikák számának mennyiségétől függ.

Egy állapot-nyilvántartó szolgáltatásban a bejövő adatértékek replikálódnak a replikák között (az elsődleges és az összes aktív formátumú másodlagos zónák). Ha a replikák többsége megkapja az adatmennyiséget, a rendszer az *adatkvórumot* véglegesíti. (Öt replika esetén a három kvórum lesz.) Ez azt jelenti, hogy minden pillanatban a replikák kvóruma lesz a legújabb adattal. Ha a replikák meghiúsulnak (azaz kettőből öt), a kvórum értéke alapján kiszámíthatja, hogy helyreállítható-e. (Mivel az öt replika közül a fennmaradó három továbbra is fennáll, garantált, hogy legalább egy replika teljes adattal fog rendelkezni.)

Ha a replikák kvóruma meghibásodik, a rendszer deklarálja, hogy a partíció *kvórum elvesztése* állapotban van. Tegyük fel, hogy egy partíciónak öt replikája van, ami azt jelenti, hogy legalább hármat garantál a teljes adatmennyiség. Ha a replikák kvóruma (három öt) meghiúsul, Service Fabric nem tudja megállapítani, hogy a fennmaradó replikák (két öt) elegendő adattal rendelkeznek-e a partíció visszaállításához. Azokban az esetekben, amikor a Service Fabric észleli a kvórum elvesztését, az alapértelmezett viselkedése, hogy megakadályozza a partíció további írásait, deklarálja a kvórum elvesztését, és várjon, amíg a replikák visszaállíthatók.

Annak megállapítása, hogy vészhelyzet történt-e egy állapot-nyilvántartó szolgáltatás esetében, majd a kezelése három szakaszt követ:

1. Annak megállapítása, hogy van-e kvórum elvesztése vagy sem.
   
   A kvórum elvesztése akkor van deklarálva, ha egy állapot-nyilvántartó szolgáltatás replikáinak többsége egy időben le van lebontva.
2. Annak megállapítása, hogy a kvórum elvesztése állandó-e.
   
   Az idő nagy részében a hibák átmenetiek. A folyamatok újraindulnak, a csomópontok újraindulnak, a virtuális gépek újraindulnak, és a hálózati partíciók gyógyulnak. Időnként azonban a hibák állandóak. Függetlenül attól, hogy a hibák állandóak-e, vagy sem, attól függ, hogy az állapot-nyilvántartó szolgáltatás megőrzi-e az állapotát, vagy csak a memóriában tartja: 
   
   - A megőrzött állapot nélküli szolgáltatások esetében a kvórum vagy a több replika meghibásodása _azonnal_ visszavezeti az állandó kvórum elvesztését. Ha a Service Fabric állapot-nyilvántartó nem állandó szolgáltatásban észleli a kvórum elvesztését, a (lehetséges) adatvesztés bejelentésével azonnal továbblép a 3. lépésre. Az adatvesztés elkerülése érdekében a Service Fabric tudja, hogy a replikák visszahívása nem vár rá. A szolgáltatás nem megőrzött jellegéből adódóan az adatok elvesznek, még akkor is, ha azok helyreállnak.
   - Az állapot-nyilvántartó állandó szolgáltatások esetében a kvórum vagy több replika meghibásodása miatt Service Fabric várnia kell, hogy a replikák visszatérjenek és visszaállítsa a kvórumot. Ennek eredményeképpen a szolgáltatás leállást eredményez a szolgáltatás érintett partíciójának (vagy a "replikakészlet-készletnek") _írásakor_ . Előfordulhat azonban, hogy az olvasások továbbra is lehetségesek a korlátozott konzisztencia-garanciákkal. Az az *időtartam, ameddig* Service Fabric megvárja a kvórum visszaállítását, mert a folytatás egy (lehetséges) adatvesztési esemény, és egyéb kockázatokat hordoz. Ez azt jelenti, hogy a Service Fabric nem folytassa a következő lépéssel, kivéve, ha a rendszergazda lépéseket tesz az adatvesztés kinyilvánítása érdekében.
3. Annak megállapítása, hogy az adatok elvesznek-e, és a biztonsági másolatokból való visszaállítás

   Ha a kvórum elvesztését deklarálták (akár automatikusan, akár rendszergazdai műveleten keresztül), Service Fabric és a szolgáltatások bekerülnek annak meghatározására, hogy az adatvesztés ténylegesen megszakadt-e. Ezen a ponton Service Fabric azt is tudja, hogy a többi replika nem jön vissza. Ez a döntés akkor történt, amikor a rendszer nem várta, hogy a kvórum elvesztését megszüntették. A szolgáltatásra vonatkozó legjobb művelet általában lefagy, és megvárja az adott rendszergazdai beavatkozást.
   
   Ha Service Fabric meghívja a `OnDataLossAsync` metódust, mindig a _feltételezett_ adatvesztés miatt. Service Fabric biztosítja, hogy a rendszer a hívást a _legjobb_ fennmaradó replikára továbbítsa. Ez az a replika, amely a legtöbb folyamatot elvégezte. 
   
   A _feltételezett_ adatvesztés oka az, hogy a fennmaradó replika állapota megegyezik az elsődlegesvel, amikor a kvórum elvész. Azonban anélkül, hogy ez az állapot össze legyen hasonlítva a szolgáltatással, nincs jó módszer arra, hogy a Service Fabric vagy az operátorok biztosan tudják.     
   
   Mi a módszer tipikus implementációja `OnDataLossAsync` ?
   1. A rendszer elindította a megvalósítási naplókat `OnDataLossAsync` , és kikapcsolja a szükséges rendszergazdai riasztásokat.
   1. A megvalósítás általában szünetel, és megvárja a további döntéseket és a manuális teendőket. Ennek az az oka, hogy akkor is elő kell készíteni a biztonsági mentéseket, ha vannak ilyenek. 
   
      Ha például két különböző szolgáltatás koordinálja az adatokat, előfordulhat, hogy ezeket a biztonsági másolatokat módosítani kell annak érdekében, hogy a visszaállítás után a két szolgáltatás által biztosított információk konzisztensek legyenek. 
   1. Gyakran van valamilyen más telemetria vagy kipufogó a szolgáltatásból. Lehetséges, hogy ez a metaadatok más szolgáltatásokban vagy naplókban találhatók. Ezek az információk igény szerint használhatók annak megállapításához, hogy az elsődlegesen nem voltak-e olyan hívások, amelyek nem voltak jelen a biztonsági mentésben vagy replikálva az adott replikára. Előfordulhat, hogy ezeket a hívásokat újra kell játszani, vagy a visszaállítás előtt hozzá kell adni a biztonsági mentéshez.  
   1. A megvalósítás összehasonlítja a fennmaradó replikát az összes elérhető biztonsági mentésben szereplő állapottal. Ha Service Fabric megbízható gyűjteményeket használ, elérhetők [eszközök és folyamatok](service-fabric-reliable-services-backup-restore.md) . A cél az, hogy a replika állapota elegendő-e, és hogy megtekinthető-e a biztonsági mentés.
   1. Az összehasonlítás elvégzése után és a visszaállítás befejeződése után (ha szükséges) a szolgáltatás kódjának **igaz** értéket kell visszaadnia, ha bármilyen változás történt. Ha a replika azt állapította meg, hogy az állapot a legjobb elérhető példánya, és nem történt módosítás, a kód **hamis** értéket ad vissza. 
   
      A **true** érték azt jelzi, hogy a _többi_ fennmaradó replika mostantól inkonzisztens lehet. A rendszer elveti és újraépíti ezt a replikát. A **false** érték azt jelzi, hogy nem történt változás az állapotban, így a többi replika is megtarthatja a kívánt értéket. 

Rendkívül fontos, hogy a szolgáltatás szerzője a szolgáltatások éles környezetben történő üzembe helyezése előtt a lehetséges adatvesztést és meghibásodási forgatókönyveket alkalmazza. Az adatvesztés elleni védelem érdekében fontos, hogy rendszeres időközönként [biztonsági mentést](service-fabric-reliable-services-backup-restore.md) végezzenek az állapot-nyilvántartó szolgáltatások állapotáról egy földrajzi redundáns tárolóba. 

Győződjön meg arról is, hogy van lehetőség az állapot visszaállítására. Mivel a különböző szolgáltatásokból származó biztonsági másolatok különböző időpontokban készülnek, meg kell győződnie arról, hogy a visszaállítást követően a szolgáltatások konzisztens nézettel rendelkeznek egymással kapcsolatban. 

Vegyünk például egy olyan helyzetet, amelyben az egyik szolgáltatás létrehoz egy számot, és tárolja azt, majd elküldi azt egy másik szolgáltatásnak, amely szintén tárolja azt. A visszaállítást követően előfordulhat, hogy a második szolgáltatás rendelkezik a számmal, de az első nem, mert a biztonsági másolata nem tartalmazza ezt a műveletet.

Ha azt állapítja meg, hogy a fennmaradó replikák nem elégségesek az adatvesztési forgatókönyvek folytatásához, és a szolgáltatás állapotát nem lehet telemetria vagy kipufogóból létrehozni, a biztonsági mentések gyakorisága határozza meg a lehető legjobb helyreállítási időpontot (RPO). Service Fabric számos eszközt biztosít a különböző meghibásodási forgatókönyvek teszteléséhez, beleértve az állandó kvórumot és az adatvesztést, amely biztonsági másolatból történő visszaállítást igényel. Ezek a forgatókönyvek a Service Fabric tesztelési eszközeinek részét képezik, amelyeket a hiba-elemzési szolgáltatás kezel. Ezekről az eszközökről és mintákról további információt [a hiba-elemzési szolgáltatás bemutatása](service-fabric-testability-overview.md)című témakörben talál. 

> [!NOTE]
> A rendszerszolgáltatások a kvórum elvesztését is elérhetik. A hatás a szóban forgó szolgáltatásra jellemző. Például az elnevezési szolgáltatás Kvórumának elvesztése befolyásolja a névfeloldást, míg a Feladatátvételi felügyelő szolgáltatásban a kvórum elvesztése blokkolja az új szolgáltatások létrehozását és feladatátvételét. 
> 
> A Service Fabric rendszerszolgáltatások ugyanazt a mintát követik, mint az állami felügyelettel kapcsolatos szolgáltatások, de nem javasoljuk, hogy próbálja meg áthelyezni a kvórum elvesztését és a lehetséges adatvesztést. Ehelyett azt javasoljuk, hogy  [kérjen támogatást](service-fabric-support.md) , hogy megoldást találjon a helyzetére. Általában csak a lefelé irányuló replikák visszaküldését érdemes várni.
>

#### <a name="troubleshooting-quorum-loss"></a>A kvórum hibáinak elhárítása

Előfordulhat, hogy a replikák átmeneti meghibásodás miatt időnként megszakadnak. Várjon egy ideig, amíg a Service Fabric megpróbálja felvenni őket. Ha a replikák a vártnál több ideig voltak leálltak, kövesse az alábbi hibaelhárítási műveleteket:
- Előfordulhat, hogy a replikák összeomlanak. A replika szintű állapotadatok és az alkalmazás naplófájljainak megtekintése. Gyűjtse össze az összeomlási memóriaképeket, és végezze el a szükséges műveleteket a helyreállításhoz.
- Lehet, hogy a replika folyamata nem válaszol. Ellenőrizze az alkalmazás naplóiban, hogy ennek ellenőrzéséhez. A folyamat-memóriaképek gyűjtése, majd a nem válaszoló folyamat leállítása. Service Fabric létre fog hozni egy helyettesítő folyamatot, és megkísérli a replika visszahelyezését.
- Előfordulhat, hogy a replikákat futtató csomópontok nem állnak le. Indítsa újra az alapul szolgáló virtuális gépet, hogy a csomópontok fel legyenek vezetve.

Esetenként előfordulhat, hogy nem lehet helyreállítani a replikákat. A meghajtók például sikertelenek voltak, vagy a gépek fizikailag nem válaszolnak. Ezekben az esetekben az Service Fabricnak nem kell várnia a replika helyreállítására.

Ne *használja ezeket* a módszereket, ha az esetleges adatvesztés elfogadhatatlan a szolgáltatás online állapotba helyezéséhez. Ebben az esetben minden erőfeszítést el kell végezni a fizikai gépek helyreállítása felé.

Az alábbi műveletek adatvesztést okozhatnak. A követés előtt tekintse meg a következőt:.
   
> [!NOTE]
> Ezeket a metódusokat _soha nem_ lehet biztonságos módon használni, mint az adott partíciók esetében. 
>

- Használja a `Repair-ServiceFabricPartition -PartitionId` vagy az API-t `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` . Ez az API lehetővé teszi a partíció AZONOSÍTÓjának megadását, hogy kilépjen a kvórum elvesztésével és a lehetséges adatvesztéssel.
- Ha a fürt olyan gyakori hibákba ütközik, amelyek következtében a szolgáltatások kvórum elvesztése állapotba kerülnek, és a lehetséges _adatvesztés elfogadható_, a megfelelő [QuorumLossWaitDuration](/powershell/module/servicefabric/update-servicefabricservice) érték megadásával a szolgáltatás automatikusan helyreállítható. A helyreállítás végrehajtása előtt Service Fabric várnia kell a megadott `QuorumLossWaitDuration` értéket (az alapértelmezett érték a végtelen). Ez a módszer *nem* ajánlott, mert váratlan adatvesztést okozhat.

## <a name="availability-of-the-service-fabric-cluster"></a>A Service Fabric-fürt rendelkezésre állása
Általánosságban elmondható, hogy a Service Fabric-fürt egy olyan, szigorúan elosztott környezet, amely egyetlen meghibásodási pontot sem okoz. Egy csomópont meghibásodása nem eredményezi a fürt rendelkezésre állási és megbízhatósági problémáit, elsősorban azért, mert a Service Fabric rendszerszolgáltatások a korábban megadott irányelvek szerint követik egymást. Azaz mindig három vagy több replikával futnak alapértelmezés szerint, és a rendszerszolgáltatások, amelyek állapot nélküliek futnak az összes csomóponton. 

Az alapul szolgáló Service Fabric hálózatkezelési és meghibásodási észlelési rétegek teljes mértékben el vannak osztva. A legtöbb rendszerszolgáltatás újraépíthető a fürt metaadataiból, vagy megtudhatja, hogyan szinkronizálja az állapotát más helyekről. A fürt rendelkezésre állása veszélybe kerülhet, ha a rendszerszolgáltatások a korábban leírtaknak megfelelő kvórum-vesztési helyzetbe kerülnek. Ezekben az esetekben előfordulhat, hogy nem tud bizonyos műveleteket végrehajtani a fürtön (például a frissítés megkezdéséhez vagy új szolgáltatások üzembe helyezéséhez), de maga a fürt is. 

A futó fürtön futó szolgáltatások továbbra is futnak ezekben a feltételekben, kivéve, ha a rendszerszolgáltatások működésének folytatásához írásra van szükségük. Ha például Feladatátvételi felügyelő a kvórum elvesztésekor, az összes szolgáltatás továbbra is futni fog. A sikertelenül működő szolgáltatások azonban nem lesznek automatikusan újraindulni, mivel ez Feladatátvételi felügyelő bevonását igényli. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Adatközpont vagy Azure-régió meghibásodása
Ritka esetekben előfordulhat, hogy egy fizikai adatközpont átmenetileg elérhetetlenné válik az áramellátás vagy a hálózati kapcsolat elvesztése miatt. Ezekben az esetekben az adott adatközpontban vagy az Azure-régióban található Service Fabric-fürtök és-szolgáltatások elérhetetlenné válnak. _Az adatai azonban megmaradnak_. 

Az Azure-ban futó fürtök esetében az [Azure állapot lapján][azure-status-dashboard]megtekintheti az kimaradások frissítéseit. Abban a nagyon valószínűtlen esetben, ha egy fizikai adatközpont részben vagy teljesen megsemmisül, az ott üzemeltetett Service Fabric-fürtök vagy a bennük található szolgáltatások elvesznek. Ez a veszteség magában foglalja az adatközponton vagy régióban kívülről nem készített biztonsági mentés állapotát.

Két különböző stratégia áll rendelkezésre egy adott adatközpont vagy régió állandó vagy tartós meghibásodásának megtúléléséhez: 

- Különálló Service Fabric-fürtöket futtathat több ilyen régióban, és a feladatátvétel és a feladat-visszavétel egyes mechanizmusait felhasználhatja ezen környezetek között. Ez a több fürt aktív/aktív vagy aktív/passzív modellje további felügyeleti és műveleti kódokat igényel. Ehhez a modellhez az egyik adatközpontban vagy régióban lévő szolgáltatásokból származó biztonsági másolatok koordinálására is szükség van, hogy azok más adatközpontokban vagy régiókban is elérhetők legyenek, ha az egyik sikertelen. 
- Futtasson egyetlen Service Fabric-fürtöt, amely több adatközpontra vagy régióra terjed ki. A stratégia minimálisan támogatott konfigurációja három adatközpont vagy régió. A régiók vagy adatközpontok ajánlott száma öt. 
  
  Ehhez a modellhez összetettebb fürtözött topológia szükséges. Az előny azonban az, hogy az egyik adatközpont vagy régió meghibásodása egy vészhelyzetből normális hiba miatt alakul. Ezeket a hibákat olyan mechanizmusok tudják kezelni, amelyek egy adott régióban lévő fürtökhöz működnek. A tartalék tartományok, a frissítési tartományok és a Service Fabric elhelyezési szabályok biztosítják, hogy a munkaterhelések szét legyenek terjesztve, hogy a normál hibákat eltűrik. 
  
  További információ azokról a házirendekről, amelyek segíthetnek az ilyen típusú fürtökön belüli szolgáltatások üzemeltetésében: [Service Fabric Services elhelyezési házirendjei](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>A fürtök meghibásodását eredményező véletlenszerű hibák
Service Fabric a *vetőmag-csomópontok* fogalmával rendelkezik. Ezek olyan csomópontok, amelyek megőrzik a mögöttes fürt rendelkezésre állását. 

A vetőmag-csomópontok segítségével biztosítható, hogy a fürt a többi csomóponttal való bérletek létrehozásával, valamint bizonyos típusú hibák esetén tiebreakers szolgáljon. Ha a véletlenszerű hibák elveszik a fürtben található vetőmag-csomópontok többségét, és azokat nem gyorsan hozzák vissza, a fürt automatikusan leáll. A fürt ezután sikertelen lesz. 

Az Azure-ban Service Fabric erőforrás-szolgáltató kezeli Service Fabric fürt konfigurációját. Alapértelmezés szerint az erőforrás-szolgáltató kiosztja a magok csomópontját a hibák és a frissítési tartományok között az *elsődleges csomópont típusaként*. Ha az elsődleges csomópont típusa ezüst vagy arany tartósságként van megjelölve, akkor ha eltávolít egy vetőmag-csomópontot (az elsődleges csomópont típusának skálázásával vagy a manuális eltávolításával), a fürt megpróbál előléptetni egy másik, nem magot tartalmazó csomópontot az elsődleges csomópont típusának rendelkezésre álló kapacitásával. Ez a kísérlet sikertelen lesz, ha a fürt megbízhatósági szintjénél kevesebb kapacitás áll rendelkezésre, mint az elsődleges csomópont típusa.

Az önálló Service Fabric-fürtökben és az Azure-ban az elsődleges csomópont típusa a magok futtatásának egyike. Ha elsődleges csomópont-típust határoz meg, Service Fabric automatikusan kihasználja a csomópontok számát, amelyet akár kilenc vetőmag-csomópont létrehozásával, mind a rendszerszolgáltatások hét replikájának létrehozásával biztosít. Ha a véletlenszerű hibák egy halmaza egyidejűleg a replikák többségét kiveszi, a rendszerszolgáltatások kvórum elvesztését fogják megadni. Ha a vetőmag-csomópontok többsége elveszik, akkor a fürt hamarosan leáll.

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan szimulálhatja a különböző hibákat a [tesztelési keretrendszer](service-fabric-testability-overview.md)használatával.
- További katasztrófa-helyreállítási és magas rendelkezésre állású erőforrások olvasása. A Microsoft nagy mennyiségű útmutatót tett közzé ezekkel a témakörökkel kapcsolatban. Bár ezek az erőforrások bizonyos, más termékekben használt technikákra vonatkoznak, számos általános ajánlott eljárást tartalmaznak, amelyeket a Service Fabric kontextusban alkalmazhat:
  - [Rendelkezésre állási ellenőrzőlista](/azure/architecture/checklist/resiliency-per-service)
  - [Vész-helyreállítási részletezés végrehajtása](../azure-sql/database/disaster-recovery-drills.md)
  - [Vészhelyreállítás és magas szintű rendelkezésre állás az Azure-alkalmazásokhoz][dr-ha-guide]
- További információ a [Service Fabric támogatási lehetőségeiről](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: /windows/win32/perfctrs/specifying-a-counter-path
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: /previous-versions/azure/dn251004(v=azure.100)


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
