---
title: Dinamikus hely gyorsítás Azure CDNon keresztül
description: A Azure CDN támogatja a dinamikus webhely-gyorsítási (DSA) optimalizálást a dinamikus tartalommal rendelkező fájlokhoz.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 5387fdc224cd77ee5273767df5033a51dc27608c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008361"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Dinamikus hely gyorsítás Azure CDNon keresztül

A közösségi média, az elektronikus kereskedelem és a Hyper-személyre szabott webes fejlesztés révén a végfelhasználók számára kiszolgált tartalom gyorsan növekszik, és valós időben jön létre. A felhasználók egy gyors, megbízható és személyre szabott webes élményt várnak, amely független a böngészőtől, a helytől, az eszköztől vagy a hálózattól. Azonban az ilyen tapasztalatokat kihasználó újítások is lelassítják a lapok letöltését, és a fogyasztói élmény minőségét kockáztatjuk. 

A standard Content Delivery Network (CDN) funkció lehetővé teszi a fájlok a végfelhasználók számára történő gyorsítótárazását a statikus fájlok továbbításának felgyorsításához. A dinamikus webalkalmazások esetében azonban a tartalom az Edge-helyekre való gyorsítótárazása nem lehetséges, mert a kiszolgáló a felhasználói viselkedésre válaszul hozza létre a tartalmat. Az ilyen tartalmak továbbításának felgyorsítása bonyolultabb, mint a hagyományos peremhálózat-gyorsítótárazás, és egy teljes körű megoldást igényel, amely az összes elemet a kezdetektől fogva a teljes adatelérési út mentén tökéletesen lehangolja. Az Azure CDN Dynamic site Acceleration (DSA) optimalizálásával a dinamikus tartalommal rendelkező weblapok teljesítménye mérhetővé fejlődött.

A Akamai-ből és a **Azure CDN-ből származó** **Azure CDN** a DSA-optimalizálást a végpontok létrehozásakor a menüre **optimalizált** menüben biztosítjuk. A Microsoft által kínált dinamikus webhely-gyorsítás az Azure-beli [bejárati ajtó szolgáltatáson](../frontdoor/front-door-overview.md)keresztül érhető el.

> [!Important]
> A **Akamai** -profilokból való Azure CDN esetében lehetősége van egy CDN-végpont optimalizálásának módosítására a létrehozása után.
>   
> A **Verizon Azure CDN** típusú profilok esetén nem módosíthatja a CDN-végpont optimalizálását a végpont létrehozása után.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>CDN-végpont konfigurációja a dinamikus fájlok továbbításának felgyorsításához

Ha egy CDN-végpontot úgy szeretne konfigurálni, hogy optimalizálja a dinamikus fájlok kézbesítését, használhatja a Azure Portal, a REST API-kat vagy az ügyfél-SDK-k bármelyikét, hogy ugyanezt programozott módon végezze el. 

**CDN-végpont konfigurálása a DSA-optimalizáláshoz a Azure Portal használatával:**

1. A **CDN-profil** lapon válassza a **végpont** lehetőséget.

   ![Új CDN-végpont hozzáadása](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Megjelenik a **Végpont hozzáadása** panel.

2. **A rendszerre optimalizált** területen válassza a **dinamikus hely gyorsítás** lehetőséget.

    ![Új CDN-végpont létrehozása a DSA-vel](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. A mintavételi **útvonalhoz** adjon meg egy érvényes elérési utat a fájlhoz.

    A mintavételi útvonal a DSA-re jellemző szolgáltatás, és a létrehozáshoz érvényes elérési út szükséges. A DSA egy kisméretű mintavételi *elérésiút* -fájlt használ a forrás-kiszolgálón, hogy optimalizálja a CDN hálózati útválasztási konfigurációit. A mintavételi útvonal fájljában letöltheti és feltöltheti a fájlt a webhelyére, vagy használhat egy olyan meglévő eszközt, amely körülbelül 10 KB méretű.

4. Adja meg a többi szükséges végponti beállítást (további információért lásd: [új CDN-végpont létrehozása](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), majd válassza a **Hozzáadás** lehetőséget.

   A CDN-végpont létrehozása után a rendszer az összes olyan fájlhoz alkalmazza a DSA-optimalizálást, amely megfelel bizonyos feltételeknek. 


**Meglévő végpont konfigurálása a DSA-hoz (Azure CDN csak Akamai-profilokból):**

1. A **CDN-profil** lapon válassza ki a módosítani kívánt végpontot.

2. A bal oldali panelen válassza az **optimalizálás** lehetőséget. 

   Megjelenik az **optimalizálás** lap.

3. **A rendszerre optimalizált** területen válassza a **dinamikus hely gyorsítás** lehetőséget, majd kattintson a **Mentés** gombra.

> [!Note]
> A DSA extra díjat számít fel. További információ: [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>DSA-optimalizálás Azure CDN használatával

A dinamikus hely gyorsításának Azure CDN felgyorsítja a dinamikus eszközök kézbesítését az alábbi módszerek használatával:

-   [Útvonal-optimalizálás](#route-optimization)
-   [TCP-optimalizálások](#tcp-optimizations)
-   [Objektum kiprefektusa (csak a Akamai Azure CDN)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptív képtömörítés (Azure CDN csak Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Útvonal-optimalizálás

Az útvonal-optimalizálás azért fontos, mert az Internet dinamikus hely, ahol a forgalom és az átmeneti kimaradások folyamatosan változnak a hálózati topológiában. A Border Gateway Protocol (BGP) az Internet útválasztási protokollja, de lehet, hogy gyorsabb útvonalak találhatók a közvetítői pontok közötti (PoP-) kiszolgálókon keresztül. 

Az útvonal-optimalizálás kiválasztja a forrás optimális elérési útját, hogy a helyek folyamatosan elérhetők legyenek, és a dinamikus tartalmat a lehető leggyorsabb és legmegbízhatóbb útvonalon keresztül továbbítsák a végfelhasználók számára. 

A Akamai-hálózat technikákat használ a valós idejű adatok gyűjtésére és a különböző elérési utak összehasonlítására a Akamai-kiszolgáló különböző csomópontjain, valamint az Open interneten az alapértelmezett BGP-útvonalon, amely meghatározza a forrás és a CDN Edge közötti leggyorsabb útvonalat. Ezek a technikák elkerülik az internetes torlódási pontokat és a hosszú útvonalakat. 

Hasonlóképpen, a Verizon-hálózat a többhelyes DNS, a nagy kapacitású támogatási durranás és az állapot-ellenőrzések kombinációját használja, hogy meghatározza a legjobb átjárókat az ügyfél és a forrás közötti legjobb adattovábbításhoz.
 
Ennek eredményeképpen a teljes dinamikus és tranzakciós tartalmak gyorsabban és megbízhatóbban jutnak el a végfelhasználók számára, még akkor is, ha nem gyorsítótárazható. 

### <a name="tcp-optimizations"></a>TCP-optimalizálások

A Transmission Control Protocol (TCP) az IP-hálózaton lévő alkalmazások közötti információk továbbítására használt Internet Protocol Suite szabványa.  Alapértelmezés szerint több visszamenőleges kérelem szükséges a TCP-kapcsolatok beállításához, valamint a hálózati torlódások elkerülésének korlátaihoz, ami a nagy hatékonyságot eredményezi. A **Akamai Azure CDN** a következő három területen optimalizálja ezt a problémát: 

 - [A TCP lassú indításának megszüntetése](#eliminating-tcp-slow-start)
 - [Állandó kapcsolatok kihasználása](#leveraging-persistent-connections)
 - [TCP-csomagok paramétereinek hangolása](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>A TCP lassú indításának megszüntetése

A TCP *lassú indítás* a TCP protokoll algoritmusa, amely megakadályozza a hálózati torlódást a hálózaton keresztül továbbított adatmennyiség korlátozásával. A kis torlódások ablakának mérete a küldő és a fogadó között a maximális érték elérésekor vagy a csomagok elvesztésének észlelése után indul el.

 A **Akamai** és **a Azure CDN a Verizon** -profilokból egyaránt Azure CDN a következő három lépéssel megszünteti a TCP lassú indítását:

1. Az állapot-és sávszélesség-figyelés az Edge PoP-kiszolgálók közötti kapcsolatok sávszélességének mérésére szolgál.
    
2. A metrikák az Edge PoP-kiszolgálók között vannak megosztva, hogy mindegyik kiszolgáló tisztában legyen a hálózati feltételekkel és a többi durranás kiszolgáló állapotával.  
    
3. A CDN Edge-kiszolgálók feltételezik néhány továbbítási paramétert, például azt, hogy mi az optimális ablakméret, ha más CDN Edge-kiszolgálókkal kommunikál a közelségében. Ez a lépés azt jelenti, hogy a kezdeti torlódási ablak mérete növelhető, ha a CDN Edge-kiszolgálók közötti kapcsolat állapota nagyobb adatátviteli kapacitást biztosít.  

#### <a name="leveraging-persistent-connections"></a>Állandó kapcsolatok kihasználása

A CDN használatával kevesebb egyedi gép csatlakozik a forrás-kiszolgálóhoz közvetlenül a forráshoz csatlakozó felhasználókhoz képest. A Azure CDN a felhasználói kérések összevonásával kevesebb kapcsolatot létesít a forrással.

Ahogy korábban említettük, több kézfogási kérelemre van szükség a TCP-kapcsolat létrehozásához. A HTTP-fejléc által megvalósított állandó kapcsolatok, a `Keep-Alive` meglévő TCP-kapcsolatok újbóli használata több HTTP-kérelem esetében, az átállási idő és a továbbítás felgyorsításának megtakarítása érdekében. 

A **Verizon Azure CDN** is rendszeres életben tartási csomagokat küld a TCP-kapcsolaton keresztül, hogy megakadályozza a nyitott kapcsolat lezárását.

#### <a name="tuning-tcp-packet-parameters"></a>TCP-csomagok paramétereinek hangolása

**Azure CDN a Akamai** a kiszolgáló és a kiszolgáló közötti kapcsolatokra vonatkozó paramétereket hangolja le, és csökkenti a helyen beágyazott tartalom lekéréséhez szükséges hosszú távú útvonalak mennyiségét a következő módszerek használatával:

- Megnövelheti a kezdeti torlódási időszakot, így több csomag is elküldhető, és nem kell nyugtát várnia.
- A kezdeti újraküldési időkorlát csökkentése, hogy a rendszer elveszítse az adatvesztést, és gyorsabban történjen az újratovábbítás.
- A minimális és a maximális újrapróbálkozási időkorlát csökkentése a várakozási idő csökkentéséhez, mielőtt a csomagok elvesztek az átvitel során.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Objektum kiprefektusa (csak a Akamai Azure CDN)

A legtöbb webhely egy HTML-oldalból áll, amely különböző más erőforrásokra, például képekre és parancsfájlokra hivatkozik. Amikor egy ügyfél egy weblapon kér le egy weblapot, a böngésző először letölti és elemzi a HTML-objektumot, majd további kérelmeket tesz elérhetővé az oldal teljes betöltéséhez szükséges csatolt eszközökhöz. 

A *prefektus* egy olyan módszer, amellyel a HTML-oldalba ágyazott képeket és parancsfájlokat beolvashatja, miközben a HTML-t a böngésző kézbesíti, és még a böngésző is megkéri ezeket az objektumokat. 

Ha bekapcsolja a kiinduló lehetőséget, amikor a CDN a HTML-alapú alapoldalt szolgálja ki az ügyfél böngészőjében, a CDN elemzi a HTML-fájlt, és további kérelmeket készít minden kapcsolódó erőforráshoz, és tárolja azt a gyorsítótárában. Ha az ügyfél a kapcsolódó eszközökre vonatkozó kérelmeket küld, a CDN Edge-kiszolgáló már rendelkezik a kért objektumokkal, és azonnal kiszolgálhatja őket, anélkül, hogy a forrást oda kellene kapcsolni. Ez az optimalizálás a gyorsítótárazható és nem gyorsítótárazható tartalmat is előnyben részesül.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptív képtömörítés (Azure CDN csak Akamai)

Bizonyos eszközök, különösen a mobilok, időről időre lassabb hálózati sebességgel rendelkeznek. Ezekben az esetekben előnyösebb, hogy a felhasználó gyorsabban kapjon kisebb képeket a weblapján ahelyett, hogy hosszú időt kellene várnia a teljes felbontású képekre.

Ez a szolgáltatás automatikusan figyeli a hálózati minőséget, és szabványos JPEG-tömörítési módszereket alkalmaz, ha a hálózati sebesség lassabb a szállítási idő javítása érdekében.

Adaptív képtömörítés | Fájlkiterjesztések  
--- | ---  
JPEG-tömörítés | . jpg,. jpeg,. JPE,. sablon,. jgig,. JGI

## <a name="caching"></a>Gyorsítótárazás

A DSA használatával a gyorsítótárazás alapértelmezés szerint ki van kapcsolva a CDN-ben, még akkor is, ha a válaszban szerepel a forrás `Cache-Control` vagy a `Expires` fejléc. A DSA általában olyan dinamikus eszközökhöz használatos, amelyeket nem szabad gyorsítótárazni, mivel azok egyediek az egyes ügyfelek számára. A gyorsítótárazás megszüntetheti ezt a viselkedést.

Ha olyan webhellyel rendelkezik, amely a statikus és a dinamikus eszközök kombinációját használja, a legjobb megoldás a legjobb teljesítmény érdekében. 

A **Verizon** és a Akamai-profiloktól **Azure CDN standard** szintű Azure CDN esetében a gyorsítótárazási [szabályok](cdn-caching-rules.md)használatával bekapcsolhatja az adott DSA-végpontok gyorsítótárazását.

A gyorsítótárazási szabályok elérése:

1. A **CDN-profil** lap beállítások területén válassza a **gyorsítótárazási szabályok** elemet.  
    
    ![CDN-gyorsítótárszabályok gomb](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Megnyílik a **gyorsítótárazási szabályok** lap.

2. Hozzon létre egy globális vagy egyéni gyorsítótárazási szabályt a DSA-végpont gyorsítótárazásának bekapcsolásához. 

Csak a Verizon-profilokból **származó Azure CDN Premium** esetén a [szabályok motor](./cdn-verizon-premium-rules-engine.md)használatával kapcsolja be a gyorsítótárazást adott DSA-végpontokra. A létrehozott szabályok csak a DSA-ra optimalizált profilhoz tartozó végpontokat érintik. 

A szabályok motor elérése:
    
1. A **CDN-profil** lapon válassza a **kezelés** lehetőséget.  
    
    ![CDN-profil kezelése gomb](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Megnyílik a CDN felügyeleti portál.

2. A CDN felügyeleti portálján válassza az **ADN**, majd a **szabályok motor** elemet. 

    ![A DSA szabályainak motorja](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Azt is megteheti, hogy két CDN-végpontot is használhat: egy DSA-val optimalizált végpontot, amely dinamikus eszközöket és egy statikus optimalizálási típussal optimalizált másik végpontot (például általános webes kézbesítés) biztosít a gyorsítótárazható eszközök kézbesítéséhez. Módosítsa a weblap URL-címeit, hogy közvetlenül a használni kívánt CDN-végponton lévő eszközre hivatkozzon. 

Például: `mydynamic.azureedge.net/index.html` egy dinamikus lap, és betöltődik a DSA-végpontból.  A HTML-oldal több olyan statikus objektumra hivatkozik, mint például a JavaScript-kódtárak vagy a statikus CDN-végpontról betöltött képek, például a `mystatic.azureedge.net/banner.jpg` és a `mystatic.azureedge.net/scripts.js` .