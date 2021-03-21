---
title: Hibaelhárítási útmutató – Azure DNS
description: Ebben a képzési tervben megismerheti a Azure DNS gyakori problémáinak elhárítását
services: dns
author: rohinkoul
ms.service: dns
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: fae63c61949302e25c9dee2899577fa4f0d2a975
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965578"
---
# <a name="azure-dns-troubleshooting-guide"></a>Azure DNS hibaelhárítási útmutató

Ez a cikk a gyakori Azure DNS kérdésekre vonatkozó hibaelhárítási információkat tartalmaz.

Ha ezek a lépések nem oldják meg a problémát, megkeresheti vagy közzéteheti a problémát a [Microsoft Q&a közösségi támogatással kapcsolatos kérdéseit](/answers/topics/azure-virtual-network.html)is. Vagy egy Azure-támogatási kérést is megnyithat.


## <a name="i-cant-create-a-dns-zone"></a>Nem tudok DNS-zónát létrehozni

A leggyakoribb hibák elhárításához próbálja ki az alábbi lépéseket:

1.  A hiba okának megállapításához tekintse át a Azure DNS naplókat.
2.  Minden DNS-zónanévnek egyedinek kell lennie a saját erőforráscsoportján belül. Ez azt is megteheti, hogy két azonos nevű DNS-zóna nem tud megosztani egy erőforráscsoportot. Próbáljon meg eltérő zónanevet vagy erőforráscsoportot használni.
3.  Előfordulhat, hogy megjelenik az „Elérte vagy túllépte a zónák maximális számát az előfizetésben ({előfizetés azonosítója})” hibaüzenet. Használjon másik Azure-előfizetést, töröljön néhány zónát, vagy vegye fel a kapcsolatot az Azure ügyfélszolgálatával az előfizetésre vonatkozó korlát megemeléséhez.
4.  „A(z) {zónanév} zóna nem érhető el” hibaüzenet is megjelenhet. Ez a hiba azt jelzi, hogy az Azure DNS nem tudott névkiszolgálót lefoglalni ehhez a DNS-zónához. Próbáljon meg egy másik zónanevet használni. Vagy ha Ön a tartománynév tulajdonosa, vegye fel a kapcsolatot az Azure ügyfélszolgálatával a névkiszolgálók lefoglalásához.


### <a name="recommended-articles"></a>Ajánlott cikkek

* [DNS-zónák és -rekordok](dns-zones-records.md)
* [DNS-zóna létrehozása](./dns-getstarted-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Nem tudok létrehozni egy DNS-rekordot

A leggyakoribb hibák elhárításához próbálja ki az alábbi lépéseket:

1.  A hiba okának megállapításához tekintse át a Azure DNS naplókat.
2.  A rekordhalmaz már létezik?  Az Azure DNS a rekordokat *rekordhalmazok* használatával kezeli, amelyek ugyanazzal a névvel és típussal rendelkező rekordok gyűjteményei. Ha egy ugyanolyan nevű és típusú rekord már létezik, akkor egy másik ugyanilyen rekord hozzáadásához szerkesztenie kell a meglévő rekordhalmazt.
3.  A DNS-zóna legfelső pontján (a zóna „gyökerén”) próbál rekordot létrehozni? Ha igen, az általános egyezmény a DNS rendszerben a „@” karakter használata a rekord neveként. Azt is vegye figyelembe, hogy a DNS-szabványok nem engedélyezik a CNAME-rekordokat a zóna csúcsán.
4.  CNAME-ütközést tapasztal?  A DNS-szabványok nem engedélyezik, hogy a CNAME rekord ugyanazzal a névvel legyen ellátva, mint bármely más típusú rekord. Ha rendelkezik egy meglévő CNAME-mel, nem tud egy másik típusú, de ugyanolyan nevű rekordot létrehozni.  A CNAME létrehozása szintén sikertelen lesz, ha a neve megegyezik egy más típusú, már létező rekord nevével. Szüntesse meg az ütközést a másik rekord eltávolításával vagy egy eltérő rekordnév beállításával.
5.  Elérte a DNS-zónában engedélyezett rekordhalmazok számának korlátját? A rekordhalmazok aktuális száma és legfelső korlátja az Azure Portalon látható, a zóna „Tulajdonságainál”. Ha elérte ezt a korlátot, töröljön néhány rekordhalmazt, vagy vegye fel a kapcsolatot az Azure támogatási szolgálatával, hogy növelje a rekord beállított korlátját ehhez a zónához, és próbálkozzon újra. 


### <a name="recommended-articles"></a>Ajánlott cikkek

* [DNS-zónák és -rekordok](dns-zones-records.md)
* [DNS-zóna létrehozása](./dns-getstarted-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Nem tudom feloldani a DNS-rekordomat

A DNS-névfeloldás egy többlépéses folyamat, amely több okból is meghiúsulhat. Az alábbi lépések segítenek megvizsgálni, hogy a DNS-feloldás miért sikertelen egy DNS-rekordnál az Azure DNS-ben üzemeltetett zónában.

1.  Győződjön meg róla, hogy a DNS-rekordok helyesen lettek beállítva az Azure DNS-ben. Tekintse át a DNS-rekordokat az Azure Portalon, és győződjön meg róla, hogy a zónanév, a rekordnév és a rekordtípus helyes.
2.  Győződjön meg róla, hogy a DNS-rekordok feloldhatók az Azure DNS névkiszolgálóin.
    - Ha DNS-lekérdezéseket hajt végre a helyi számítógépről, gyorsítótárazott eredményeket láthat, amelyek nem tükrözik a névkiszolgálók aktuális állapotát.  Továbbá a vállalati hálózatok gyakran olyan DNS-proxykiszolgálókat használnak, amelyek megakadályozzák a DNS-lekérdezések adott névkiszolgálókhoz történő továbbítását.  Ha el szeretné kerülni ezeket a problémákat, használjon webes névfeloldási szolgáltatást (ilyen például a [digwebinterface](https://digwebinterface.com)).
    - Mindenképpen adja meg a DNS-zónához tartozó helyes névkiszolgálót, ahogy az Azure Portalon látható.
    - Ellenőrizze a DNS-név (meg kell adnia a teljes nevet, a zónanevet is beleértve) és a rekordtípus helyességét
3.  Győződjön meg róla, hogy a DNS-tartománynév helyesen lett [delegálva az Azure DNS-névkiszolgálókra](dns-domain-delegation.md). [Számos, harmadik féltől származó webhely létezik, amely lehetővé teszi a DNS-delegálás ellenőrzését](https://www.bing.com/search?q=dns+check+tool). Ez egy *zónadelegálási* teszt, így csak a DNS-zónanevét, és nem a teljes rekordnevet kell megadnia.
4.  A fentiek elvégzése után meg kell történnie a DNS-rekord feloldásának. Az ellenőrzéshez ismét használható a [digwebinterface](https://digwebinterface.com), ezúttal az alapértelmezett névkiszolgáló-beállításokkal.


### <a name="recommended-articles"></a>Ajánlott cikkek

* [Tartomány delegálása az Azure DNS-be](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Hogyan adható meg a „service” és a „protocol” egy SRV-rekordnál?

Az Azure DNS DNS-rekordokat és -rekordhalmazokat (amely az ugyanazzal a névvel és típussal rendelkező rekordok gyűjteménye) kezel. Egy SRV-rekordhalmaznál a „service” és a „protocol” a rekordhalmaz nevének részeként adható meg. Az egyéb SRV-paraméterek („priority”, „weight”, „port” és „target”) külön vannak megadva, a rekordhalmaz egyes rekordjainál.

Példák az SRV-rekordnevekre („sip” szolgáltatásnév, „tcp” protokoll):

- \_sip.\_tcp (egy rekordhalmazt hoz létre a zóna legfelső pontján)
- \_sip.\_tcp.sipservice (egy „sipservice” nevű rekordhalmazt hoz létre)

### <a name="recommended-articles"></a>Ajánlott cikkek

* [DNS-zónák és -rekordok](dns-zones-records.md)
* [DNS-rekordhalmazok és -rekordok létrehozása az Azure Portal használatával](./dns-getstarted-portal.md)
* [SRV rekordtípus (Wikipédia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Következő lépések

* Tudnivalók [Azure DNS zónákról és rekordokról](dns-zones-records.md)
* A Azure DNS használatának megkezdéséhez Ismerje meg, hogyan [hozhat létre DNS-zónát](./dns-getstarted-portal.md) , és hogyan [hozhat létre DNS-rekordokat](./dns-getstarted-portal.md).
* Meglévő DNS-zóna áttelepítéséhez tekintse meg [a DNS-zónafájl importálását és exportálását](dns-import-export.md)ismertető témakört.