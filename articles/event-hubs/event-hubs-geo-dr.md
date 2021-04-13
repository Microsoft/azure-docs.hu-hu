---
title: Földrajzi katasztrófa-helyreállítás – Azure Event Hubs | Microsoft Docs
description: A földrajzi régiók használata a feladatátvételhez és a vész-helyreállítási műveletek végrehajtásához az Azure-ban Event Hubs
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: 091c6c61b079ceb8f96f04e62fb772d91732eb2f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311209"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs – geo-vész-helyreállítás 

Az adatfeldolgozási erőforrások katasztrofális leállása elleni rugalmasság számos vállalat számára szükséges, és bizonyos esetekben az iparági szabályozásoknak is meg kell követelniük. 

Az Azure Event Hubs már az egyes gépek katasztrofális meghibásodásának kockázatát, vagy akár az adatközponton belüli több meghibásodási tartományra kiterjedő fürtöket is végrehajtja, és olyan átlátható hibák észlelését és feladatátvételi mechanizmusokat valósít meg, amelyekkel a szolgáltatás továbbra is a biztos szolgáltatási szinten fog működni, és jellemzően az ilyen hibák esetén általában észrevehető megszakítások nélkül. Ha egy Event Hubs névtér lett létrehozva a [rendelkezésre állási zónák](../availability-zones/az-overview.md)számára engedélyezett beállítással, a kiesési kockázat tovább terjed három, fizikailag elkülönített létesítmény között, és a szolgáltatás elegendő kapacitási tartalékokkal rendelkezik, hogy azonnal megbirkózzanak a teljes létesítmény teljes, katasztrofális elvesztésével. 

A rendelkezésre állási zónák támogatásával rendelkező összes aktív Azure Event Hubs-fürt a súlyos hardveres hibákkal szembeni rugalmasságot és a teljes adatközpont-létesítmények katasztrofális elvesztését is biztosítja. Mégis előfordulhat, hogy súlyos helyzetek merülnek fel a fizikai megsemmisüléssel szemben, hogy még ezek az intézkedések is nem megfelelő védelmet biztosítanak. 

A Event Hubs geo-vész-helyreállítási funkció úgy lett kialakítva, hogy könnyebben helyreállítható legyen a nagy mennyiségű katasztrófa miatt, és a hibás Azure-régiót kihagyhatja a megfelelő, és nem kell módosítania az alkalmazás konfigurációját. Az Azure-régiók felhagyása jellemzően számos szolgáltatást foglal magában, és ez a funkció elsősorban az összetett alkalmazások konfigurációjának integritásának biztosítását célozza meg.  

A Geo-Disaster helyreállítási funkciója biztosítja, hogy a rendszer a névtér (Event Hubs, fogyasztói csoportok és beállítások) teljes konfigurációját folyamatosan replikálja egy elsődleges névtérről egy másodlagos névtérre, ha párosítva van, és lehetővé teszi, hogy a csak egyszer használható feladatátvételt kezdeményezzen az elsődlegesről a másodlagosra, bármikor. A feladatátvételi áthelyezés a névtér kiválasztott aliasának nevét a másodlagos névtérre irányítja át, majd megszakítja a párosítást. A feladatátvétel majdnem azonnal megkezdődik. 

> [!IMPORTANT]
> A szolgáltatás lehetővé teszi, hogy a műveletek pillanatnyi folytonossága ugyanazzal a konfigurációval történjen, de **az esemény-adatok replikálását nem**. Kivéve, ha a katasztrófa az összes zóna elvesztését okozta, a feladatátvétel után az elsődleges esemény központjában megőrzött esemény-adatok helyreállítható, és a korábbi események a hozzáférés visszaállítása után is beszerezhetők. Az események replikálása és a megfelelő névterek aktív/aktív konfigurációkban való üzemeltetése az kimaradások és a katasztrófák megbirkózása érdekében ne támaszkodjon erre a földrajzi katasztrófa utáni helyreállítási szolgáltatásra, de kövesse a [replikálási útmutatót](event-hubs-federation-overview.md).  

## <a name="outages-and-disasters"></a>Kimaradások és katasztrófák

Fontos megjegyezni az "kimaradások" és a "katasztrófák" közötti különbséget. A **leállás** az Azure Event Hubs ideiglenes nem érhető el, és hatással lehet a szolgáltatás egyes összetevőire, például az üzenetküldési tárolóra vagy akár a teljes adatközpontra is. A probléma javítása után azonban Event Hubs újra elérhetővé válik. A leállás általában nem okoz üzenetet vagy más adatvesztést. Ilyen kimaradás például áramkimaradás lehet az adatközpontban. Bizonyos kimaradások átmeneti vagy hálózati problémák miatt csak rövid kapcsolatok elvesztését okozják. 

A *katasztrófa* a Event Hubs-fürt, az Azure-régió vagy az adatközpont állandó vagy hosszú távú elvesztéseként van meghatározva. Előfordulhat, hogy a régió vagy az adatközpont újra elérhetővé válik, vagy akár órákig, akár napokig nem. Ilyen katasztrófák például a tűz, az árvíz vagy a földrengés. Az állandó vészhelyzet miatt előfordulhat, hogy bizonyos üzenetek, események vagy egyéb adatvesztést okoznak. Azonban a legtöbb esetben nem lehet adatvesztés, és az adatközpont biztonsági mentése után az üzenetek nem állíthatók helyre.

Az Azure Event Hubs földrajzi katasztrófa utáni helyreállítási funkciója vész-helyreállítási megoldás. Az ebben a cikkben ismertetett fogalmak és munkafolyamatok a katasztrófa-forgatókönyvekre, és nem átmeneti vagy átmeneti kimaradásokra vonatkoznak. A Microsoft Azure vész-helyreállítási részletes megvitatását [ebben a cikkben](/azure/architecture/resiliency/disaster-recovery-azure-applications)találja.

## <a name="basic-concepts-and-terms"></a>Alapvető fogalmak és kifejezések

A vész-helyreállítási funkció a metaadatok vész-helyreállítását valósítja meg, és az elsődleges és másodlagos vész-helyreállítási névterekre támaszkodik. 

A földrajzi katasztrófa utáni helyreállítási funkció csak a [standard és a dedikált SKU](https://azure.microsoft.com/pricing/details/event-hubs/) esetében érhető el. Nem kell módosítania a kapcsolódási karakterláncot, mert a kapcsolatok aliason keresztül történnek.

A cikk a következő kifejezéseket használja:

-  *Alias*: az Ön által beállított vész-helyreállítási konfiguráció neve. Az alias egyetlen stabil teljes tartománynevet (FQDN) tartalmazó adatkarakterláncot biztosít. Az alkalmazások ezt az alias kapcsolati karakterláncot használják a névtérhez való kapcsolódáshoz. 

-  *Elsődleges/másodlagos névtér*: az aliasnak megfelelő névterek. Az elsődleges névtér "aktív", és fogadja az üzeneteket (lehet meglévő vagy új névtér is). A másodlagos névtér "passzív", és nem fogad üzeneteket. A kettő közötti metaadatok szinkronban vannak, így mindkét alkalmazás kód vagy kapcsolati karakterlánc módosítása nélkül is zökkenőmentesen fogadhat üzeneteket. Annak biztosítása érdekében, hogy csak az aktív névtér kapjon üzeneteket, az aliast kell használnia.
-  *Metaadatok*: olyan entitások, mint az Event hubok és a fogyasztói csoportok; a névtérhez társított szolgáltatás tulajdonságai. A rendszer csak az entitásokat és azok beállításait replikálja automatikusan. Az üzenetek és események nem replikálódnak. 
-  *Feladatátvétel*: a másodlagos névtér aktiválása folyamatban van.

## <a name="supported-namespace-pairs"></a>Támogatott névtér-párok
Az elsődleges és a másodlagos névterek következő kombinációi támogatottak:  

| Elsődleges névtér | Másodlagos névtér | Támogatott | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Yes | 
| Standard | Dedikált | Yes | 
| Dedikált | Dedikált | Yes | 
| Dedikált | Standard | No | 

> [!NOTE]
> Ugyanahhoz a dedikált fürthöz tartozó névtereket nem lehet párosítani. A különálló fürtökben található névtereket is párosíthatja. 

## <a name="setup-and-failover-flow"></a>Telepítési és feladatátvételi folyamat

A következő szakasz áttekintést nyújt a feladatátvételi folyamatról, és bemutatja, hogyan állíthatja be a kezdeti feladatátvételt. 

![1][]

### <a name="setup"></a>Beállítás

Először hozzon létre vagy használjon egy meglévő elsődleges névteret, és egy új másodlagos névteret, és párosítsa a kettőt. Ez a párosítás egy aliast ad meg, amely a kapcsolódáshoz használható. Mivel aliast használ, nem kell módosítania a kapcsolódási karakterláncokat. Csak új névterek adhatók hozzá a feladatátvételi párosításhoz. 

1. Hozza létre az elsődleges névteret.
1. Hozza létre a másodlagos névteret egy másik régióban. Ez a lépés nem kötelező. A másodlagos névteret a következő lépésben a párosítás létrehozásakor hozhatja létre. 
1. A Azure Portal navigáljon az elsődleges névtérhez.
1. A bal oldali menüben válassza a **geo-helyreállítás** lehetőséget, majd válassza a **párosítás kezdeményezése** lehetőséget az eszköztáron. 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Párosítás kezdeményezése az elsődleges névtérből":::    
1. A **párosítás kezdeményezése** lapon kövesse az alábbi lépéseket:
    1. Válasszon ki egy meglévő másodlagos névteret, vagy hozzon létre egyet egy másik régióban. Ebben a példában egy meglévő névtér van kiválasztva.  
    1. **Alias** esetén adjon meg egy aliast a Geo-Dr párosításhoz. 
    1. Ezután kattintson a **Létrehozás** elemre. 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="Másodlagos névtér kiválasztása":::        
1. Ekkor meg kell jelennie a **geo-Dr alias** oldalnak. A bal oldali menüben a **geo-helyreállítás** lehetőség kiválasztásával az elsődleges névtérből is megnyithatja ezt az oldalt.

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Geo-DR alias oldal":::    
1. A **geo-Dr alias** lapon a bal oldali menüben válassza a **megosztott hozzáférési házirendek** lehetőséget az alias elsődleges kapcsolati karakterláncának eléréséhez. Használja ezt a kapcsolódási karakterláncot ahelyett, hogy közvetlenül a kapcsolódási karakterláncot használja az elsődleges/másodlagos névtérhez. 
1. Ezen **Áttekintés** lapon a következő műveleteket végezheti el: 
    1. Szüntesse meg az elsődleges és a másodlagos névterek közötti párosítást. Válassza a **párosítás megszakítása** lehetőséget az eszköztáron. 
    1. Manuálisan végezze el a feladatátvételt a másodlagos névtérbe. Válassza a **feladatátvétel** lehetőséget az eszköztáron. 
    
        > [!WARNING]
        > Ha feladatátvételt hajt végre, aktiválja a másodlagos névteret, és távolítsa el az elsődleges névteret a Geo-Disaster helyreállítási párosításból. Hozzon létre egy másik névteret, hogy új földrajzi katasztrófa utáni helyreállítási pár legyen. 

Végezetül vegyen fel némi figyelést, hogy ellenőrizze, szükség van-e feladatátvételre. A legtöbb esetben a szolgáltatás egy nagyméretű ökoszisztéma egyik része, így az automatikus feladatátvétel ritkán lehetséges, mivel a feladatátvételt a többi alrendszerrel vagy infrastruktúrával szinkronizálva kell végrehajtani.

### <a name="example"></a>Példa

Ebben a forgatókönyvben egy példa arra, hogy egy olyan pénztári (POS) megoldást vegyünk fel, amely üzeneteket vagy eseményeket bocsát ki. A Event Hubs átadja ezeket az eseményeket valamilyen leképezési vagy újraformázási megoldásnak, amely ezután a leképezett adatfeldolgozást egy másik rendszerbe továbbítja. Ezen rendszerek esetében előfordulhat, hogy az összes ilyen rendszer ugyanabban az Azure-régióban fut. Annak a döntése, hogy a rendszer mikor és milyen részben hajtja végre a feladatátvételt, az infrastruktúra adatforgalmával függ. 

A feladatátvételt a figyelési rendszerekkel vagy a testreszabott figyelési megoldásokkal automatizálhatja. Az ilyen automatizálás azonban további tervezést és munkát is igénybe vesz, amely nem tartozik a jelen cikk hatálya alá.

### <a name="failover-flow"></a>Feladatátvételi folyamat

Ha elindítja a feladatátvételt, két lépés szükséges:

1. Ha egy másik leállás következik be, azt szeretné, hogy újra lehessen adni a feladatátvételt. Ezért állítson be egy másik passzív névteret, és frissítse a párosítást. 

2. A korábbi elsődleges névtérből származó üzenetek lekérése, ha ismét elérhetővé válik. Ezt követően használja ezt a névteret a normál üzenetküldéshez a Geo-helyreállítási beállításon kívül, vagy törölje a régi elsődleges névteret.

> [!NOTE]
> Csak a meghiúsult továbbítási szemantika támogatott. Ebben a forgatókönyvben feladatátvételt hajt végre, majd egy új névtérrel újra párosítja. A sikertelen visszaállítás nem támogatott; például egy SQL-fürtben. 

![2][]

## <a name="management"></a>Kezelés

Ha hibát vétett; Előfordulhat például, hogy a kezdeti beállítás során nem a megfelelő régiókat párosítja, hanem bármikor megszakíthatja a két névtér párosítását. Ha a párosított névtereket normál névtérként szeretné használni, törölje az aliast.

## <a name="samples"></a>Példák

A [githubon található minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) bemutatja, hogyan kell beállítani és kezdeményezni a feladatátvételt. Ez a példa a következő fogalmakat mutatja be:

- A Azure Active Directory a Azure Resource Manager Event Hubs használatával való használatához szükséges beállítások. 
- A mintakód végrehajtásához szükséges lépések. 
- Küldés és fogadás az aktuális elsődleges névtérből. 

## <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe, hogy a következő szempontokat kell szem előtt tartani:

1. A tervezés szerint Event Hubs geo-vész-helyreállítás nem replikálja az adatait, ezért nem használhatja fel az elsődleges Event hub régi eltolási értékét a másodlagos esemény központján. Javasoljuk, hogy a következő módszerek egyikével indítsa újra az esemény-fogadót:

   - *EventPosition. FromStart ()* – ha szeretné beolvasni az összes adatközpontot a másodlagos esemény központján.
   - *EventPosition. FromEnd ()* – ha szeretné beolvasni az összes új adatforrást a másodlagos esemény központhoz való kapcsolódáskor.
   - *EventPosition. FromEnqueuedTime (datetime)* – ha szeretné beolvasni a másodlagos Event hub-ban fogadott összes adat egy adott dátumtól és időponttól kezdve.

2. A feladatátvétel tervezése során az időtényezőt is figyelembe kell venni. Ha például megszakad a kapcsolat a 15 – 20 percnél hosszabb ideig, dönthet úgy, hogy kezdeményezi a feladatátvételt. 
 
3. Az a tény, hogy a rendszer nem replikálja az adatreplikációt, az aktuális aktív munkamenetek nem replikálódnak. Emellett előfordulhat, hogy a duplikált észlelés és az ütemezett üzenetek nem működnek. Az új munkamenetek, az ütemezett üzenetek és az új ismétlődések is működni fognak. 

4. Egy összetett elosztott infrastruktúra feladatátvétele legalább egyszer [kipróbálható](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) . 

5. Az entitások szinkronizálása hosszabb időt is igénybe vehet, körülbelül 50-100 entitást percenként.

## <a name="availability-zones"></a>Rendelkezésre állási zónák 

A Event Hubs standard SKU támogatja a [Availability Zones](../availability-zones/az-overview.md), amely az Azure-régiókban a hibáktól elkülönített helyet biztosít. 

> [!NOTE]
> Az Azure Event Hubs standard Availability Zones támogatása csak olyan [Azure-régiókban](../availability-zones/az-region.md) érhető el, ahol elérhetők a rendelkezésre állási zónák.

A Availability Zones csak az új névtereken engedélyezheti, a Azure Portal használatával. A Event Hubs nem támogatja a meglévő névterek áttelepítését. A zóna redundancia nem tiltható le, miután engedélyezte azt a névtérben.

A rendelkezésre állási zónák használatakor a rendszer a metaadatokat és az adatokat (eseményeket) is replikálja a rendelkezésre állási zónában lévő adatközpontokban. 

![3][]

## <a name="private-endpoints"></a>Privát végpontok
Ez a szakasz további szempontokat tartalmaz, amikor a Geo-vész-helyreállítást a privát végpontokat használó névterekkel használja. Ha többet szeretne megtudni a privát végpontok Event Hubs használatával történő használatáról, tekintse meg a [privát végpontok konfigurálása](private-link-service.md)című témakört.

### <a name="new-pairings"></a>Új párosítások
Ha egy privát végponttal rendelkező elsődleges névtér és egy privát végpont nélküli másodlagos névtér között próbál létrehozni egy párosítást, akkor a párosítás sikertelen lesz. A párosítás csak akkor lesz sikeres, ha az elsődleges és a másodlagos névterek magánhálózati végpontokkal is rendelkeznek. Azt javasoljuk, hogy ugyanazokat a konfigurációkat használja az elsődleges és másodlagos névtereken, valamint azokon a virtuális hálózatokon, amelyeken a magánhálózati végpontok létre lettek hozva.  

> [!NOTE]
> Ha az elsődleges névteret privát végponttal és másodlagos névtérrel próbálja párosítani, az ellenőrzési folyamat csak azt ellenőrzi, hogy létezik-e privát végpont a másodlagos névtérben. Nem vizsgálja, hogy a végpont működik-e, vagy a feladatátvétel után fog működni. Az Ön felelőssége annak biztosítása, hogy a titkos végponttal rendelkező másodlagos névtér a feladatátvételt követően is a várt módon működjön.
>
> Annak ellenőrzéséhez, hogy a magánhálózati végpontok konfigurációja azonos-e az elsődleges és a másodlagos névterekben, küldjön egy olvasási kérelmet (például: [Event hub beszerzése](/rest/api/eventhub/get-event-hub)) a másodlagos névtérnek a virtuális hálózaton kívülről, és ellenőrizze, hogy hibaüzenetet kap-e a szolgáltatástól.

### <a name="existing-pairings"></a>Meglévő párosítások
Ha az elsődleges és a másodlagos névtér közötti párosítás már létezik, akkor az elsődleges névtéren a magánhálózati végpont létrehozása sikertelen lesz. A megoldáshoz először hozzon létre egy privát végpontot a másodlagos névtérben, majd hozzon létre egyet az elsődleges névtérhez.

> [!NOTE]
> Noha a másodlagos névtérhez csak olvasási hozzáférést engedélyezünk, a magánhálózati végpontok konfigurációjának frissítései engedélyezve vannak. 

### <a name="recommended-configuration"></a>Ajánlott konfiguráció
Ha az alkalmazáshoz és Event Hubs névterekhez vész-helyreállítási konfigurációt hoz létre, akkor az elsődleges és a másodlagos Event Hubs névterekhez egyaránt létre kell hoznia privát végpontokat az alkalmazás elsődleges és másodlagos példányait üzemeltető virtuális hálózatokon. 

Tegyük fel, hogy két virtuális hálózattal rendelkezik: VNET-1, VNET-2 és ezek az elsődleges és másodlagos névterek: EventHubs-Namespace1-Primary, EventHubs-Namespace2-másodlagos. A következő lépéseket kell elvégeznie: 

- A EventHubs-Namespace1-Primary-ben hozzon létre két privát végpontot, amely a VNET-1 és a VNET-2 alhálózatokat használja.
- EventHubs – Namespace2 – másodlagos, hozzon létre két privát végpontot, amelyek ugyanazt az alhálózatot használják, mint a VNET-1 és a VNET-2 

![Magánhálózati végpontok és virtuális hálózatok](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

Ennek a megközelítésnek az az előnye, hogy a feladatátvétel a Event Hubs névtértől független alkalmazási rétegben történhet. Vegyük példaként a következő forgatókönyveket: 

**Csak alkalmazáson belüli feladatátvétel:** Itt az alkalmazás nem létezik a VNET-1 helyen, de a VNET-2 értékre lép. Mivel a magánhálózati végpontok mind a VNET-1, mind a VNET-2 esetében mind az elsődleges, mind a másodlagos névtér esetében konfigurálva vannak, az alkalmazás csak működni fog. 

**Event Hubs csak névtér feladatátvétele**: itt újra, mivel mindkét magánhálózati végpont mind az elsődleges, mind a másodlagos névterek esetében mindkét virtuális hálózaton konfigurálva van, az alkalmazás csak működni fog. 

> [!NOTE]
> A virtuális hálózatok földrajzi katasztrófa utáni helyreállításával kapcsolatos útmutatásért lásd: [Virtual Network – üzletmenet folytonossága](../virtual-network/virtual-network-disaster-recovery-guidance.md).
 
## <a name="next-steps"></a>Következő lépések

* A [githubon található minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) egy egyszerű munkafolyamaton keresztül megy át, amely egy geo-párosítást hoz létre, és feladatátvételt kezdeményez a vész-helyreállítási forgatókönyvek esetében.
* A [REST API hivatkozás](/rest/api/eventhub/) a Geo-vész-helyreállítási konfigurációt végző API-kat ismerteti.

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

- Bevezetés az Event Hubs használatába
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.yml)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
