---
title: Azure Service Bus geo-vész-helyreállítás | Microsoft Docs
description: Földrajzi régiók használata a feladatátvételhez és a vész-helyreállításhoz Azure Service Bus
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: 2aa7ed118d0ba179ffff4f72a4d4df787edc9d88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933755"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geo-vész-helyreállítás

Az adatfeldolgozási erőforrások katasztrofális leállása elleni rugalmasság számos vállalat számára szükséges, és bizonyos esetekben az iparági szabályozásoknak is meg kell követelniük. 

Azure Service Bus már az egyes gépek katasztrofális meghibásodásának kockázatát, vagy akár olyan fürtöket is teljesít, amelyek egy adatközpontban több meghibásodási tartományt foglalnak magukban, és olyan átlátható hibák észlelését és feladatátvételi mechanizmusokat valósít meg, amelyekkel a szolgáltatás továbbra is a biztos szolgáltatási szinten fog működni, és jellemzően az ilyen hibák bekövetkezésekor észlelhető megszakítások nélkül. Ha a [rendelkezésre állási zónákhoz](../availability-zones/az-overview.md)engedélyezett Service Bus névteret hoztak létre, a kockázat kiesési kockázat a három fizikailag különálló létesítmény között tovább terjed, és a szolgáltatás elegendő kapacitási tartalékokkal rendelkezik, amelyek azonnal megbirkóznak a teljes létesítmény teljes, katasztrofális elvesztésével. 

A rendelkezésre állási zóna támogatásával rendelkező minden aktív Azure Service Bus fürtcsomópont a helyszíni üzenetkezelési termékhez képest nagyobb rugalmasságot biztosít a súlyos hardveres hibák és a teljes adatközpont-létesítmények katasztrofális elvesztése tekintetében. Azonban előfordulhat, hogy súlyos helyzetek fordulnak elő, ha az ilyen intézkedések még nem tudják megfelelően megvédeni a fizikai megsemmisítést. 

A Service Bus geo-vész-helyreállítási funkció úgy lett kialakítva, hogy könnyebben helyreállítható legyen a nagy mennyiségű katasztrófa miatt, és a hibás Azure-régiót kihagyhatja a megfelelő, és nem kell módosítania az alkalmazás konfigurációját. Az Azure-régiók felhagyása jellemzően számos szolgáltatást foglal magában, és ez a funkció elsősorban az összetett alkalmazások konfigurációjának integritásának biztosítását célozza meg. A szolgáltatás globálisan elérhető a Service Bus Premium SKU-hoz. 

A Geo-Disaster Recovery funkció biztosítja, hogy a rendszer a névterek (várólisták, témakörök, előfizetések, szűrők) teljes konfigurációját folyamatosan replikálja egy elsődleges névtérből egy másodlagos névtérbe, ha párosítva van, és lehetővé teszi, hogy egy egyszeri feladatátvételt kezdeményezzen az elsődlegesről a másodlagosra, bármikor. A feladatátvételi áthelyezés a névtér kiválasztott aliasának nevét a másodlagos névtérhez irányítja, majd megszakítja a párosítást. A feladatátvétel majdnem azonnal megkezdődik. 

> [!IMPORTANT]
> A szolgáltatás lehetővé teszi a műveletek azonnali folytonosságát ugyanazzal a konfigurációval, de **nem replikálja a várólistákban tárolt üzeneteket, illetve az előfizetéseket és a kézbesítetlen levelek várólistáit**. A várólista-szemantika megőrzése érdekében az ilyen replikáláshoz nem csak az üzenetek replikálása szükséges, hanem a közvetítő minden állapotának változása esetén is. A legtöbb Service Bus névtér esetében a szükséges replikálási forgalom messze meghaladja az alkalmazás forgalmát és a nagy átviteli sebességű várólistákat, a legtöbb üzenet továbbra is a másodlagosra replikálódik, miközben az elsődlegestől már törölve lett, ami túlzottan pazarló forgalmat okoz. A nagy késleltetésű replikációs útvonalak esetében, amelyek a földrajzi katasztrófa utáni helyreállításhoz választott számos párosításra érvényesek, előfordulhat, hogy a replikálási forgalom tartósan megtartja az alkalmazások forgalmát a késés okozta szabályozási hatások miatt.
 
> [!TIP]
> A várólisták és a témakör-előfizetések tartalmának replikálásához, valamint az aktív/aktív konfigurációk megfelelő névterének működéséhez az kimaradások és a katasztrófák megbirkózása érdekében ne támaszkodjon erre a földrajzi katasztrófa utáni helyreállítási szolgáltatásra, de kövesse a [replikálási útmutatót](service-bus-federation-overview.md).  

## <a name="outages-and-disasters"></a>Kimaradások és katasztrófák

Fontos megjegyezni az "kimaradások" és a "katasztrófák" közötti különbséget. 

A *leállás* a Azure Service Bus ideiglenes nem érhető el, és hatással lehet a szolgáltatás egyes összetevőire, például az üzenetküldési tárolóra vagy akár a teljes adatközpontra is. A probléma javítása után azonban Service Bus újra elérhetővé válik. A leállás általában nem okoz üzenetet vagy más adatvesztést. Ilyen kimaradás például áramkimaradás lehet az adatközpontban. Bizonyos kimaradások átmeneti vagy hálózati problémák miatt csak rövid kapcsolatok elvesztését okozják. 

A *katasztrófa* a Service Bus-fürt, az Azure-régió vagy az adatközpont állandó vagy hosszú távú elvesztéseként van meghatározva. Előfordulhat, hogy a régió vagy az adatközpont újra elérhetővé válik, vagy akár órákig, akár napokig nem. Ilyen katasztrófák például a tűz, az árvíz vagy a földrengés. Az állandó vészhelyzet miatt előfordulhat, hogy bizonyos üzenetek, események vagy egyéb adatvesztést okoznak. Azonban a legtöbb esetben nem lehet adatvesztés, és az adatközpont biztonsági mentése után az üzenetek nem állíthatók helyre.

A Azure Service Bus geo-vész-helyreállítási funkciója vész-helyreállítási megoldás. Az ebben a cikkben ismertetett fogalmak és munkafolyamatok a katasztrófa-forgatókönyvekre, és nem átmeneti vagy átmeneti kimaradásokra vonatkoznak. A Microsoft Azure vész-helyreállítási részletes megvitatását [ebben a cikkben](/azure/architecture/resiliency/disaster-recovery-azure-applications)találja.   

## <a name="basic-concepts-and-terms"></a>Alapvető fogalmak és kifejezések

A vész-helyreállítási funkció a metaadatok vész-helyreállítását valósítja meg, és az elsődleges és másodlagos vész-helyreállítási névterekre támaszkodik. A Geo-vész-helyreállítási funkció csak a [Premium SKU](service-bus-premium-messaging.md) esetében érhető el. Nem kell módosítania a kapcsolódási karakterláncot, mert a kapcsolatok aliason keresztül történnek.

A cikk a következő kifejezéseket használja:

-  *Alias*: az Ön által beállított vész-helyreállítási konfiguráció neve. Az alias egyetlen stabil teljes tartománynevet (FQDN) tartalmazó adatkarakterláncot biztosít. Az alkalmazások ezt az alias kapcsolati karakterláncot használják a névtérhez való kapcsolódáshoz. Az alias használatával biztosítható, hogy a kapcsolódási sztring változatlan maradjon a feladatátvétel elindításakor.

-  *Elsődleges/másodlagos névtér*: az aliasnak megfelelő névterek. Az elsődleges névtér "aktív", és fogadja az üzeneteket (ez lehet egy meglévő vagy egy új névtér). A másodlagos névtér "passzív", és nem fogad üzeneteket. A kettő közötti metaadatok szinkronban vannak, így mindkét alkalmazás kód vagy kapcsolati karakterlánc módosítása nélkül is zökkenőmentesen fogadhat üzeneteket. Annak biztosítása érdekében, hogy csak az aktív névtér kapjon üzeneteket, az aliast kell használnia. 
-  *Metaadatok*: olyan entitások, mint a várólisták, témakörök és előfizetések; a névtérhez társított szolgáltatás tulajdonságai. A rendszer csak az entitásokat és azok beállításait replikálja automatikusan. Az üzenetek nem replikálódnak.
-  *Feladatátvétel*: a másodlagos névtér aktiválása folyamatban van.

## <a name="setup"></a>Beállítás

A következő szakasz áttekintést nyújt a névterek közötti párosítás beállításáról.

![1][]

Először hozzon létre vagy használjon egy meglévő elsődleges névteret, és egy új másodlagos névteret, és párosítsa a kettőt. Ez a párosítás egy aliast ad meg, amely a kapcsolódáshoz használható. Mivel aliast használ, nem kell módosítania a kapcsolódási karakterláncokat. Csak új névterek adhatók hozzá a feladatátvételi párosításhoz. 

1. Hozza létre az elsődleges névteret.
1. Hozza létre a másodlagos névteret egy másik régióban. Ez a lépés nem kötelező. A másodlagos névteret a következő lépésben a párosítás létrehozásakor hozhatja létre. 
1. A Azure Portal navigáljon az elsődleges névtérhez.
1. A bal oldali menüben válassza a **geo-helyreállítás** lehetőséget, majd válassza a **párosítás kezdeményezése** lehetőséget az eszköztáron. 

    :::image type="content" source="./media/service-bus-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Párosítás kezdeményezése az elsődleges névtérből":::    
1. A **párosítás kezdeményezése** lapon kövesse az alábbi lépéseket:
    1. Válasszon ki egy meglévő másodlagos névteret, vagy hozzon létre egyet egy másik régióban. Ebben a példában egy meglévő névteret használunk másodlagos névtérként.  
    1. **Alias** esetén adjon meg egy aliast a Geo-Dr párosításhoz. 
    1. Ezután kattintson a **Létrehozás** elemre. 

        :::image type="content" source="./media/service-bus-geo-dr/initiate-pairing-page.png" alt-text="Másodlagos névtér kiválasztása":::        
1. Az alábbi ábrán látható módon meg kell jelennie a **Service Bus geo-Dr alias** oldalnak. A bal oldali menüben található **geo-helyreállítás** lehetőség kiválasztásával az elsődleges névtér oldalának **geo-Dr alias** oldalára is rákereshet. 

    :::image type="content" source="./media/service-bus-geo-dr/service-bus-geo-dr-alias-page.png" alt-text="Service Bus geo-DR alias oldal":::
1. A **geo-Dr alias** lapon a bal oldali menüben válassza a **megosztott hozzáférési házirendek** lehetőséget az alias elsődleges kapcsolati karakterláncának eléréséhez. Használja ezt a kapcsolódási karakterláncot ahelyett, hogy közvetlenül a kapcsolódási karakterláncot használja az elsődleges/másodlagos névtérhez. Az alias kezdetben az elsődleges névtérre mutat.
1. Váltson át az **Áttekintés** lapra. A következő műveleteket végezheti el: 
    1. Szüntesse meg az elsődleges és a másodlagos névterek közötti párosítást. Válassza a **párosítás megszakítása** lehetőséget az eszköztáron. 
    1. Manuálisan adja át a feladatátvételt a másodlagos névtérnek. 
        1. Válassza a **feladatátvétel** lehetőséget az eszköztáron. 
        1. Az alias beírásával erősítse meg, hogy át kívánja adni a feladatátvételt a másodlagos névtérnek. 
        1. Kapcsolja be a **biztonságos feladatátvételi** lehetőséget, hogy a rendszer biztonságosan átadja a feladatokat a másodlagos névtérnek. Ezzel a szolgáltatással gondoskodhat arról, hogy a függőben lévő geo-DR replikációk a másodlagosra váltás előtt legyenek végrehajtva. 
        1. Ezután válassza a **feladatátvétel** lehetőséget. 
        
            :::image type="content" source="./media/service-bus-geo-dr/failover-page.png" alt-text="{alt-text}":::
    
            > [!IMPORTANT]
            > Ha feladatátvételt hajt végre, aktiválja a másodlagos névteret, és távolítsa el az elsődleges névteret a Geo-Disaster helyreállítási párosításból. Hozzon létre egy másik névteret, hogy új földrajzi katasztrófa utáni helyreállítási pár legyen. 

1. Végezetül vegyen fel némi figyelést, hogy ellenőrizze, szükség van-e feladatátvételre. A legtöbb esetben a szolgáltatás egy nagyméretű ökoszisztéma egyik része, így az automatikus feladatátvétel ritkán lehetséges, mivel a feladatátvételt a többi alrendszerrel vagy infrastruktúrával szinkronizálva kell végrehajtani.

### <a name="service-bus-standard-to-premium"></a>A prémium szintű Service Bus
Ha [áttelepítette a Azure Service Bus standard névteret a prémium szintű Azure Service Busre](service-bus-migrate-standard-premium.md), akkor a vész-helyreállítási konfigurációt a **PS/CLI** vagy a **REST API** használatával kell létrehoznia a korábban meglévő alias (azaz a Service Bus standard névtér-kapcsolódási karakterlánc) segítségével.

Ennek oka az, hogy az áttelepítés során a Azure Service Bus szabványos névtér-kapcsolódási karakterlánc/DNS-név alias lesz a Azure Service Bus Premium-névtérhez.

Az ügyfélalkalmazások ezt az aliast (azaz Azure Service Bus a szabványos névtér-kapcsolati karakterláncot) kell használniuk ahhoz, hogy csatlakozni tudjanak a prémium szintű névtérhez, ahol be lett állítva a vész-helyreállítási párosítás.

Ha a portál segítségével állítja be a vész-helyreállítási konfigurációt, akkor a portál ezt a kikötést ellátja Önnek.


## <a name="failover-flow"></a>Feladatátvételi folyamat

A feladatátvételt manuálisan indítja el az ügyfél (explicit módon egy parancson keresztül, vagy az ügyfél által birtokolt üzleti logikán keresztül, amely elindítja a parancsot), és soha nem az Azure-ban. Az ügyfél teljes körű tulajdonjogot és láthatóságot biztosít az Azure gerincén való kimaradás feloldása érdekében.

![4][]

A feladatátvétel elindítása után –

1. Az ***alias*** -kapcsolódási karakterlánc frissült, hogy a másodlagos prémium névtérre mutasson.

2. Az ügyfelek (küldők és fogadók) automatikusan csatlakoznak a másodlagos névtérhez.

3. Az elsődleges és a másodlagos prémium névtér közötti meglévő párosítás megszakadt.

A feladatátvétel kezdeményezése után –

1. Ha egy másik leállás következik be, azt szeretné, hogy újra lehessen adni a feladatátvételt. Ezért állítson be egy másik passzív névteret, és frissítse a párosítást. 

2. A korábbi elsődleges névtérből származó üzenetek lekérése, ha ismét elérhetővé válik. Ezt követően használja ezt a névteret a normál üzenetküldéshez a Geo-helyreállítási beállításon kívül, vagy törölje a régi elsődleges névteret.

> [!NOTE]
> Csak a meghiúsult továbbítási szemantika támogatott. Ebben a forgatókönyvben feladatátvételt hajt végre, majd egy új névtérrel újra párosítja. A sikertelen visszaállítás nem támogatott; például egy SQL-fürtben. 

A feladatátvételt a figyelési rendszerekkel vagy a testreszabott figyelési megoldásokkal automatizálhatja. Az ilyen automatizálás azonban további tervezést és munkát is igénybe vesz, amely nem tartozik a jelen cikk hatálya alá.

![2][]

## <a name="management"></a>Kezelés

Ha hibát vétett; Előfordulhat például, hogy a kezdeti beállítás során nem a megfelelő régiókat párosítja, hanem bármikor megszakíthatja a két névtér párosítását. Ha a párosított névtereket normál névtérként szeretné használni, törölje az aliast.

## <a name="use-existing-namespace-as-alias"></a>Meglévő névtér használata aliasként

Ha olyan helyzettel rendelkezik, amelyben nem változtathatja meg a termelők és a felhasználók kapcsolatait, a névtér nevét felhasználhatja alias neveként. Tekintse [meg a githubhoz tartozó mintakód itt](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Példák

A [githubon található minták](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) bemutatják, hogyan kell beállítani és kezdeményezni a feladatátvételt. Ezek a minták a következő fogalmakat szemléltetik:

- .NET-minta és-beállítások szükségesek ahhoz, hogy a Azure Active Directory a Azure Resource Manager a Service Bus használatával, a beállításához és a földrajzi katasztrófa utáni helyreállítás engedélyezéséhez.
- A mintakód végrehajtásához szükséges lépések.
- Meglévő névtér használata aliasként.
- A következő lépésekkel engedélyezheti a földrajzi katasztrófa utáni helyreállítást a PowerShell vagy a parancssori felület használatával.
- [Küldés és fogadás](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) az aktuális elsődleges vagy másodlagos névtérből az alias használatával.

## <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe a következő szempontokat, hogy ne feledje a jelen kiadást:

1. A feladatátvétel tervezése során az időtényezőt is figyelembe kell venni. Ha például megszakad a kapcsolat a 15 – 20 percnél hosszabb ideig, dönthet úgy, hogy kezdeményezi a feladatátvételt.

2. Az a tény, hogy nem replikálódnak az adathalmazok, a jelenleg aktív munkamenetek nem replikálódnak. Emellett előfordulhat, hogy a duplikált észlelés és az ütemezett üzenetek nem működnek. Az új munkamenetek, az új ütemezett üzenetek és az új ismétlődések is működni fognak. 

3. Egy összetett elosztott infrastruktúra feladatátvétele legalább egyszer [kipróbálható](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) .

4. Az entitások szinkronizálása hosszabb időt is igénybe vehet, körülbelül 50-100 entitást percenként. Az előfizetések és a szabályok entitásként is számítanak.

## <a name="availability-zones"></a>Rendelkezésre állási zónák

A Service Bus Premium SKU támogatja a [Availability Zones](../availability-zones/az-overview.md), amely az ugyanazon az Azure-régióban található, hibátlanul elszigetelt helyszíneket biztosít. A Service Bus az üzenetküldési tároló három példányát kezeli (1 elsődleges és 2 másodlagos). A Service Bus az adatok és a felügyeleti műveletek szinkronizálásában mindhárom másolatot megtartja. Ha az elsődleges másolat meghiúsul, az egyik másodlagos másolat az elsődleges értékre kerül, az észlelt állásidő nélkül. Ha az alkalmazások átmeneti leválasztást látnak Service Bus, az SDK-ban lévő újrapróbálkozási logika automatikusan újra csatlakozik Service Bushoz. 

A rendelkezésre állási zónák használatakor a rendszer a metaadatokat és az adatokat (üzeneteket) is replikálja a rendelkezésre állási zóna adatközpontjai között. 

> [!NOTE]
> A prémium szintű Azure Service Bus Availability Zones támogatása csak olyan Azure- [régiókban](../availability-zones/az-region.md) érhető el, ahol rendelkezésre áll a rendelkezésre állási zónák.

A Availability Zones csak az új névtereken engedélyezheti, a Azure Portal használatával. A Service Bus nem támogatja a meglévő névterek áttelepítését. A zóna redundancia nem tiltható le, miután engedélyezte azt a névtérben.

![3][]

## <a name="private-endpoints"></a>Privát végpontok
Ez a szakasz további szempontokat tartalmaz, amikor a Geo-vész-helyreállítást a privát végpontokat használó névterekkel használja. Ha többet szeretne megtudni a Service Bustel rendelkező privát végpontok használatáról, tekintse meg a [Azure Service Bus integrálása az Azure privát kapcsolattal](private-link-service.md)című témakört.

### <a name="new-pairings"></a>Új párosítások
Ha egy privát végponttal rendelkező elsődleges névtér és egy privát végpont nélküli másodlagos névtér között próbál létrehozni egy párosítást, akkor a párosítás sikertelen lesz. A párosítás csak akkor lesz sikeres, ha az elsődleges és a másodlagos névterek magánhálózati végpontokkal is rendelkeznek. Azt javasoljuk, hogy ugyanazokat a konfigurációkat használja az elsődleges és másodlagos névtereken, valamint azokon a virtuális hálózatokon, amelyeken a magánhálózati végpontok létre lettek hozva. 

> [!NOTE]
> Ha az elsődleges névteret privát végponttal és a másodlagos névtérrel próbálja párosítani, az érvényesítési folyamat csak azt ellenőrzi, hogy létezik-e privát végpont a másodlagos névtérben. Nem vizsgálja, hogy a végpont működik-e, vagy a feladatátvétel után fog működni. Az Ön felelőssége annak biztosítása, hogy a titkos végponttal rendelkező másodlagos névtér a feladatátvételt követően is a várt módon működjön.
>
> Annak ellenőrzéséhez, hogy a magánhálózati végponti konfigurációk azonosak-e, küldjön egy [Get Queues](/rest/api/servicebus/stable/queues/get) kérést a másodlagos névtérnek a virtuális hálózaton kívülről, és ellenőrizze, hogy hibaüzenetet kap-e a szolgáltatástól.

### <a name="existing-pairings"></a>Meglévő párosítások
Ha az elsődleges és a másodlagos névtér közötti párosítás már létezik, akkor az elsődleges névtéren a magánhálózati végpont létrehozása sikertelen lesz. A megoldáshoz először hozzon létre egy privát végpontot a másodlagos névtérben, majd hozzon létre egyet az elsődleges névtérhez.

> [!NOTE]
> Noha a másodlagos névtérhez csak olvasási hozzáférést engedélyezünk, a magánhálózati végpontok konfigurációjának frissítései engedélyezve vannak. 

### <a name="recommended-configuration"></a>Ajánlott konfiguráció
Az alkalmazás és a Service Bus vész-helyreállítási konfigurációjának létrehozásakor saját végpontokat kell létrehoznia az elsődleges és a másodlagos Service Bus névterekhez az alkalmazás elsődleges és másodlagos példányait üzemeltető virtuális hálózatokon.

Tegyük fel, hogy két virtuális hálózattal rendelkezik: VNET-1, VNET-2 és ezek az elsődleges és második névterek: ServiceBus-Namespace1-Primary, ServiceBus-Namespace2-másodlagos. A következő lépéseket kell elvégeznie: 

- A ServiceBus-Namespace1-Primary-ben hozzon létre két privát végpontot, amely a VNET-1 és a VNET-2 alhálózatokat használja.
- ServiceBus – Namespace2 – másodlagos, hozzon létre két privát végpontot, amelyek ugyanazt az alhálózatot használják, mint a VNET-1 és a VNET-2 

![Magánhálózati végpontok és virtuális hálózatok](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


Ennek a megközelítésnek az az előnye, hogy a feladatátvétel a Service Bus névtértől független alkalmazási rétegben történhet. Vegyük példaként a következő forgatókönyveket: 

**Csak alkalmazáson belüli feladatátvétel:** Itt az alkalmazás nem létezik a VNET-1 helyen, de a VNET-2 értékre lép. Mivel a magánhálózati végpontok mind a VNET-1, mind a VNET-2 esetében mind az elsődleges, mind a másodlagos névtér esetében konfigurálva vannak, az alkalmazás csak működni fog. 

**Service Bus csak névtér feladatátvétele**: itt újra, mivel mindkét magánhálózati végpont mind az elsődleges, mind a másodlagos névterek esetében mindkét virtuális hálózaton konfigurálva van, az alkalmazás csak működni fog. 

> [!NOTE]
> A virtuális hálózatok földrajzi katasztrófa utáni helyreállításával kapcsolatos útmutatásért lásd: [Virtual Network – üzletmenet folytonossága](../virtual-network/virtual-network-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a Geo-vész-helyreállítási [REST API referenciát](/rest/api/servicebus/stable/disasterrecoveryconfigs).
- Futtassa a Geo-vész-helyreállítási [mintát a githubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Tekintse meg a földrajzi katasztrófa utáni helyreállítási [mintát, amely üzeneteket küld egy aliasnak](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [REST API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
