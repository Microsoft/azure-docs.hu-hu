---
title: SQL Server VM biztonsági mentése a virtuális gép paneljéről
description: Ebből a cikkből megtudhatja, hogyan készíthet biztonsági mentést SQL Server adatbázisokról az Azure Virtual Machines szolgáltatásban a virtuális gép ablaktáblán.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88891657"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>SQL Server biztonsági mentése a virtuális gép paneljéről

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni az Azure-beli virtuális gépeken futó SQL Serverről a [Azure Backup](backup-overview.md) szolgáltatással. A SQL Server virtuális gépek biztonsági mentését két módszerrel végezheti el:

- Egyetlen SQL Server Azure-beli virtuális gép: a cikkben szereplő utasítások azt ismertetik, hogyan lehet közvetlenül a virtuálisgép-nézetből biztonsági mentést készíteni egy SQL Server VMról.
- Több SQL Server Azure-beli virtuális gép: beállíthat egy Recovery Services-tárolót, és konfigurálhat több virtuális gép biztonsági mentését. Ehhez a forgatókönyvhöz kövesse az [ebben a cikkben](backup-sql-server-database-azure-vms.md) szereplő utasításokat.

## <a name="before-you-start"></a>Előkészületek

1. Ellenőrizze a környezetet a [támogatási mátrixban](sql-support-matrix.md).
2. A SQL Server VM Azure Backup [áttekintése](backup-azure-sql-database.md) .
3. Ellenőrizze, hogy a virtuális gép rendelkezik-e [hálózati kapcsolattal](backup-sql-server-database-azure-vms.md#establish-network-connectivity).

## <a name="configure-backup-on-the-sql-server"></a>A biztonsági mentés konfigurálása a SQL Serveron

A biztonsági mentést a virtuális gép **biztonsági mentési** paneljén engedélyezheti a SQL Server VM. Ez a módszer két dolgot tesz:

- Regisztrálja az SQL virtuális gépet a Azure Backup szolgáltatással, hogy hozzáférést biztosítson.
- A virtuális gépen futó összes SQL Server-példány újravédése. Ez azt jelenti, hogy a biztonsági mentési szabályzatot a rendszer az összes meglévő adatbázisra alkalmazza, valamint azokat az adatbázisokat, amelyek a jövőben lesznek hozzáadva ezekhez a példányokhoz.

1. Kattintson a lap tetején található szalagcímre a SQL Server biztonsági mentési nézet megnyitásához.

    ![SQL Server biztonsági mentési nézet](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >Nem látja a szalagcímet? A szalagcím csak azokhoz az SQL Server virtuális gépekhez jelenik meg, amelyeket az Azure Marketplace-lemezképek használatával hoztak létre. Emellett az Azure-beli virtuális gépek biztonsági mentésével védett virtuális gépek esetében is megjelenik. Más lemezképek esetén az [itt](backup-sql-server-database-azure-vms.md)leírtak szerint állíthatja be a biztonsági mentést.

2. Adja meg az Recovery Services-tároló nevét. A tároló egy logikai entitás, amely az összes biztonsági mentést tárolja és kezeli. Új tár létrehozásakor:

    - A rendszer ugyanabban az előfizetésben és régióban jön létre, mint a védeni kívánt SQL Server VM.
    - A rendszer az összes biztonsági mentéshez a Geo-redundáns tárolás (GRS) beállítással jön létre. Ha módosítani szeretné a redundancia típusát, ezt a virtuális gép védelme előtt kell végrehajtania. További információkért tekintse meg [ezt a cikket](backup-create-rs-vault.md#set-storage-redundancy).

3. Válassza ki a **biztonsági mentési szabályzatot**. Kiválaszthatja az alapértelmezett házirendet, illetve a tárban létrehozott többi meglévő szabályzatot is. Ha új szabályzatot szeretne létrehozni, tekintse át [ezt a cikket](backup-sql-server-database-azure-vms.md#create-a-backup-policy) részletes útmutatóként.

    ![Biztonsági mentési szabályzat kiválasztása](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. Válassza a **Biztonsági mentés engedélyezése** parancsot. A művelet végrehajtása több percet is igénybe vehet.

    ![Válassza a biztonsági mentés engedélyezése lehetőséget.](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. A művelet befejezését követően a tár **neve** megjelenik a szalagcímben.

    ![A tár neve megjelenik a szalagcímben](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. Válassza ki a szalagcímet a tár nézetéhez, ahol megtekintheti az összes regisztrált virtuális gépet és azok védelmi állapotát.

    ![Tároló nézet regisztrált virtuális gépekkel](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. A nem Piactéri lemezképek esetében a regisztráció sikeres lehet, de a **biztonsági mentést** nem lehet elindítani, amíg a Azure Backup-bővítmény engedélyt nem kap a SQL Server. Ilyen esetekben a **Backup Readiness** oszlop **nem áll készen**. A nem Piactéri lemezképek esetében manuálisan kell [hozzárendelni a megfelelő engedélyeket](backup-azure-sql-database.md#set-vm-permissions) , hogy a biztonsági mentést elindítsa.

    ![A biztonsági mentés készültsége nem áll készen](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. Ha további műveleteket vagy figyelést szeretne végrehajtani a biztonsági másolaton SQL Server VM, lépjen a megfelelő Recovery Services-tárolóra. Nyissa meg a **biztonsági másolati elemeket** , és tekintse meg az összes olyan adatbázist, amelyről biztonsági másolatot készített a tárban, és aktiválja az igény szerinti biztonsági mentést és visszaállítást. Ehhez hasonlóan tekintse meg a **biztonsági mentési feladatok** lehetőséget, hogy [Figyelje](manage-monitor-sql-database-backup.md) a műveleteknek megfelelő feladatokat, például a védelem, a biztonsági mentés és a visszaállítás konfigurálását.

    ![Tekintse meg a biztonsági másolati elemek biztonsági mentését tartalmazó adatbázisokat](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>A biztonsági mentés nem lesz automatikusan konfigurálva olyan új SQL Server példányokon, amelyek később hozzáadhatók a védett virtuális géphez. Az újonnan hozzáadott példányok biztonsági mentésének konfigurálásához meg kell nyitnia azt a tárolót, amelyre a virtuális gép regisztrálva van, és kövesse az [itt](backup-sql-server-database-azure-vms.md)felsorolt lépéseket.

## <a name="next-steps"></a>Következő lépések

Az alábbiak végrehajtásának módját ismerheti meg:

- [SQL Server adatbázisok biztonsági másolatának visszaállítása](restore-sql-database-azure-vm.md)
- [Biztonsági másolatok SQL Server adatbázisok kezelése](manage-monitor-sql-database-backup.md)
