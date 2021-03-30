---
title: A beadási v1 konfigurálása
description: A App Service Environment v1 konfigurálása, kezelése és figyelése. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 598e43d07c213cfeb25f0ecbc7bd02b6ec54b7ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962587"
---
# <a name="configuring-an-app-service-environment-v1"></a>App Service Environment v1 konfigurálása

> [!NOTE]
> Ez a cikk a App Service Environment v1-es verzióról szól.  A App Service Environment újabb verziója könnyebben használható, és nagyobb teljesítményű infrastruktúrán fut. Ha többet szeretne megtudni az új verzióról, kezdje a [app Service Environment bevezetésével](intro.md).
> 

## <a name="overview"></a>Áttekintés
Magas szinten az Azure App Service Environment több fő összetevőből áll:

* A App Service Environment üzemeltetett szolgáltatásban futó számítási erőforrások
* Tárolás
* Egy adatbázis
* Klasszikus (v1) vagy Resource Manager (v2) Azure Virtual Network (VNet) 
* Olyan alhálózat, amelyen fut a App Service Environment üzemeltetett szolgáltatás

### <a name="compute-resources"></a>Számítási erőforrások
A négy erőforráskészlet számítási erőforrásait használja.  Az egyes App Service Environment (beadási) előtérben és három lehetséges munkavégző készlettel rendelkeznek. Nem kell mindhárom munkavégző készletet használnia – ha szeretné, csak egy vagy kettőt használhat.

Az erőforráskészlet (az előtér-és a munkavégzők) gazdagépei nem érhetők el közvetlenül a bérlők számára. A RDP protokoll (RDP) nem használható a hozzájuk való kapcsolódáshoz, a kiépítés megváltoztatásához vagy rendszergazdaként való kezeléséhez.

Beállíthatja az erőforráskészlet mennyiségét és méretét. Egy beadási csomag esetében négy mérettel rendelkezik, amelyek P1-től P4-ig vannak megjelölve. A méretekkel és azok díjszabásával kapcsolatos további információkért lásd: [app Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/).
A mennyiség vagy a méret módosítását méretezési műveletnek nevezzük.  Egyszerre csak egy skálázási művelet lehet folyamatban.

**Előtér-kezelőfelületek**: az előtér a saját előfizetésében tárolt alkalmazások HTTP/HTTPS-végpontja. Az előtér-munkaterhelések nem futnak.

* A bevezetési folyamat két P2s, amely elegendő a fejlesztési/tesztelési feladatokhoz és az alacsony szintű éles számítási feladatokhoz. Erősen ajánljuk a P3s közepes és nehéz üzemi számítási feladatokhoz.
* A mérsékelt és a nagy teljesítményű számítási feladatokhoz legalább négy P3s van szükség ahhoz, hogy az ütemezett karbantartás során elegendő legyen a kezdeti Befejezés. Az ütemezett karbantartási tevékenységek egyszerre egy előtért eredményeznek. Ez csökkenti a rendelkezésre álló előtér-kapacitást a karbantartási tevékenységek során.
* Az előtér-Befejezés akár egy órát is igénybe vehet. 
* A részletes Finomhangolás érdekében figyelnie kell a CPU százalékos arányát, a memória százalékos arányát és az aktív kérelmek metrikáját az előtér-készlethez. Ha a P3s futtatásakor a CPU vagy a memória százalékos aránya meghaladja a 70%-ot, adjon hozzá további előtér-végpontokat. Ha az aktív kérelmek értékének átlaga 15 000 – 20 000 kérelemre vonatkozik, akkor további előtér-értékeket is fel kell vennie. Az általános cél a CPU és a memória százalékos arányának 70% alatti megtartása, valamint az aktív kérések, amelyek a P3s futtatásakor az alábbi 15 000-kérelmekre vonatkoznak.  

**Feldolgozók**: az alkalmazások ténylegesen futnak. A App Service-csomagok vertikális felskálázásakor a rendszer felhasználja a feldolgozókat a társított munkakészletben.

* Nem lehet azonnal felvenni a feldolgozókat. Akár egy órát is igénybe vehetnek.
* Egy számítási erőforrás méretének méretezése bármely készlet esetében < egy órányi frissítési tartományba kerül. 20 frissítési tartomány van jelen a központnál. Ha 10 példánnyal méretezi a munkavégző készlet számítási méretét, akkor akár 10 órát is igénybe vehet.
* Ha megváltoztatja a munkavégző készletben használt számítási erőforrások méretét, a rendszer a munkavégző készletben futó alkalmazások hideg indítását fogja okozni.

A leggyorsabb módja, ha módosítani szeretné egy olyan munkavégző készlet számítási erőforrásának méretét, amely nem futtat alkalmazásokat:

* A feldolgozók mennyiségének méretezése a 2 értékre.  A portál minimális méretezési mérete 2. A példányok felszabadítása néhány percig is eltarthat. 
* Válassza ki az új számítási méretet és a példányok számát. Innentől számítva akár 2 órát is igénybe veheti.

Ha alkalmazásai nagyobb számítási erőforrás-méretet igényelnek, akkor nem használhatja ki az előző útmutatást. Az alkalmazásokat futtató munkavégző készlet méretének módosítása helyett egy másik munkavégző készletet tölthet fel a kívánt méretű feldolgozókkal, és áthelyezheti alkalmazásait a készletbe.

* Hozza létre a szükséges számítási méret további példányait egy másik munkavégző készletben. Ez akár egy órát is igénybe vesz.
* Rendeljen újra olyan App Service-csomagokat, amelyek olyan alkalmazásokat üzemeltetnek, amelyeknek nagyobb méretűnek kell lenniük az újonnan konfigurált munkavégző készlethez. Ez egy gyors művelet, amelynek elvégzéséhez kevesebb mint egy percet kell igénybe venni.  
* Ha már nincs szüksége a fel nem használt példányokra, az első munkavégző készletet le kell méreteznie. A művelet végrehajtása néhány percet vesz igénybe.

Automatikus **skálázás**: az egyik olyan eszköz, amely segíthet a számítási erőforrások felhasználásának kezelésében, automatikus skálázás. Az előtér-vagy feldolgozói készletek automatikus skálázását is használhatja. Olyan dolgok is megtehetnek, mint például az egyes készletekhez tartozó példányok megemelése reggel, és az esti időszakban csökkenthetők. Vagy előfordulhat, hogy olyan példányokat is hozzáadhat, amelyekben a feldolgozói készletben rendelkezésre álló munkavégzők száma egy bizonyos küszöbérték alá esik.

Ha szeretné beállítani az autoskálázási szabályokat a számítási erőforráskészlet metrikái között, akkor ne feledje, hogy milyen időpontra van szükség a kiépítés során. További információ az automatikus skálázásról App Service környezetekről: az automatikus [skálázás konfigurálása egy app Service Environment][ASEAutoscale].

### <a name="storage"></a>Tárolás
Az egyes előállítók 500 GB tárhellyel vannak konfigurálva. Ez a terület a központhoz tartozó összes alkalmazásban használatos. Ez a tárolóhely a szolgáltató része, és jelenleg nem lehet átváltani a tárolóhely használatára. Ha a virtuális hálózat útválasztásának vagy biztonságának módosítását végzi, akkor továbbra is engedélyeznie kell az Azure Storage-hoz való hozzáférést, vagy a kiegészítő funkció nem tud működni.

### <a name="database"></a>Adatbázis
Az adatbázis tartalmazza a környezetet meghatározó információkat, valamint a rajta futó alkalmazások részleteit. Ez is az Azure-beli előfizetés részét képezi. Nem valami, ami közvetlen képességgel rendelkezik a manipulálására. Ha a virtuális hálózat útválasztásának vagy biztonságának módosítását végzi, akkor továbbra is engedélyeznie kell a SQL Azure--vagy a bemenő rendszer nem tud működni.

### <a name="network"></a>Network (Hálózat)
A beadáshoz használt VNet lehet az egyik, amikor létrehozta a központot, vagy az idő előtt elvégezte azt. Amikor létrehozza az alhálózatot a központilag történő létrehozás során, arra kényszeríti a beléptetést, hogy a virtuális hálózattal megegyező erőforráscsoport legyen. Ha szüksége van arra, hogy a szolgáltató által használt erőforráscsoport eltérő legyen a VNet, akkor létre kell hoznia egy Resource Manager-sablon használatával.

A virtuális hálózatra vonatkozóan bizonyos korlátozások vonatkoznak:

* A virtuális hálózatnak regionális virtuális hálózatnak kell lennie.
* Legalább 8 olyan címmel rendelkező alhálózatnak kell lennie, ahol a központilag telepítve van.
* Ha egy alhálózatot a beadási szolgáltatás üzemeltetésére használ, az alhálózat címtartomány nem módosítható. Ezért azt javasoljuk, hogy az alhálózat legalább 64-es címet tartalmazzon, hogy az a jövőben bekövetkező kiegészítőket is kielégítse.
* Az alhálózaton, de a kiegészítő szolgáltatásban nem lehet semmi más.

A beadást tartalmazó üzemeltetett szolgáltatástól eltérően a [virtuális hálózat][virtualnetwork] és az alhálózat felhasználói vezérlés alatt állnak.  A virtuális hálózatot a Virtual Network felhasználói felületén vagy a PowerShellen keresztül felügyelheti.  A központilag üzembe helyezhető egy klasszikus vagy Resource Manager-VNet.  A portál és az API felülete némileg eltér a klasszikus és a Resource Manager-virtuális hálózatok között, de a bevezető élmény ugyanaz.

A VNet által üzemeltetett szolgáltatás saját RFC1918 IP-címeket használhat, vagy nyilvános IP-címeket is használhat.  Ha olyan IP-címtartományt szeretne használni, amely nem tartozik a RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), akkor létre kell hoznia a VNet és az alhálózatot, amelyet a szolgáltató a központú létrehozás előtt használ.

Mivel ez a funkció a virtuális hálózatba helyezi a Azure App Servicet, az azt jelenti, hogy a szolgáltatón keresztül üzemeltetett alkalmazásai mostantól hozzáférhetnek a ExpressRoute-vagy helyek közötti virtuális magánhálózat (VPN) használatával elérhetővé tett erőforrásokhoz. A App Service Environmenton belüli alkalmazások esetében nincs szükség további hálózatkezelési funkciókra ahhoz, hogy hozzáférjenek a App Service Environment üzemeltető virtuális hálózat számára elérhető erőforrásokhoz. Ez azt jelenti, hogy nincs szükség VNET-integráció vagy Hibrid kapcsolatok használatára, hogy a virtuális hálózathoz vagy a hálózathoz csatlakozó erőforrásokhoz kapjon. Mindkét funkciót továbbra is használhatja, azonban a virtuális hálózathoz nem csatlakoztatott hálózatokban lévő erőforrások eléréséhez.

A VNET-integráció használatával például integrálhatja az előfizetésben található virtuális hálózattal, de nincs csatlakoztatva ahhoz a virtuális hálózathoz, amelyen a szolgáltató található. Továbbra is használhatja a Hibrid kapcsolatok a más hálózatokban lévő erőforrások elérésére, akárcsak a szokásos módon.  

Ha a virtuális hálózata ExpressRoute VPN-kapcsolattal van konfigurálva, érdemes lehet néhány útválasztási igényt bemutatnia a központhoz. Vannak olyan, felhasználó által definiált útvonal-(UDR-) konfigurációk, amelyek nem kompatibilisek a központtal. További információ a ExpressRoute-t használó virtuális hálózatban lévő benyújtó szolgáltatásról: [app Service Environment futtatása virtuális hálózaton a ExpressRoute-vel][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>A bejövő forgalom biztonságossá tétele
Két elsődleges módszer van a beérkező forgalom vezérlésére.  A hálózati biztonsági csoportok (NSG-EK) segítségével szabályozhatja, hogy milyen IP-címek férhessenek hozzá a szolgáltatóhoz a [Bejövő forgalom vezérléséhez egy app Service Environment](app-service-app-service-environment-control-inbound-traffic.md) , és belső Load BALANCER (ILB) használatával is konfigurálhatja a bemenő adatforgalmat.  Ezeket a funkciókat együtt is használhatja, ha a NSG használatával szeretné korlátozni a hozzáférést a ILB.

A bevezetés létrehozásakor a rendszer létrehoz egy VIP-t a VNet.  Két VIP-típus létezik, külső és belső.  Ha külső virtuális IP-címmel hoz létre egy előkészítő szolgáltatást, akkor a beadásban lévő alkalmazásai elérhetők lesznek egy internetre irányítható IP-címen keresztül. Ha kijelöli a belső ILB, és nem lesz közvetlenül elérhető az interneten.  A ILB-beadáshoz továbbra is külső virtuális IP-cím szükséges, de csak az Azure felügyeleti és karbantartási hozzáférésekhez használható.  

A ILB bevezetésének létrehozásakor meg kell adnia a ILB-előkészítő által használt altartományt, és a saját DNS-t is kezelnie kell a megadott altartományhoz.  Mivel a altartomány nevét is be kell állítania, a HTTPS-hozzáféréshez használt tanúsítványt is kezelnie kell.  A beadást követően a rendszer felszólítja a tanúsítvány megadására.  Ha többet szeretne megtudni a ILB-t tartalmazó [belső Load Balancer és a app Service Environment használatával][ILBASE]kapcsolatos további tudnivalókért. 

## <a name="portal"></a>Portál
A App Service Environment felügyeletét és figyelését a Azure Portal felhasználói felületének használatával végezheti el. Ha van egy bekapcsoló, akkor valószínű, hogy megjelenik a App Service szimbólum az oldalsávon. Ez a szimbólum App Service környezetek ábrázolására szolgál a Azure Portalban:

![App Service Environment szimbólum][1]

Az összes App Service-környezetét felsoroló felhasználói felület megnyitásához használhatja az ikont, vagy kiválaszthatja a francia idézőjelet (">" szimbólum) az oldalsáv alján a App Service környezetek kiválasztásához. A felsorolt ASE egyikének kiválasztásával megnyithatja a figyeléshez és felügyelethez használt felhasználói felületet.

![Felhasználói felület a App Service Environment monitorozásához és kezeléséhez][2]

Ez az első panel a bemutatókhoz tartozó egyes tulajdonságokat, valamint az erőforráskészlet metrikai diagramját mutatja be. Az **Essentials** blokkban megjelenített tulajdonságok némelyike olyan hiperhivatkozásokat is magában foglal, amelyek a hozzá társított panelt nyitják meg. Kiválaszthatja például a **Virtual Network** nevet a virtuális hálózathoz társított felhasználói felület megnyitásához. **App Service terveket** és **alkalmazásokat** az egyes megnyitott lapokon, amelyek felsorolják ezeket az elemeket.  

### <a name="monitoring"></a>Figyelés
A diagramok lehetővé teszik különböző teljesítménymutatók megjelenítését az egyes erőforráskészletokban. Az előtér-készlet esetében figyelheti az átlagos PROCESSZORt és memóriát. A munkavégző készletek esetében nyomon követheti a felhasznált mennyiséget és az elérhető mennyiséget.

Több App Service-csomag is használhatja a munkavégző készlet feldolgozóit. A számítási feladatok nem ugyanolyan módon oszlanak el, mint az előtér-kiszolgálókon, így a CPU és a memóriahasználat nem sokat nyújt a hasznos információkhoz. Fontos, hogy nyomon kövessük, hány alkalmazottat használtak fel, és melyek elérhetők – különösen akkor, ha a rendszer kezelését mások számára kívánja használni.  

A riasztások beállításához a diagramokon nyomon követhető összes mérőszámot is használhatja. A riasztások beállítása itt ugyanúgy működik, mint a App Serviceban. Riasztást állíthat be a **riasztások** felhasználói felület részben, vagy bármely metrika felhasználói felületén, és a **riasztás hozzáadása** lehetőség választásával.

![Metrikák felhasználói felület][3]

Az imént tárgyalt metrikák a App Service Environment metrikák. A App Service csomag szintjén is elérhetők mérőszámok. A CPU és a memória figyelése sok értelmet tesz lehetővé.

A teljes App Service csomagok dedikált App Service csomagok. Ez azt jelenti, hogy az adott App Service-csomaghoz lefoglalt gazdagépeken futó alkalmazások csak a App Service terv alkalmazásai. Ha szeretné megtekinteni a App Service-csomag részleteit, a App Service tervet a beugró felhasználói felületen található bármelyik listából vagy a **tallózás app Service a csomagok** között (amely felsorolja az összeset).   

### <a name="settings"></a>Beállítások
A beadási panelen egy olyan **Beállítások** szakasz található, amely számos fontos funkciót tartalmaz:

**Beállítások**  >  Tulajdonságok: a **Settings (beállítások** ) panel automatikusan megnyílik, amikor felveszi a beadási **panelt**. A tetején a **Tulajdonságok** láthatók. Itt számos elem van, amelyek redundánsak az **alapvető** erőforrásoknál, de a **virtuális IP-cím** és a **kimenő IP-címek** is hasznosak.

![Beállítások panel és tulajdonságok][4]

**Beállítások**  >  **IP-címek**: Amikor létrehoz egy IP-SSL (SSL-) alkalmazást a beadásban, szüksége lesz egy IP SSL címre. Ahhoz, hogy beszerezze az egyiket, a szolgáltatónak meg kell IP SSLa a hozzárendelni kívánt címeket. A kisegítő szolgáltatás létrehozásakor egy IP SSL-címnek kell megadnia erre a célra, de továbbiak is hozzáadhatók. A további IP SSL címekért díjat számítunk fel, ahogy az a [app Service díjszabásban][AppServicePricing] is látható (az SSL-kapcsolatok szakaszban). A kiegészítő díj a IP SSL díj.

**Beállítások**  >  **Előtér-készlet**  /  **Munkavégző készletek**: ezen erőforráskészlet-lapok mindegyike lehetővé teszi, hogy csak az adott erőforráskészlet adatait lássuk el, az erőforráskészlet teljes méretezésére szolgáló vezérlőelemek mellett.  

Az egyes erőforráskészlet alappaneljének diagramja az adott erőforráskészlet metrikáit tartalmazza. Akárcsak a beadási panelen található diagramoknál, beléphet a diagramba, és igény szerint állíthatja be a riasztásokat. Egy adott erőforráskészlet bekapcsolási paneljéről érkező riasztások beállítása ugyanaz, mint az erőforráskészlet esetében. A Worker Pool **Beállítások** paneljén hozzáférhet az ebben a feldolgozó készletben futó összes alkalmazáshoz vagy app Service-csomaghoz.

![Worker Pool-beállítások felhasználói felülete][5]

### <a name="portal-scale-capabilities"></a>Portál méretezési képességei
Három skálázási művelet létezik:

* A IP SSL-használatra rendelkezésre álló, a központhoz tartozó IP-címek számának módosítása.
* Az erőforráskészlet által használt számítási erőforrás méretének módosítása.
* Az erőforráskészlet által használt számítási erőforrások számának módosítása manuálisan vagy automatikus skálázással.

A portálon háromféle módon szabályozhatja, hogy hány kiszolgáló van az erőforrás-készletekben:

* Egy méretezési művelet a legfelső szintű bevezető panelről. Az előtér-és a munkavégző készleteknél több méretezési konfigurációt is végezhet. Mindegyiket egyetlen műveletként alkalmazza a rendszer.
* Manuális méretezési művelet az egyes erőforráskészlet- **méretezési** panelről, amely a **Beállítások** területen található.
* Automatikus skálázás, amely az egyes erőforráskészlet- **méretezési** panelről állítható be.

Ha a skálázási műveletet a kisegítő panelen szeretné használni, húzza a csúszkát a kívánt mennyiségre, és mentse. Ez a felhasználói felület támogatja a méret módosítását is.  

![Felhasználói felület méretezése][6]

Ha egy adott erőforráskészlet manuális vagy automatikus méretezési képességeit szeretné használni, válassza a **Beállítások**  >  **előtér-készlet**  /  **feldolgozói készletek** lehetőséget. Ezután nyissa meg a módosítani kívánt készletet. Lépjen a **Beállítások**  >  **felskálázás** vagy a **Beállítások** vertikális felskálázás menüpontra  >  . A **kibővíthető** panel lehetővé teszi a példányok mennyiségének vezérlését. A vertikális **felskálázás** lehetővé teszi az erőforrások méretének szabályozását.  

![Méretezési beállítások felhasználói felülete][7]

## <a name="fault-tolerance-considerations"></a>Hibatűrési megfontolások
Beállíthat egy App Service Environment, amely akár 55 teljes számítási erőforrást is használhat. Ezen 55 számítási erőforrások esetében csak 50 használható a munkaterhelések üzemeltetéséhez. Ennek oka kettős. Legalább két előtér-számítási erőforrás van.  Ezzel a beállítással akár 53-et is meghagyhat a munkavégző készlet kiosztásának támogatásához. A hibatűrés biztosítása érdekében további számítási erőforrásra van szükség, amely a következő szabályoknak megfelelően van lefoglalva:

* Minden munkavégző készletnek legalább 1 további számítási erőforrásra van szüksége, amely nem érhető el a munkaterhelések hozzárendeléséhez.
* Ha a munkavégző készletben lévő számítási erőforrások mennyisége egy bizonyos érték fölé kerül, akkor a hibatűréshez egy másik számítási erőforrásra van szükség. Az előtér-készletben nem ez az eset.

Egyetlen munkavégző készleten belül a hibatűrési követelmények a munkavégző készlethez rendelt X-erőforrások adott értékére vonatkoznak:

* Ha az X 2 és 20 közötti értékre esik, a számítási feladatokhoz felhasználható számítási erőforrások mennyisége X-1.
* Ha az X 21 és 40 között van, akkor a számítási feladatokhoz használható számítási erőforrások mennyisége X-2.
* Ha az X 41 és 53 között van, akkor a számítási feladatokhoz használható számítási erőforrások mennyisége X-3.

A minimális helyigény 2 előtér-kiszolgálóval és 2 feldolgozóval rendelkezik.  A fenti utasítások alapján az alábbiakban néhány példát láthat a tisztázásra:  

* Ha 30 feldolgozója van egyetlen készletben, akkor ezek közül 28 felhasználható a számítási feladatok tárolására.
* Ha két feldolgozója van egyetlen készletben, akkor a munkaterhelések üzemeltetéséhez 1 használható.
* Ha 20 feldolgozója van egyetlen készletben, akkor a 19 a munkaterhelések üzemeltetéséhez használható.  
* Ha 21 feldolgozója van egyetlen készletben, akkor a munkaterhelések üzemeltetéséhez továbbra is csak 19 használható.  

A hibatűrési szempont fontos, de meg kell őriznie, ahogy az egyes küszöbértékek fölé skálázást. Ha több kapacitást szeretne felvenni 20 példányból, akkor a 22-es vagy újabb verzióra ugorjon, mert 21 nem növeli a kapacitást. Ugyanez érvényes a 40-nél újabb értékre, ahol a kapacitást hozzáadó következő szám 42.  

## <a name="deleting-an-app-service-environment"></a>App Service Environment törlése
Ha törölni szeretne egy App Service Environment, egyszerűen használja a **Törlés** műveletet a app Service Environment panel tetején. Ha ezt teszi, a rendszer felszólítja, hogy adja meg a App Service Environment nevét annak megerősítéséhez, hogy valóban ezt szeretné. Vegye figyelembe, hogy ha töröl egy App Service Environment, akkor a benne található összes tartalmat is törli.  

![App Service Environment felhasználói felület törlése][9]  

## <a name="getting-started"></a>Első lépések
App Service környezetek megkezdéséhez tekintse meg a [app Service Environment létrehozása](app-service-web-how-to-create-an-app-service-environment.md)című témakört.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md