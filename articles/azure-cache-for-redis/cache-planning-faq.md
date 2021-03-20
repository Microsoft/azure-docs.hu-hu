---
title: Azure cache a Redis-tervezéssel kapcsolatos gyakori kérdések
description: Megtudhatja, milyen gyakori kérdésekre ad választ az Azure cache Redis való tervezéséhez
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398b567e173691068e6dd932e075d6598dcfe601
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537405"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Azure cache a Redis-tervezéssel kapcsolatos gyakori kérdések

Ez a cikk az Azure cache Redis való tervezésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="common-questions-and-answers"></a>Gyakori kérdések és válaszok
Ez a szakasz a következő gyakori kérdéseket ismerteti:

* [Azure cache a Redis teljesítményéhez](#azure-cache-for-redis-performance)
* [Milyen régióban érdemes megkeresni a gyorsítótárat?](#in-what-region-should-i-locate-my-cache)
* [Hol találhatók a gyorsítótárazott adataim?](#where-do-my-cached-data-reside)
* [Mennyit kell fizetnem az Azure cache Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Használhatom az Azure cache-t a Redis Azure Government-felhővel, az Azure China 21Vianet-felhővel vagy Microsoft Azure Germanyával?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Azure cache a Redis teljesítményéhez
Az alábbi táblázat a standard és prémium gyorsítótárak különböző méretekben történő tesztelésekor megfigyelt maximális sávszélességet mutatja a `redis-benchmark.exe` IAAS virtuális gépről a Redis-végponthoz tartozó Azure cache használatával. TLS átviteli sebesség esetén a Redis-benchmark a stunnel használatával csatlakozik az Azure cache-hez a Redis-végponthoz.

>[!NOTE] 
>Ezek az értékek nem garantáltak, és ezekhez a számokhoz nem biztosítunk SLA-t, de jellemzőnek kell lennie. Az alkalmazás megfelelő gyorsítótár-méretének meghatározásához be kell töltenie a saját alkalmazásának tesztelését.
>Előfordulhat, hogy ezek a számok változhatnak, mivel az újabb eredményeket rendszeresen közzéteszjük.
>

Ebből a táblázatból a következő következtetéseket tudjuk felhívni:

* Az azonos méretű gyorsítótárak átviteli sebessége magasabb a prémium szinten a standard szinthez képest. Például egy 6 GB-os gyorsítótárral a P1 adatátviteli sebessége 180 000 kérelem/másodperc (RPS), a C3 100 000 RPS-hez képest.
* A Redis fürtszolgáltatással a teljesítmény lineárisan növekszik a fürtben lévő szegmensek számának növelésével. Ha például 10 szegmensből álló P4-fürtöt hoz létre, akkor az elérhető átviteli sebesség 400 000 * 10 = 4 000 000 RPS.
* A nagyobb méretű kulcsok átviteli sebessége magasabb a prémium szinten a standard szinthez képest.

| Tarifacsomag | Méret | Processzormagok | Rendelkezésre álló sávszélesség | 1 KB-os érték mérete | 1 KB-os érték mérete |
| --- | --- | --- | --- | --- | --- |
| **Standard gyorsítótár mérete** | | |**Megabit/mp (MB/s)/megabájt/mp (MB/s)** |**Másodpercenkénti kérelmek (RPS) nem SSL** |**Másodpercenkénti kérelmek (RPS) SSL** |
| C0 | 250 MB | Megosztott | 100/12,5  |  15 000 |   7500 |
| C1 |   1 GB | 1      | 500/62,5  |  38 000 |  20 720 |
| C2 | 2,5 GB | 2      | 500/62,5  |  41 000 |  37 000 |
| C3 csomag |   6 GB | 4      | 1000/125  | 100.000 |  90,000 |
| C4 |  13 GB | 2      | 500/62,5  |  60.000 |  55 000 |
| C5 csomag |  26 GB | 4      | 1 000/125 | 102 000 |  93 000 |
| C6 |  53 GB | 8      | 2 000/250 | 126 000 | 120 000 |
| **Prémium szintű gyorsítótár mérete** | |**CPU-magok/szegmensek** | **Megabit/mp (MB/s)/megabájt/mp (MB/s)** |**Másodpercenkénti kérelmek (RPS) nem SSL, szegmens szerint** |**Másodpercenkénti kérelmek másodpercenkénti száma (RPS)** |
| P1 |   6 GB |  2 | 1 500/187,5 | 180 000 | 172 000 |
| P2 |  13 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P3 |  26 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P4 |  53 GB |  8 | 6 000/750   | 400,000 | 373 000 |
| P5 | 120 GB | 20 | 6 000/750   | 400,000 | 373 000 |

A stunnel beállításával vagy a Redis-eszközök (például) letöltésével kapcsolatos utasításokért `redis-benchmark.exe` lásd: [how can do Run Redis commands?](cache-development-faq.md#how-can-i-run-redis-commands).

### <a name="in-what-region-should-i-locate-my-cache"></a>Milyen régióban érdemes megkeresni a gyorsítótárat?
A legjobb teljesítmény és a legalacsonyabb késés érdekében keresse meg az Azure-gyorsítótárat a Redis ugyanabban a régióban, ahol a gyorsítótár-ügyfélalkalmazás található.

### <a name="where-do-my-cached-data-reside"></a>Hol találhatók a gyorsítótárazott adataim?
A Redis készült Azure cache a virtuális gép vagy a virtuális gépek RAM memóriájában tárolja az alkalmazás adatait, attól függően, hogy milyen szintű a gyorsítótár. Az adatai szigorúan abban az Azure-régióban találhatók, amelyet alapértelmezés szerint kiválasztott. Az adatközpont két esetben hagyhatja el a régiót:
* Ha engedélyezi az adatmegőrzést a gyorsítótárban, az Azure cache for Redis az Ön tulajdonában lévő Azure Storage-fiókba készít biztonsági másolatot az adatairól. Ha az Ön által megadott Storage-fiók egy másik régióban található, akkor az adatai egy másolata fog történni.
* Ha a Geo-replikációt állítja be, és a másodlagos gyorsítótár egy másik régióban található, amely normál esetben az, hogy az adatait a rendszer replikálja az adott régióba.

Ezen funkciók használatához explicit módon konfigurálnia kell az Azure cache-t a Redis. Emellett teljes körűen szabályozhatja azt a régiót, amelyhez a Storage-fiók vagy a másodlagos gyorsítótár található.

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Mennyit kell fizetnem az Azure cache Redis?
Az Azure cache for Redis díjszabása [itt](https://azure.microsoft.com/pricing/details/cache/)található. A díjszabási oldal óradíjas és havi díjszabás szerint sorolja fel az árakat. A gyorsítótárak számlázása percenként történik a gyorsítótár létrehozásának időpontjától egészen a gyorsítótár törlésekor. Nincs lehetőség a gyorsítótár számlázásának leállítására vagy felfüggesztésére.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Használhatom az Azure cache-t a Redis Azure Government-felhővel, az Azure China 21Vianet-felhővel vagy Microsoft Azure Germanyával?
Igen, a Redis-hez készült Azure cache Azure Government felhőben, az Azure China 21Vianet-felhőben és Microsoft Azure Germany érhető el. A Redis Azure cache eléréséhez és kezeléséhez használt URL-címek eltérőek ezekben a felhőkben az Azure nyilvános felhővel szemben.

| Felhőbeli   | Redis DNS-utótagja            |
|---------|---------------------------------|
| Nyilvános  | *. redis.cache.windows.net       |
| US Gov  | *. redis.cache.usgovcloudapi.net |
| Németország | *. redis.cache.cloudapi.de       |
| Kína   | *. redis.cache.chinacloudapi.cn  |

Az Azure cache más Felhőkkel való Redis való használatának szempontjaival kapcsolatos további információkért tekintse meg a következő hivatkozásokat.

- [Azure Government-adatbázisok – Azure cache a Redis-hez](../azure-government/compare-azure-government-global-azure.md)
- [Azure China 21Vianet-felhő – Azure cache a Redis-hoz](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

További információ az Azure cache Redis való használatáról a PowerShell használatával Azure Government felhőben, az Azure China 21Vianet Cloud és a Microsoft Azure Germanyban: [Kapcsolódás más felhőhöz – Azure cache a Redis powershellhez](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

## <a name="next-steps"></a>Következő lépések

További információ [Az Azure cache Redis-ről – gyakori kérdések](cache-faq.md).