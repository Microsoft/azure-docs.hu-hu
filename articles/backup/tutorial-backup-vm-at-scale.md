---
title: Oktatóanyag – több Azure-beli virtuális gép biztonsági mentése
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Recovery Services-tárolót, hogyan határozhat meg biztonsági mentési házirendet, és hogyan készíthet egyszerre több virtuális gép biztonsági mentését.
ms.date: 07/26/2020
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a9517ffc1e37d50f7c0e57b9ed53fb8bcf55fd70
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89180575"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Több virtuális gép biztonsági mentése az Azure Portalon

Amikor adatok biztonsági mentését végzi az Azure-ban, egy Recovery Services-tároló nevű Azure-erőforrásban tárolja a másolatokat. A helyreállításitár-erőforrás a legtöbb Azure-szolgáltatás Beállítások menüjéből elérhető. A legtöbb Azure-szolgáltatás beállítások menüjében a Recovery Services-tár integrálásának előnye a könnyű biztonsági mentés. Ugyanakkor a vállalat minden adatbázisával vagy virtuális gépén unalmasan dolgozhat. Mi történik, ha egy részleg vagy egy helyszín összes virtuális gépének adatairól szeretne biztonsági mentést készíteni? A biztonsági mentési szabályzat létrehozásával és a kívánt virtuális gépekre való alkalmazásával egyszerűen készíthető biztonsági mentés több virtuális gépre. Ez az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
>
> * Recovery Services-tároló létrehozása
> * Biztonsági mentési szabályzat meghatározása
> * A biztonsági mentési szabályzat alkalmazása több virtuális gép védelme érdekében
> * A védett virtuális gépek biztonsági mentési feladatának manuális aktiválása

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A helyreállítási tár tartalmazza a biztonsági mentési adatokat és a védett virtuális gépekre vonatkozó biztonsági mentési szabályzatot. A virtuális gépek biztonsági mentése egy helyi folyamat. Nem lehet biztonsági mentést készíteni egy virtuális gépről egyik helyről a Recovery Services-tárolóra egy másik helyen. Ezért minden olyan Azure-helyen, ahol biztonsági mentésre szoruló virtuális gépek vannak, legalább egy helyreállítási tárnak kell lennie.

1. A bal oldali menüben válassza a **Minden szolgáltatás** lehetőséget.

    ![Válassza a Minden szolgáltatás elemet](./media/tutorial-backup-vm-at-scale/click-all-services.png)

1. A **Minden szolgáltatás** párbeszédpanelen írja be a következőt: *Recovery Services*. A rendszer a megadott kulcsszavak alapján szűri az erőforrások listáját. Az erőforrások listájából válassza a **Recovery Services-tárolók** elemet.

    ![Recovery Services-tárolók megadása és kiválasztása](./media/tutorial-backup-vm-at-scale/all-services.png)

    Megjelenik az előfizetésben található Recovery Services-tárolók listája.

1. A **Recovery Services-tárolók** irányítópultján kattintson a **Hozzáadás** elemre.

    ![Recovery Services-tároló hozzáadása](./media/tutorial-backup-vm-at-scale/add-button-create-vault.png)

1. A helyreállítási tár menüjében:

    * Írja be a *MyRecoveryServicesVault* **nevet**.
    * Megjelenik az aktuális előfizetési azonosító az **Előfizetés** mezőben. Ha további előfizetéssel rendelkezik, választhat másik előfizetést is az új tárolóhoz.
    * Az **Erőforráscsoport** mezőben válassza a **Meglévő használata** és a *myResourceGroup* elemet. Ha a *myResourceGroup* nem létezik, válassza az **új létrehozása** lehetőséget, és írja be a *myResourceGroup*.
    * A **Hely** legördülő menüből válassza a *Nyugat-Európa* elemet.

    ![Recovery Services tár értékei](./media/tutorial-backup-vm-at-scale/review-and-create.png)

    A helyreállítási tárnak ugyanazon a helyen kell lennie, mint a virtuális gépeknek, amelyeknek védelmet biztosít. Ha több régióban rendelkezik virtuális gépekkel, minden régióban hozzon létre egy Recovery Services-tárolót. Ez az oktatóanyag egy Recovery Services-tárolót hoz létre *Nyugat-Európában* , mert ott jött létre a *myVM* (a gyors üzembe helyezéssel létrehozott virtuális gép).

1. Amikor készen áll a Recovery Services-tároló létrehozására, válassza a **Létrehozás** elemet.

    ![Recovery Services-tároló létrehozása](./media/tutorial-backup-vm-at-scale/click-create-button.png)

1. A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje az állapotértesítéseket a portál jobb felső sarkában található **Értesítések** területen. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában. Ha nem látja a tárat, válassza a **Frissítés** lehetőséget.

     ![Biztonsági mentési tárak listájának frissítése](./media/tutorial-backup-vm-at-scale/refresh-button.png)

A létrehozott helyreállítási tárak alapértelmezés szerint georedundáns tárolással rendelkeznek. Az adatok rugalmassága érdekében a georedundáns tárolás többször replikálja az adatokat két Azure-régió között.

## <a name="set-backup-policy-to-protect-vms"></a>Biztonsági mentési szabályzat beállítása a virtuális gépek védelme érdekében

A helyreállítási tár létrehozása után a következő lépés a tároló konfigurálása a használt adattípushoz, illetve a biztonsági mentési szabályzat beállítása. A biztonsági mentési szabályzat adja meg a helyreállítási pontok gyakoriságának és elhelyezési idejének menetrendjét. A házirend emellett tartalmazza a helyreállítási pontok megőrzési tartományát. Ebben az oktatóanyagban tegyük fel, hogy üzleti tevékenysége egy szállodai, stadionbeli és éttermeivel és koncessziókkal rendelkező sport-komplexum, és a virtuális gépeken lévő adatok védelme is megtörténik. Az alábbi lépésekkel a pénzügyi adatokra vonatkozó biztonsági mentési szabályzatot hozhat létre.

1. A helyreállítási tárak listájából válassza a **myRecoveryServicesVault** tárolót az irányítópult megnyitásához.

   ![Forgatókönyv menü megnyitása](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

1. A tároló irányítópultjának menüjében válassza a **biztonsági** mentés lehetőséget a biztonsági mentés menü megnyitásához.

1. A Biztonsági mentés célja alatt, a **Hol futnak az alkalmazások és szolgáltatások?** legördülő menüből válassza az *Azure* lehetőséget. A **Miről szeretne biztonsági másolatot készíteni** legördülő menüből válassza a *virtuális gép* lehetőséget, és válassza a **biztonsági mentés** lehetőséget.

    Ezek a műveletek előkészítik a helyreállítási tárat a virtuális géppel folytatott interakcióra. A helyreállítási tárak rendelkeznek egy alapértelmezett szabályzattal, amely naponta hoz létre egy visszaállítási pontot, és aztán 30 napig meg is őrzi.

    ![Biztonsági mentés célja](./media/tutorial-backup-vm-at-scale/backup-goal.png)

1. Új szabályzat létrehozásához a biztonsági mentési házirend menüben válassza a **biztonsági mentési házirend kiválasztása** legördülő menüt, majd az *új házirend létrehozása* lehetőséget.

    ![Új szabályzat létrehozása](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

1. Ekkor megnyílik a **biztonsági mentési házirend** panel. Töltse ki a következő adatokat:
   * A szabályzat neveként írja be a *Pénzügy* **nevet** . Adja meg a következő értékeket a biztonsági mentési szabályzathoz:
   * A **Biztonsági mentés gyakorisága** mezőben állítsa be az *amerikai középidő* időzónáját. Mivel a sportkomplexum Texasban van, a tulajdonos helyi időt szeretne használni. Hagyja a biztonsági mentés gyakoriságát a napi 3:30 értéken.
   * **A napi biztonsági mentési pontok megőrzése** mezőben állítson be 90 napos időszakot.
   * **A heti biztonsági mentési pontok megőrzése** mezőben használja a *Hétfő* visszaállítási pontot, és adjon meg egy 52 hetes megőrzési periódust.
   * **A h biztonsági mentési pontok megőrzése** mezőben használja a hónap első vasárnapját visszaállítási pontként, és adjon meg egy 36 hónapos megőrzési periódust.
   * Törölje **Az éves biztonsági mentési pontok megőrzése** kijelölését. A pénzügyi vezető nem szeretné 36 hónapnál hosszabb ideig megőrizni az adatokat.
   * A biztonsági mentési szabályzat létrehozásához kattintson **az OK gombra** .

     ![Biztonsági mentési szabályzat beállításai](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     A biztonsági mentési szabályzat létrehozása után társítsa a szabályzatot a virtuális gépekkel.

1. A **Virtual Machines** területen válassza a **Hozzáadás** lehetőséget.

     ![Virtuális gépek hozzáadása](./media/tutorial-backup-vm-at-scale/add-virtual-machines.png)

1. Ekkor megnyílik a **virtuális gépek kiválasztása** panel. Válassza a *myVM* lehetőséget, majd az **OK** gombra kattintva telepítse a biztonsági mentési szabályzatot a virtuális gépekre.

    Megjelenik az összes olyan virtuális gép, amely ugyanazon a helyen található, és még nincs biztonsági mentési szabályzathoz társítva. A *myVMH1* és a *myVMR1* van kiválasztva, mint a *Pénzügy* szabályzattal társítandó virtuális gép.

    ![Válassza ki a védelemmel ellátni kívánt virtuális gépeket](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

1. A virtuális gépek kiválasztása után válassza a **biztonsági mentés engedélyezése** lehetőséget.

    Ha az üzembe helyezés befejeződött, értesítést fog kapni arról, hogy a telepítés sikeresen befejeződött.

## <a name="initial-backup"></a>Kezdeti biztonsági mentés

Engedélyezte a biztonsági mentést a Recovery Services-tárolók számára, de a kezdeti biztonsági mentés nem lett létrehozva. Ez egy vész-helyreállítási ajánlott eljárás az első biztonsági mentés elindításához, hogy az adatai védve legyenek.

Biztonsági mentési feladat manuális futtatása:

1. A tároló irányítópultján **kattintson a** **biztonsági másolati elemek elemre** a biztonsági másolati elemek menü megnyitásához.

    ![Biztonsági másolati elemek](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Megnyílik a **Biztonsági másolati elemek** menü.

1. A **biztonsági mentési elemek** menüben válassza az **Azure-beli virtuális gép** lehetőséget a tárolóhoz társított virtuális gépek listájának megnyitásához.

    ![Virtuális gépek listája](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

1. Megnyílik a **Biztonsági mentési elemek** listája.

    ![Biztonsági mentési feladat elindul](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

1. A **biztonsági mentési elemek** listában válassza a három pontot a  helyi menü megnyitásához.

1. A helyi menüben válassza a **Biztonsági mentés** elemet.

    ![Helyi menü – biztonsági mentés kiválasztása](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Megnyílik a Biztonsági mentés menü.

1. A biztonsági mentés menüben adja meg a helyreállítási pont megőrzésének utolsó napját, majd kattintson az **OK gombra**.

    ![A biztonsági mentés most helyreállítási pontjának utolsó napjának beállítása](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Az üzembehelyezési értesítések értesítik, hogy a biztonsági mentési feladat elindult, és hogy a feladat állapotát a Biztonsági mentési feladatok oldalon figyelheti. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt is igénybe vehet.

    A kezdeti biztonsági mentési feladat befejeződése után megtekintheti annak állapotát a Biztonsági mentési feladat menüben. A manuális biztonsági mentési feladat létrehozta a *myVM* kezdeti visszaállítási pontját. Ha más virtuális gépekről is szeretne biztonsági másolatot készíteni, minden érintett géphez ismételje meg a fent ismertetett lépéseket.

    ![Biztonsági mentési feladatok csempe](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben az oktatóanyagban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az oktatóanyagban létrehozott összes erőforrást a Azure Portal.

1. A **myRecoveryServicesVault** irányítópultján **kattintson a** **biztonsági másolati elemek elemre** a biztonsági másolati elemek menü megnyitásához.

    ![Biztonsági mentési elemek menü megnyitása](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

1. A **biztonsági mentési elemek** menüben válassza az **Azure-beli virtuális gép** lehetőséget a tárolóhoz társított virtuális gépek listájának megnyitásához.

    ![Virtuális gépek listája](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Megnyílik a **Biztonsági mentési elemek** listája.

1. A **biztonsági mentési elemek** menüben kattintson a három pontra a helyi menü megnyitásához.

    ![A biztonsági mentési elemek menüben, a helyi menü megnyitása](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

1. A helyi menüben válassza a **biztonsági mentés leállítása** lehetőséget a biztonsági mentés leállítása menü megnyitásához.

    ![Biztonsági mentés leállítása menü](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

1. A **Biztonsági mentés leállítása** menüben válassza a felső legördülő menüt, majd a **Biztonsági másolatok adatainak törlése** lehetőséget.

1. A **biztonságimásolat-elem nevének megadására** szolgáló mezőbe írja be a *myVM* nevet.

1. Ha a biztonsági mentési elem ellenőrzése megtörtént (megjelenik egy pipa), a **biztonsági mentés leállítása** gomb engedélyezve van. A házirend leállításához és a visszaállítási pontok törléséhez válassza a **biztonsági mentés leállítása** lehetőséget.

    ![A tár törléséhez válassza a biztonsági mentés leállítása lehetőséget.](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

    >[!NOTE]
    >A törölt elemek 14 napig őrzik meg a helyreállított törlési állapotot. Csak az adott időszak után törölhető a tároló. További információ: [Azure Backup Recovery Services](backup-azure-delete-vault.md)-tároló törlése.

1. Ha nincs több elem a tárolóban, válassza a **Törlés** lehetőséget.

    ![Törlés kiválasztása](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    A tár törlése után visszatérhet a Recovery Services-tárolók listájához.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket végezte el az Azure Portalon:

> [!div class="checklist"]
>
> * Recovery Services-tároló létrehozása
> * A tároló beállítása a virtuális gépek védelméhez
> * Egyéni biztonsági mentési és adatmegőrzési szabályzat létrehozása
> * A szabályzat hozzárendelése több védendő virtuális géphez
> * A virtuális gépek biztonsági mentésének manuális elindítása

Folytassa a következő oktatóanyaggal, ha tudni szeretné, hogyan lehet lemezről visszaállítani Azure-beli virtuális gépeket.

> [!div class="nextstepaction"]
> [Virtuális gépek visszaállítása a parancssori felülettel](./tutorial-restore-disk.md)
