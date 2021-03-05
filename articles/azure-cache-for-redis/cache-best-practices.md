---
title: Ajánlott eljárások az Azure Cache for Redis használatához
description: Az ajánlott eljárások követésével megtudhatja, hogyan használhatja hatékonyan az Azure cache-t a Redis.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 9754a043c90c01f889be9639d2d045fb1929de17
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178116"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Ajánlott eljárások az Azure Cache for Redis használatához 
Az ajánlott eljárások követésével maximalizálhatja az Azure cache teljesítményének és költséghatékony felhasználásának hatékonyságát a Redis-példány esetében.

## <a name="configuration-and-concepts"></a>Konfigurálás és fogalmak
 * **Használjon standard vagy prémium szintű üzemi rendszereket.**  Az alapszintű csomag egyetlen csomópontos rendszer, amely nem rendelkezik adatreplikálással, és nem biztosít SLA-t. Ezen kívül használjon legalább C1 szintű gyorsítótárat.  A C0 cache-gyorsítótárak egyszerű fejlesztési és tesztelési forgatókönyvekhez készültek, mivel közös CPU-mag, kevés memória és a "zajos szomszédok" problémái vannak.

 * **Ne feledje, hogy a Redis egy memóriában tárolt adattároló.**  [Ez a cikk az](cache-troubleshoot-data-loss.md) adatvesztést okozó egyes forgatókönyveket ismerteti.

 * Fejlessze a rendszerét úgy, hogy a [javítás és a feladatátvétel miatt](cache-failover.md) **képes legyen kezelni a kapcsolatok visszavert adatait** .

 * **Konfigurálja a [maxmemory fenntartott beállítást](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , hogy javítsa a rendszerrugalmasságot** a memória nyomási körülményei között.  A megfelelő foglalási beállítás különösen fontos a nagy írási terhelésű számítási feladatokhoz, vagy ha nagyobb értékeket (100 KB) tárol a Redis-ben. A gyorsítótár méretének 10%-ában kell kezdődnie, és ha írási – nagy terheléssel rendelkezik, növelje a százalékos arányt.

 * **A Redis kisebb értékekkel működik a legjobban**, ezért érdemes lehet nagyobb mennyiségű kulcsot feldarabolni több kulcsra.  [Ebben a Redis-vitában](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)néhány szempontot figyelembe kell venni, hogy alaposan meg kell fontolnia.  Olvassa el [ezt a cikket](cache-troubleshoot-client.md#large-request-or-response-size) egy olyan problémával kapcsolatban, amelyet nagy értékek okozhatnak.

 * **Keresse meg a gyorsítótár-példányt és az alkalmazást ugyanabban a régióban.**  Ha más régióban lévő gyorsítótárhoz csatlakozik, az jelentősen megnövelheti a késést, így a megoldás megbízhatósága is csökkenhet.  Habár az Azure-on kívülről is csatlakozhat, nem ajánlott *különösen a Redis gyorsítótárként való használatakor*.  Ha a Redis-t csak kulcs/érték tárolóként használja, akkor a késés nem lehet az elsődleges szempont. 

 * **Kapcsolatok újrafelhasználása.**  Az új kapcsolatok létrehozása költséges, és növeli a késést, így a lehető legnagyobb mértékben újrahasznosíthatja a kapcsolatokat. Ha úgy dönt, hogy új kapcsolatokat hoz létre, győződjön meg róla, hogy lezárta a régi kapcsolatokat (még a felügyelt memória nyelvein is, például a .NET vagy a Java esetében).

 * **Konfigurálja az ügyféloldali függvénytárat úgy, hogy legalább 15 másodperces *csatlakozási időkorlátot* használjon**, így a rendszeridőt még nagyobb CPU-feltételek mellett is csatlakozhat.  A kis kapcsolat időtúllépési értéke nem garantálja, hogy a kapcsolat az adott időkereten belül van-e.  Ha valami probléma merül fel (magas szintű ügyfél-CPU, magas kiszolgálói processzor stb.), akkor egy rövid kapcsolat időtúllépési értéke miatt sikertelen lesz a kapcsolódási kísérlet. Ez a viselkedés gyakran rosszabb helyzetet tesz lehetővé.  A rövidebb időtúllépések támogatása helyett a rendszer arra kényszeríti a rendszert, hogy indítsa újra a kapcsolódási kísérlet folyamatát, ami egy *csatlakozási > sikertelen > újrapróbálkozási* hurokhoz vezethet. Általánosságban azt javasoljuk, hogy a kapcsolat időkorlátját 15 másodperc vagy annál nagyobb értékre hagyja. Jobb, ha a kapcsolódási kísérletet 15 vagy 20 másodperc után nem sikerül végrehajtani, mint hogy a művelet gyorsan meghiúsuljon. Egy ilyen újrapróbálkozási hurok miatt a leállás tovább tart, mint ha a rendszer már csak hosszabb ideig tart.  
     > [!NOTE]
     > Ez az útmutató a *kapcsolódási kísérletre* vonatkozik, és nem kapcsolódik ahhoz az időponthoz, ameddig várni szeretne egy *műveletre* , például a Get vagy a Complete értékre.

 * **Kerülje a költséges műveleteket** – bizonyos Redis műveletek, például a [Keys](https://redis.io/commands/keys) parancs *nagyon* drágák, és el kell kerülni őket.  További információ: néhány megfontolandó szempont a [hosszan futó parancsokról](cache-troubleshoot-server.md#long-running-commands)

 * **TLS titkosítás használata** – az Azure cache for Redis ALAPÉRTELMEZÉS szerint TLS titkosítású kommunikációt igényel.  A TLS 1,0, 1,1 és 1,2 verziók jelenleg támogatottak.  Azonban a TLS 1,0 és a 1,1 egy olyan útvonalon van, amely az iparágra kiterjedő elavult, ezért a TLS 1,2-et használja, ha ez egyáltalán lehetséges.  Ha az ügyféloldali kódtár vagy eszköz nem támogatja a TLS-t, akkor a titkosítatlan kapcsolatok engedélyezése [a Azure Portal vagy a](cache-configure.md#access-ports) [felügyeleti API](/rest/api/redis/redis/update)-kon keresztül végezhető el.  Olyan esetekben, ahol a titkosított kapcsolatok nem lehetségesek, ajánlott a gyorsítótár és az ügyfélalkalmazás virtuális hálózatra helyezése.  A virtuális hálózati gyorsítótár-forgatókönyvben használt portokkal kapcsolatos további információkért tekintse meg ezt a [táblázatot](cache-how-to-premium-vnet.md#outbound-port-requirements).

 * **Üresjárat időkorlátja** – az Azure Redis jelenleg 10 perces üresjárati időtúllépést biztosít a kapcsolatok esetében, ezért ezt 10 percnél rövidebb értékre kell állítani.

## <a name="memory-management"></a>Memória kezelése
A Redis Server-példányon belül számos olyan dolgot kell megfontolni, amelyet érdemes figyelembe venni.  Íme néhány:

 * **Válasszon ki egy [kizárási szabályzatot](https://redis.io/topics/lru-cache) , amely az alkalmazáshoz használható.**  Az Azure Redis alapértelmezett házirendje a *felejtő LRU*, ami azt jelenti, hogy csak a TTL-értékkel rendelkező kulcsok jogosultak a kizárásra.  Ha egyetlen kulcs sem rendelkezik TTL-értékkel, akkor a rendszer nem távolítja el a kulcsokat.  Ha azt szeretné, hogy a rendszer bármilyen kulcsot kizárjon a memória nyomása alatt, érdemes megfontolnia a *allkeys-LRU* szabályzat használatát.

 * **Állítsa be a lejárati értéket a kulcsokra.**  A lejáratok a kulcsok proaktív módon történő eltávolítása helyett a memória nyomására várnak.  Ha a kizárás a memória nyomása miatt nem indul el, a kiszolgáló további terhelést okozhat.  További információkért tekintse meg a [lejárati](https://redis.io/commands/expire) és [EXPIREAT](https://redis.io/commands/expireat) parancsok dokumentációját.

## <a name="client-library-specific-guidance"></a>Ügyféloldali függvénytár-specifikus útmutató
 * [StackExchange. Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java – melyik ügyfelet kell használnia?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Saláta (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [ASP.NET munkamenet-szolgáltató](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Mikor biztonságos az Újrapróbálkozás?
Sajnos nincs egyszerű válasz.  Minden alkalmazásnak el kell döntenie, hogy milyen műveleteket lehet újrapróbálni, és nem.  Az egyes műveletek különböző követelményekkel és a kulcsok közötti függőségekkel rendelkeznek.  Íme néhány dolog, amit érdemes figyelembe vennie:

 * Az ügyféloldali hibák akkor is beszerezhetők, ha a Redis sikeresen futtatta a futtatni kívánt parancsot.  Például:
    - Az időtúllépés egy ügyféloldali fogalom.  Ha a művelet elérte a kiszolgálót, a kiszolgáló akkor is futtatja a parancsot, ha az ügyfél várakozik.  
    - Ha a szoftvercsatorna-kapcsolaton hiba történik, nem lehet tudni, hogy a művelet ténylegesen futott-e a kiszolgálón.  A kapcsolati hiba például akkor fordulhat elő, ha a kiszolgáló feldolgozta a kérést, de az ügyfél megkapja a választ.
 * Hogyan reagál az alkalmazás, ha véletlenül kétszer ugyanazt a műveletet futtatom?  Például mi a teendő, ha egy egész számot kétszer kell megnövelni a helyett?  Az alkalmazásom ugyanarra a kulcsra ír több helyről?  Mi a teendő, ha az újrapróbálkozási logika felülírja az alkalmazás egy másik része által beállított értéket?

Ha szeretné tesztelni, hogyan működik a kód a hibák között, érdemes lehet az [Újraindítás funkciót](cache-administration.md#reboot)használni. Az újraindítás lehetővé teszi, hogy megtekintse, hogyan befolyásolja a kapcsolódási visszavertség az alkalmazást.

## <a name="performance-testing"></a>Teljesítménytesztelés
 * **Kezdés a használatával `redis-benchmark.exe`** a saját Perf-tesztek írása előtt a lehetséges átviteli sebesség/késés érdekében.  Redis – a teljesítményteszt dokumentációja [itt található](https://redis.io/topics/benchmarks).  Vegye figyelembe, hogy a Redis-benchmark nem támogatja a TLS-t, ezért a teszt futtatása előtt [engedélyeznie kell a nem TLS portot a portálon](cache-configure.md#access-ports) .  [A redis-benchmark.exe Windows-kompatibilis verziója itt található](https://github.com/MSOpenTech/redis/releases)
 * A teszteléshez használt ügyfél virtuális gépnek **ugyanabban a régióban** kell lennie, mint a Redis cache-példánynak.
 * **Azt javasoljuk** , hogy a Dv2 virtuálisgép-sorozatokat az ügyfélhez hasonlóan használja, mivel ezek a hardverek jobbak, és a lehető legjobb eredményeket fogják biztosítani.
 * Győződjön meg arról, hogy az ügyfél által használt virtuális gép rendelkezik **legalább annyi számítási és sávszélességgel* , mint a tesztelt gyorsítótár. 
 * **Tesztelés feladatátvételi feltételek** alapján a gyorsítótárban. Fontos, hogy ne a teljesítmény tesztelése a gyorsítótárban legyen stabil állapotú körülmények között. Tesztelje is a feladatátvételi feltételek alatt, és mérje fel a CPU/kiszolgáló terhelését a gyorsítótárban az idő alatt. A feladatátvételt [az elsődleges csomópont újraindításával](cache-administration.md#reboot)indíthatja el. Ez lehetővé teszi, hogy megtudja, hogyan viselkedik az alkalmazása az átviteli sebesség és a késés tekintetében a feladatátvételi feltételek során (a frissítések során történik, és egy nem tervezett esemény során fordulhat elő). Ideális esetben a CPU/kiszolgáló betöltési csúcsát több mint 80%-ra szeretné megtekinteni a feladatátvétel során még don't't, mivel ez hatással lehet a teljesítményre.
 * A **prémium P2 és újabb** verziók 4 vagy több magot tartalmazó virtuális gépeken futnak. Ez akkor hasznos, ha a TLS titkosítási/visszafejtési munkaterhelést több mag között terjeszti le, hogy a teljes CPU-használatot használja.  [A VM-méretek és a magok részletes ismertetését itt találja:](cache-planning-faq.md#azure-cache-for-redis-performance)
 * **Engedélyezze a VRSS** az ügyfélszámítógépen, ha Windows rendszeren van.  [Részletekért lásd itt](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).  Példa a PowerShell parancsprogramra:
     >PowerShell-ExecutionPolicy korlátlan Enable-NetAdapterRSS-Name (Get-NetAdapter). név 

 * **Érdemes lehet prémium szintű Redis-példányokat használni**.  Ezek a gyorsítótár-méretek jobb hálózati késést és átviteli sebességet biztosítanak, mivel a processzor és a hálózat számára is jobb hardveren futnak.

     > [!NOTE]
     > A megfigyelt teljesítmény eredményei [itt jelennek](cache-planning-faq.md#azure-cache-for-redis-performance) meg a referenciában.   Ügyeljen arra is, hogy az SSL/TLS felvesz némi terhelést, így eltérő késést és/vagy átviteli sebességet érhet el, ha átviteli titkosítást használ.

### <a name="redis-benchmark-examples"></a>Redis-Benchmark példák
**Tesztelés előtti beállítás**: Készítse elő a gyorsítótár-példányt az alább felsorolt késési és átviteli sebesség-tesztelési parancsokhoz szükséges adatokkal.
> Redis-benchmark-h yourcache.redis.cache.windows.net-a yourAccesskey-t SET-n 10-d 1024 

**A késés tesztelése**: a Get-kérések tesztelése egy 1k hasznos adat használatával.
> Redis-benchmark-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-d 1024-P 50-c 4

**Az átviteli sebesség tesztelése:** A folyamattal rendelkező GET-kérések 1k hasznos adatokkal rendelkeznek.
> Redis-benchmark-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-n 1000000-d 1024-P 50-c 50