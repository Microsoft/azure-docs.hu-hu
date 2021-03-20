---
title: forgalomáttekintő az Azure-ban Traffic Manager
description: Ebből a bevezetőből megtudhatja, hogyan működik a Traffic Manager forgalom nézete.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 01/22/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 66376655c61903761d93ea228c6d72fa05734353
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743049"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager forgalomáttekintő

A Traffic Manager DNS (Tartománynévrendszer) szintű útválasztást biztosít. Ez a szolgáltatás lehetővé teszi, hogy a végfelhasználók a választott útválasztási módszer alapján a megfelelő végpontokra irányítsák a felhasználókat. A forgalomáttekintő Traffic Managert biztosít a felhasználói bázisok (DNS-feloldó részletességi szint) és a forgalmi minta megtekintésével. A forgalomáttekintő engedélyezésekor a rendszer feldolgozza ezeket az információkat, hogy a gyakorlatban hasznosítható elemzéseket biztosítson. 

A forgalomáttekintő használatával a következőket teheti:
- Ismerje meg, hogy a felhasználói bázisok hol találhatók (legfeljebb egy helyi DNS-feloldó szintű részletességgel).
- megtekintheti az ezekből a régiókból származó, az Azure Traffic Manager által kezelt DNS-lekérdezésekben megfigyelt forgalom mennyiségét.
- betekintést nyerhet a felhasználók által tapasztalt jellemző késésre.
- részletesen tekintse át az egyes felhasználói bázisok konkrét forgalmi mintáit azon Azure-régiókba, ahol végpontokkal rendelkezik. 

A forgalomáttekintő segítségével például megtudhatja, hogy mely régiókban nagy mennyiségű forgalom van, de nagyobb késések esetén is csökkenhet. Ezt az információt felhasználva megtervezheti az új Azure-régiók lábnyomának kibővítését. Így a felhasználók alacsonyabb késési élményt nyújtanak.

## <a name="how-traffic-view-works"></a>A forgalomáttekintő működése

Forgalomáttekintő működéséhez tekintse meg a profil utolsó hét napján fogadott bejövő lekérdezéseket. A bejövő lekérdezések adatainál forgalomáttekintő kibontja a DNS-feloldó forrás IP-címét, amelyet a felhasználók helyének jelölésére használtak. Ezek az információk egy DNS-feloldó szintjén vannak csoportosítva, amely felhasználói alapterületeket hoz létre. Traffic Manager karbantartja az IP-címek földrajzi információit. Traffic Manager ezután megtekinti azokat az Azure-régiókat, amelyeken a lekérdezés átirányítja a forgalmat, és létrehoz egy adatforgalmi térképet az adott régiókban lévő felhasználók számára.
 
A következő lépésben a Traffic Manager összekapcsolja a felhasználói alaprégiót az Azure region-hozzárendeléssel a hálózati intelligencia késési tábláival. Ez a táblázat a különböző végfelhasználói hálózatok számára van fenntartva, hogy megértse az adott régiókban lévő felhasználók által az Azure-régiókhoz való csatlakozáskor tapasztalt átlagos késéseket. Ezeket a számításokat ezután a rendszer a helyi DNS-feloldó IP-szinten kombinálja, mielőtt azok bemutatják Önt. Az információkat többféleképpen is felhasználhatja.

A forgalmi nézet adatfrissítésének gyakorisága több belső szolgáltatási változótól függ. Az adatfrissítések azonban 24 óránként egyszer frissülnek.

>[!NOTE]
>Az forgalomáttekintőban leírt késés a végfelhasználó és az Azure-régiók között jellemző késés, amelyhez csatlakoztak, és nem a DNS keresési késése. Forgalomáttekintő a helyi DNS-feloldó és az Azure-régió közötti késés legjobb becslését biztosítja, ha nincs elegendő adat, a visszaadott késés pedig NULL lesz. 

## <a name="visual-overview"></a>Vizuális áttekintés

Amikor megnyitja a Traffic Manager oldal **forgalomáttekintő** szakaszát, egy földrajzi Térkép jelenik meg, amely forgalomáttekintői információk átfedésével rendelkezik. A Térkép információt nyújt a Traffic Manager profiljához tartozó felhasználói bázisról és végpontokról.

![Traffic Manager forgalomáttekintő földrajzi nézet][1]

### <a name="user-base-information"></a>Felhasználói alapinformációk

Azok a helyi DNS-feloldók, amelyekhez a helyadatok elérhetők, megjelennek a térképen. A DNS-feloldó színe azt jelzi, hogy az adott DNS-feloldót használó végfelhasználók milyen átlagos késéssel rendelkeznek a Traffic Manager lekérdezésekhez.

Ha a térképen a DNS-feloldási hely fölé viszi a mutatót, az a következőket jeleníti meg:
- a DNS-feloldó IP-címe
- a Traffic Manager által látott DNS-lekérdezési forgalom mennyisége
- azok a végpontok, amelyeken a DNS-feloldó felé irányuló forgalmat irányították, a végpont és a DNS-feloldó közötti vonalként 
- az adott hely és a végpont közötti átlagos késés, amely a csatlakozáshoz használt vonal színét jelöli.

### <a name="endpoint-information"></a>Végpont adatai

Az Azure-régiók, amelyekben a végpontok találhatók, kék pontként jelennek meg a térképen. Ha a végpont kívül van, és nem rendelkezik Azure-régióval, akkor a Térkép tetején jelennek meg. Válassza ki bármelyik végpontot a különböző helyszínek (a használt DNS-feloldó alapján) megjelenítéséhez, ahonnan a rendszer a forgalmat a végponthoz irányította. A kapcsolatok a végpont és a DNS-feloldó helye közötti vonalként jelennek meg. Az adott párt közötti reprezentatív késésnek megfelelően színesek. Megtekintheti a végpont nevét, valamint azt az Azure-régiót, amelyben a fut. A Traffic Manager profil által átirányított kérelmek teljes mennyisége is megjelenik.


## <a name="tabular-listing-and-raw-data-download"></a>Táblázatos Listázás és nyers adatok letöltése

Az forgalomáttekintő adatokat táblázatos formátumban tekintheti meg Azure Portalban. Minden DNS-feloldási IP-cím/Endpoint pár esetében megjelenik egy bejegyzés, amely a következőket jeleníti meg:

* A DNS-feloldó IP-címe.
* A név.
* Annak az Azure-régiónak a földrajzi helye, amelyben a végpont található (ha elérhető).
* A DNS-feloldó által a végponthoz társított kérelmek mennyisége.
* Az adott DNS-t használó végfelhasználók jellemző késése (ahol elérhető). 

A forgalomáttekintő adatait CSV-fájlként is letöltheti, amely a választott elemzési munkafolyamatok részeként is használható.

## <a name="billing"></a>Számlázás

Forgalomáttekintő használatakor a rendszer az elemzések létrehozásához használt adatpontok száma alapján számláz. Jelenleg az egyetlen adatpont-típust használja a Traffic Manager profiljához kapott lekérdezések. A díjszabással kapcsolatos további információkért látogasson el a [Traffic Manager díjszabási oldalára](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Gyakori kérdések

* [Mit tesz forgalomáttekintő?](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [Milyen előnyökkel jár a forgalomáttekintő használata?](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [Miben különbözik forgalomáttekintő az Azure monitoron keresztül elérhető Traffic Manager metrikákkal?](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [A forgalomáttekintő használja az EDNS-ügyfél alhálózati adatait?](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [Hány napi adatmennyiséget forgalomáttekintő használni?](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [Hogyan kezeli a forgalomáttekintő a külső végpontokat?](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [Engedélyeznie kell a forgalomáttekintő az előfizetésem minden profiljához?](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Hogyan kapcsolható ki a forgalomáttekintő?](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [Hogyan működik forgalomáttekintő számlázás?](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [Traffic Manager működéséről](traffic-manager-overview.md)
- További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)
- Megtudhatja, hogyan [hozhat létre Traffic Manager-profilt](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png