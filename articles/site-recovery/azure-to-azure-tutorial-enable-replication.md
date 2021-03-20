---
title: Oktatóanyag az Azure-beli virtuális gépek vészhelyzeti helyreállításának beállításához Azure Site Recovery
description: Ebben az oktatóanyagban az Azure-beli virtuális gépek vész-helyreállítását egy másik Azure-régióba állítja be a Site Recovery szolgáltatás használatával.
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 473a264ef497cab4bd4f88372600161b33178099
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656869"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>Oktatóanyag: vész-helyreállítás beállítása Azure-beli virtuális gépekhez

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be az Azure-beli virtuális gépek vész-helyreállítását [Azure site Recovery](site-recovery-overview.md)használatával. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az Azure-beállítások és-engedélyek ellenőrzése
> * A replikálni kívánt virtuális gépek előkészítése
> * Recovery Services-tároló létrehozása
> * VIRTUÁLIS gépek replikálásának engedélyezése

Ha engedélyezi a virtuális gép replikálását a vész-helyreállítás beállításához, akkor a Site Recovery mobilitási szolgáltatás bővítmény a virtuális gépre települ, és regisztrálja azt a Azure Site Recovery. A replikáció során a rendszer a virtuálisgép-lemezek írásait egy, a forrás régiójában lévő cache Storage-fiókba továbbítja. Az adatok innen érkeznek a célhelyre, a helyreállítási pontok pedig az adatokból jönnek létre. Ha egy virtuális gépet a vész-helyreállítási folyamat során hajt végre, a rendszer helyreállítási pontot használ a virtuális gép a célként megadott régióban való visszaállításához.

> [!NOTE]
> Az oktatóanyagok a legegyszerűbb alapértelmezett beállításokkal nyújtanak útmutatást. Ha egyéni beállításokkal szeretné beállítani az Azure VM vész-helyreállítást, tekintse át [ezt a cikket](azure-to-azure-how-to-enable-replication.md).

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt:

- [Tekintse át a támogatott régiókat](azure-to-azure-support-matrix.md#region-support). Az Azure-beli virtuális gépek vész-helyreállítását az azonos földrajzbeli két régió között is beállíthatja.
- Szüksége lesz egy vagy több Azure-beli virtuális gépre. Ellenőrizze, hogy a [Windows](azure-to-azure-support-matrix.md#windows) vagy [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) rendszerű virtuális gépek támogatottak-e.
- Tekintse át a virtuális gépek [számítási](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [tárolási](azure-to-azure-support-matrix.md#replicated-machines---storage)és [hálózati](azure-to-azure-support-matrix.md#replicated-machines---networking) követelményeit.
- Ez az oktatóanyag feltételezi, hogy a virtuális gépek nem titkosítottak. Ha a titkosított virtuális gépek vész-helyreállítását szeretné beállítani, [kövesse ezt a cikket](azure-to-azure-how-to-enable-replication-ade-vms.md).

## <a name="check-azure-settings"></a>Az Azure-beállítások keresése

Győződjön meg az engedélyek és a beállítások megadásáról a célként megadott régióban.

### <a name="check-permissions"></a>Engedélyek keresése

Az Azure-fióknak engedéllyel kell rendelkeznie Recovery Services-tároló létrehozásához, valamint virtuális gépek létrehozásához a céltartományban.

- Ha most létrehozott egy ingyenes Azure-előfizetést, akkor Ön a fiók rendszergazdája, és nincs szükség további műveletre.
- Ha nem Ön a rendszergazda, akkor a rendszergazdával együttműködve kapja meg a szükséges engedélyeket.
    - Tár **létrehozása**: rendszergazdai vagy tulajdonosi engedélyek az előfizetéshez. 
    - **Site Recovery műveletek kezelése a** tárolóban: a *site Recovery közreműködő* beépített Azure-szerepköre.
    - **Azure-beli virtuális gépek létrehozása a célként megadott régióban**: vagy a beépített *virtuálisgép-közreműködő* szerepkör vagy a következőkre vonatkozó konkrét engedélyek:
        - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
        - Írás egy Azure Storage-fiókba.
        - Írás egy Azure által felügyelt lemezre.

### <a name="verify-target-settings"></a>Cél beállításainak ellenőrzése

A vész-helyreállítási folyamat során a rendszer a forrás régiójából végez feladatátvételt, és a virtuális gépeket a célként megadott régióban hozza létre. 

Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a célként megadott régióban. Létre kell hoznia olyan virtuális gépeket, amelyek mérete megegyezik a forrás régiójában lévő virtuális gépekkel. A vész-helyreállítás beállításakor Site Recovery a célként megadott virtuális gép méretének (vagy a legközelebbi lehetséges méretének) a megadására kerül sor.


## <a name="prepare-vms"></a>Virtuális gépek előkészítése

Győződjön meg arról, hogy a virtuális gépek kimenő kapcsolattal rendelkeznek, és a legfelső szintű tanúsítványokat. 


### <a name="set-up-vm-connectivity"></a>Virtuálisgép-kapcsolat beállítása

A replikálni kívánt virtuális gépeknek kimenő hálózati kapcsolatra van szükségük.

> [!NOTE]
> A Site Recovery nem támogatja a hitelesítési proxy használatát a hálózati kapcsolat vezérléséhez.

#### <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, engedélyezze az alábbi URL-címek elérését:

| **Név**                  | **Kereskedelmi**                               | **Államigazgatás**                                 | **Leírás** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Tárolás                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Lehetővé teszi az adatok írását a virtuális gépről a forrásrégió gyorsítótárjának tárfiókjába. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Hitelesítést és engedélyezést biztosít a Site Recovery szolgáltatás URL-címeihez. |
| Replikálás               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Lehetővé teszi a virtuális gép és a Site Recovery szolgáltatás közötti kommunikációt. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Lehetővé teszi a virtuális gép számára a Site Recovery monitorozási és diagnosztikai adatainak írását. |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolat az IP-címtartományokhoz

Ha hálózati biztonsági csoportokat (NSG) használ a kapcsolatok vezérléséhez, hozzon létre olyan szolgáltatás-címkén alapuló NSG-szabályokat, amelyek engedélyezik a HTTPS-kimenetet a 443-as port számára ezen [szolgáltatási címkék](../virtual-network/service-tags-overview.md#available-service-tags)(IP-címek csoportjai) számára:

**Tag** | **Engedélyezés** 
--- | ---
Storage-címke  |Lehetővé teszi az adatok a virtuális gépről a gyorsítótárbeli Storage-fiókba való írását.   
Azure AD-címke | Engedélyezi az Azure AD-nek megfelelő összes IP-cím elérését.   
EventsHub címke | Hozzáférést biztosít Site Recovery figyeléshez.  
AzureSiteRecovery címke | Engedélyezi a Site Recovery szolgáltatás elérését bármely régióban.   
GuestAndHybridManagement címke | Akkor használja, ha szeretné automatikusan frissíteni a replikálásra engedélyezett virtuális gépeken futó Site Recovery mobilitási ügynököt.

[További](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) információ a szükséges címkékről és a címkézési példákról.

### <a name="verify-vm-certificates"></a>Virtuális gép tanúsítványainak ellenőrzése

Győződjön meg arról, hogy a virtuális gépek rendelkeznek a legfelső szintű tanúsítvánnyal. Ellenkező esetben a virtuális gép nem regisztrálható Site Recovery biztonsági korlátozások miatt.

- **Windows rendszerű virtuális gépek**: telepítse a virtuális gépre a legújabb Windows-frissítéseket, hogy az összes megbízható főtanúsítvány a gépen legyen. A leválasztott környezetekben kövesse a Windows Update és a tanúsítványok frissítéseinek szabványos folyamatait.
- **Linuxos virtuális gépek**: kövesse a Linux-terjesztő által biztosított útmutatást a legújabb megbízható főtanúsítványok és a visszavont tanúsítványok listájának (CRL) beszerzéséhez.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Hozzon létre egy Recovery Services-tárolót bármely régióban, kivéve azt a forrás-régiót, amelyről a virtuális gépeket replikálni kívánja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A keresőmezőbe írja be a *helyreállítás* kifejezést. A **szolgáltatások** területen válassza a **Recovery Services**-tárolók lehetőséget.

    ![Recovery Services-tárolók keresése](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. **Recovery Services**-tárolókban válassza a **Hozzáadás** lehetőséget.
4. Az **Recovery Services**-tároló  >  **alapjai** területen válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárolót.
5. Az **erőforráscsoport** területen válasszon ki egy meglévő erőforráscsoportot a tárolóhoz, vagy hozzon létre egy újat.
6. A tár **neve** mezőben adjon meg egy rövid nevet a tároló azonosításához.
7. A **régió** területen válassza ki azt az Azure-régiót, amelyben a tárolót helyezni kívánja. [Keresse meg a támogatott régiókat](https://azure.microsoft.com/pricing/details/site-recovery/).
8. Válassza az **Áttekintés + létrehozás** lehetőséget.

   ![Tároló beállításai a lapon új tár létrehozásához](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. A **felülvizsgálat + létrehozás** területén válassza a **Létrehozás** lehetőséget.

10. A tár üzembe helyezése megkezdődik. Kövesse az értesítések előrehaladását.
11. A tár üzembe helyezése után válassza a **rögzítés az irányítópulton** lehetőséget a gyors hivatkozáshoz való mentéshez. Az új tár megnyitásához válassza az **erőforrás** megnyitása lehetőséget. 
    
    ![A tár telepítés utáni megnyitására és az irányítópultra való rögzítésre szolgáló gombok](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Site Recovery engedélyezése

A tároló beállításainál válassza a **site Recovery engedélyezése** lehetőséget.

![A Site Recovery a tárolóban való engedélyezésének kiválasztása](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>A replikáció engedélyezése

Válassza ki a forrás beállításait, és engedélyezze a virtuális gép replikálását. 

### <a name="select-source-settings"></a>Forrás beállításainak kiválasztása

1. A tároló > **site Recovery** oldalon, az **Azure Virtual Machines** területen válassza a **replikáció engedélyezése** lehetőséget.

    ![Az Azure-beli virtuális gépek replikálását engedélyező kijelölés](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. A **forrás** >  **forrás helye** területen válassza ki azt a forrás-Azure-régiót, amelyben a virtuális gépek jelenleg futnak.
3. Az **Azure-beli virtuális gépek üzembe helyezési modelljében** hagyja meg az alapértelmezett **Resource Manager** -beállítást.
4. A **forrás-előfizetés** területen válassza ki azt az előfizetést, amelyben a virtuális gépek futnak. Bármely olyan előfizetést kiválaszthat, amely ugyanabban a Azure Active Directory-(AD-) bérlőben található, mint a tároló.
5. A **forrás erőforráscsoport** területen válassza ki a virtuális gépeket tartalmazó erőforráscsoportot.
6. A **rendelkezésre állási zónák közötti vész-helyreállításnál** hagyja meg az alapértelmezett **nem** beállítást.

     ![A forrás beállítása](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. Kattintson a **Tovább** gombra.

### <a name="select-the-vms"></a>A virtuális gépek kiválasztása

Site Recovery lekéri a kiválasztott előfizetéshez/erőforráscsoporthoz társított virtuális gépeket.

1. A **Virtual Machines** területen válassza ki azokat a virtuális gépeket, amelyeknek engedélyezni szeretné a vész-helyreállítást.

     ![Virtuális gépek kiválasztása a replikáláshoz](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. Kattintson a **Tovább** gombra.

### <a name="review-replication-settings"></a>Replikációs beállítások áttekintése

1. A **replikációs beállítások** lapon tekintse át a beállításokat. Site Recovery alapértelmezett beállításokat/házirendet hoz létre a célként megadott régióhoz. Ebben az oktatóanyagban az alapértelmezett beállításokat használjuk.
2. Válassza a **Replikáció engedélyezése** elemet.

    ![Lap a beállítások testreszabásához és a replikáció engedélyezéséhez](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. Az értesítések replikálási folyamatának nyomon követése.

     ![Az értesítések előrehaladásának követése nyomon ](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![ követheti a sikeres replikálási értesítést](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. Az Ön által engedélyezett virtuális gépek a tároló > **replikált elemek** lapon jelennek meg.

    ![Virtuális gép a replikált elemek oldalon](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte a vész-helyreállítást egy Azure-beli virtuális gépen. Most futtasson egy részletezést, és győződjön meg róla, hogy a feladatátvétel a várt módon működik-e.

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](azure-to-azure-tutorial-dr-drill.md)
