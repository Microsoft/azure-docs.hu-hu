---
title: Szolgáltatás kvótái és korlátozásai – Azure Batch |} A Microsoft Docs
description: Alapértelmezett Azure Batch-kvóták, korlátozások és megkötések ismertetése, és növeli a hogyan kvótát
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/13/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 820eddff7da3bb52ca94ea0cb7e2361d89892a4a
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595337"
---
# <a name="batch-service-quotas-and-limits"></a>A Bach szolgáltatás kvótái és korlátozásai

Mivel az más Azure-szolgáltatásokkal, korlátozva van az egyes erőforrásokat a Batch szolgáltatáshoz társított. Ezeket a korlátokat számos az előfizetés vagy a fiók szintjén az Azure által alkalmazott alapértelmezett kvóta. Ez a cikk ismerteti azokat az alapértelmezett beállításokat, és hogyan kérheti a kvóta növeli.

Kvóta ne feledje, tervezési és a Batch számítási feladatok vertikális. Például ha a készlet nem éri el a számítási csomópontok megadott célszámát, lehet, hogy elérte magkvótájának határértékét a Batch-fiók.

Több Batch számítási feladatot is futtathat egyetlen Batch-fiókon, de el is oszthatja a számítási feladatokat ugyanazon előfizetéshez, de különböző Azure-régiókhoz tartozó Batch-fiókok között.

Ha azt tervezi, éles számítási feladatok futtatása a Batchben, szükség lehet egy vagy több az alapértelmezettnél eggyel a kvóták növelése érdekében. Ha szeretné a kvóta növeléséhez, akkor megnyithatja az online [támogatási kérést](#increase-a-quota) díjmentesen.

> [!NOTE]
> Kvóta kreditjét, nem kapacitás garantálja. Ha nagyméretű kapacitásigények, lépjen kapcsolatba az Azure-támogatás.

## <a name="resource-quotas"></a>Erőforráskvóták
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>Magkvóták felhasználói előfizetési módban

Ha beállítása készletlefoglalási móddal létrehozott Batch-fiók **felhasználói előfizetés**, eltérő kvóták érvényesek. Ebben a módban a Batch virtuális gépei és egyéb erőforrások jönnek létre közvetlenül az előfizetésben egy készlet létrehozásakor. Az Azure Batch magkvóták nem hozza létre ezt a módot a alkalmazni. Ehelyett a kvótákat, az előfizetéséhez tartozó területi számítási magot, és más erőforrások lépnek érvénybe. További információ a kvóta [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Készlet blobméretének korlátjai

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| **Számítási csomópontok [készletben a csomópontok közötti kommunikáció engedélyezve](batch-mpi.md)**  ||
| A Batch szolgáltatás készletlefoglalási mód | 100 |
| Előfizetés készletlefoglalási móddal a Batch | 80 |
| **Számítási csomópontok [egyéni Virtuálisgép-rendszerkép használatával létrehozott készlet](batch-custom-images.md)**<sup>1</sup> ||
| Dedikált csomópontok | 2000 |
| Alacsony prioritású csomópontok | 1000 |

<sup>1</sup> készletek, amelyek nem a csomópontok közötti kommunikáció engedélyezve van.

## <a name="other-limits"></a>Egyéb korlátok

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| [Egyidejű feladatok](batch-parallel-node-tasks.md) számítási csomópontok | csomópont-magok száma 4 x |
| [Alkalmazások](batch-application-packages.md) Batch-fiókonként | 20 |
| Alkalmazáscsomagok alkalmazásonként | 40 |
| Az alkalmazáscsomagok száma készletenként | 10 |
| A tevékenység maximális élettartama | 180 nap<sup>1</sup> |

<sup>1</sup> egy feladatot, amikor megjelenik a feladat befejezését követően a maximális élettartama 180 nap. Befejezett feladatok megőrzése 7 napig; a maximális élettartamon belül nem befejezett tevékenységek adatai nem érhető el.

## <a name="view-batch-quotas"></a>Batch-kvóták megtekintése

A Batch fiókra vonatkozó kvóták az megtekintése a [az Azure portal][portal].

1. Válassza ki **Batch-fiókok** a portálon, majd válassza ki az Önt érdeklő Batch-fiókhoz.
1. Válassza ki **kvóták** a Batch-fiók menüjében.
1. A Batch-fiók telepítve a kvóták megtekintése

    ![Batch-fiókra vonatkozó kvóták][account_quotas]

## <a name="increase-a-quota"></a>A kvóta növeléséhez

Kövesse az alábbi lépéseket egy kvótát a Batch-fiók vagy az előfizetés használatával növelheti a [az Azure portal][portal]. Kvótanövelést típusát a Batch-fiók készletlefoglalási módjától függ. Kérje egy kvótájának növelését, meg kell adnia a Virtuálisgép-sorozatok, amelyet szeretne növelését. Ha alkalmazza a kvótájának növelését, a rendszer alkalmazza minden sorozat virtuális gépei esetében.

### <a name="increase-cores-quota-in-batch"></a>A Batch szolgáltatásban magkvóta növeléséhez 

1. Válassza ki a **súgó + támogatás** csempét az irányítópulton, vagy a kérdőjel (**?**) a portál jobb felső sarkában.
1. Válassza ki **új támogatási kérelem** > **alapjai**.
1. A **alapjai**:
   
    a. **Probléma típusa** > **szolgáltatás és az előfizetések korlátai (kvóták)**
   
    b. Válassza ki előfizetését.
   
    c. **Kvóta típusa** > **Batch**
      
    Kattintson a **Tovább** gombra.
    
1. A **részletek**:
      
    a. A **adja meg az adatokat**, adja meg a helyet, a kvóta típusa és a Batch-fiók.
    
    ![Batch-kvótájának növelését][quota_increase]

    Kvótatípusok:

    * **Batch-fiókonként**  
        Értékek csak egyetlen Batch-fiók, többek között a dedikált és alacsony prioritású magok, valamint a feladatok és -készletek száma.
        
    * **Régiónként**  
        Olyan értékek, amelyek érvényesek az összes Batch-fiókok, egy régióban, és a Batch-fiókok régiónként és előfizetésenként számát tartalmazza.

    Alacsony prioritású kvóta egyetlen értéket az összes Virtuálisgép-sorozatok esetében. Ha korlátozott termékváltozatok van szüksége, ki kell választania **alacsony prioritású magok** , és a Virtuálisgép-családok kéréséhez.

    b. Válassza ki a **súlyossági** a következők szerint a [üzletmenetre gyakorolt hatás][support_sev].

    Kattintson a **Tovább** gombra.

1. A **kapcsolattartási adatok**:
   
    a. Válassza ki a **elsődleges kapcsolattartási módszert**.
   
    b. Győződjön meg arról, és adja meg a szükséges kapcsolattartási adatokat.
   
    Válassza ki **létrehozás** a támogatási kérést szeretne beküldeni.

Miután a támogatási kérelem elküldése, az Azure-támogatás felveszi Önnel a kapcsolatot. Kvóta kérelmek előfordulhat, hogy hajtható végre, néhány percen belül, illetve legfeljebb két munkanapon belül.

## <a name="related-quotas-for-vm-pools"></a>Virtuálisgép-készletek a kapcsolódó kvóták

A virtuális gép konfigurációjában, automatikusan üzembe helyezett Azure-beli virtuális hálózathoz a Batch-készletek foglalása további Azure hálózati erőforrásaival. A következő erőforrások a virtuális hálózat minden egyes 50 készletcsomópontokat van szükség:

* Egy [hálózati biztonsági csoport](../virtual-network/security-overview.md#network-security-groups)
* Egy [nyilvános IP-cím](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Egy [terheléselosztó](../load-balancer/load-balancer-overview.md)

Ezeket az erőforrásokat az előfizetést, amely tartalmazza a virtuális hálózat, a Batch-készlet létrehozásakor megadott vannak lefoglalva. Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](../azure-subscription-service-limits.md) vonatkoznak. Ha azt tervezi, hogy a nagy méretű készletet üzemelő példányok virtuális hálózatban, ellenőrizze az előfizetéshez tartozó kvóták ezekhez az erőforrásokhoz. Szükség esetén kérheti növelését az Azure Portalon kiválasztásával **súgó + támogatás**.


## <a name="related-topics"></a>Kapcsolódó témakörök
* [Hozzon létre egy Azure Batch-fiókot az Azure portal használatával](batch-account-create-portal.md)
* [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md)
* [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
