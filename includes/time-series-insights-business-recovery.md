---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 04/01/2021
ms.openlocfilehash: 6529aa49d06e64947deb5ae54db0c39ad2575569
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106288609"
---
## <a name="business-disaster-recovery"></a>Üzleti katasztrófák helyreállítása

Ez a szakasz a Azure Time Series Insights azon funkcióit ismerteti, amelyek az alkalmazások és szolgáltatások futását biztosítják, még akkor is, ha vészhelyzet történik (más néven az *üzleti katasztrófák helyreállítása*).

### <a name="high-availability"></a>Magas rendelkezésre állás

Azure-szolgáltatásként a Azure Time Series Insights bizonyos *magas rendelkezésre állású* funkciókat biztosít az Azure-régió szintjén elérhető redundanciák használatával. Az Azure például támogatja a vész-helyreállítási lehetőségeket az Azure *régiók közötti rendelkezésre állási* funkciójával.

Az Azure-on keresztül elérhető további magas rendelkezésre állási funkciók (és bármely Azure Time Series Insights-példány számára elérhetők) a következők:

- **Feladatátvétel**: az Azure [geo-replikációt és terheléselosztást](/azure/architecture/resiliency/recovery-loss-azure-region)biztosít.
- **Adatok helyreállítása** és **tárolása – helyreállítás**: [Az Azure számos lehetőséget kínál az adatok megőrzésére és helyreállítására](/azure/architecture/resiliency/recovery-data-corruption).
- **Azure site Recovery**: az Azure helyreállítási funkciókat biztosít a [Azure site Recoveryon](../articles/site-recovery/index.yml)keresztül.
- **Azure Backup**: a [Azure Backup](../articles/backup/backup-architecture.md) az Azure-beli virtuális gépek helyszíni és felhőalapú biztonsági mentését is támogatja.

Győződjön meg arról, hogy a megfelelő Azure-funkciók lehetővé teszik, hogy globális, régiók közötti magas rendelkezésre állást biztosítson az eszközök és a felhasználók számára.

> [!NOTE]
> Ha az Azure úgy van beállítva, hogy engedélyezze a régiók közötti rendelkezésre állást, a Azure Time Series Insights nem szükséges további régiók közötti rendelkezésre állási konfiguráció.

### <a name="iot-and-event-hubs"></a>IoT és Event hubok

Néhány Azure IoT-szolgáltatás beépített üzleti katasztrófa-helyreállítási funkciókat is tartalmaz:

- [Azure IoT hub magas rendelkezésre állású vész-helyreállítás](../articles/iot-hub/iot-hub-ha-dr.md), amely a régión belüli redundanciát is magában foglalja
- [Azure Event Hubs házirendek](../articles/event-hubs/event-hubs-geo-dr.md)
- [Redundancia az Azure Storage szolgáltatásban](../articles/storage/common/storage-redundancy.md)

A Azure Time Series Insights más szolgáltatásokkal való integrálása további vész-helyreállítási lehetőségeket biztosít. Előfordulhat például, hogy az telemetria eljuttatott értesítéseket a Backup Azure Blob Storage-adatbázisa őrzi meg.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

Több módon is megtarthatja a Azure Time Series Insights-adatait,-alkalmazásait és-szolgáltatásait, még akkor is, ha azok megszakadnak.

Azonban előfordulhat, hogy az Azure Time Series-környezet teljes biztonsági másolatát is meg kell határoznia az alábbi célokból:

- Az adatok és a forgalom átirányításához a Azure Time Series Insights kifejezetten *feladatátvételi példányként*
- Az adatok és a naplózási információk megőrzése

Általánosságban elmondható, hogy a Azure Time Series Insights-környezet duplikálása a legjobb módszer, ha egy második Azure Time Series Insights-környezetet hoz létre egy biztonsági mentési Azure-régióban. Az eseményeket az elsődleges esemény forrásaként is elküldi erre a másodlagos környezetre. Győződjön meg arról, hogy egy második dedikált fogyasztói csoportot használ. Kövesse a forrás üzleti katasztrófák helyreállítási irányelveit a korábban leírtak szerint.

Ismétlődő környezet létrehozásához:

1. Hozzon létre egy környezetet egy második régióban. További információért olvassa el [a Azure Portal új Azure Time Series Insights környezet létrehozása](../articles/time-series-insights/time-series-insights-get-started.md)című témakört.
1. Hozzon létre egy második dedikált fogyasztói csoportot az eseményforrás számára.
1. Az eseményforrás összekötése az új környezettel. Győződjön meg arról, hogy a második dedikált fogyasztói csoportot jelölte ki.
1. Tekintse át a Azure Time Series Insights [IoT hub](../articles/time-series-insights/how-to-ingest-data-iot-hub.md) és [Event Hubs](../articles/time-series-insights/concepts-access-policies.md) dokumentációját.

Ha egy esemény következik be:

1. Ha az elsődleges régiót a vész-incidensek befolyásolják, átirányítja a műveleteket a biztonsági mentési Azure Time Series Insights környezetbe.
1. Mivel a hub-sorozatszámok a feladatátvételt követően újraindulnak, a különböző fogyasztói csoportokkal rendelkező régiókban/környezetekben hozza létre újra az eseményforrás, hogy elkerülje az ismétlődő események kinézetének létrehozását.
1. A második régió használatával biztonsági mentést készíthet, és helyreállíthatja az összes Azure Time Series Insights telemetria és a lekérdezési adatokat.

> [!IMPORTANT]
> Feladatátvétel esetén:
>
> - Késés is előfordulhat.
> - Előfordulhat, hogy a rendszer a műveletek átirányítása közben az üzenet feldolgozásának pillanatnyi csúcsát is felhasználja.
>
> További információért olvassa el a [Azure Time Series Insights késleltetésének enyhítését](../articles/time-series-insights/time-series-insights-environment-mitigate-latency.md)ismertető témakört.