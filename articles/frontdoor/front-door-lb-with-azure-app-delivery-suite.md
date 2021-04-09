---
title: Azure bejárati ajtó – terheléselosztás az Azure Application Delivery Suite szolgáltatással | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan javasolja az Azure a terheléselosztást az Application Delivery Suite használatával
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 50e047325ad17710794b1640715ab1938373fe85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019463"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Terheléselosztás az Azure alkalmazáskézbesítési csomagjával

## <a name="introduction"></a>Bevezetés
A Microsoft Azure különböző globális és regionális szolgáltatásokat biztosít a hálózati forgalom elosztásának és terheléselosztásának kezeléséhez: 

* Application Gateway
* Front Door 
* Load Balancer  
* Traffic Manager

Az Azure számos régiója és a többtényezős architektúrája mellett ezen szolgáltatások együttes használatával robusztus, méretezhető és nagy teljesítményű alkalmazásokat hozhat létre.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="Application Delivery Suite":::
 
Ezek a szolgáltatások két kategóriába sorolhatók:
1. A **globális terheléselosztási szolgáltatások** , mint például a Traffic Manager és a bejárati ajtó a végfelhasználók által a helyi háttérrendszer, a felhők és a hibrid helyszíni szolgáltatások közötti forgalom elosztását is lehetővé teszi. A globális terheléselosztás a legközelebbi szolgáltatási háttérre irányítja át a forgalmat, és a szolgáltatás megbízhatóságának változásaira a folyamatos rendelkezésre állás és a nagy teljesítmény fenntartása érdekében a felhasználók számára biztosít. 
1. A regionális terheléselosztási **szolgáltatások** , például a terheléselosztó és az Application Gateway lehetővé teszik a virtuális gépek (VM-EK) forgalmának elosztását virtuális hálózatokon (virtuális hálózatok) vagy szolgáltatási végpontokon belül egy adott régióban.

Ha egyesíti ezeket a globális és regionális szolgáltatásokat, az alkalmazása a megbízható és biztonságos végpontok közötti forgalom előnyeit élvezheti, amelyet a végfelhasználók a IaaS, a Péter vagy a helyszíni szolgáltatásoknak küldenek. A következő szakaszban leírjuk ezeket a szolgáltatásokat.

## <a name="global-load-balancing"></a>Globális terheléselosztás
A **Traffic Manager** globális DNS-terheléselosztást biztosít. A bejövő DNS-kérelmeket tekinti át, és kifogástalan háttérrel válaszol, az ügyfél által kiválasztott útválasztási házirend után. Az útválasztási módszerek beállításai a következők:
- * * A teljesítmény-útválasztás a legközelebb álló háttérbe küldi a kérelmeket a lehető legkevesebb késéssel.
- A **prioritási útválasztás** az összes adatforgalmat a háttérbe irányítja, más háttérrel pedig biztonsági másolatként.
- A **súlyozott ciklikus időszeleteléses útválasztás** az egyes háttérekhez rendelt súlyozás alapján osztja el a forgalmat.
- A **földrajzi útválasztás** biztosítja azokat a kérelmeket, amelyek az adott földrajzi régiókból származnak, és ezeket a régiókat leképező háttérrendszer kezeli. (Például a spanyolországi kérelmeket a franciaországi Közép-Azure régióba kell átirányítani)
- Az **alhálózat-útválasztás** lehetővé teszi, hogy az IP-címtartományok a háttérrendszer számára legyenek leképezve, így az adott IP-címekre érkező kéréseket a rendszer elküldi az adott háttérrendszer számára. (Például a vállalati HQ IP-címéről csatlakozó felhasználók különböző webes tartalmakat kapnak, mint az általános felhasználók)

Az ügyfél közvetlenül csatlakozik ehhez a háttérhöz. Az Azure Traffic Manager észleli, ha egy háttér állapota sérült, majd átirányítja az ügyfeleket egy másik, kifogástalan állapotú példányra. A szolgáltatással kapcsolatos további információkért tekintse meg az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) dokumentációját.

Az **Azure bejárati ajtó** dinamikus webhely-gyorsítást (DSA) biztosít, beleértve a globális http-terheléselosztást is.  A bejövő HTTP-kérések útvonalait a legközelebbi szolgáltatási háttér/régió felé irányítja a megadott állomásnév, URL-útvonal és konfigurált szabályok alapján.  
A bejárati ajtó leállítja a HTTP-kérelmeket a Microsoft hálózatának peremén, és aktívan ellenőrzi az alkalmazás vagy az infrastruktúra állapotának vagy késésének változásait.  A bejárati ajtó ekkor mindig a leggyorsabb és az elérhető (kifogástalan) háttér felé irányítja a forgalmat. A szolgáltatással kapcsolatos további információért tekintse meg a bejárati ajtó [útválasztási architektúrájának](front-door-routing-architecture.md) részleteit és a [forgalom útválasztási módszereit](front-door-routing-methods.md) .

## <a name="regional-load-balancing"></a>Regionális terheléselosztás
A Application Gateway az Application Delivery Controller (ADC) szolgáltatást nyújtja, amely különböző, 7. rétegbeli terheléselosztási funkciókat kínál az alkalmazáshoz. Lehetővé teszi az ügyfelek számára a webfarmok hatékonyságának optimalizálását azáltal, hogy kiszervezik a CPU-igényes TLS-lezárást az Application Gateway felé. A további 7. rétegbeli útválasztási képességek a bejövő forgalom ciklikus időszeleteléses eloszlását, a cookie-alapú munkamenet-affinitást, az URL-alapú útválasztást, valamint az egyetlen Application Gateway mögötti több webhely üzemeltetésének lehetőségét is tartalmazzák. Application Gateway konfigurálható internetes végpontként, csak belső végpontként, vagy mindkettő kombinációja. A Application Gateway teljes körűen felügyelt Azure-szolgáltatás, amely biztosítja a méretezhetőséget és a nagyfokú rendelkezésre állást. Diagnosztikai és naplózási képességek széles skáláját biztosítja a jobb kezelhetőség érdekében.

A Load Balancer az Azure SDN stack szerves részét képezi, amely nagy teljesítményű, kis késleltetésű, 4. rétegbeli terheléselosztási szolgáltatásokat biztosít minden UDP-és TCP-protokollhoz. Nyilvános vagy belső elosztott terhelésű végpontokat is beállíthat olyan szabályok definiálásával, amelyek leképezik a bejövő kapcsolatokat a háttérbeli készletekkel. A TCP vagy a HTTPS használatával végzett állapot-ellenőrzéssel a szolgáltatás rendelkezésre állását is kezelheti.

## <a name="choosing-a-global-load-balancer"></a>Globális Load Balancer kiválasztása
Ha globális terheléselosztást választ a Traffic Manager és az Azure között a globális útválasztáshoz, érdemes megfontolnia, hogy mi a hasonló, és miben különbözik a két szolgáltatástól.   Mindkét szolgáltatás biztosít
- **Többszörös földrajzi redundancia:** Ha az egyik régió kikerül a szolgáltatásból, a forgalom zökkenőmentesen irányítja a legközelebbi régiót, az alkalmazás tulajdonosának beavatkozása nélkül.
- **Legközelebbi régió útválasztása:** A rendszer automatikusan átirányítja a forgalmat a legközelebbi régióba

</br>Az alábbi táblázat a Traffic Manager és az Azure bejárati ajtó közötti különbségeket ismerteti:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Bármely protokoll:** Mivel Traffic Manager a DNS-rétegben működik, bármilyen típusú hálózati forgalmat átirányíthat; HTTP, TCP, UDP stb. | **Http-gyorsítás:** A bejárati ajtónál a forgalom a Microsoft hálózatának peremén található. A HTTP/S kérelmek a késés és az átviteli sebesség növelését fogják látni, ami csökkenti a TLS-egyeztetés késését.|
|Helyszíni **Útválasztás:** A DNS-réteg útválasztásával a forgalom mindig pontról pontra kerül.  A fiókirodából a helyszíni adatközpontba történő útválasztás közvetlen elérési utat is igénybe vehet. még a saját hálózatán is a Traffic Manager használatával. | **Független méretezhetőség:** Mivel a bevezető ajtó együttműködik a HTTP-kérelemmel, a különböző URL-címekre irányuló kérések átirányíthatók különböző háttér-/regionális szolgáltatási készletekbe (a-szolgáltatásokra) a szabályok és az alkalmazások által használt szolgáltatások állapota alapján.|
|**Számlázási formátum:** A DNS-alapú számlázás a felhasználók és a több felhasználóval rendelkező szolgáltatások számára is méretezhető, a magasabb szintű használat érdekében. |**Beágyazott biztonság:** A bejárati ajtó lehetővé teszi olyan szabályok használatát, mint például a ráta korlátozása és az IP ACL-nek, hogy a forgalom elérje az alkalmazást. 

</br>Azt javasoljuk, hogy az ügyfelek a http-munkaterheléshez a bejárati ajtóval használható teljesítmény, működőképesség és biztonsági előnyök miatt használják a bejáratot. A Traffic Manager és a bejárati ajtó párhuzamosan használható az alkalmazás összes adatforgalmának kiszolgálására. 

## <a name="building-with-azures-application-delivery-suite"></a>Kiépítés az Azure Application Delivery Suite szolgáltatással 
Javasoljuk, hogy minden webhelyet, API-t és szolgáltatást földrajzilag redundánsan lehessen biztosítani, hogy a lehető legközelebb elérhető legyen a forgalom a felhasználók számára a legközelebbi helyről.  A több terheléselosztási szolgáltatás kombinálásával a megbízhatóság és a teljesítmény maximalizálása érdekében a földrajzi és a zónabeli redundancia kiépítését teszi lehetővé.

Az alábbi ábrán egy példaként ismertetünk egy olyan architektúrát, amely az összes szolgáltatás kombinációját használja egy globális webszolgáltatás létrehozásához. Az építész arra használta Traffic Manager, hogy átirányítsa a forgalmat a fájl-és objektum-kézbesítés globális háttereként. A bejárati ajtó használata közben globálisan átirányíthatja az URL-címeket, amelyek megfelelnek a/Store/*-nak a App Servicera áttelepített szolgáltatásnak. Végül irányítsa az összes többi kérést a regionális Application Gateway átjáróra.

Az IaaS szolgáltatás minden egyes régiójában az alkalmazás fejlesztői úgy döntöttek, hogy a/images/*-mintának megfelelő URL-címek a dedikált virtuális gépekből származnak. A virtuális gépek ezen készlete eltér a webfarm többi részétől.

Emellett a dinamikus tartalmat kiszolgáló alapértelmezett virtuálisgép-készletnek a magas rendelkezésre állású fürtön üzemeltetett háttér-adatbázissal kell kommunikálnia. A teljes telepítés Azure Resource Manageron keresztül van konfigurálva.

Az alábbi ábrán a forgatókönyv architektúrája látható:

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="Application Delivery Suite – részletes architektúra":::

> [!NOTE]
> Ez a példa az Azure által kínált terheléselosztási szolgáltatások számos lehetséges konfigurációjának egyike. A Traffic Manager, a bejárati ajtó, a Application Gateway és a Load Balancer összekeverhető, és a legjobban megfelel a terheléselosztási igényeknek. Ha például a TLS/SSL-kiszervezés vagy a 7. rétegbeli feldolgozás nem szükséges, Load Balancer Application Gateway helyett használható.

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
