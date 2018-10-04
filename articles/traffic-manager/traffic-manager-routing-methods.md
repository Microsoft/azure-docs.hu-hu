---
title: Az Azure Traffic Manager - forgalom-útválasztási módszerei |} A Microsoft Docs
description: Ez segít megérteni a különböző forgalom-útválasztási módszer a Traffic Manager által használt cikkei
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jpconnock
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: kumud
ms.openlocfilehash: ab5f6d10e4f9edc4d899e976291482bb3a795c07
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248924"
---
# <a name="traffic-manager-routing-methods"></a>Traffic Manager útválasztási módszerek

Az Azure Traffic Manager támogatja, határozza meg, hogyan irányíthatja a hálózati forgalmat a különböző Szolgáltatásvégpontok hat forgalom-útválasztási módszer. A Traffic Manager bármilyen profilhoz társítva van hozzá minden DNS-lekérdezést kap, a forgalom-útválasztási módszer vonatkozik. A forgalom-útválasztási módszer meghatározza, hogy melyik végponthoz a DNS-választ adott vissza.

Négy forgalom-útválasztási módszer érhető el a Traffic Managerben:

* **[Prioritás](#priority):** kiválasztása **prioritású** Ha szeretne egy elsődleges végpontot használja minden forgalom, és adja meg a biztonsági mentések esetben az elsődleges vagy a biztonsági mentési végpont sem érhető el.
* **[Súlyozott](#weighted):** kiválasztása **súlyozott** szeretné elosztani a forgalmat végpontok között, vagy egyenlő arányban vagy súlyok, amely alapján határozzák meg.
* **[Teljesítmény](#performance):** kiválasztása **teljesítmény** Ha végpontok különböző földrajzi helyeken rendelkezik, és a végfelhasználók számára, hogy a legalacsonyabb hálózati késéssel szempontjából a "legközelebbi" végpont használni szeretne.
* **[Földrajzi](#geographic):** kiválasztása **földrajzi** úgy, hogy a felhasználók mely földrajzi helye alapján meghatározott végpontokhoz (Azure, külső vagy beágyazott) a rendszer átirányítja a DNS-lekérdezés származik. Ez lehetővé teszi a Traffic Manager-ügyfelek olyan forgatókönyvekben, ahol, hogy a felhasználó földrajzi régióban, és azokat, amelyek alapján útválasztás fontos engedélyezéséhez. Ilyenek például az adatok szuverenitását megbízás tartalom és a felhasználói felület honosítása megfelel, és a különféle régiókból származó forgalom mérése.
* **[Típushoz](#multivalue):** kiválasztása **típushoz** a Traffic Manager-profilok, amelyek csak IPv4/IPv6-címek végpontként rendelkezhet. Ehhez a profilhoz fogadásakor egy lekérdezést, a rendszer az összes kifogástalan állapotú végpontok adja vissza.
* **[Alhálózat](#subnet):** kiválasztása **alhálózati** forgalom-útválasztási módszer beállítása a végfelhasználói IP-címtartományok beállítását leképezése egy adott végpontot egy Traffic Manager-profilon belül. Amikor kérelem érkezik, a végpont az egyik rendelendő a kérés IP-forráscím adott vissza. 


Traffic Manager-profilok közé tartozik a végpontonkénti állapotot és a végpont az Automatikus feladatátvétel figyelését. További információkért lásd: [Traffic Manager végpont figyelése](traffic-manager-monitoring.md). Egyetlen Traffic Manager-profil csak egyetlen forgalom-útválasztási módszert használhatja. A profil bármikor választhat a különböző forgalom-útválasztási módszert. Egy percen belül lépnek életbe a módosítások, és üzemkimaradást sem okoz merül fel. Forgalom-útválasztási módszer kombinálható is beágyazott Traffic Manager-profilok használatával. A beágyazási lehetővé teszi, hogy kifinomult és rugalmas forgalom-útválasztási beállítani, a nagyobb méretű, összetett alkalmazások igényeihez. További információkért lásd: [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Prioritás forgalom-útválasztási módszer

Gyakran egy szervezet szeretné a szolgáltatások megbízhatóságot biztosítanak egy vagy több biztonsági mentési szolgáltatás telepítésével, abban az esetben, ha az elsődleges szolgáltatás leáll. A "Priority" forgalom-útválasztási módszer lehetővé teszi, hogy az Azure-ügyfelek egyszerűen implementálni a feladatátvételi minta.

! [Az azure Traffic Manager-on "Priority" forgalom-útválasztási módszer] [1]

A Traffic Manager-profil Szolgáltatásvégpontok rangsorolt listáját tartalmazza. Alapértelmezés szerint a Traffic Manager minden forgalmat küld az elsődleges (legnagyobb prioritás) végponthoz. Ha az elsődleges végpont nem érhető el, a Traffic Manager a második végpontra irányítja a forgalmat. Ha az elsődleges és másodlagos végpontok nem érhetők el, áramlik a forgalom, a harmadik, és így tovább. A végpont rendelkezésre állását a konfigurált állapota (engedélyezve vagy letiltva), és a folyamatban lévő végpont-monitorozás alapul.

### <a name="configuring-endpoints"></a>Végpontok konfigurálása

Az Azure Resource Manager, a végpontok a "prioritás" tulajdonság használatát végpont prioritásának beállítása. Ez a tulajdonság értéke 1 és 1000 között. Alacsonyabb érték egy nagyobb prioritást jelölnek. Végpontok prioritás értéke nem lehet megosztani. A következő tulajdonság beállítása nem kötelező. Ha nincs megadva, a végpont sorrend alapján alapértelmezett prioritást szolgál.

##<a name = "weighted"></a>Súlyozott forgalom-útválasztási módszer
"Súlyozott" forgalom-útválasztási módszer lehetővé teszi, hogy egyenletesen osztja el a forgalmat, vagy egy előre meghatározott súlyozási használandó.

! [Az azure Traffic Manager "Súlyozott" forgalom-útválasztási módszer] a(z) [2]

Súlyozott forgalom-útválasztási módszer esetében a súlyt rendel a Traffic Manager-profil konfigurációjának a végpontot. A súly egy egész számot 1 és 1000. Ez a paraméter nem kötelező. Ha nincs megadva, a Traffic Manager-példányok "1" alapértelmezett súlyozást használ. A nagyobb súlyt, annál magasabb a prioritás.

Minden egyes megadott DNS-lekérdezés érkezett a Traffic Manager véletlenszerűen választ elérhető végpontot. A valószínűsége annak, a végpont kiválasztása a hozzárendelt összes elérhető végpontok súlyok alapul. Az azonos súly használatával-még akkor is, az adatforgalom eloszlása eredményez az összes végpontok között. Ezekre a végpontokra, a DNS-válaszok ritkább vagy gyakoribb visszaadandó magasabb vagy alacsonyabb súlyok használata a meghatározott végpontokhoz okoz.

A súlyozott mód néhány hasznos forgatókönyveket teszi lehetővé:

* Fokozatos alkalmazásfrissítés: százalékát az új végpontot irányíthatja a forgalmat, és fokozatosan növelje idővel 100 %-a forgalmat.
* Alkalmazás migrálását az Azure-bA:-profil létrehozása az Azure és a külső végpontokat. A végpontok előnye a végpont súlyának módosíthatja.
* Felhőbeli tartalékkapacitás további kapacitás: gyorsan bontsa ki a-felhőbe egy helyi központi mögött egy Traffic Manager-profil helyezésével. A felhőben extra kapacitásra van szüksége, amikor hozzáadása vagy engedélyezze a több végpontot, és adja meg, milyen forgalom részét az egyes végpontok irányul.

Az Azure portal használata mellett súlyok Azure Powershellt, CLI és a REST API-k használatával is beállíthatja.

Fontos megérteni, hogy a DNS-válaszok a rekurzív DNS-kiszolgálók, az ügyfelek által használt DNS-nevek feloldására és az ügyfelek által lettek gyorsítótárazva. A gyorsítótárazás súlyozott forgalom disztribúciók hatással lehetnek. Ha az ügyfelek és a rekurzív DNS-kiszolgálók száma túl nagy, az adatforgalom eloszlása a várt módon működik. Azonban ha az ügyfelek vagy a rekurzív DNS-kiszolgálók száma alacsony, gyorsítótárazást is jelentősen tevékenységdiagramon forgalom elosztása.

Gyakori alkalmazási helyzetek a következők:

* Fejlesztési és tesztelési környezetek
* Alkalmazások – kommunikáció
* Alkalmazásokat célzó egy szűk felhasználói-talál, amelyek egy közös rekurzív DNS-infrastruktúra (például egy proxyn keresztül történő kapcsolódás vállalati alkalmazottak)

Az összes, DNS-alapú forgalom útválasztási rendszeren, nem csak az Azure Traffic Manager gyakoriak a gyorsítótárazási DNS következmények. Bizonyos esetekben explicit módon a a DNS-gyorsítótár kiürítése előfordulhat, hogy adja meg a probléma megoldásához. Más esetekben egy alternatív forgalom-útválasztási módszer megfelelőbb lehet.

## <a name = "performance"></a>Teljesítmény-forgalom-útválasztási módszer

Az egész világon üzembe helyezése a végpontok két vagy több helyen irányítaná a forgalmat a helyre, '' Önhöz legközelebbi számos alkalmazás válaszkészségét növelheti. A "Teljesítmény" forgalom-útválasztási módszer biztosítja ezt a lehetőséget.

! [Az azure Traffic Manager "Teljesítmény" forgalom-útválasztási módszer] [3]

A "legközelebbi" végpont nem feltétlenül legközelebbi mért földrajzi távolság. Ehelyett a "Teljesítmény" forgalom-útválasztási módszer alapján lehetet nyilvántartani hálózati késés a legközelebbi végpontot határozza meg. A TRAFFIC Manager egy internetes késés tábla nyomon követéséhez az üzenetváltási idő közötti IP-címtartományok és minden egyes Azure-adatközpont tárolja.

A TRAFFIC Manager megkeresi a beérkező DNS-kérelem az internetes késés táblázatban forrás IP-címét. A TRAFFIC Manager majd úgy dönt, elérhető végpontot, amely adott IP-címtartomány a legkisebb késéssel rendelkezik, és adja vissza, hogy a végpont a DNS-válaszban lévő Azure-adatközpontban.

A [Traffic Manager működése](traffic-manager-how-it-works.md), a Traffic Manager nem DNS-lekérdezéseket közvetlenül az ügyfelektől fogadott adatok. Inkább DNS-lekérdezések származnak a rekurzív DNS-szolgáltatás, hogy az ügyfelek használatára vannak konfigurálva. Ezért az IP-cím segítségével meghatározhatja a legközelebbi"végpontja nem az ügyfél IP-címet, de a rekurzív DNS szolgáltatás IP-címe. A gyakorlatban az IP-címet az ügyfél egy jó proxy.


A TRAFFIC Manager bevezetéséhez rendszeresen frissíti az internetes késés táblázat változásainak a globális internetes és az új Azure-régió. Azonban alkalmazásteljesítmény alapján változik a terhelés valós idejű változata létezik az interneten keresztül. Teljesítmény forgalom-útválasztási nem figyeli egy adott szolgáltatásvégpont terhelését. Azonban ha egy végpont elérhetetlenné válik, a Traffic Manager foglalja bele DNS-lekérdezési válaszadás felgyorsítása érdekében.


Tudnivalók:

* Ha a profilt tartalmaz ugyanazon Azure-régióban több végpontot, majd a Traffic Manager elosztja a forgalmat egyenlően a az adott régióban elérhető végpontok között. Ha inkább egy másik forgalomelosztás egy adott régión belül, akkor használhatja [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md).
* Az összes engedélyezett végpontok legközelebbi Azure-régióban állapotromlást, ha a Traffic Manager forgalom áthelyezi a végpontok a következő legközelebbi Azure-régióban. Ha szeretne egy előnyben részesített feladatátvételi definiálják, [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md).
* A külső végpontokat vagy a beágyazott végpontokat a teljesítmény forgalom-útválasztási módszer használatakor szüksége ezekre a végpontokra helyének megadásához. Válassza ki a legközelebb áll az üzembe helyezés az Azure-régióban. Ezeken a helyeken az értékeket az internetes késés tábla által támogatott.
* Az algoritmus, amely kiválasztja a végpont a determinisztikus. Ismétlődő DNS-lekérdezéseket az ugyanazon ügyféltől érkező egyazon végpont irányítja. Általában az ügyfelek különböző rekurzív DNS-kiszolgálók utazás közben használt. Az ügyfél lehet, hogy átirányíthatók egy másik végpontot. Útválasztás is hatással lehet az internetes késés tábla frissítéseit. Ezért a teljesítmény forgalom-útválasztási módszer nem garantálja, hogy egy ügyfél mindig van irányítva az egyazon végpont.
* Amikor az internetes késés tábla változik, Észreveheti, hogy egyes ügyfelek egy másik végpontra irányítja. Ez a változás útválasztási el pontosabb aktuális késési adatok alapján. Ezek a frissítések elengedhetetlen a teljesítmény forgalom-útválasztási pontosságát, folyamatosan fejlődik, az interneten.

## <a name = "geographic"></a>Földrajzi forgalom-útválasztási módszer

Traffic Manager-profilok konfigurálható a földrajzi útválasztási mód használatára, hogy a felhasználók vannak bizonyos végpontokra irányítva (Azure-ban, külső vagy beágyazott) alapú mely földrajzi helye határozza meg a DNS-lekérdezés származik. Ez lehetővé teszi a Traffic Manager-ügyfelek olyan forgatókönyvekben, ahol, hogy a felhasználó földrajzi régióban, és azokat, amelyek alapján útválasztás fontos engedélyezéséhez. Ilyenek például az adatok szuverenitását megbízás tartalom és a felhasználói felület honosítása megfelel, és a különféle régiókból származó forgalom mérése.
Ha egy profil földrajzi útválasztásra van konfigurálva, minden végpont társított, hogy a profil kell rendelkezik egy földrajzi helyhez rendelve. Földrajzi régió következő részletességi lehet 
- Globális – bármelyik régióban
- Regionális csoportosítás – például Afrikában, Közel-Kelet, Ausztrália/csendes-óceáni stb. 
- Ország vagy régió – például Írországban Perui, Hongkong KKT stb. 
- Állam/megye – például az USA-Kalifornia, Ausztrália-Queensland, Kanada-Alberta stb. (Megjegyzés: a részletesség szintje csak a állapotok támogatott / tartományok, Ausztrália, Kanadában és USA).

Ha egy régió vagy egy régióban van rendelve egy végpontot, csak az adott végpontra ezekben a régiókban érkező kérelmeket legyen irányítva. A TRAFFIC Manager meghatározni azt a régiót, amelyből egy felhasználó kérdezi le, a – általában ez a felhasználó nevében a lekérdezés végrehajtása a helyi DNS-feloldó IP-címét a DNS-lekérdezés forrás IP-címét használja.  

![Az Azure Traffic Manager az "Földrajzi" forgalom-útválasztási módszer](./media/traffic-manager-routing-methods/geographic.png)

A TRAFFIC Manager beolvassa a DNS-lekérdezés forrás IP-címét, és úgy dönt, hogy melyik származó van a földrajzi régióban. Ezután úgy tűnik, hogy van-e egy végpontot, amely rendelkezik-e rendelve a földrajzi régióban. A granularitási legalacsonyabb (állam/megye ahol támogatott, ellenkező esetben az ország/régió szintjén) elindítja a Keresés és a legmagasabb szintű, amely legfeljebb egészen **világ**. Az első egyezés található e bejárási használatával elsődlegesként lett megjelölve a végponthoz való visszatéréshez a lekérdezésekre adott válaszok. Egy beágyazott típusú végpont, a gyermek-profilon belül a végpont egyezéseit ad vissza, ha az útválasztási módszer alapján. Ez a viselkedés alkalmazhatók a következő szempontokat:

- A földrajzi régióban is le lehet képezni csak egy végpontot a Traffic Manager-profil földrajzi útválasztási az útválasztási típus esetén. Ez biztosítja, hogy az Útválasztás, a felhasználók a determinisztikus, és ügyfelek egyértelmű földrajzi határokon olyan esetekben is engedélyezheti.
- Ha egy felhasználó régió két különböző végpontok földrajzi hozzárendelés származnak, a Traffic Manager választja ki a végpont a legalacsonyabb granularitással, és nem veszi figyelembe a érkező kérések útválasztására régió a másik végpontnak. Például vegyünk két végpontot - Endpoint1 és típusú Endpoint2 földrajzi útválasztási típus egy profilt. Endpoint1 Írország érkező forgalom fogadására van konfigurálva, és típusú Endpoint2 Európa érkező forgalom fogadására van konfigurálva. Ha egy kérés Írország származik, mindig átirányítja Endpoint1.
- Egy régióban is le lehet képezni csak egy végpontot, mivel a Traffic Manager adja vissza, függetlenül attól, hogy a végpont kifogástalan-e.

    >[!IMPORTANT]
    >Erősen ajánlott, hogy a földrajzi útválasztási mód használó ügyfelek társítsa a beágyazott típusú végpontok, amely rendelkezik alárendelt profilokat tartalmazó belül legalább két végpontot.
- Ha egy végpont egyezést talál, és, hogy a végpont a **leállítva** állapotában a Traffic Manager NODATA választ adja vissza. Ebben az esetben nincs további keresések végzett alakult a földrajzi hierarchia. Ez a viselkedés akkor is beágyazott végpont esetében alkalmazható, ha a gyermek-profil van a **leállítva** vagy **letiltott** állapota.
- Ha a végpont jeleníti meg egy **letiltott** állapotát, nem fog szerepelni a folyamat megfelelő régióban. Ez a viselkedés akkor is beágyazott végpont esetében alkalmazható, ha a végpont van a **letiltott** állapota.
- Ha egy lekérdezést, amely nincs megfeleltetés rendelkezik az adott profilhoz földrajzi régió származik, a Traffic Manager NODATA választ adja vissza. Ezért erősen ajánlott, hogy ügyfeleink használja egy végpontot, ideális típusú, beágyazott legalább két végpontot, és a régió a gyermek-profilon belül a földrajzi útválasztásának **világ** rendelve. Ez is biztosítja, hogy bármilyen IP-címek, amelyek nem felelnek meg a régiót kezeli.

A [Traffic Manager működése](traffic-manager-how-it-works.md), a Traffic Manager nem DNS-lekérdezéseket közvetlenül az ügyfelektől fogadott adatok. Inkább DNS-lekérdezések származnak a rekurzív DNS-szolgáltatás, hogy az ügyfelek használatára vannak konfigurálva. Ezért az IP-cím segítségével meghatározhatja a régió nem az ügyfél IP-cím, de a rekurzív DNS szolgáltatás IP-címét. A gyakorlatban az IP-címet az ügyfél egy jó proxy.

## <a name = "multivalue"></a>Többértékű forgalom-útválasztási módszer
A **típushoz** forgalom-útválasztási módszer lehetővé teszi, hogy több kifogástalan állapotú végpontok kérjen a DNS-lekérdezés egyetlen válasz. Ez lehetővé teszi a hívónak ehhez ügyféloldali újrapróbálkozások más végpontokkal rendelkező esetén a visszaadott végpont éppen nem válaszol. Ez a minta egy szolgáltatás rendelkezésre állásának növelése és a társított egy megfelelően működő végpont beszerzése egy új DNS-lekérdezést a késés csökkentése érdekében. Többértékű esetén használt útválasztási módszer csak akkor, ha a "Külső" típusú végpontok és a rendszer által megadott IPv4 vagy IPv6-címek működik. Ehhez a profilhoz fogadásakor egy lekérdezést, kifogástalan állapotú végpontok összes adja vissza, és vonatkoznak konfigurálható visszatérési maximális számát.

## <a name = "subnet"></a>Alhálózat forgalom-útválasztási módszer
A **alhálózati** forgalom-útválasztási módszer lehetővé teszi a felhasználói IP-címtartományok készletét leképezheti-profilban meghatározott végpontokhoz. Ezt követően a Traffic Manager kap egy DNS-lekérdezést a profilhoz, ha azt vizsgálata a forrás (a legtöbb esetben ez lesz a DNS-feloldási a hívó által használt kimenő IP-címe) a kérés IP-címét határozza meg, melyik végponthoz van leképezve, és a t adja vissza hat végpont a lekérdezésekre adott válaszok. Az IP-cím rendelhető hozzá egy végpontot a CIDR-tartományt (például 1.2.3.0/24) vagy egy címtartományt (pl. 1.2.3.4-5.6.7.8) formájában adható meg. Végponthoz társított IP-címtartományokkal kell, hogy a profilon belül egyedinek kell lennie, és a egy átfedésben van egy másik végpont IP-címkészlet nem rendelkezhet ugyanazzal a profillal.
Ha vannak olyan végpontok, amelyhez az IP-cím is le lehet képezni, a Traffic Manager NODATA választ küld. Ezért erősen ajánlott minden lehetséges IP-címtartományok vannak megadva, a végpontok közötti biztosítása.
Útválasztási alhálózat segítségével a felhasználók számára egy adott IP-címtér csatlakozik egy másik felhasználói élményt nyújthat. Például alhálózat útválasztási révén ügyfél teheti átirányíthatók egy másik végpontot a vállalati office érkező kérelmek ahol, előfordulhat, hogy tesztelni egy alkalmazás csak belső verzióját. Egy másik helyzet lehet, hogy szeretné-e egy másik felhasználói élményt nyújtson az adott szolgáltatótól (például felhasználók általi letiltása adott Internetszolgáltató) csatlakozó felhasználók.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozhat létre magas rendelkezésre állású alkalmazásokat segítségével [Traffic Manager végpont figyelése](traffic-manager-monitoring.md)




