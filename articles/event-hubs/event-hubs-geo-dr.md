---
title: Geo-vészhelyreállítás – Azure Event Hubs| Microsoft Docs
description: Földrajzi régiók használata feladatátvételhez és vészhelyreállítás végrehajtásához a Azure Event Hubs
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: b2cf2b0ebef2b460b626e45d6b52309c9281d6ce
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739241"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs – Geo-vészhelyreállítás 

Az adatfeldolgozási erőforrások katasztrofális kimaradásai elleni rugalmasság számos vállalat számára követelmény, és bizonyos esetekben az iparági szabályozások is megkövetelik. 

Azure Event Hubs már az egyes gépek vagy akár teljes állványok katasztrofális meghibásodásának kockázatát is elterjedi az adatközponton belül több hibatartományt is átvevő fürtök között, és transzparens hibaészlelési és feladatátvételi mechanizmusokat valósít meg, amelyek révén a szolgáltatás továbbra is a garantált szolgáltatási szinteken belül fog működni, és általában nem jár észrevehető megszakításokkal ilyen meghibásodások esetén. Ha egy Event Hubs-névtér a rendelkezésre állási [](../availability-zones/az-overview.md)zónák engedélyezett beállításával lett létrehozva, a szolgáltatáskimaradási kockázat tovább terjed három fizikailag elkülönített létesítmény között, és a szolgáltatás elegendő kapacitással rendelkezik ahhoz, hogy azonnal kezelni tudja a teljes létesítmény teljes, katasztrofális elvesztését. 

A teljes Azure Event Hubs fürtmodell rendelkezésre állási zónák támogatásával rugalmasságot biztosít a hardverhibák és akár a teljes adatközpont-létesítmények katasztrofális elvesztése esetén is. Mégis előfordulhatnak olyan széles körű fizikai megsemmisítéssel kapcsolatos helyzetek, amelyek ellen még ezek a intézkedések sem tudnak megfelelően védekezni. 

Az Event Hubs geo-vészhelyreállítási szolgáltatás úgy lett kialakítva, hogy megkönnyítsen egy ilyen nagyságrendű katasztrófa utáni helyreállítást, és ne hagyjon fel egy hibás Azure-régiót jó használatra és anélkül, hogy módosítania kell az alkalmazáskonfigurációkat. Egy Azure-régió elhagyása általában több szolgáltatást foglal magában, és ez a funkció elsősorban az összetett alkalmazáskonfiguráció integritásának megőrzését segíti.  

Az Geo-Disaster helyreállítási szolgáltatás biztosítja, hogy egy névtér (Event Hubs, fogyasztói csoportok és beállítások) teljes konfigurációja folyamatosan replikálva lesz egy elsődleges névtérből egy másodlagos névtérbe, ha párosítva van, és lehetővé teszi, hogy bármikor kezdeményezzen csak egyszer feladatátvételt az elsődlegesről a másodlagosra. A feladatátvételi áthelyezés a névtér kiválasztott aliasnevét a másodlagos névtérre fogja átpontosni, majd megszakítja a párosítást. A feladatátvétel szinte azonnal megkezdődött. 

> [!IMPORTANT]
> A funkció lehetővé teszi a műveletek azonnali folytonosságát ugyanazokkal a konfigurációval, de nem replikálja az **eseményadatokat.** Hacsak a katasztrófa nem okozta az összes zóna elvesztését, az elsődleges eseményközpontban a feladatátvételt követően megőrzött eseményadatok helyreállíthatók, és a korábbi események onnan szerezhetők be a hozzáférés visszaállítása után. Az eseményadatok replikálása és a megfelelő névterek aktív/aktív konfigurációkban való üzemeltetése érdekében a kimaradások és katasztrófák esetén [](event-hubs-federation-overview.md)ne támaszkodjon erre a geo-vészhelyreállítási szolgáltatáskészletre, hanem kövesse a replikációs útmutatót.  

## <a name="outages-and-disasters"></a>Kimaradások és katasztrófák

Fontos megjegyezni a "kimaradások" és a "katasztrófák" közötti különbséget. A **szolgáltatáskimaradás** a szolgáltatás Azure Event Hubs elérhetetlensége, és hatással lehet a szolgáltatás egyes összetevőire, például egy üzenetkezelési tárolóra vagy akár a teljes adatközpontra is. A probléma megoldása után azonban a Event Hubs újra elérhetővé válik. A kimaradás általában nem okoz üzenetek vagy más adatok elvesztését. Ilyen kimaradás lehet például az adatközpont áramkimaradása. Bizonyos kimaradások átmeneti vagy hálózati problémák miatt csak rövid kapcsolatvesztést eredményeznek. 

A *katasztrófa* egy felhőbeli fürt, Azure-régió vagy adatközpont állandó vagy hosszabb távú Event Hubs elvesztése. Előfordulhat, hogy a régió vagy adatközpont ismét elérhetővé válik, vagy órákig vagy napokig nem érhető el. Ilyen katasztrófák például a tűz, az elárasztás vagy a földrengés. Az állandóvá válik katasztrófa egyes üzenetek, események vagy egyéb adatok elvesztését okozhatja. A legtöbb esetben azonban nem szabad adatvesztést bevetni, és az üzenetek helyreállíthatóak, amint az adatközpontról biztonsági adatokat küld.

A vészhelyreállítás geo-vészhelyreállítási funkciója Azure Event Hubs vészhelyreállítási megoldás. A cikkben ismertetett fogalmak és munkafolyamatok a katasztrófahelyzetek, és nem az átmeneti vagy ideiglenes kimaradások esetén érvényesek. A vészhelyreállítás részletes leírását a Microsoft Azure ebben [a cikkben talál.](/azure/architecture/resiliency/disaster-recovery-azure-applications)

## <a name="basic-concepts-and-terms"></a>Alapfogalmak és kifejezések

A vészhelyreállítási funkció a metaadatok vészhelyreállítását valósítja meg, és elsődleges és másodlagos vészhelyreállítási névtereket alkalmaz. 

A geo-vészhelyreállítási funkció csak a [standard és dedikált SKUs-hez érhető el.](https://azure.microsoft.com/pricing/details/event-hubs/) A kapcsolati sztringet nem kell változtatnia, mivel a kapcsolat egy aliason keresztül valósul meg.

Ebben a cikkben a következő kifejezéseket használjuk:

-  *Alias:* A beállított vészhelyreállítási konfiguráció neve. Az alias egyetlen stabil teljes tartománynév (FQDN) kapcsolati sztringet biztosít. Az alkalmazások ezt az alias kapcsolati sztringet használják a névtérhez való csatlakozáshoz. 

-  *Elsődleges/másodlagos névtér:* Az aliasnak megfelelő névterek. Az elsődleges névtér "aktív" és üzeneteket fogad (lehet meglévő vagy új névtér is). A másodlagos névtér "passzív", és nem fogad üzeneteket. A két metaadat szinkronban van, így mindkettő zökkenőmentesen fogadhat üzeneteket az alkalmazáskód vagy a kapcsolati sztring változtatása nélkül. Annak biztosításához, hogy csak az aktív névtér fogad üzeneteket, az aliast kell használnia.
-  *Metaadatok:* Entitások, például eseményközpontok és fogyasztói csoportok; és a névtérhez társított szolgáltatás tulajdonságai. A rendszer csak az entitásokat és azok beállításait replikálja automatikusan. Az üzenetek és események nem replikálódnak. 
-  *Feladatátvétel:* A másodlagos névtér aktiválásának folyamata.

## <a name="supported-namespace-pairs"></a>Támogatott névtérpárok
Az elsődleges és másodlagos névterek következő kombinációi támogatottak:  

| Elsődleges névtér | Másodlagos névtér | Támogatott | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Yes | 
| Standard | Dedikált | Yes | 
| Dedikált | Dedikált | Yes | 
| Dedikált | Standard | No | 

> [!NOTE]
> Azonos dedikált fürtben nem párosíthat névtereket. A különálló fürtökben található névtereket párosíthatja. 

## <a name="setup-and-failover-flow"></a>Beállítási és feladatátvételi folyamat

A következő szakasz áttekintést nyújt a feladatátvételi folyamatról, és ismerteti a kezdeti feladatátvétel beállításának folyamatát. 

![1][]

### <a name="setup"></a>Beállítás

Először hozzon létre vagy használjon egy meglévő elsődleges névteret és egy új másodlagos névteret, majd párosítsa a kettőt. Ez a párosítás egy aliast biztosít, amely a csatlakozáshoz használható. Mivel aliast használ, nem kell módosítania a kapcsolati sztringeket. A feladatátvételi párosításhoz csak új névterek hozzáadhatóak. 

1. Hozza létre az elsődleges névteret.
1. Hozza létre a másodlagos névteret egy másik régióban. Ez a lépés nem kötelező. A következő lépésben létrehozhatja a másodlagos névteret a párosítás létrehozása során. 
1. A Azure Portal keresse meg az elsődleges névteret.
1. A bal oldali menüben válassza a **Geo-helyreállítás,** majd az **eszköztár Párosítás kezdeményezése** parancsát. 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Párosítás kezdeményezése az elsődleges névtérből":::    
1. A Párosítás **kezdeményezése lapon** kövesse az alábbi lépéseket:
    1. Válasszon ki egy meglévő másodlagos névteret, vagy hozzon létre egyet egy másik régióban. Ebben a példában egy meglévő névtér van kiválasztva.  
    1. Az **Alias névhez** adjon meg egy aliast a geo-dr párosításhoz. 
    1. Ezután kattintson a **Létrehozás** elemre. 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="A másodlagos névtér kiválasztása":::        
1. Meg kell jelenni a **Geo-DR aliaslapon.** Az elsődleges névtérről is navigálhat erre az oldalra a bal oldali menü **Geo-recovery** elemének kiválasztásával.

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Geo-DR aliaslap":::    
1. A **Geo-DR alias** oldalán  válassza a bal oldali menü Megosztott hozzáférési szabályzatok elemét az alias elsődleges kapcsolati sztringjének eléréséhez. Ezt a kapcsolati sztringet használja ahelyett, hogy közvetlenül az elsődleges/másodlagos névtérhez használta volna a kapcsolati sztringet. 
1. Ezen az **Áttekintés lapon** a következő műveleteket használhatja: 
    1. Az elsődleges és a másodlagos névtér párosításának megszakítása. Válassza **a Párosítás törése** lehetőséget az eszköztáron. 
    1. Manuális feladatátvétel a másodlagos névtérre. Válassza **az eszköztár** Feladatátvétel gombját. 
    
        > [!WARNING]
        > A feladat-visszahelyezés aktiválja a másodlagos névteret, és eltávolítja az elsődleges névteret a Geo-Disaster Recovery párosításból. Hozzon létre egy másik névteret egy új geo-vészhelyreállítási pár létrehozásához. 

Végül monitorozást kell hozzáadnia annak észlelésére, hogy szükség van-e feladatátvételre. A legtöbb esetben a szolgáltatás egy nagy ökoszisztéma része, ezért az automatikus feladatátvétel ritkán lehetséges, mivel a feladatátvételt gyakran a fennmaradó alrendszerrel vagy infrastruktúrával szinkronban kell elvégezni.

### <a name="example"></a>Példa

Példaként tekintsünk meg egy POS-megoldást, amely üzeneteket vagy eseményeket bocsát ki. Event Hubs ezeket az eseményeket egy leképezési vagy újraformatikus megoldásnak továbbítja, amely ezután továbbítja a leképezett adatokat egy másik rendszernek további feldolgozásra. Ezen a ponton az összes rendszer üzemeltetve lehet ugyanabban az Azure-régióban. A feladatátvétel mikor és melyik részével kapcsolatos döntés az infrastruktúra adatáramlásától függ. 

A feladatátvételt automatizálhatja monitorozási rendszerekkel vagy egyénileg létrehozott monitorozási megoldásokkal. Az ilyen automatizálás azonban további tervezést és munkát vesz igénybe, amely a jelen cikk hatókörét nem terjed ki.

### <a name="failover-flow"></a>Feladatátvételi folyamat

Ha elindítja a feladatátvételt, két lépésre van szükség:

1. Ha egy másik kimaradás lép fel, szeretné újra a feladatátvételt. Ezért állítson be egy másik passzív névteret, és frissítse a párosítást. 

2. Miután ismét elérhetővé válik, lekérhetők az üzenetek a korábbi elsődleges névtérből. Ezt követően használja ezt a névteret normál üzenetküldéshez a geohelyreállítási beállításon kívül, vagy törölje a régi elsődleges névteret.

> [!NOTE]
> Csak a feladatátvételi szemantika támogatott. Ebben a forgatókönyvben feladatátvételt fog, majd újra párosítja egy új névtérrel. A vissza- és visszaeső nem támogatott; például egy SQL-fürtben. 

![2][]

## <a name="management"></a>Kezelés

Ha hibát vétett; Ha például a kezdeti beállítás során rossz régiókat párosított, bármikor megszakíthatja a két névtér párosítását. Ha a párosított névtereket normál névtérként szeretné használni, törölje az aliast.

## <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe az alábbi szempontokat:

1. A geo-Event Hubs helyreállítása nem replikálja az adatokat, ezért nem használhatja újra az elsődleges eseményközpont régi eltolási értékét a másodlagos eseményközpontban. Javasoljuk, hogy indítsa újra az esemény fogadót az alábbi módszerek egyikével:

   - *EventPosition.FromStart() –* Ha a másodlagos eseményközpont összes adatát be szeretné olvasni.
   - *EventPosition.FromEnd() –* Ha az összes új adatot be szeretné olvasni a másodlagos eseményközponttal való kapcsolatból.
   - *EventPosition.FromEnqueuedTime(dateTime)* – Ha egy adott dátumtól és időponttól szeretné beolvasni a másodlagos eseményközpontba fogadott összes adatot.

2. A feladatátvétel tervezése során figyelembe kell vennie az időtényezőt is. Ha például 15–20 percnél hosszabb ideig megszakad a kapcsolat, dönthet úgy, hogy kezdeményezi a feladatátvételt. 
 
3. Az, hogy nincs replikálva adat, azt jelenti, hogy az aktuális aktív munkamenetek nem replikálódnak. Emellett előfordulhat, hogy a duplikált üzenetek észlelése és az ütemezett üzenetek nem működnek. Működni fognak az új munkamenetek, az ütemezett üzenetek és az új ismétlődések. 

4. Egy összetett elosztott infrastruktúra feladat-átveszi legalább egyszer. [](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) 

5. Az entitások szinkronizálása némi időt, körülbelül 50–100 entitást is igénybe vehet percenként.

## <a name="availability-zones"></a>Rendelkezésre állási zónák 

A Event Hubs standard termékváltozat támogatja [a Availability Zones,](../availability-zones/az-overview.md)így hiba elkülönített helyeket biztosít egy Azure-régión belül. 

> [!NOTE]
> A Availability Zones Standard Azure Event Hubs csak olyan [Azure-régiókban](../availability-zones/az-region.md) érhető el, ahol rendelkezésre állási zónák vannak.

Az új Availability Zones csak az új névterek számára engedélyezheti a Azure Portal. Event Hubs nem támogatja a meglévő névterek migrálását. Miután engedélyezte a zónaredundaniát a névtérben, nem tilthatja le.

A rendelkezésre állási zónák használata során a rendszer a metaadatokat és az adatokat (eseményeket) is replikálja a rendelkezésre állási zónában lévő adatközpontok között. 

![3][]

## <a name="private-endpoints"></a>Privát végpontok
Ez a szakasz további szempontokat tartalmaz a geo-vészhelyreállítás privát végpontokat használó névterekkel való használata esetén. A privát végpontok és a Event Hubs használatával kapcsolatos további információkért lásd: [Privát végpontok konfigurálása.](private-link-service.md)

### <a name="new-pairings"></a>Új párosítások
Ha egy elsődleges névtér és egy privát végpont nélküli másodlagos névtér között próbál párosítást létrehozni, a párosítás sikertelen lesz. A párosítás csak akkor lesz sikeres, ha az elsődleges és a másodlagos névtérnek is vannak privát végpontjai. Javasoljuk, hogy az elsődleges és másodlagos névterek, valamint a privát végpontokat létrehozó virtuális hálózatok esetében is ugyanazt a konfigurációt használja.  

> [!NOTE]
> Amikor megpróbálja párosítani az elsődleges névteret a privát végponttal és egy másodlagos névtérvel, az ellenőrzési folyamat csak azt ellenőrzi, hogy létezik-e privát végpont a másodlagos névtérben. Nem ellenőrzi, hogy a végpont működik-e vagy működik-e a feladatátvétel után. Az Ön felelőssége, hogy a privát végponttal bíró másodlagos névtér a várt módon működjön a feladatátvételt követően.
>
> Annak teszteléséhez, hogy a privát végpontok konfigurációi azonosak-e az elsődleges és másodlagos névterek esetén, küldjön olvasási kérést (például [Eseményközpont](/rest/api/eventhub/get-event-hub)lekérése) a másodlagos névtérre a virtuális hálózaton kívülről, és ellenőrizze, hogy a szolgáltatástól kapott-e hibaüzenetet.

### <a name="existing-pairings"></a>Meglévő párosítások
Ha már létezik párosítás az elsődleges és a másodlagos névtér között, a privát végpont létrehozása az elsődleges névtérben sikertelen lesz. A probléma megoldásához először hozzon létre egy privát végpontot a másodlagos névtéren, majd hozzon létre egyet az elsődleges névtérhez.

> [!NOTE]
> Bár a másodlagos névtérhez csak olvasási hozzáférést engedélyezünk, a privát végpont konfigurációjának frissítései engedélyezettek. 

### <a name="recommended-configuration"></a>Ajánlott konfiguráció
Amikor vészhelyreállítási konfigurációt hoz létre az alkalmazáshoz és egy Event Hubs-névtérhez, privát végpontokat kell létrehoznia az elsődleges és a másodlagos Event Hubs-névterek számára is az alkalmazás elsődleges és másodlagos példányait üzemeltető virtuális hálózatokon. 

Tegyük fel, hogy két virtuális hálózata van: VNET-1, VNET-2 és ezek az elsődleges és másodlagos névterek: EventHubs-Namespace1-Primary, EventHubs-Namespace2-Secondary. A következő lépéseket kell megtennie: 

- Az EventHubs-Namespace1-Primary virtuális hálózatban hozzon létre két privát végpontot, amelyek a VNET-1 és a VNET-2 alhálózatát használják
- Az EventHubs-Namespace2-Secondary virtuális hálózatban hozzon létre két privát végpontot, amelyek ugyanazt az alhálózatot használják a VNET-1 és a VNET-2 hálózatból 

![Privát végpontok és virtuális hálózatok](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

Ennek a megközelítésnek az az előnye, hogy a feladatátvétel a névtértől független alkalmazásrétegben Event Hubs történhet. Vegyük példaként a következő forgatókönyveket: 

**Csak alkalmazásra vonatkozó feladatátvétel:** Itt az alkalmazás nem létezik a VNET-1-ben, hanem át lesz áthelyezve a VNET-2 hálózatra. Mivel mindkét privát végpont a VNET-1 és a VNET-2 virtuális hálózatban is konfigurálva van mind az elsődleges, mind a másodlagos névterek számára, az alkalmazás csak működni fog. 

**Event Hubs** feladatátvétel: Itt is, mivel mindkét privát végpont az elsődleges és a másodlagos névtér virtuális hálózatán is konfigurálva van, az alkalmazás egyszerűen működni fog. 

> [!NOTE]
> A virtuális hálózatok geo-vészhelyreállításával kapcsolatos útmutatásért lásd: Virtual Network [üzletmenet-folytonosság.](../virtual-network/virtual-network-disaster-recovery-guidance.md)
 
## <a name="next-steps"></a>Következő lépések
Tekintse át az alábbi mintákat vagy referenciadokumentációkat. 
- [.NET GeoDR-minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/DotNet/GeoDRClient) 
- [Java GeoDR-minta](https://github.com/Azure-Samples/eventhub-java-manage-event-hub-geo-disaster-recovery)
- [.NET – Azure.Messaging.EventHubs-minták](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [.NET – Microsoft.Azure.EventHubs-minták](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet)
- [Java – azure-messaging-eventhubs-minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Java – azure-eventhubs-minták](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java)
- [Python-példák](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
- [JavaScript-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
- [REST API-referencia](/rest/api/eventhub/)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
