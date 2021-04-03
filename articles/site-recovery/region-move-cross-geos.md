---
title: Azure-beli virtuális gépek áthelyezése a kormányzati és a nyilvános régiók között Azure Site Recovery
description: Azure Site Recovery használatával Azure-beli virtuális gépeket helyezhet át az Azure Government és a nyilvános régiók között.
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: a76ebf95b92b6e1251a04daa9ffb48a9abe15b50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425347"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Azure-beli virtuális gépek áthelyezése Azure Government- és nyilvános régiók között 

Előfordulhat, hogy át szeretné helyezni a IaaS virtuális gépeket a Azure Government és a nyilvános régiók között a meglévő virtuális gépek rendelkezésre állásának növelése, a kezelhetőség javítása vagy az irányítási okokból, az [itt](azure-to-azure-move-overview.md)részletezett módon.

Amellett, hogy a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával felügyelje és koordinálja a helyszíni gépek és az Azure virtuális gépek vész-helyreállítását az üzletmenet folytonossága és a vész-helyreállítás (BCDR) érdekében, a site Recovery használatával is kezelheti az Azure-beli virtuális gépek másodlagos régióba való áthelyezését.       

Ez az oktatóanyag bemutatja, hogyan helyezheti át az Azure-beli virtuális gépeket a Azure Government és a nyilvános régiók között Azure Site Recovery használatával. Ugyanezt kiterjesztheti olyan virtuális gépek áthelyezésére, amelyek nem ugyanazon a földrajzi fürtön belül vannak. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek ellenőrzése
> * A forrásként szolgáló virtuális gépek előkészítése
> * A cél régió előkészítése
> * Adatmásolás a célként megadott régióba
> * A konfiguráció tesztelése
> * Az áthelyezés végrehajtása
> * A forrás régió erőforrásainak elvetése

> [!IMPORTANT]
> Ebből az oktatóanyagból megtudhatja, hogyan helyezhet át Azure-beli virtuális gépeket az Azure-beli virtuális gépekre vonatkozó normál vész-helyreállítási megoldás által nem támogatott Azure Government és nyilvános régiók vagy régiók között. Ha a forrás-és a célcsoportok pár [támogatott](./azure-to-azure-support-matrix.md#region-support), tekintse meg ezt a [dokumentumot](azure-to-azure-tutorial-migrate.md) az áthelyezéshez. Ha a követelmény a rendelkezésre állás javítása azáltal, hogy a virtuális gépeket egy rendelkezésre állási csoportba helyezi a kitűzött virtuális gépek egy másik régióban való áthelyezésével, tekintse meg az oktatóanyagot [itt](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Ez a módszer nem ajánlott a DR nem támogatott régió párok közötti konfigurálására, mivel a párok definiálják az adatkésést, ami kritikus fontosságú egy DR-forgatókönyv esetében.

## <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése

> [!NOTE]
> Győződjön meg arról, hogy ismeri a forgatókönyv [architektúráját és összetevőit](physical-azure-architecture.md) . Ez az architektúra a **virtuális gépek fizikai kiszolgálóként való kezelésével az** Azure-beli virtuális gépek áthelyezésére szolgál.

- Tekintse át az összes összetevő [támogatási követelményeit](vmware-physical-secondary-support-matrix.md) .
- Győződjön meg arról, hogy a replikálni kívánt kiszolgálók megfelelnek az Azure-beli [virtuális gépek követelményeinek](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Készítsen elő egy fiókot a mobilitási szolgáltatás automatikus telepítéséhez minden replikálni kívánt kiszolgálón.

- Vegye figyelembe, hogy miután felvette a feladatátvételt az Azure-beli célcsoportba, nem végezheti el a feladatátvételt közvetlenül a forrás régiójába. A replikációt újra be kell állítania a célhelyre.

### <a name="verify-azure-account-permissions"></a>Azure-fiók engedélyeinek ellenőrzése

Győződjön meg arról, hogy az Azure-fiókja rendelkezik a virtuális gépek Azure-ba történő replikálásához szükséges engedélyekkel.

- Tekintse át a gépek Azure-ba való replikálásához szükséges [engedélyeket](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) .
- Ellenőrizze és módosítsa az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) engedélyeit. 

### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Állítsa be a cél [Azure-hálózatot](../virtual-network/quick-create-portal.md).

- Az Azure-beli virtuális gépek akkor kerülnek ebbe a hálózatba, amikor a feladatátvétel után jönnek létre.
- A hálózatnak ugyanabban a régióban kell lennie, mint a Recovery Services-tárolónak


### <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása

Hozzon létre egy [Azure Storage-fiókot](../storage/common/storage-account-create.md).

- Site Recovery replikálja a helyszíni gépeket az Azure Storage-ba. Az Azure-beli virtuális gépek a tárolóból a feladatátvételt követően jönnek létre.
- A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.


## <a name="prepare-the-source-vms"></a>A forrásként szolgáló virtuális gépek előkészítése

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatást minden replikálni kívánt kiszolgálón telepíteni kell. A Site Recovery automatikusan telepíti ezt a szolgáltatást, amikor engedélyezi a replikációt a kiszolgálón. Az automatikus telepítéshez elő kell készítenie egy fiókot, amelyet Site Recovery fog használni a kiszolgálóhoz való hozzáféréshez.

- Tartományi vagy helyi fiókot is használhat
- Windows rendszerű virtuális gépek esetén, ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi gépen. Ehhez a Register ( **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**) területen adja hozzá a DWORD bejegyzés **LocalAccountTokenFilterPolicy**, amelynek értéke 1.
- A beállítás parancssori felületről való letiltásához írja be a következőt a beállításjegyzékbe:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux esetén a fióknak a forrás Linux-kiszolgáló gyökerének kell lennie.


## <a name="prepare-the-target-region"></a>A cél régió előkészítése

1. Győződjön meg arról, hogy Azure-előfizetése lehetővé teszi-e virtuális gépek létrehozását a vészhelyreállításhoz használni kívánt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

2. Ellenőrizze, hogy előfizetése rendelkezik-e a megfelelő erőforrásokkal a forrásként szolgáló virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. Ha Site Recovery használ az adatmásolásra a célhelyre, akkor a célként megadott virtuális gép méretének vagy a lehető legközelebb méretének kell megegyeznie.

3. Győződjön meg arról, hogy minden, a forrás hálózatkezelési elrendezésben azonosított összetevőhöz létrehoz egy célként megadott erőforrást. Ez fontos annak biztosítása érdekében, hogy a cél régióba való kivágás után a virtuális gépek a forrásban lévő összes funkcióval és funkcióval rendelkezzenek.

    > [!NOTE]
    > Azure Site Recovery automatikusan felkeresi és létrehoz egy virtuális hálózatot, amikor engedélyezi a forrás virtuális gép replikálását, vagy előre létrehoz egy hálózatot, és hozzárendeli a virtuális GÉPHEZ a replikálás engedélyezéséhez a felhasználói folyamaton. Az alább leírtak szerint azonban minden más erőforráshoz manuálisan kell létrehoznia azokat a célcsoportban.

     A következő dokumentumokban a forrás virtuális gép konfigurációja alapján hozza létre az Ön számára releváns leggyakrabban használt hálózati erőforrásokat.

    - [Network Security Groups (Hálózati biztonsági csoportok)](../virtual-network/manage-network-security-group.md)
    - [Terheléselosztók](../load-balancer/index.yml)
    - [Nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md)
    
    Bármely más hálózati összetevő esetében tekintse meg a hálózatkezelés [dokumentációját](../index.yml?pivot=products&panel=network).

4. Ha tesztelni szeretné a konfigurációt, manuálisan [hozzon létre nem éles hálózatot](../virtual-network/quick-create-portal.md) a célként megadott régióban. Ez minimális beavatkozást eredményez az éles környezetben, és ajánlott.

## <a name="copy-data-to-the-target-region"></a>Adatmásolás a célként megadott régióba
Az alábbi lépések bemutatják, hogyan használhatja a Azure Site Recovery az Adatmásolást a célként megadott régióba.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>A forrásrégió kivételével bármelyik régióban létrehozhat tárolót.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com)  >  **Recovery Servicesba**.
2. Kattintson **az erőforrás létrehozása**  >  **felügyeleti eszközök**  >  **biztonsági mentése és site Recovery** elemre.
3. A **Név** mezőben adja meg a **ContosoVMVault** rövid nevet. Ha több, mint egy. az előfizetés mezőben válassza ki a megfelelőt.
4. Hozzon létre egy **ContosoRG** nevű erőforráscsoportot.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók kereséséhez tekintse meg a földrajzi elérhetőség [Azure site Recovery díjszabását](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Recovery Services-tárolóban kattintson az **Áttekintés**  >  **ConsotoVMVault**  >  **+ replikálás** elemre.
7. Válassza **Az Azure**-ba  >  **nem virtualizált/egyéb** lehetőséget.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Állítsa be a konfigurációs kiszolgálót a virtuális gépek felderítésére.


Állítsa be a konfigurációs kiszolgálót, regisztrálja a tárolóban, és fedezze fel a virtuális gépeket.

1. Kattintson **site Recovery** az  >  **infrastruktúra előkészítése**  >  **forrás** lehetőségre.
2. Ha nem rendelkezik konfigurációs kiszolgálóval, kattintson a **+ konfigurációs kiszolgáló** elemre.
3. A **kiszolgáló hozzáadása** területen győződjön meg arról, hogy a **konfigurációs kiszolgáló** megjelenik a **kiszolgáló típusa mezőben**.
4. Töltse le a Site Recovery egyesített telepítési telepítőfájlt.
5. Töltse le a tároló regisztrációs kulcsát. Ezt az egyesített telepítő futtatásakor kell megadnia. A kulcs a generálásától számított öt napig érvényes.

   ![A forrás beállítása](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>A konfigurációs kiszolgáló regisztrálása a tárolóban

A Kezdés előtt tegye a következőket: 

#### <a name="verify-time-accuracy"></a>Időtartam pontosságának ellenőrzése
Győződjön meg arról, hogy a konfigurációs kiszolgáló számítógépén a rendszer órája szinkronizálva van egy [időkiszolgálóval](/windows-server/networking/windows-time-service/windows-time-service-top). Meg kell egyeznie. Ha legalább 15 perc van hátra, a telepítés sikertelen lehet.

#### <a name="verify-connectivity"></a>Kapcsolat ellenőrzése
Győződjön meg arról, hogy a számítógép a környezet alapján fér hozzá ezekhez az URL-címekhez: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Az IP-cím alapú tűzfalszabályok lehetővé teszik a kommunikációt a HTTPS (443) porton fent felsorolt összes Azure URL-címhez. Az IP-címtartományok egyszerűsítése és korlátozása érdekében javasoljuk, hogy az URL-szűrést végezze el.

- **Kereskedelmi IP** -címek – engedélyezi az [Azure-adatközpont IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653)és a https (443) portot. Az előfizetéshez tartozó Azure-régió IP-címtartományok engedélyezése a HRE, a biztonsági mentési, a replikálási és a tárolási URL-címek támogatásához.  
- **Kormányzati IP** -címek – engedélyezi a [Azure Government adatközpont IP-tartományait](https://www.microsoft.com/en-us/download/details.aspx?id=57063)és a https (443) portot az összes USA Korm.-régióhoz (Virginia, Texas, Arizona és Iowa) a HRE, a biztonsági mentési, a replikálási és a tárolási URL-címek támogatásához.  

#### <a name="run-setup"></a>A telepítő futtatása
Futtassa az egyesített telepítőt helyi rendszergazdaként a konfigurációs kiszolgáló telepítéséhez. A Process Server és a fő célkiszolgáló is alapértelmezés szerint telepítve van a konfigurációs kiszolgálón.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

A regisztráció befejeződése után a konfigurációs kiszolgáló megjelenik a tároló **Beállítások**  >  **kiszolgálók** lapján.

### <a name="configure-target-settings-for-replication"></a>A cél beállításainak konfigurálása a replikáláshoz

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson az **infrastruktúra előkészítése**  >  **cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. A cél telepítési modell meghatározása.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![Cél](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Új replikációs házirend létrehozásához kattintson **site Recovery infrastruktúra**-  >  **replikációs házirendek**  >  **+ replikációs házirend** elemre.
2. A **Replikációs szabályzat létrehozása** beállításnál adja meg a szabályzat nevét.
3. Az **RPO küszöbértéke** beállításnál adja meg a helyreállítási időkorlátot (RPO). Ez az érték határozza meg, hogy az adathelyreállítási pontok milyen gyakran jöjjenek létre. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
4. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú (hány órás) legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók. A rendszer a prémium tárolóra replikált gépek esetében 24 órás, a standard tárolóra replikált gépek esetében 72 órás megőrzést támogat.
5. Az alkalmazás **-konzisztens Pillanatképek gyakorisága** mezőben adhatja meg, hogy az alkalmazás-konzisztens pillanatképeket tartalmazó helyreállítási pontok milyen gyakran jöjjenek létre. A szabályzat létrehozásához kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/physical-azure-disaster-recovery/replication-policy.png)


A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. Alapértelmezés szerint a rendszer automatikusan létrehoz egy megfelelő szabályzatot a feladat-visszavételre is. Ha például a replikációs házirend a **rep-Policy** , akkor létrejön egy feladat-visszavételi szabályzat **rep-Policy-feladat-visszavétel** . Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

### <a name="enable-replication"></a>A replikáció engedélyezése

- Site Recovery telepíti a mobilitási szolgáltatást, ha a replikáció engedélyezve van.
- Ha engedélyezi a replikálást egy-kiszolgáló számára, a módosítások életbe léptetése 15 percet vagy hosszabb időt is igénybe vehet, és megjelenik a portálon.

1. Kattintson az **alkalmazás**  >  **forrásának** replikálása elemre.
2. A **Forrás** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **gép típusa** területen válassza a **fizikai gépek** lehetőséget.
4. Válassza ki a Process Server (a konfigurációs kiszolgáló) elemet. Ezután kattintson az **OK** gombra.
5. A **cél** mezőben válassza ki azt az előfizetést és erőforráscsoportot, amelyben létre szeretné hozni az Azure-beli virtuális gépeket a feladatátvételt követően. Válassza ki az Azure-ban használni kívánt üzembe helyezési modellt (klasszikus vagy erőforrás-kezelés).
6. Válassza ki az adatok replikálásához használni kívánt Azure-tárfiókot. 
7. Válassza ki azt az Azure-hálózatot, valamint alhálózatot, amelyhez a feladatátvételt követően felálló Azure virtuális gépek csatlakozni fognak.
8. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Válassza a **Konfigurálás később** lehetőséget az Azure-hálózat számítógépenkénti kiválasztásához. 
9. A **fizikai gépek** területen kattintson a **+ fizikai gép** elemre. Adja meg a nevet és az IP-címet. Válassza ki a replikálni kívánt gép operációs rendszerét. A kiszolgálók felderítése és listázása néhány percet vesz igénybe. 

   > [!WARNING]
   > Meg kell adnia az áthelyezni kívánt Azure-beli virtuális gép IP-címét

10. A **Tulajdonságok**  >  **konfigurálása tulajdonságok** területen válassza ki azt a fiókot, amelyet a Process-kiszolgáló a mobilitási szolgáltatás automatikus telepítéséhez fog használni a gépen.
11. A replikációs **Beállítások**  >  **replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs házirend van-e kiválasztva. 
12. Kattintson a **replikáció engedélyezése** lehetőségre. A **védelem engedélyezése** feladat előrehaladását a beállítások   >  **feladatok**  >  **site Recovery feladatok** menüpontban követheti nyomon. A **védelem véglegesítése** feladatok futtatása után a gép készen áll a feladatátvételre.


A hozzáadott kiszolgálók figyeléséhez ellenőrizheti a **konfigurációs kiszolgálók** utolsó felderített idejét a következő  >  **helyen:**. Ha gépeket szeretne felvenni anélkül, hogy az ütemezett felderítési időt kellene várnia, jelölje ki a konfigurációs kiszolgálót (ne kattintson rá), majd kattintson a **frissítés** gombra.

## <a name="test-the-configuration"></a>A konfiguráció tesztelése


1. Navigáljon a tárolóhoz, a **Beállítások**  >  **replikált elemek** területen kattintson arra a virtuális gépre, amelyet át szeretne helyezni a célhelyre, kattintson a **+ feladatátvételi teszt** ikonra.
2. A **Feladatátvételi teszt** területen válasszon ki egy helyreállítási pontot a feladatátvétel végrehajtásához:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontnak adja át. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás az összes virtuális gép feladatait a legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át. Megjelenik az időbélyeg.
   - **Egyéni**: Bármelyik helyreállítási pontot kiválaszthatja.

3. Válassza ki azt a célként szolgáló Azure-beli virtuális hálózatot, amelyre át szeretné helyezni az Azure-beli virtuális gépeket a konfiguráció teszteléséhez. 

   > [!IMPORTANT]
   > Javasoljuk, hogy használjon külön Azure-beli virtuálisgép-hálózatot a feladatátvételi teszthez, nem pedig azt a éles hálózatot, amelybe a replikálás engedélyezése után végül a virtuális gépeket át szeretné helyezni.

4. Az áthelyezés tesztelésének megkezdéséhez kattintson **az OK** gombra. A folyamat előrehaladásának megtekintéséhez a virtuális gépre kattintva megnyithatja a tulajdonságait. Vagy kattintson a **Feladatátvételi teszt** feladatra a tároló neve > **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. Ha törölni szeretné az áthelyezés tesztelésének részeként létrehozott virtuális gépet, kattintson a **feladatátvételi teszt** törlése lehetőségre a replikált elemen. A **jegyzetek** területen jegyezze fel és mentse a teszttel kapcsolatos megfigyeléseket.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Hajtsa végre az áthelyezést a célhelyre, és erősítse meg.

1. Keresse meg a tárolót, a **Beállítások**  >  **replikált elemek** területen kattintson a virtuális gépre, majd a **feladatátvétel** elemre.
2. A **Feladatátvétel** területen válassza a **Legújabb** elemet. 
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamat a **feladatok** lapon követhető. 
4. A feladatok befejezése után győződjön meg arról, hogy a virtuális gép a várt módon jelenik meg a cél Azure-régióban.
5. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Véglegesítés** parancsra. Ezzel befejezi az áthelyezési folyamatot a célként megadott régióba. Várjon, amíg a véglegesítő feladatok befejeződik.

## <a name="discard-the-resource-in-the-source-region"></a>Az erőforrás elvetése a forrás régióban 

- Navigáljon a virtuális géphez.  Kattintson a **replikáció letiltására**.  Ezzel leállítja a virtuális gép adatok másolásának folyamatát.  

   > [!IMPORTANT]
   > Ennek a lépésnek a végrehajtásával elkerülhető az ASR-replikáció felszámítása.

Ha nem tervezi a forrás-erőforrások újrafelhasználását, folytassa a következő lépésekkel.

1. Folytassa az összes olyan hálózati erőforrás törlését a forrás régiójában, amelyet a [forrásként szolgáló virtuális gépek előkészítése](#prepare-the-source-vms) során a 4. lépés részeként adott meg. 
2. Törölje a megfelelő Storage-fiókot a forrás régióban.



## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure-beli virtuális gépet egy másik Azure-régióba helyezett át. Most már beállíthatja az áthelyezett virtuális gép vész-helyreállítását.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)
