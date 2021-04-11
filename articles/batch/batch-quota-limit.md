---
title: Szolgáltatási kvóták és korlátok
description: Ismerje meg az alapértelmezett Azure Batch kvótákat, korlátozásokat és korlátozásokat, valamint a kvóta növelésének módját
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: seodec18
ms.openlocfilehash: 6e17a90cc573205bcb964a0428e0b7320323b8a6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553549"
---
# <a name="batch-service-quotas-and-limits"></a>A Bach szolgáltatás kvótái és korlátozásai

A többi Azure-szolgáltatáshoz hasonlóan a Batch szolgáltatáshoz társított bizonyos erőforrások is korlátozottak. Ezen korlátok közül sok az Azure által az előfizetés vagy a fiók szintjén alkalmazott alapértelmezett kvóták.

Tartsa szem előtt ezeket a kvótákat a Batch-munkaterhelések tervezése és skálázása során. Ha például a készlet nem éri el a megadott számítási csomópontok számát, akkor előfordulhat, hogy elérte a Batch-fiókhoz tartozó fő kvóta korlátját.

Több batch számítási feladatot is futtathat egyetlen batch-fiókban, vagy eloszthatja a számítási feladatokat olyan batch-fiókok között, amelyek ugyanabban az előfizetésben, de különböző Azure-régiókban találhatók.

Ha éles számítási feladatokat kíván futtatni a Batch szolgáltatásban, előfordulhat, hogy az alapértelmezettnél nagyobb mennyiségű kvótát kell megadnia. A kvóta [növeléséhez a kvóta növelését díjmentesen kérheti](#increase-a-quota) le.

## <a name="resource-quotas"></a>Erőforráskvóták

A kvóta határérték, nem kapacitási garancia. Ha nagyméretű kapacitásra van szüksége, vegye fel a kapcsolatot az Azure ügyfélszolgálatával.

Azt is vegye figyelembe, hogy a kvóták nem garantált értékek. A kvóták a Batch szolgáltatás módosításaitól vagy a kvóta értékének módosítására vonatkozó felhasználói kéréstől függően változhatnak.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Alapvető kvóták

### <a name="cores-quotas-in-batch-service-mode"></a>Magok kvótái a Batch szolgáltatás módban

A Batch által támogatott összes virtuálisgép-sorozathoz alapvető kvóták léteznek. Ezek az alapvető kvóták a Azure Portal **kvóták** lapján jelennek meg. A virtuálisgép-sorozatokra vonatkozó kvóták a lent részletezett támogatási kéréssel frissíthetők. A dedikált csomópontok esetében a Batch az egyes virtuálisgép-sorozatokra vonatkozó alapkvóta-korlátot, valamint a teljes batch-fiókra vonatkozó teljes kvóta-korlátot kényszeríti ki. Az alacsony prioritású csomópontok esetében a Batch csak a Batch-fiókhoz tartozó összes alapvető kvótát érvényesíti a különböző virtuálisgép-sorozatok megkülönböztetése nélkül.

### <a name="cores-quotas-in-user-subscription-mode"></a>Magok kvótái felhasználói előfizetési módban

Ha a készlet kiosztási móddal rendelkező [Batch-fiókját](accounts.md) **felhasználói előfizetésre** állította, a Batch-beli virtuális gépek és egyéb erőforrások közvetlenül az előfizetésben jönnek létre, amikor létrejön vagy átméretezi a készletet. A Azure Batch alapkvóták nem érvényesek, és az előfizetésében lévő kvóták a regionális számítási magok, a soron belüli számítási magok, valamint más erőforrások használata és kikényszerítve.

Ha többet szeretne megtudni ezekről a kvótáról, tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="pool-size-limits"></a>Készlet méretének korlátai

A készlet méretének korlátait a Batch szolgáltatás állítja be. Az [erőforrás-kvótákkal](#resource-quotas)ellentétben ezek az értékek nem módosíthatók. Csak a csomópontok közötti kommunikációt és az egyéni lemezképeket tartalmazó készletek rendelkeznek a normál kvótától eltérő korlátozásokkal.

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| **A [csomópontok közötti kommunikációt engedélyező készlet](batch-mpi.md) számítási csomópontjai**  ||
| Batch szolgáltatás-készlet kiosztási módja | 100 |
| Batch-előfizetési készlet lefoglalási módja | 80 |
| **[Felügyelt rendszerkép 1-es erőforrásával létrehozott készlet](batch-custom-images.md)számítási csomópontjai**<sup></sup> ||
| Dedikált csomópontok | 2000 |
| Alacsony prioritású csomópontok | 1000 |

<sup>1</sup> a nem csomópontok közötti kommunikációt engedélyező készletek esetén.

## <a name="other-limits"></a>Egyéb korlátok

Ezeket a további korlátokat a Batch szolgáltatás állítja be. Az [erőforrás-kvótákkal](#resource-quotas)ellentétben ezek az értékek nem módosíthatók.

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| [Egyidejű feladatok](batch-parallel-node-tasks.md) /számítási csomópontok | 4 x csomópont-magok száma |
| [Alkalmazások](batch-application-packages.md) batch-fiókkal | 20 |
| Alkalmazás-csomagok eszközönként | 40 |
| Alkalmazáscsomag/készlet | 10 |
| Feladat maximális élettartama | 180 nap<sup>1</sup> |
| [Csatlakoztatások](virtual-file-mount.md) /számítási csomópontok | 10 |
| Tanúsítványok/készlet | 12 |

<sup>1</sup> a feladat maximális élettartama, amikor a feladat bekerül a feladatba, 180 nap. A Befejezett feladatok hét napig fennmaradnak; a maximális élettartamon belül nem befejezett feladatok adatai nem érhetők el.

## <a name="view-batch-quotas"></a>Batch-kvóták megtekintése

A Batch-fiók kvótáinak megtekintése a [Azure Portalban](https://portal.azure.com):

1. Válassza a **Batch-fiókok** lehetőséget, majd válassza ki azt a Batch-fiókot, amelyre kíváncsi.
1. Válassza a **kvóták** lehetőséget a Batch-fiók menüjében.
1. A Batch-fiókra jelenleg alkalmazott kvóták megtekintése.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Képernyőfelvétel a Azure Portal batch-fiókokra vonatkozó kvóták megjelenítéséről.":::

## <a name="increase-a-quota"></a>Kvóta emelése

A Batch-fiókra vagy az előfizetésre vonatkozó kvóta növelését a [Azure Portal](https://portal.azure.com) vagy az Azure- [kvóta REST API](#azure-quota-rest-api)használatával kérheti le.

A kvóta megnövekedésének típusa a Batch-fiók készlet-kiosztási módjától függ. A kvóta növeléséhez meg kell adnia azt a virtuálisgép-sorozatot, amelynek a kvótáját emelni szeretné. A kvóta növelésének alkalmazása esetén a rendszer a virtuális gépek minden sorozatára alkalmazza.

A támogatási kérelem elküldését követően az Azure-támogatás kapcsolatba lép Önnel. A kvóta-kérelmek néhány percen belül, vagy akár két munkanapon belül is elvégezhető.

### <a name="azure-portal"></a>Azure Portal

1. A **kvóták** lapon válassza a **kérelmek kvótájának növelése** lehetőséget. Másik lehetőségként kiválaszthatja a **Súgó + támogatás** csempét a portál irányítópultján (vagy a kérdéses jellel (**?**) a portál jobb felső sarkában), majd válassza az **új támogatási kérés lehetőséget.**

1. Az **alapjaiban**:

    1. A **probléma típusa** beállításnál válassza a **szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.
    1. Válassza ki előfizetését.
    1. A **kvóta típusa** beállításnál válassza a **Batch** elemet.
    1. A folytatáshoz válassza a **Tovább** gombot.

1. A **Details** (Részletek) lapon:

    1. A **részletek** megadása szakaszban adja meg a helyet, a kvóta típusát és a Batch-fiókot (ha van ilyen), majd válassza ki a kvótá (ka) t a növeléshez.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="A kvóta részletei képernyő képernyőképe a kvóta növelésének kérelmezése során.":::

       A kvóta típusai a következők:

       - **/Batch-fiók**  
         Ezzel a beállítással kérheti a kvóta növelését egyetlen batch-fiókra, beleértve a dedikált és alacsony prioritású magot, valamint a feladatok és készletek számát.

         Ha ezt a lehetőséget választja, adja meg azt a Batch-fiókot, amelyre a kérést alkalmazni szeretné, majd válassza ki a frissíteni kívánt kvótát (ka) t. Adja meg az egyes erőforrásokhoz kért új korlátot.

         Az alacsony prioritású kvóta egyetlen érték az összes virtuálisgép-sorozaton belül. Ha korlátozott SKU-ra van szüksége, ki kell választania az **alacsony prioritású magok** elemet, és fel kell vennie a lekéréses virtuálisgép-családokat.

       - **A régió összes fiókja**  
         Ezzel a beállítással olyan kvótákat igényelhet, amelyek egy adott régió összes batch-fiókjára vonatkoznak, például a Batch-fiókok száma régiónként/előfizetéssel.

    1. A **támogatási módszer** területen válasszon ki egy **súlyosságot** az [üzleti hatás](https://aka.ms/supportseverity) és az előnyben részesített kapcsolattartási módszer és a támogatási nyelv alapján.

    1. A **kapcsolattartási adatok** területen ellenőrizze és adja meg a szükséges kapcsolattartási adatokat.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd válassza a **Létrehozás** lehetőséget a támogatási kérelem elküldéséhez.

### <a name="azure-quota-rest-api"></a>Azure-kvóta REST API

Az Azure-kvóta REST API használatával az előfizetés szintjén vagy a Batch-fiók szintjén is megnövelhető a kvóta.

A részleteket és példákat lásd: [kvóta növelésének igénylése az Azure támogatási REST API használatával](/rest/api/support/quota-payload#azure-batch).

## <a name="related-quotas-for-vm-pools"></a>Virtuálisgép-készletek kapcsolódó kvótái

[Az Azure-beli virtuális hálózatban üzembe helyezett virtuálisgép-konfigurációban található batch-készletek](batch-virtual-network.md) automatikusan további Azure hálózati erőforrásokat foglalnak magukban. Ezek az erőforrások a Batch-készlet létrehozásakor megadott virtuális hálózatot tartalmazó előfizetésben jönnek létre.

A következő erőforrások jönnek létre egy virtuális hálózat minden 100-es készlet-csomópontján:

- Egy [hálózati biztonsági csoport](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Egy [nyilvános IP-cím](../virtual-network/public-ip-addresses.md)
- Egy [Load Balancer](../load-balancer/load-balancer-overview.md)

Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](../azure-resource-manager/management/azure-subscription-service-limits.md) vonatkoznak. Ha a nagyméretű készletek központi telepítését egy virtuális hálózatban tervezi, előfordulhat, hogy egy vagy több ilyen erőforráshoz kvóta-növelést kell kérnie.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- Ismerje meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](../azure-resource-manager/management/azure-subscription-service-limits.md).