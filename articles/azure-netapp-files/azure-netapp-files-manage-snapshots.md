---
title: Pillanatképek kezelése Azure NetApp Files használatával | Microsoft Docs
description: Útmutató a pillanatképek létrehozásához, kezeléséhez és használatához Azure NetApp Files használatával.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/20/2021
ms.author: b-juche
ms.openlocfilehash: a18c53d972fbb38dc0b0e557d14b2fbffbff15fa
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174359"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Pillanatképek kezelése az Azure NetApp Filesszal

Azure NetApp Files támogatja az igény szerinti pillanatképek létrehozását és a pillanatkép-szabályzatok használatát az automatikus pillanatkép-létrehozás ütemezéséhez. Új kötetre is visszaállíthat egy pillanatképet, visszaállíthat egyetlen fájlt egy ügyféllel, vagy visszaállíthat egy meglévő kötetet egy pillanatkép használatával.

> [!NOTE] 
> A régiók közötti replikáció pillanatkép-kezelésével kapcsolatos megfontolásokat a [régiók közötti replikáció használatára vonatkozó követelmények és megfontolások](cross-region-replication-requirements-considerations.md)című témakörben talál.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Igény szerinti pillanatkép létrehozása kötethez

Igény szerint kötet-pillanatképeket is létrehozhat. 

1.  Lépjen arra a kötetre, amelyhez pillanatképet szeretne készíteni. Kattintson a **Pillanatképek** elemre.

    ![Pillanatképek navigálása](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Kattintson a **+ Pillanatkép hozzáadása** lehetőségre egy kötethez igény szerinti pillanatkép létrehozásához.

    ![Pillanatkép hozzáadása](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Az új pillanatkép ablakban adja meg a létrehozandó új pillanatkép nevét.   

    ![Új pillanatkép](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Kattintson az **OK** gombra. 

## <a name="manage-snapshot-policies"></a>Pillanatkép-házirendek kezelése

A pillanatkép-szabályzatok használatával automatikusan elvégezheti a kötetek pillanatképeit. Szükség szerint módosíthatja a pillanatkép-szabályzatot, vagy törölheti a már nem szükséges pillanatkép-szabályzatot is.  

### <a name="register-the-feature"></a>A szolgáltatás regisztrálása

A **Pillanatkép-házirend** szolgáltatás jelenleg előzetes verzióban érhető el. Ha először használja ezt a funkciót, első lépésként regisztrálnia kell a funkciót. 

1. Regisztrálja a szolgáltatást: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. A szolgáltatás regisztrálási állapotának ellenõrzése: 

    > [!NOTE]
    > A **RegistrationState** a `Registering` módosítás előtt legfeljebb 60 percig lehet `Registered` . A folytatás előtt várjon, amíg az állapot **regisztrálva** lesz.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
Használhatja az [Azure CLI-parancsokat](/cli/azure/feature) is, `az feature register` és `az feature show` regisztrálhatja a funkciót, és megjelenítheti a regisztrációs állapotot. 

### <a name="create-a-snapshot-policy"></a>Pillanatkép-szabályzat létrehozása 

A pillanatkép-szabályzat lehetővé teszi a pillanatképek létrehozásának gyakoriságát óránként, naponta, hetente vagy havonta. Meg kell adnia a köteten megőrizni kívánt Pillanatképek maximális számát is.  

1.  A NetApp-fiók nézetében kattintson a **Pillanatkép-házirend** elemre.

    ![Pillanatkép-házirend navigációja](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  A pillanatkép-házirend ablakban állítsa be a házirend állapotát **engedélyezve** értékre. 

3.  Óránkénti, napi, heti vagy havi pillanatkép-szabályzatok létrehozásához kattintson az **óránként**, **naponta**, **hetente** vagy **havonta** fülre. A **megőrizni kívánt Pillanatképek számának** meghatározása.  

    Tekintse meg a kötetek számára engedélyezett maximális számú pillanatképet tartalmazó [Azure NetApp Files erőforrás-korlátozásait](azure-netapp-files-resource-limits.md) . 

    Az alábbi példa az óránkénti Pillanatképek házirend-konfigurációját mutatja be. 

    ![Pillanatkép-szabályzat óránként](../media/azure-netapp-files/snapshot-policy-hourly.png)

    Az alábbi példa a napi Pillanatképek házirend-konfigurációját mutatja be.

    ![Pillanatkép-szabályzat naponta](../media/azure-netapp-files/snapshot-policy-daily.png)

    Az alábbi példa a heti Pillanatképek házirend-konfigurációját mutatja be.

    ![Pillanatkép-szabályzat hetente](../media/azure-netapp-files/snapshot-policy-weekly.png)

    Az alábbi példa a havi Pillanatképek házirend-konfigurációját mutatja be.

    ![Pillanatkép-szabályzat havonta](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Kattintson a **Mentés** gombra.  

Ha további pillanatkép-házirendeket kell létrehoznia, ismételje meg a 3. lépést.
A létrehozott szabályzatok megjelennek a pillanatkép-szabályzat lapon.

Ha azt szeretné, hogy a kötet a pillanatkép-szabályzatot használja, a [kötetre kell alkalmaznia a házirendet](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Pillanatkép-szabályzat alkalmazása kötetre

Ha azt szeretné, hogy a kötet egy létrehozott pillanatkép-szabályzatot használjon, a kötetre kell alkalmaznia a szabályzatot. 

A régiók közötti replikációban nem alkalmazhat pillanatkép-szabályzatot a célként megadott kötetre.  

1.  Lépjen a **kötetek** lapra, kattintson a jobb gombbal arra a kötetre, amelyre a pillanatkép-szabályzatot alkalmazni kívánja, majd válassza a **Szerkesztés** lehetőséget.

    ![Kötetek jobb gombbal kattintva menü](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  A Szerkesztés ablak **Pillanatkép-házirend** területén válassza ki a kötethez használni kívánt szabályzatot.  A szabályzat alkalmazásához kattintson **az OK** gombra.  

    ![Pillanatkép-szabályzat szerkesztése](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Pillanatkép-szabályzat módosítása 

Módosíthat egy meglévő pillanatkép-szabályzatot a házirend állapotának, a pillanatképek gyakoriságának (óránként, naponta, hetente vagy havonta), illetve a megtartani kívánt Pillanatképek számának módosításához.  
 
1.  A NetApp-fiók nézetében kattintson a **Pillanatkép-házirend** elemre.

2.  Kattintson a jobb gombbal a módosítani kívánt pillanatkép-szabályzatra, majd válassza a **Szerkesztés** lehetőséget.

    ![Pillanatkép-házirend kattintson a jobb gombbal a menüre](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Hajtsa végre a megjelenő pillanatkép-házirend ablak módosításait, majd kattintson a **Mentés** gombra. 

### <a name="delete-a-snapshot-policy"></a>Pillanatkép-szabályzat törlése 

Törölheti azt a pillanatkép-szabályzatot, amelyet már nem szeretne megőrizni.   

1.  A NetApp-fiók nézetében kattintson a **Pillanatkép-házirend** elemre.

2.  Kattintson a jobb gombbal a módosítani kívánt pillanatkép-szabályzatra, majd válassza a **Törlés** lehetőséget.

    ![Pillanatkép-házirend kattintson a jobb gombbal a menüre](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Kattintson az **Igen** gombra annak megerősítéséhez, hogy törölni kívánja a pillanatkép-szabályzatot.   

    ![Pillanatkép-szabályzat törlésének megerősítése](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="edit-the-hide-snapshot-path-option"></a>A pillanatkép elérési útjának elrejtése lehetőség szerkesztése
A pillanatkép elérési útjának elrejtése beállítás azt szabályozza, hogy látható-e a kötet pillanatképének elérési útja. Egy [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) -vagy [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) -kötet létrehozása során lehetősége van megadnia, hogy a pillanatkép elérési útja rejtett legyen-e. Ezt követően szükség szerint szerkesztheti a pillanatkép elérési útjának elrejtése beállítást.  

> [!NOTE]
> A régiók közötti replikálásban a [cél kötet](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume) esetében a pillanatkép elérési útjának elrejtése beállítás alapértelmezés szerint engedélyezve van, és a beállítás nem módosítható. 

1. A kötet pillanatkép-elérési útjának elrejtése beállításának megtekintéséhez válassza ki a kötetet. A **Pillanatkép elérési útjának elrejtése** mező jelzi, hogy a beállítás engedélyezve van-e.   
    ![A pillanatkép elérési útjának elrejtése mezőt ismertető képernyőkép.](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. A pillanatkép elérési útjának elrejtése beállítás szerkesztéséhez kattintson a kötet lapon a **Szerkesztés** gombra, és szükség szerint módosítsa a **Pillanatkép elérési útjának elrejtése** beállítást.   
    ![A mennyiségi pillanatkép szerkesztése lehetőséget ismertető képernyőkép.](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Pillanatkép visszaállítása új kötetre

Jelenleg csak egy új kötetre állíthatja vissza a pillanatképet. 
1. A pillanatképek listájának megjelenítéséhez válassza a kötetek panel **Pillanatképek** elemét. 
2. Kattintson a jobb gombbal a visszaállítani kívánt pillanatképre, majd válassza a menü **új kötet visszaállítása** elemét.  

    ![Pillanatkép visszaállítása új kötetre](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. A kötet létrehozása ablakban adja meg az új kötet adatait:  
    * **név**   
        Adja meg a létrehozni kívánt kötet nevét.  
        
        A névnek egyedinek kell lennie az erőforráscsoporton belül. Legalább három karakter hosszúnak kell lennie.  Bármilyen alfanumerikus karakter használható.

    * **Kvóta**  
        Itt adhatja meg a kötethez lefoglalni kívánt logikai tárterület mennyiségét.  

    ![Visszaállítás új kötetre](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Kattintson a **felülvizsgálat + létrehozás** gombra.  Kattintson a **Létrehozás** lehetőségre.   
    Az új kötet ugyanazt a protokollt használja, mint amelyet a pillanatkép használ.   
    Az új kötet, amelybe a pillanatkép vissza lett állítva, megjelenik a kötetek panelen.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>Fájl visszaállítása pillanatképből ügyfél használatával

Ha nem szeretné [visszaállítani a teljes pillanatképet egy kötetre](#restore-a-snapshot-to-a-new-volume), lehetősége van visszaállítani egy fájlt egy pillanatképből egy olyan ügyfél használatával, amelyhez a kötet csatlakoztatva van.  

A csatlakoztatott kötet tartalmazza  `.snapshot` az ügyfél számára elérhető pillanatkép-könyvtárat (az NFS-ügyfeleken) vagy `~snapshot` (az SMB-ügyfeleken). A pillanatkép könyvtára a kötet pillanatképeit reprezentáló alkönyvtárakat tartalmaz. Minden alkönyvtár tartalmazza a pillanatkép fájljait. Ha véletlenül töröl vagy felülír egy fájlt, visszaállíthatja a fájlt a szülő írható-olvasható könyvtárára úgy, hogy a fájlt egy pillanatkép-alkönyvtárból az írható-olvasható könyvtárba másolja. 

A pillanatkép-címtárak elérését a [Pillanatkép-elérési út elrejtése lehetőség](#edit-the-hide-snapshot-path-option)használatával szabályozhatja. Ezzel a beállítással szabályozható, hogy a címtár el legyen-e rejtve az ügyfelektől. Ezért a pillanatképekben lévő fájlokhoz és mappákhoz való hozzáférést is szabályozza.  

A NFSv 4.1 nem jeleníti meg a `.snapshot` könyvtárat ( `ls -la` ). Ha azonban a pillanatkép-elérési út elrejtése beállítás nincs beállítva, a könyvtárat a NFSv 4.1-es verzióján keresztül is elérheti az `.snapshot` `cd <snapshot-path>` ügyfél parancssorában található paranccsal. 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Fájl visszaállítása Linux NFS-ügyfél használatával 

1. A `ls` Linux parancs használatával listázhatja a címtárból visszaállítani kívánt fájlt `.snapshot` . 

    Például:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. A `cp` parancs használatával másolja a fájlt a szülő könyvtárba.  

    Például: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Fájl visszaállítása Windows-ügyfél használatával 

1. Ha a `~snapshot` kötet könyvtára rejtett, jelenítse meg a szülő könyvtár [rejtett elemeit](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) `~snapshot` .

    ![Rejtett elemek megjelenítése](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. A `~snapshot` visszaállítani kívánt fájl megkereséséhez navigáljon a belül található alkönyvtárra.  Kattintson a jobb gombbal a fájlra. Válassza a **Másolás** lehetőséget.  

    ![Fájl másolása a visszaállításhoz](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Térjen vissza a szülő könyvtárhoz. Kattintson a jobb gombbal a szülő könyvtárra, és válassza ki a `Paste` fájlt a könyvtárba.

    ![A visszaállítandó fájl beillesztése](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. Azt is megteheti, hogy a jobb gombbal a szülő könyvtárra kattint, majd kiválasztja a **Tulajdonságok** elemet, majd a **korábbi verziók** lapon megtekintheti a pillanatképek listáját, és a **visszaállítás** elemre kattintva állíthatja vissza a fájlt.  

    ![Korábbi verziók tulajdonságai](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="revert-a-volume-using-snapshot-revert"></a>Kötet visszaállítása pillanatkép-visszavonással

A pillanatkép-visszavonási funkció lehetővé teszi, hogy gyorsan visszaállítson egy kötetet arra az állapotba, amely egy adott pillanatkép készítésekor megtörtént. A legtöbb esetben a kötetek visszafordítása sokkal gyorsabb, mint az egyes fájlok pillanatképből az aktív fájlrendszerbe való visszaállítása. Emellett a pillanatképek új kötetre való visszaállításához is nagyobb a terület. 

A hangerő visszaállítása lehetőség a kötetek Pillanatképek menüjében található. Miután kiválasztott egy pillanatképet a visszaállításhoz, Azure NetApp Files visszaállít a kötetet a kijelölt pillanatkép készítésekor tárolt adatokhoz és időbélyegekhez. 

> [!IMPORTANT]
> A kiválasztott pillanatkép elkészítése után elvégzett aktív fájlrendszerbeli adatok és Pillanatképek elvesznek. A pillanatkép-visszavonási művelet lecseréli a célként megadott köteten lévő *összes* olyan adatmennyiséget, amely a kijelölt pillanatképben lévő adatokkal rendelkezik. A pillanatképek kiválasztásakor figyelmet kell fordítani a pillanatkép tartalmára és a létrehozási dátumra. A pillanatkép-visszaállítási művelet nem vonható vissza.

1. Ugrás a kötetek **Pillanatképek** menüjére.  Kattintson a jobb gombbal arra a pillanatképre, amelyet a REVERT művelethez használni szeretne. Válassza a **kötet visszaállítása** lehetőséget. 

    ![Képernyőkép a pillanatképek jobb gombbal kattintásos menüjéről](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. A kötet visszaállítása Pillanatképbe ablakba írja be a kötet nevét, majd kattintson a **REVERT**(helyreállítás) elemre.   

    A kötet most már vissza lett állítva a kiválasztott pillanatkép időpontjára.

    ![Képernyőfelvétel a kötet visszaállítása pillanatkép-ablakra](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="delete-snapshots"></a>Pillanatképek törlése  

Törölheti azokat a pillanatképeket, amelyeket már nem kell megtartania. 

> [!IMPORTANT]
> A pillanatkép törlési művelete nem vonható vissza. A törölt Pillanatképek nem állíthatók helyre. 

1. Ugrás a kötetek **Pillanatképek** menüjére. Kattintson a jobb gombbal a törölni kívánt pillanatképre. Válassza a **Törlés** elemet.

    ![Képernyőkép a pillanatképek jobb gombbal kattintásos menüjéről](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. A pillanatkép törlése ablakban erősítse meg, hogy törölni kívánja a pillanatképet az **Igen** gombra kattintva. 

    ![A pillanatkép törlését igazoló képernyőkép](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>Következő lépések

* [Pillanatkép-szabályzatokkal kapcsolatos problémák elhárítása](troubleshoot-snapshot-policies.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files Pillanatképek 101 videó](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [Mi az az Azure-beli alkalmazáskonzisztens pillanatkép-készítési eszköz?](azacsnap-introduction.md)
