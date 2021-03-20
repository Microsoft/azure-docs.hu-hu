---
title: Irányelvek a megbízható gyűjteményekhez
description: Irányelvek és javaslatok Service Fabric megbízható gyűjtemények Azure Service Fabric alkalmazásban való használatához.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: f12db76f324d07c178b49150d4e574476e7d9929
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784325"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Irányelvek és javaslatok az Azure-Service Fabric megbízható gyűjteményekhez
Ez a szakasz a megbízható állapot-kezelő és a megbízható gyűjtemények használatához nyújt útmutatást. A cél az, hogy segítse a felhasználókat a gyakori buktatók elkerülésében.

Az irányelvek a feltételek *elvégzése*, a *megfontolás*, a *elkerülés* és a *nem* megfelelő egyszerű javaslatok szerint vannak rendszerezve.

* Ne módosítsa az olvasási műveletek által visszaadott egyéni típusú objektumokat (például `TryPeekAsync` vagy `TryGetValueAsync` ). A megbízható gyűjtemények, akárcsak a párhuzamos gyűjtemények, az objektumokra mutató hivatkozást adnak vissza.
* A módosítás előtt készítsen részletesen egy egyéni típusú visszaadott objektumot. Mivel a struktúrák és a beépített típusok értéke a pass-by-Value, nem kell részletes másolatot készítenie rajtuk, hacsak nem tartalmaznak hivatkozással megadott mezőket vagy tulajdonságokat, amelyeket módosítani kíván.
* Ne használja `TimeSpan.MaxValue` az időkorlátot. A holtpontok észleléséhez időtúllépést kell használni.
* Ne használjon tranzakciót a véglegesítés, a megszakított vagy a használat után.
* Ne használjon enumerálást a-ben létrehozott tranzakciós hatókörön kívül.
* Ne hozzon létre tranzakciót egy másik tranzakció `using` utasításán belül, mert a holtpontot eredményezhet.
* Ne hozzon létre megbízható állapotot, `IReliableStateManager.GetOrAddAsync` és használja a megbízható állapotot ugyanabban a tranzakcióban. Ez egy InvalidOperationException eredményez.
* Győződjön meg arról, hogy a `IComparable<TKey>` megvalósítás helyes. A rendszer az `IComparable<TKey>` ellenőrzőpontok és a sorok egyesítésének függőségét veszi igénybe.
* A frissítési zárolást akkor használja, ha olyan elemeket olvas be, amelyekkel frissíteni kívánja azt, hogy megakadályozza a holtpontok bizonyos osztályait.
* Vegye figyelembe, hogy a megbízható gyűjtemények száma nem haladhatja meg a 1000-ot. A megbízható gyűjtemények több elemmel is előnyben részesülnek, kevesebb elemmel.
* Ügyeljen arra, hogy az elemek (például a TKey + TValue megbízható szótárhoz) az 80 kilobájt alatt legyenek: kisebb a jobb. Ez csökkenti a nagyméretű objektum-halom használatának mennyiségét, valamint a lemez és a hálózat i/o-követelményeit. Gyakran csökkenti a duplikált adat replikálását, ha az érték csak egy kis részét frissíti. Ennek a megbízható szótárban való elérésének gyakori módja, ha a sorokat több sorba szeretné bontani.
* Érdemes lehet biztonsági mentési és visszaállítási funkciókat használni a vész-helyreállításhoz.
* A különböző elkülönítési szintek miatt ne keverje az egyentitásos műveleteket és a többentitásos műveleteket (például `GetCountAsync` `CreateEnumerableAsync` ) ugyanabban a tranzakcióban.
* Végezze el a InvalidOperationException kezelését. A rendszer többféle okból is megszakíthatja a felhasználói tranzakciókat. Ha például a megbízható állapot-kezelő módosítja a szerepkört az elsődlegesen, vagy ha egy hosszan futó tranzakció blokkolja a tranzakciós napló csonkítása. Ilyen esetekben előfordulhat, hogy a felhasználó olyan InvalidOperationException kap, amely azt jelzi, hogy a tranzakciójuk már meg van szakítva. Feltételezve, hogy a felhasználó nem kérte a tranzakció megszüntetését, a kivétel kezelésére a legjobb megoldás, ha a tranzakciót el szeretné érni, ellenőrizze, hogy a lemondási token be van-e állítva (vagy a replika szerepköre módosult-e), és ha nem hoz létre új tranzakciót, és próbálkozzon újra.  

A következő szempontokat érdemes figyelembe venni:

* Az alapértelmezett időtúllépés négy másodperc az összes megbízható gyűjtemény API-hoz. A legtöbb felhasználónak az alapértelmezett időtúllépést kell használnia.
* Az alapértelmezett lemondási jogkivonat `CancellationToken.None` minden megbízható gyűjtemény API-ban szerepel.
* A megbízható szótár kulcs Type paraméterének (*TKey*) megfelelően kell megvalósítani a `GetHashCode()` -t és a-t `Equals()` . A kulcsok nem változtathatók meg.
* Ha magas rendelkezésre állást szeretne biztosítani a megbízható gyűjtemények számára, minden szolgáltatásnak rendelkeznie kell legalább egy célként megadott és minimális replikakészlet 3-as mérettel.
* A másodlagos olvasási műveletek a kvórumnak nem véglegesített verziókat is tartalmazhatnak.
  Ez azt jelenti, hogy az egyetlen másodlagosból beolvasott adatok egyik verziója hamis lehet.
  Az Elsődlegesből való beolvasás mindig stabil: soha nem lehet hamisan haladni.
* A megbízható gyűjteményben az alkalmazás által megőrzött adatok biztonsága/adatvédelme a saját döntése, valamint a tárolási felügyelet által biztosított védelem tárgya. I.E. Az operációs rendszer lemezének titkosítása felhasználható az inaktív adatok védelme érdekében.
* `ReliableDictionary` a számbavétel a kulcs alapján rendezett, rendezett adatszerkezetet használ. A számbavétel hatékonyságát a véglegesíti a rendszer egy ideiglenes szórótábla adja hozzá, és később áthelyezi a fő rendezett adatstruktúra utáni ellenőrzőpontra. A hozzáadási/frissítési/törlési lehetőség az o (1) és a legrosszabb esetben a (z) O (log n) esetek legjobb futtatókörnyezetét adja meg, ha a kulcs jelenléte ellenőrzés alatt áll. A beolvasás lehet az O (1) vagy az O (log n), attól függően, hogy a legutóbbi véglegesítés vagy egy régebbi véglegesítés után olvassa-e a rendszer.

## <a name="volatile-reliable-collections"></a>Felejtő megbízható gyűjtemények
Az illékony megbízható gyűjtemények használatának eldöntése során vegye figyelembe a következőket:

* ```ReliableDictionary``` illékony támogatással rendelkezik
* ```ReliableQueue``` illékony támogatással rendelkezik
* ```ReliableConcurrentQueue``` NEM rendelkezik felejtő támogatással
* A megőrzött szolgáltatások nem hozhatók létre illékonyak. A ```HasPersistedState``` jelző módosítása a ```false``` teljes szolgáltatás teljesen új létrehozásához
* A felejtő szolgáltatások nem hozhatók létre. A ```HasPersistedState``` jelző módosítása a ```true``` teljes szolgáltatás teljesen új létrehozásához
* ```HasPersistedState``` a szolgáltatás szintű konfiguráció. Ez azt jelenti, hogy **minden** gyűjteményt megtartanak vagy változékonyak. Az illékony és megőrzött gyűjtemények nem kombinálhatók
* A felejtő partíció Kvórumának elvesztése a teljes adatvesztést eredményezi
* A biztonsági mentés és a visszaállítás nem érhető el a felejtő szolgáltatásokhoz

## <a name="next-steps"></a>Következő lépések
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Az adatkezelés
  * [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
  * [Értesítések](service-fabric-reliable-services-notifications.md)
  * [Szerializálás és frissítés](service-fabric-application-upgrade-data-serialization.md)
  * [Megbízható állapot-kezelő konfigurációja](service-fabric-reliable-services-configuration.md)
* Egyéb
  * [Reliable Services rövid útmutató](service-fabric-reliable-services-quick-start.md)
  * [Fejlesztői referenciák megbízható gyűjteményekhez](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
