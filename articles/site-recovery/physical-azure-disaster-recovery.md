---
title: Fizikai helyi kiszolgálók vész-helyreállításának beállítása Azure Site Recovery
description: Ismerje meg, hogyan állíthatja be a vész-helyreállítást az Azure-ba helyszíni Windows-és Linux-kiszolgálókon a Azure Site Recovery szolgáltatással.
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.openlocfilehash: 5775f8ed9b657502f4865f3c4f311ea0dcd269ea
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581550"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Vész-helyreállítás beállítása az Azure-ba helyszíni fizikai kiszolgálók esetén

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be a helyszíni fizikai Windows-és Linux-kiszolgálók vész-helyreállítását az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure és a helyszíni előfeltételek beállítása
> * Helyreállítási tár létrehozása a Site Recovery számára 
> * A forrás-és cél replikációs környezetek beállítása
> * Replikációs házirend létrehozása
> * Kiszolgáló replikálásának engedélyezése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Győződjön meg arról, hogy ismeri a forgatókönyv [architektúráját és összetevőit](physical-azure-architecture.md) .
- Tekintse át az összes összetevő [támogatási követelményeit](vmware-physical-secondary-support-matrix.md) .
- Győződjön meg arról, hogy a replikálni kívánt kiszolgálók megfelelnek az Azure-beli [virtuális gépek követelményeinek](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Készítse elő az Azure-t. Szüksége lesz egy Azure-előfizetésre, egy Azure-beli virtuális hálózatra és egy Storage-fiókra.
- Készítsen elő egy fiókot a mobilitási szolgáltatás automatikus telepítéséhez minden replikálni kívánt kiszolgálón.

Mielőtt elkezdené, vegye figyelembe a következőket:

- Az Azure-ba történő feladatátvétel után a fizikai kiszolgálók nem adhatók vissza a helyszíni fizikai gépekhez. Csak a VMware virtuális gépeken végezhető el a feladat-visszavétel. 
- Ez az oktatóanyag a fizikai kiszolgáló vész-helyreállítását állítja be az Azure-ba a legegyszerűbb beállításokkal. Ha többet szeretne megtudni a további beállításokról, olvassa el a útmutatók:
    - Állítsa be a [replikációs forrást](physical-azure-set-up-source.md), beleértve a site Recovery konfigurációs kiszolgálót is.
    - A [replikációs cél](physical-azure-set-up-target.md) beállítása.
    - [Replikációs szabályzat](vmware-azure-set-up-replication.md) konfigurálása és a [replikáció engedélyezése](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Azure-fiók beállítása

Microsoft Azure- [fiók](https://azure.microsoft.com/)beszerzése.

- Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is.
- További információ a [site Recovery díjszabásáról](site-recovery-faq.md#pricing)és [díjszabásáról](https://azure.microsoft.com/pricing/details/site-recovery/).
- Megtudhatja, hogy mely [régiókat támogatja](https://azure.microsoft.com/pricing/details/site-recovery/) a site Recovery.

### <a name="verify-azure-account-permissions"></a>Azure-fiók engedélyeinek ellenőrzése

Győződjön meg arról, hogy az Azure-fiókja rendelkezik a virtuális gépek Azure-ba történő replikálásához szükséges engedélyekkel.

- Tekintse át a gépek Azure-ba való replikálásához szükséges [engedélyeket](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) .
- Ellenőrizze és módosítsa az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) engedélyeit. 



### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Hozzon létre egy [Azure-hálózatot](../virtual-network/quick-create-portal.md).

- Az Azure-beli virtuális gépek akkor kerülnek ebbe a hálózatba, amikor a feladatátvétel után jönnek létre.
- A hálózatnak ugyanabban a régióban kell lennie, mint a Recovery Services-tárolónak


## <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása

Hozzon létre egy [Azure Storage-fiókot](../storage/common/storage-account-create.md).

- Site Recovery replikálja a helyszíni gépeket az Azure Storage-ba. Az Azure-beli virtuális gépek a tárolóból a feladatátvételt követően jönnek létre.
- A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatást minden replikálni kívánt kiszolgálón telepíteni kell. A Site Recovery automatikusan telepíti ezt a szolgáltatást, amikor engedélyezi a replikációt a kiszolgálón. Az automatikus telepítéshez elő kell készítenie egy fiókot, amelyet Site Recovery fog használni a kiszolgálóhoz való hozzáféréshez.

- Tartományi vagy helyi fiókot is használhat
- Windows rendszerű virtuális gépek esetén, ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi gépen. Ehhez a Register ( **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**) területen adja hozzá a DWORD bejegyzés **LocalAccountTokenFilterPolicy**, amelynek értéke 1.
- A beállítás parancssori felületről való letiltásához írja be a következőt a beállításjegyzékbe:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux esetén a fióknak a forrás Linux-kiszolgáló gyökerének kell lennie.


## <a name="create-a-vault"></a>Tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Védelmi cél kiválasztása

Válassza ki, hogy mit szeretne replikálni, és replikálja a következőre:.

1. Kattintson a **Helyreállítási tárak** > tár elemre.
2. Az erőforrás menüben kattintson site Recovery az   >  **infrastruktúra előkészítése**  >  **védelmi cél** elemre.
3. A **védelem célja** beállításnál válassza **Az Azure**-ba  >  **nem virtualizált/egyéb** lehetőséget.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Állítsa be a konfigurációs kiszolgálót, regisztrálja a tárolóban, és fedezze fel a virtuális gépeket.

1. Kattintson **site Recovery** az  >  **infrastruktúra előkészítése**  >  **forrás** lehetőségre.
2. Ha nem rendelkezik konfigurációs kiszolgálóval, kattintson a **+ konfigurációs kiszolgáló** elemre.
3. A **kiszolgáló hozzáadása** területen győződjön meg arról, hogy a **konfigurációs kiszolgáló** megjelenik a **kiszolgáló típusa mezőben**.
4. Töltse le a Site Recovery egyesített telepítési telepítőfájlt.
5. Töltse le a tároló regisztrációs kulcsát. Ezt az egyesített telepítő futtatásakor kell megadnia. A kulcs a generálásától számított öt napig érvényes.

   ![Képernyőfelvétel: a telepítési fájl és a regisztrációs kulcs letöltésének lehetőségei.](./media/physical-azure-disaster-recovery/source-environment.png)


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


## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson az **infrastruktúra előkészítése**  >  **cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. A cél telepítési modell meghatározása.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![Képernyőkép a cél környezet beállításának lehetőségeiről.](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Új replikációs házirend létrehozásához kattintson **site Recovery infrastruktúra**-  >  **replikációs házirendek**  >  **+ replikációs házirend** elemre.
2. A **Replikációs szabályzat létrehozása** beállításnál adja meg a szabályzat nevét.
3. Az **RPO küszöbértéke** beállításnál adja meg a helyreállítási időkorlátot (RPO). Ez az érték határozza meg, hogy az adathelyreállítási pontok milyen gyakran jöjjenek létre. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
4. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú (hány órás) legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók. A rendszer a prémium tárolóra replikált gépek esetében 24 órás, a standard tárolóra replikált gépek esetében 72 órás megőrzést támogat.
5. Az alkalmazás **-konzisztens Pillanatképek gyakorisága** mezőben adhatja meg, hogy az alkalmazás-konzisztens pillanatképeket tartalmazó helyreállítási pontok milyen gyakran jöjjenek létre. A szabályzat létrehozásához kattintson az **OK** gombra.

    ![Képernyőkép a replikációs házirend létrehozási lehetőségeiről.](./media/physical-azure-disaster-recovery/replication-policy.png)


A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. Alapértelmezés szerint a rendszer automatikusan létrehoz egy megfelelő szabályzatot a feladat-visszavételre is. Ha például a replikációs házirend a **rep-Policy** , akkor létrejön egy feladat-visszavételi szabályzat **rep-Policy-feladat-visszavétel** . Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezze a replikációt az egyes kiszolgálókon.

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
10. A **Tulajdonságok**  >  **konfigurálása tulajdonságok** területen válassza ki azt a fiókot, amelyet a Process-kiszolgáló a mobilitási szolgáltatás automatikus telepítéséhez fog használni a gépen.
11. A replikációs **Beállítások**  >  **replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs házirend van-e kiválasztva. 
12. Kattintson a **replikáció engedélyezése** lehetőségre. A **védelem engedélyezése** feladat előrehaladását a beállítások   >  **feladatok**  >  **site Recovery feladatok** menüpontban követheti nyomon. A **védelem véglegesítése** feladatok futtatása után a gép készen áll a feladatátvételre.


A hozzáadott kiszolgálók figyeléséhez ellenőrizheti a **konfigurációs kiszolgálók** utolsó felderített idejét a következő  >  **helyen:**. Ha gépeket szeretne felvenni anélkül, hogy az ütemezett felderítési időt kellene várnia, jelölje ki a konfigurációs kiszolgálót (ne kattintson rá), majd kattintson a **frissítés** gombra.

## <a name="next-steps"></a>Következő lépések

[Futtasson vész-helyreállítási részletezést](tutorial-dr-drill-azure.md).
