---
title: Azure-beli virtuális gépek áthelyezése egy másik régióba Azure Site Recovery
description: Az Azure IaaS virtuális gépek egyik Azure-régióból a másikba való áthelyezéséhez használja a Azure Site Recovery.
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 5ae930240872c00c8dbb45857e4e77d82766eadf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93398062"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Azure-beli virtuális gépek áthelyezése másik Azure-régióba

Előfordulhat, hogy az Azure-infrastruktúra szolgáltatásként (IaaS) virtuális gépeket szeretne áthelyezni egyik régióból a másikba, hogy javítsa a megbízhatóságot, a rendelkezésre állást, a felügyeletet vagy a szabályozást. Ez az oktatóanyag bemutatja, hogyan helyezhet át virtuális gépeket egy másik régióba Azure Site Recovery használatával. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Előfeltételek ellenőrzése
> * A forrásként szolgáló virtuális gépek előkészítése
> * A cél régió előkészítése
> * Adatmásolás a célként megadott régióba
> * A konfiguráció tesztelése
> * Az áthelyezés végrehajtása
> * Erőforrások elvetése a forrás régiójából


> [!IMPORTANT]
> Ez a cikk bemutatja, hogyan helyezhet át Azure-beli virtuális gépeket az egyik régióból *a másikba*. Ha a cél az infrastruktúra rendelkezésre állásának javítása a virtuális gépek rendelkezésre állási zónákra való áthelyezésével, tekintse meg az [Azure-beli virtuális gépek áthelyezése Availability Zonesre](move-azure-vms-avset-azone.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy rendelkezik Azure-beli virtuális gépekkel a forrásként szolgáló Azure *-régióban*, amelyet át szeretne helyezni.
- Győződjön meg arról, hogy a kiválasztott [forrástartomány-kombináció támogatott](./azure-to-azure-support-matrix.md#region-support), és körültekintően válassza ki a kívánt régiót.
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.
- A fiók engedélyeinek ellenőrzése. Ha most hozta létre az ingyenes Azure-fiókját, *akkor Ön* az előfizetés rendszergazdája. Ha nem a rendszergazda, akkor a rendszergazdával együttműködve szerezze be a szükséges engedélyeket:
  -  Ha engedélyezni szeretné a virtuális gép replikálását, és az Site Recovery használatával másolja az adatait a célhelyre, rendelkeznie kell a virtuális gép Azure-erőforrásokban való létrehozásához szükséges engedélyekkel. A „Virtuális gépek közreműködője” beépített szerepkör rendelkezik ezekkel az engedélyekkel. Az engedélyekkel a következőket teheti:
        - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
        - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
        - Írás a kiválasztott tárfiókra.

  - A Site Recovery-műveletek kezeléséhez is szükséges engedélyek. A Site Recovery közreműködő szerepkör rendelkezik az Azure Recovery Services-tárolóban Site Recovery műveletek kezeléséhez szükséges engedélyekkel.

## <a name="prepare-the-source-vms"></a>A forrásként szolgáló virtuális gépek előkészítése

1. Győződjön meg arról, hogy az áthelyezni kívánt Azure-beli virtuális gépek rendelkeznek a legfelső szintű tanúsítványokkal. Ha nem, akkor a biztonsági korlátozások miatt nem engedélyezheti az Adatmásolást a célként megadott régióba.

    - Windows rendszerű virtuális gépek esetén telepítse a legújabb Windows-frissítéseket, hogy a megbízható legfelső szintű tanúsítványok a gépen legyenek. A leválasztott környezetekben kövesse a szervezete szabványos Windows Update és tanúsítvány-frissítési folyamatait.
    - Linux rendszerű virtuális gépek esetén kövesse a linuxos forgalmazójának útmutatását a legutóbb megbízható főtanúsítványok és a visszavont tanúsítványok listájának lekéréséhez.
2. Győződjön meg arról, hogy nem használ hitelesítési proxyt az áthelyezni kívánt virtuális gépek hálózati kapcsolatának szabályozására.
3. Ha az áthelyezni kívánt virtuális gép nem fér hozzá az internethez, és tűzfal-proxyt használ a kimenő hozzáférés vezérléséhez, ellenőrizze a [követelményeket](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).
4. Dokumentálja a forrás hálózatkezelési elrendezést és az összes olyan erőforrást, amelyet jelenleg használ, beleértve a terheléselosztást, a hálózati biztonsági csoportokat és a nyilvános IP-címeket az ellenőrzéshez.

## <a name="prepare-the-target-region"></a>A cél régió előkészítése

1. Az Azure-előfizetésében ellenőrizze, hogy létrehozhat-e virtuális gépeket a vész-helyreállítási célra használt régióban. Ha szükséges, forduljon az ügyfélszolgálathoz, és engedélyezze a szükséges kvótát.

2. Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a forrásként szolgáló virtuális gépek támogatásához. Ha Site Recovery használatával másol Adatmásolást a célhelyre, akkor a célként megadott virtuális gépek méretének vagy legközelebb álló méretének kell megegyeznie.

3. Győződjön meg arról, hogy minden, a forrás hálózatkezelési elrendezésben azonosított összetevőhöz létrehoz egy cél erőforrást. Ezzel biztosíthatja, hogy a virtuális gépek minden olyan funkciót és funkciót felrendelkezzenek a céltartományban, amely a forrás régióban található.

   Azure Site Recovery automatikusan felkeresi és létrehoz egy virtuális hálózati és Storage-fiókot, amikor engedélyezi a forrás virtuális gép replikálását. Ezeket az erőforrásokat előre is létrehozhatja és hozzárendelheti a virtuális géphez az Enable-Replication lépés részeként. A célként megadott régióban azonban manuálisan kell létrehoznia a többi erőforrást. Tekintse át a következő dokumentumokat a leggyakrabban használt hálózati erőforrások létrehozásához a forrás virtuális gép konfigurációja alapján:

   - [Hálózati biztonsági csoportok](../virtual-network/manage-network-security-group.md)
   - [Terheléselosztók](../load-balancer/index.yml)
   - [Nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md)
    
   A többi hálózati összetevővel kapcsolatban lásd az [Azure hálózati dokumentációját](../index.yml?pivot=products&panel=network). 

4. A konfiguráció teszteléséhez az áthelyezés végrehajtása előtt manuálisan [hozzon létre egy nem éles hálózatot](../virtual-network/quick-create-portal.md) a céltartományban. A telepítés tesztelése során a telepítő minimális beavatkozást okoz az éles környezetben, és javasoljuk.
    
## <a name="copy-data-to-the-target-region"></a>Adatmásolás a célként megadott régióba
Az alábbi lépések a Azure Site Recovery használatával másolják az adatfájlokat a célhelyre.

### <a name="create-the-vault-in-any-region-except-the-source"></a>A tároló létrehozása bármely régióban, a forrás kivételével

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com)  >  **Recovery Servicesba**.
2. Válassza **az erőforrás létrehozása**  >  **felügyeleti eszközök**  >  **biztonsági mentése és site Recovery** lehetőséget.
3. A **név** mezőben adja meg a **ContosoVMVault** rövid nevét. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. Hozzon létre egy **ContosoRG** nevű erőforráscsoportot.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez tekintse meg a [Azure site Recovery díjszabását](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Recovery Services-tárolók esetében válassza az **Áttekintés**  >  **ConsotoVMVault**  >  **+ replikálás** lehetőséget.
7. A **forrás** mezőben válassza az **Azure** lehetőséget.
8. A **forrás helye** mezőben válassza ki azt a forrás-Azure-régiót, ahol a virtuális gépek jelenleg futnak.
9. Válassza ki a Azure Resource Manager telepítési modellt. Ezután válassza ki a **forrás-előfizetést** és a **forrás erőforráscsoportot**.
10. A beállítások mentéséhez kattintson **az OK gombra** .

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Az Azure-beli virtuális gépek replikálásának engedélyezése és az adatok másolásának megkezdése

Site Recovery beolvassa az előfizetéshez és az erőforráscsoporthoz társított virtuális gépek listáját.

1. Válassza ki az áthelyezni kívánt virtuális gépet, majd kattintson **az OK gombra**.
2. A **Beállítások** területen válassza a vész- **helyreállítás** lehetőséget.
3. A vész- **helyreállítási célcsoport konfigurálásához**  >  válassza ki azt a célként megadott régiót, amelyre replikálni kíván.
4. Válassza az alapértelmezett cél-erőforrások vagy az előre létrehozott beállítások használatát.
5. A feladatok elindításához válassza a **replikáció engedélyezése** lehetőséget.


 

## <a name="test-the-configuration"></a>A konfiguráció tesztelése


1. Nyissa meg a tárolót. A **Beállítások**  >  **replikált elemek** területen válassza ki azt a virtuális gépet, amelyet át szeretne helyezni a célként megadott régióba. Ezután válassza a **feladatátvételi teszt** lehetőséget.
2. A feladatátvételi **teszt** területen válassza ki a feladatátvételhez használandó helyreállítási pontot:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontnak adja át. Megjelenik az időbélyeg. A rendszer nem töltött le időt az adatfeldolgozás során, ezért ez a beállítás alacsony helyreállítási időcélkitűzést (RTO) biztosít.
   - Az **app-konzisztens legújabb**: az összes virtuális gép feladatátvétele a legújabb, alkalmazás-konzisztens helyreállítási ponttal történik. Megjelenik az időbélyeg.
   - **Egyéni**: Bármelyik helyreállítási pontot kiválaszthatja.

3. Válassza ki azt a célként szolgáló Azure-beli virtuális hálózatot, amelyre át szeretné helyezni az Azure-beli virtuális gépeket a konfiguráció teszteléséhez.

   > [!IMPORTANT]
   > Javasoljuk, hogy használjon külön Azure-beli virtuálisgép-hálózatot a feladatátvételi teszthez, nem pedig a célként megadott régió üzemi hálózatát.

4. Az áthelyezés tesztelésének megkezdéséhez kattintson **az OK gombra**. A folyamat nyomon követéséhez válassza ki a virtuális gépet, és nyissa meg a **tulajdonságait.** Vagy válassza ki a **feladatátvételi teszt** feladatot a tárolóban. Ezután válassza a **Beállítások**  >  **feladatok**  >  **site Recovery feladatok** lehetőséget.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. A teszteléshez létrehozott virtuális gép törléséhez válassza a **feladatátvételi teszt** törlése elemet a replikált elemen. A **jegyzetekből** jegyezze fel és mentse a teszttel kapcsolatos észrevételeket.

## <a name="perform-the-move-and-confirm"></a>Az áthelyezés és a megerősítés végrehajtása

1. Nyissa meg a tárolót a **Beállítások**  >  **replikált elemek** területen, válassza ki a virtuális gépet, majd válassza a **feladatátvétel** lehetőséget.
1. A **feladatátvételhez** válassza a **legutóbbi** lehetőséget. 
2. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. Site Recovery megkísérli leállítani a forrás virtuális gépet a feladatátvétel elindítása előtt. A feladatátvétel azonban akkor is folytatódik, ha a Leállítás sikertelen. A feladatátvételi folyamat a **feladatok** lapon követhető.
3. Ha a művelet elkészült, győződjön meg arról, hogy a virtuális gép a várt módon jelenik meg a cél Azure-régióban.
4. A **replikált elemek** területen kattintson a jobb gombbal a virtuális gépre, és válassza a **commit**(végrehajtás) lehetőséget. Ez befejezi az áthelyezést. Várjon, amíg a véglegesítési feladatok befejeződik.

## <a name="discard-the-resources-from-the-source-region"></a>Erőforrások elvetése a forrás régiójából

- Nyissa meg a virtuális gépet, és válassza a **replikáció letiltása** lehetőséget. Ezzel leállítja a virtuális gép adatok másolásának folyamatát.

  > [!IMPORTANT]
  > Ennek a lépésnek a végrehajtásával elkerülhető, hogy az áthelyezés után Site Recovery replikáció után ne kelljen fizetni.

Ha nem tervezi a forrás-erőforrások újrafelhasználását, kövesse az alábbi lépéseket:

1. Törölje a forrásként szolgáló [virtuális gépek előkészítésének](#prepare-the-source-vms)4. lépésében felsorolt összes releváns hálózati erőforrást.
2. Törölje a megfelelő Storage-fiókot a forrás régióban.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan helyezhet át Azure-beli virtuális gépeket egy másik Azure-régióba. Most már konfigurálhatja a vész-helyreállítást ezekhez a virtuális gépekhez.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)
