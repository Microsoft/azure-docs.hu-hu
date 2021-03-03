---
title: A dedikált Event hubok áttekintése – Azure Event Hubs | Microsoft Docs
description: Ez a cikk áttekintést nyújt a dedikált Azure Event Hubsről, amely az Event hub egybérlős üzembe helyezését kínálja.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 721acf354c7d14c1362b4f760982af37d59115f9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715615"
---
# <a name="overview-of-event-hubs-dedicated"></a>A dedikált Event Hubs áttekintése

*Event Hubs-fürtök* egybérlős üzemelő példányokat biztosítanak a legigényesebb folyamatos átviteli igényű ügyfelek számára. Ez az egybérlős ajánlat garantált 99,99%-os garantált szolgáltatási szinttel rendelkezik, és csak a dedikált díjszabási szinten érhető el. Egy Event Hubs fürt másodpercenként több millió eseményt tud befogadni a garantált kapacitással és a másodpercenkénti késéssel. A dedikált fürtön belül létrehozott névterek és az Event hubok tartalmazzák a standard ajánlat összes funkcióját, többek között a bejövő korlátok nélkül. Emellett a népszerű [Event Hubs Capture](event-hubs-capture-overview.md) szolgáltatást is tartalmazza díjmentesen. Ez a funkció lehetővé teszi, hogy automatikusan batch-és naplózza az adatstreameket az Azure Storage-ba vagy a Azure Data Lakeba. 

A fürtök kiépítése és kiszámlázása **Kapacitási egységek (ke)** alapján történik, a processzor-és memória-erőforrások előre lefoglalt mennyisége. Minden fürthöz 1, 2, 4, 8, 12, 16 vagy 20 ke lehet vásárolni. A betöltés és az adatfolyam-továbbítás mennyisége számos tényezőtől függ, például a következőktől: 

- Termelők és fogyasztók száma
- Hasznos adat alakzat
- Kimenő forgalom aránya

> [!NOTE]
> Az összes Event Hubs-fürt alapértelmezetten a Kafka-kompatibilis, és támogatja a Kafka-végpontokat, amelyeket a meglévő Kafka-alapú alkalmazások használhatnak. Ha a Kafka engedélyezve van a fürtön, nem érinti a nem Kafka használati eseteit; nincs lehetőség, vagy le kell tiltania a Kafka-t a fürtön.

## <a name="why-dedicated"></a>Miért dedikált?

A dedikált Event Hubs három vonzó előnyt kínál a nagyvállalati szintű kapacitást igénylő ügyfelek számára:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Az egybérlős kapacitás a jobb teljesítmény érdekében

A dedikált fürt teljes mértékben garantálja a kapacitást. Akár gigabájt adatfolyamot is tartalmazhat, amely teljes körűen tartós tárolást és másodlagos késést biztosít a forgalom adatforgalmának befogadásához. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>A funkciókhoz tartozó és kizárólagos hozzáférés 
A dedikált ajánlat olyan funkciókat tartalmaz, mint például a capture díjmentesen, és kizárólagos hozzáférés a közelgő funkciókhoz, például Bring Your Own Key (BYOK). A szolgáltatás a terheléselosztást, az operációs rendszer frissítéseit, a biztonsági javításokat és a particionálást is kezeli. Így kevesebb időt is igénybe vehet az infrastruktúra karbantartására és az ügyféloldali funkciók kiépítésére.  

#### <a name="cost-savings"></a>Költségmegtakarítás
A magas beáramlású köteteknél (>100 átviteli egységekben) a fürt óránként jóval kevesebb költséggel jár, mint a standard ajánlatban található hasonló mennyiségű átviteli egység megvásárlása.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Kvóták és korlátozások dedikált Event Hubs

Az dedikált Event Hubs ajánlat számlázása rögzített havi díjszabással történik, amely legalább 4 órányi használatot biztosít. A dedikált szint a standard csomag összes funkcióját biztosítja, de nagyvállalati szintű kapacitással és korlátokkal rendelkező ügyfelek számára igényes számítási feladatokkal. 

| Szolgáltatás | Standard | Dedikált |
| --- |:---|:---|
| Sávszélesség | 20 TUs (legfeljebb 40 TUs) | 20 ke |
| Névterek |  1 | 50/CU |
| Event Hubs |  10/névtér | 1000/névtér |
| Bejövő események | Díj/millió esemény | Tartalmazza |
| Üzenet mérete | 1 000 000 bájt | 1 000 000 bájt |
| Partíciók | 32/Event hub | 1024/Event hub<br/>2000/CU |
| Fogyasztói csoportok | 20/Event hub | Az Event hub-ban nincs korlát/CU, 1000 |
| Felügyelt kapcsolatok | 1 000 tartalmazott, 5 000 Max | 100 kb és max. |
| [Esemény-megőrzés](event-hubs-features.md#event-retention) | 7 nap, 84 GB/TU | 90 nap, 10 TB tartalmaz/CU |
| Rögzítés | Óránkénti fizetés | Tartalmazza |

További kvóták és korlátozások: [Event Hubs kvóták és korlátok](event-hubs-quotas.md)

## <a name="how-to-onboard"></a>Útmutató a bevezetéshez

A [Event Hubs-fürtnek](event-hubs-dedicated-cluster-create-portal.md) a [Azure Portal](https://aka.ms/eventhubsclusterquickstart) használatával történő létrehozásának önkiszolgáló élménye már előzetes verzióban érhető el. Ha bármilyen kérdése van, vagy segítségre van szüksége a dedikált Event Hubs bevezetéséhez, lépjen kapcsolatba a [Event Hubs csapatával](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Gyakori kérdések

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Következő lépések

A dedikált Event Hubs további részleteiért forduljon a Microsoft értékesítési képviselőjéhez vagy a Microsoft ügyfélszolgálatahoz. Létrehozhat egy fürtöt is, vagy további információkat kaphat Event Hubs díjszabási szintjeiről az alábbi hivatkozásokra kattintva:

- [Event Hubs-fürt létrehozása a Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Dedikált Event Hubs díjszabása](https://azure.microsoft.com/pricing/details/event-hubs/). Felveheti a kapcsolatot a Microsoft értékesítési képviselőjével vagy Microsoft ügyfélszolgálata a dedikált Event Hubs kapacitás további részleteinek megismeréséhez.
- A [Event HUBS GYIK](event-hubs-faq.md) tartalmazza a díjszabási információkat, és válaszokat ad a Event Hubsekkel kapcsolatos gyakori kérdésekre.
