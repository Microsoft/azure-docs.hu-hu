---
title: Microsoft Azure Recovery Services-tároló törlése
description: Ebből a cikkből megtudhatja, hogyan távolíthatja el a függőségeket, majd törölhet egy Azure Backup Recovery Services-tárolót.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: 1526e9aeef1574f261dcb1a58ee12a12fbf08866
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864958"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Azure Backup Recovery Services-tároló törlése

Ez a cikk egy [Azure Backup](backup-overview.md) Recovery Services-tároló törlését ismerteti. A függőségek eltávolítására, majd a tároló törlésére vonatkozó utasításokat tartalmaz.

## <a name="before-you-start"></a>Előkészületek

Az alábbi függőségek egyike esetén sem törölhet Recovery Services-tárolót:

- Védett adatforrásokat (például IaaS virtuális gépeket, SQL-adatbázisokat, Azure-fájlmegosztást) tartalmazó tárolót nem lehet törölni.
- Olyan tárolót nem lehet törölni, amely biztonsági másolatokat tartalmaz. A biztonságimásolat-adatok a törlést követően az adatok állapota helyreállíthatóan törölt lesz.
- Nem törölhet olyan tárolót, amely biztonsági mentési állapotot tartalmaz a törölt törlési állapotban.
- Regisztrált Storage-fiókkal rendelkező tárolót nem lehet törölni.

Ha a függőségek eltávolítása nélkül próbálja meg törölni a tárolót, a következő hibaüzenetek valamelyikét fogja tapasztalni:

- A tár nem törölhető, mert létező erőforrásokat tartalmaz. Győződjön meg arról, hogy nincsenek olyan biztonsági mentési elemek, védett kiszolgálók vagy biztonságimásolat-felügyeleti kiszolgálók, amelyek ehhez a tárolóhoz vannak társítva. A törlés folytatásához szüntesse meg a tárolóhoz társított alábbi tárolók regisztrációját.

- A helyreállítási tár nem törölhető, mivel a tárolóban helyreállítható módon törölt biztonsági másolati elemek vannak. A helyreállított törölt elemek a törlési művelet 14 napja után véglegesen törlődnek. A biztonsági mentési elemek végleges törlését követően próbálja meg törölni a tár törlését, és nincs olyan elem, amely a tárolóban marad. További információ: Azure Backup helyreállítható [Törlés](./backup-azure-security-feature-cloud.md).

## <a name="proper-way-to-delete-a-vault"></a>Tár törlésének megfelelő módja

>[!WARNING]
>A következő művelet romboló, ezért nem vonható vissza. A rendszer véglegesen törli a védett kiszolgálóhoz társított biztonsági mentési adatokat és biztonsági másolati elemeket. Legyen óvatos.

A tárolók megfelelő törléséhez az alábbi lépéseket kell követnie:

- **1. lépés**: a Soft delete funkció letiltása. A Soft delete letiltásához szükséges lépéseket [itt találja](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) .

- **2. lépés**: a helyreállítható törlés letiltása után ellenőrizze, hogy vannak-e korábban megmaradt elemek a helyreállított törölt állapotban. Ha vannak olyan elemek, amelyekről törölhető a törölt állapot, akkor törölnie kell a *törlést* , majd újra *törölnie* kell őket. Az [alábbi lépéseket követve](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) megkeresheti a helyreállítható elemek törlését és véglegesen törölheti azokat.

- **3. lépés**: az alábbi három helyen ellenőrizze, hogy vannak-e védett elemek:

  - **Felhőalapú védett elemek**: lépjen a tároló irányítópult menüjébe > **biztonsági másolati elemek elemre**. Az itt felsorolt összes elemet el kell távolítani a biztonsági mentés **leállítása** vagy a biztonsági mentési adatok **törlése** mellett.  Ezeket a [lépéseket követve](#delete-protected-items-in-the-cloud) távolíthatja el ezeket az elemeket.
  - **SQL Server példány**: lépjen a tároló irányítópult menüjébe > **biztonsági mentési infrastruktúra** által  >  **védett kiszolgálók** elemre. A védett kiszolgálók lapon válassza ki a regisztrálni kívánt kiszolgálót. A tár törléséhez meg kell szüntetnie az összes kiszolgáló regisztrációját. Kattintson a jobb gombbal a védett kiszolgálóra, és válassza a **Regisztráció törlése** lehetőséget.
  - **Mars-védelemmel ellátott kiszolgálók**: lépjen a tároló irányítópult menüjébe > **biztonsági mentési infrastruktúra** által  >  **védett kiszolgálók** elemre. Ha a MARS-védelemmel ellátott kiszolgálókkal rendelkezik, az itt felsorolt összes elemet törölni kell a biztonsági mentési adatokkal együtt. A MARS-védelemmel ellátott kiszolgálók törléséhez [kövesse az alábbi lépéseket](#delete-protected-items-on-premises) .
  - **MABS vagy DPM felügyeleti kiszolgálók**: lépjen a tár irányítópult menüjébe > **biztonsági mentési infrastruktúra** biztonsági  >  **mentési felügyeleti kiszolgálók** elemre. Ha DPM vagy Azure Backup Server (MABS) rendelkezik, akkor az itt felsorolt összes elemet törölni kell, vagy a biztonsági mentési adatokkal együtt meg kell szüntetni a regisztrációt. A felügyeleti kiszolgálók törléséhez [kövesse az alábbi lépéseket](#delete-protected-items-on-premises) .

- **4. lépés**: Győződjön meg arról, hogy az összes regisztrált Storage-fiók törölve van. Lépjen a tároló irányítópult menüjébe > **biztonsági mentési infrastruktúra**  >  **Storage-fiókok** elemre. Ha az itt felsorolt Storage-fiókokkal rendelkezik, törölje az összes regisztrációját. További információ a fiók regisztrációjának megszüntetéséről: [Storage-fiók regisztrációjának törlése](manage-afs-backup.md#unregister-a-storage-account).
- **5. lépés**: Győződjön meg arról, hogy nincsenek létrehozott privát végpontok a tárolóhoz. Nyissa meg a tároló irányítópultjának menü > **privát végponti kapcsolatok** menüpontot, > ha a tárolóban létrehozott vagy létrehozott magánhálózati végponti kapcsolatok vannak, akkor a tár törlésének folytatása előtt győződjön meg róla, hogy el lesznek távolítva. 

A lépések elvégzése után továbbra is [törölheti a](#delete-the-recovery-services-vault)tárolót.

Ha a helyszínen vagy a felhőben nem található védett elem, de a tár törlési hibája továbbra is fennáll, hajtsa végre az Recovery Services-tároló törlésének lépéseit a [Azure Resource Manager használatával](#delete-the-recovery-services-vault-by-using-azure-resource-manager)

## <a name="delete-protected-items-in-the-cloud"></a>Védett elemek törlése a felhőben

Először olvassa el az első **[lépések](#before-you-start)** szakaszt a függőségek és a tár törlési folyamatának megismeréséhez.

A védelem leállításához és a biztonsági mentési adatok törléséhez hajtsa végre a következő lépéseket:

1. A portálon nyissa meg **Recovery Services**-tárolót, majd lépjen a **biztonsági másolati elemek elemre**. Ezután a biztonságimásolat- **kezelés típusa** listából válassza ki a védett elemeket a felhőben (például Azure Virtual Machines, Azure Storage [a Azure Files szolgáltatás] vagy SQL Server az Azure Virtual machines).

    ![Válassza ki a biztonsági mentés típusát.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Ekkor megjelenik a kategória összes elemének listája. Kattintson a jobb gombbal a biztonsági mentési elem kiválasztásához. Attól függően, hogy a biztonsági másolati elem védett-e, vagy sem, a menü a **biztonsági mentés leállítása** vagy a **biztonsági másolat törlése** ablaktáblát jeleníti meg.

    - Ha megjelenik a **biztonsági mentés leállítása** ablaktábla, válassza a **biztonsági másolat adatainak törlése** lehetőséget a legördülő menüből. Adja meg a biztonsági másolati elem nevét (ez a kis-és nagybetűk megkülönböztetése), majd válasszon ki egy okot a legördülő menüből. Adja meg a megjegyzéseit, ha van ilyen. Ezután válassza a **biztonsági mentés leállítása** lehetőséget.

        ![A biztonsági mentés leállítása panel.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Ha megjelenik a **biztonsági mentési adatok törlése** ablaktábla, adja meg a biztonsági másolati elem nevét (ez a mező megkülönbözteti a kis-és nagybetűket), majd válasszon ki egy okot a legördülő menüből. Adja meg a megjegyzéseit, ha van ilyen. Ezután válassza a **Törlés** lehetőséget.

         ![A biztonsági mentési adattábla törlése panel.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

   Ez a beállítás törli az ütemezett biztonsági mentéseket, továbbá törli az igény szerinti biztonsági mentést is.
3. Keresse meg az **értesítés** ikont: ![ az értesítés ikonja.](./media/backup-azure-delete-vault/messages.png) A folyamat befejeződése után a szolgáltatás a következő üzenetet jeleníti meg: a biztonsági mentés *leállítása és* a biztonsági másolati elemektörlése. *A művelet sikeresen befejeződött*.
4. Válassza a **frissítés** lehetőséget a **biztonsági másolati elemek** menüben, hogy meggyőződjön arról, hogy a biztonsági mentési elem törölve lett.

      ![A biztonsági másolati elemek törlése oldal.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Védett elemek törlése a helyszínen

Először olvassa el az első **[lépések](#before-you-start)** szakaszt a függőségek és a tár törlési folyamatának megismeréséhez.

1. A tároló irányítópultjának menüjében válassza a **biztonsági mentési infrastruktúra** elemet.
2. A helyszíni forgatókönyvtől függően válasszon a következő lehetőségek közül:

      - A MARS területen válassza a **védett kiszolgálók** elemet, majd  **Azure Backup az ügynököt**. Ezután válassza ki a törölni kívánt kiszolgálót.

        ![A MARS lapon válassza ki a tárolót az irányítópult megnyitásához.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - A MABS vagy a DPM lapon válassza a **biztonsági mentési felügyeleti kiszolgálók** lehetőséget. Ezután válassza ki a törölni kívánt kiszolgálót.

          ![A MABS vagy DPM válassza ki a tárolót az irányítópult megnyitásához.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. A **Törlés** panel figyelmeztető üzenettel jelenik meg.

     ![A törlés panel.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Tekintse át a figyelmeztető üzenetet és az utasításokat a belefoglalt szövegben.
    > [!NOTE]
    >
    >- Ha a védett kiszolgáló szinkronizálva van az Azure-szolgáltatásokkal és a biztonsági mentési elemekkel, akkor a beleegyezik jelölőnégyzet megjeleníti a függő biztonsági mentési elemek számát és a biztonsági másolati elemek megtekintésére szolgáló hivatkozást.
    >- Ha a védett kiszolgáló nincs szinkronizálva az Azure-szolgáltatásokkal és a biztonsági mentési elemekkel, akkor a beleegyezett jelölőnégyzet csak a biztonsági mentési elemek számát jeleníti meg.
    >- Ha nincsenek biztonsági másolati elemek, a beleegyezett jelölőnégyzettel a törlést kéri a rendszer.

4. Jelölje be a beleegyezett jelölőnégyzetet, majd válassza a **Törlés** lehetőséget.

5. Győződjön meg arról, hogy az **értesítési** ikon ![ törli a biztonsági mentési fájlokat ](./media/backup-azure-delete-vault/messages.png) . A művelet befejeződése után a szolgáltatás megjeleníti az üzenetet: a biztonsági mentés leállítása és a biztonsági másolati *elemek törlésének törlése.* *A művelet sikeresen befejeződött*.
6. Válassza a **frissítés** lehetőséget a **biztonsági mentési elemek** menüben, hogy biztosan törölje a biztonsági mentési elemet.

>[!NOTE]
>Ha töröl egy helyszíni védett elemet egy olyan portálról, amely függőségeket tartalmaz, a "kiszolgáló regisztrációjának törlése egy romboló művelet, és nem vonható vissza. A rendszer véglegesen törli az összes biztonsági mentési adatokat (az adatok visszaállításához szükséges helyreállítási pontokat) és a védett kiszolgálóhoz társított biztonsági mentési elemeket. "

A folyamat befejezése után törölheti a biztonsági mentési elemeket a felügyeleti konzolról:

- [Biztonsági másolati elemek törlése a MARS felügyeleti konzolról](#delete-backup-items-from-the-mars-management-console)
- [Biztonsági másolati elemek törlése a MABS vagy a DPM felügyeleti konzolról](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Biztonsági másolati elemek törlése a MARS felügyeleti konzolról

>[!NOTE]
>Ha a biztonsági mentés leállítása nélkül törölte vagy elvesztette a forrás gépet, a következő ütemezett biztonsági mentés sikertelen lesz. A régi helyreállítási pont a szabályzatnak megfelelően lejár, de az utolsó egyszeri helyreállítási pont mindig megmarad, amíg le nem állítja a biztonsági mentést, és nem törli az adatvesztést. Ezt a [szakasz](#delete-protected-items-on-premises)lépéseit követve teheti meg.

1. Nyissa meg a MARS felügyeleti konzolját, lépjen a **műveletek** ablaktáblára, és válassza a **biztonsági mentés időzítése** elemet.
2. Az **ütemezett biztonsági mentés módosítása vagy leállítása** lapon válassza a **Leállítás a biztonsági mentési ütemezés használatával lehetőséget, és törölje az összes tárolt biztonsági** mentést. Ezután válassza a **tovább** lehetőséget.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Az **ütemezett biztonsági mentés leállítása** lapon válassza a **Befejezés** lehetőséget.

    ![Állítsa le az ütemezett biztonsági mentést.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. A rendszer felszólítja, hogy adjon meg egy biztonsági PIN-kódot (személyes azonosító számot), amelyet manuálisan kell előkészítenie. Ehhez először jelentkezzen be a Azure Portalba.
5. Nyissa meg **Recovery Services** tároló  >  **beállításai**  >  **tulajdonságokat**.
6. A **biztonsági PIN-kód** területen válassza a **készítés** elemet. Másolja ezt a PIN-kódot. A PIN-kód csak öt percig érvényes.
7. A felügyeleti konzolon illessze be a PIN-kódot, majd kattintson **az OK gombra**.

    ![Biztonsági PIN-kód létrehozása.](./media/backup-azure-delete-vault/security-pin.png)

8. A **biztonsági mentési folyamat módosítása** lapon a következő üzenet jelenik meg: a *törölt biztonsági mentési adat 14 napig megmarad. Ez idő után véglegesen törlődik a biztonsági mentési adatvesztés.*  

    ![Törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-delete-vault/deleted-backup-data.png)

A helyszíni biztonsági mentési elemek törlését követően kövesse a portál következő lépéseit.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Biztonsági másolati elemek törlése a MABS vagy a DPM felügyeleti konzolról

>[!NOTE]
>Ha a biztonsági mentés leállítása nélkül törölte vagy elvesztette a forrás gépet, a következő ütemezett biztonsági mentés sikertelen lesz. A régi helyreállítási pont a szabályzatnak megfelelően lejár, de az utolsó egyszeri helyreállítási pont mindig megmarad, amíg le nem állítja a biztonsági mentést, és nem törli az adatvesztést. Ezt a [szakasz](#delete-protected-items-on-premises)lépéseit követve teheti meg.

A MABS vagy a DPM felügyeleti konzolról kétféleképpen törölhet biztonsági másolati elemeket.

#### <a name="method-1"></a>1. módszer

A védelem leállításához és a biztonsági másolatok állapotának törléséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a DPM Felügyeleti konzol, majd a navigációs sávon kattintson a **védelem** elemre.
2. A Megjelenítés ablaktáblán válassza ki a védelmi csoport eltávolítani kívánt tagját. Kattintson a jobb gombbal a **csoporttagok védelmének leállítása** lehetőség kiválasztásához.
3. A **védelem leállítása** párbeszédpanelen válassza a **védett adatok törlése** lehetőséget, majd jelölje be a **tárterület online törlése** jelölőnégyzetet. Ezután válassza a **védelem leállítása** lehetőséget.

    ![Válassza a védett adatok törlése lehetőséget a védelem leállítása ablaktáblán.](./media/backup-azure-delete-vault/delete-storage-online.png)

    A védett tag állapota a *rendelkezésre álló inaktív replikára* módosul.

4. Kattintson a jobb gombbal az inaktív védelmi csoportra, és válassza az **inaktív védelem eltávolítása** lehetőséget.

    ![Inaktív védelem eltávolítása.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Az **inaktív védelem törlése** ablakban jelölje be az **online tárterület törlése** jelölőnégyzetet, majd kattintson az **OK gombra**.

    ![Online tárterület törlése.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>2. módszer

Nyissa meg a **MABS-kezelő** vagy a **DPM felügyeleti** konzolt. Az **adatvédelmi módszer kiválasztása** területen törölje a jelet a szeretnék  **online védelem** jelölőnégyzetből.

  ![Válassza ki az adatvédelmi módszert.](./media/backup-azure-delete-vault/data-protection-method.png)

A helyszíni biztonsági mentési elemek törlését követően kövesse a portál következő lépéseit.

## <a name="delete-the-recovery-services-vault"></a>A helyreállítási tár törlése

1. Ha az összes függőség el lett távolítva, görgessen a tároló menü **Essentials (alapvető** erőforrások) paneljére.
2. Ellenőrizze, hogy nincsenek-e biztonsági másolati elemek, biztonságimásolat-kezelési kiszolgálók vagy replikált elemek felsorolva. Ha az elemek továbbra is megjelennek a tárolóban, [olvassa el az](#before-you-start) előkészületek című szakaszt.

3. Ha nincs több elem a tárolóban, válassza a **Törlés** lehetőséget a tároló irányítópultján.

    ![Válassza a Törlés lehetőséget a tároló irányítópultján.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Válassza az **Igen** lehetőséget annak ellenőrzéséhez, hogy törölni kívánja-e a tárolót. A tár törölve. A portál visszatér az **új** szolgáltatás menüjébe.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>A Recovery Services-tároló törlése a PowerShell használatával

Először olvassa el az első **[lépések](#before-you-start)** szakaszt a függőségek és a tár törlési folyamatának megismeréséhez.

A védelem leállításához és a biztonsági másolatok törléséhez:

- Ha az SQL-t használja az Azure virtuális gépek biztonsági mentése és az automatikus védelem engedélyezése SQL-példányok esetén, először tiltsa le az automatikus védelmet.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  [További információ](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) a Azure Backup-védelemmel ellátott elemek védelmének letiltásáról.

- Állítsa le a védelmet és töröljön adatokat a felhőben található összes biztonsági mentéssel védett elemhez (például: IaaS VM, Azure file share stb.):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [További információ](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   tudnivalók a biztonsági másolattal védett elemek védelmének letiltásáról.

- A Azure Backup ügynök (MARS) használatával védett helyszíni fájlok és mappák Azure-ba történő biztonsági mentését a következő PowerShell-paranccsal törölheti az egyes MARS PowerShell-modulokból származó biztonsági másolatok adatainak törléséhez:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Ezt követően a következő üzenet jelenik meg:

    *Microsoft Azure Backup biztosan el szeretné távolítani ezt a biztonsági mentési szabályzatot? A törölt biztonsági mentési adat 14 napig őrzi meg a rendszer. Ez idő után véglegesen törlődik a biztonsági mentési adatvesztés. <br/> [Y] igen [A] igen az összes [N] nem [L] nem az összes [S] felfüggesztése [?] Súgó (az alapértelmezett érték az "Y"):*

- A MABS (Microsoft Azure Backup Server) vagy a DPM (System Center Data Protection Manager) által az Azure-ba védett helyszíni gépeken a következő paranccsal törölheti az Azure-ban tárolt biztonsági másolati adataikat.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Ezt követően a következő üzenet jelenik meg:

   *Microsoft Azure Backup* Biztosan el kívánja távolítani ezt a biztonsági mentési szabályzatot? A törölt biztonsági mentési adat 14 napig őrzi meg a rendszer. Ez idő leteltével a biztonsági mentés adatai véglegesen törlődnek. <br/>
   [Y] igen [A] igen az összes [N] nem [L] nem az összes [S] felfüggesztése [?] Súgó (az alapértelmezett érték "Y"):*

A biztonsági másolatok törlése után törölje a helyszíni tárolók és a felügyeleti kiszolgálók regisztrációját.

- A Azure Backup Agent (MARS) használatával védett helyszíni fájlok és mappák az Azure-ba történő biztonsági mentéssel:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    [További](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) információ a Windows Server vagy más tárolók a tárból való regisztrációjának törléséről.

- A MABS (Microsoft Azure Backup-kiszolgáló) vagy az Azure-ba (System Center adatvédelem kezelése) védett helyszíni gépek esetén:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [További](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) információ a biztonságimásolat-kezelési tárolók a tárból való regisztrációjának törléséről.

Miután véglegesen törölte a biztonsági mentést, és nem regisztrálja az összes tárolót, folytassa a tár törlésével.

Recovery Services-tároló törlése:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[További](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) információ a Recovery Services-tároló törléséről.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>A Recovery Services-tároló törlése a CLI használatával

Először olvassa el az első **[lépések](#before-you-start)** szakaszt a függőségek és a tár törlési folyamatának megismeréséhez.

> [!NOTE]
> Jelenleg Azure Backup CLI csak az Azure-beli virtuális gépek biztonsági másolatainak kezelését támogatja, így a tároló csak akkor törölhető, ha a tár Azure-beli virtuális gépek biztonsági másolatait tartalmazza. Azure Backup CLI használatával nem törölheti a tárolót, ha a tároló az Azure-beli virtuális gépektől eltérő típusú biztonsági másolati elemeket tartalmaz.

A meglévő Recovery Services-tároló törléséhez hajtsa végre a következő lépéseket:

- A védelem leállítása és a biztonsági másolatok törlése

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    További információkért tekintse meg ezt a [cikket](/cli/azure/backup/protection#az-backup-protection-disable).

- Meglévő Recovery Services tároló törlése:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    További információkért tekintse meg ezt a [cikket](/cli/azure/backup/vault)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Az Recovery Services-tároló törlése Azure Resource Manager használatával

Ez a Recovery Services-tároló törlésének lehetősége csak akkor javasolt, ha az összes függőség el lett távolítva, és a tár *törlési hibája* továbbra is bekerül. Próbálja ki a következő tippek bármelyikét vagy mindegyikét:

- A tár menü **Essentials** paneljén ellenőrizze, hogy nincsenek-e biztonsági másolati elemek, biztonságimásolat-kezelési kiszolgálók vagy replikált elemek felsorolva. Ha vannak biztonsági másolati elemek, tekintse meg a [kezdés előtti](#before-you-start) szakaszt.
- Próbálja meg újból [törölni a tárat a portálról](#delete-the-recovery-services-vault) .
- Ha az összes függőség el lett távolítva, és még mindig beolvassa a tár *törlési hibáját*, a ARMClient eszközzel hajtsa végre a következő lépéseket (a Megjegyzés után).

1. A chocolatey letöltéséhez és telepítéséhez nyissa meg a [chocolatey.org](https://chocolatey.org/) . Ezután telepítse a ARMClient a következő parancs futtatásával:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Jelentkezzen be az Azure-fiókjába, majd futtassa a következő parancsot:

    `ARMClient.exe login [environment name]`

3. A Azure Portal Gyűjtse össze a törölni kívánt tároló előfizetés-AZONOSÍTÓját és erőforráscsoport-nevét.

További információ a ARMClient parancsról: [ARMCLIENT readme](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Recovery Services-tároló törlése a Azure Resource Manager ügyfél használatával

1. Futtassa az alábbi parancsot az előfizetés-azonosító, az erőforráscsoport neve és a tár neve használatával. Ha nem rendelkezik függőségekkel, a tár törlődik a következő parancs futtatásakor:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Ha a tároló nem üres, a következő hibaüzenet jelenik meg: a tár *nem törölhető, mert a tárban található meglévő erőforrások.* Egy védett elem vagy tároló egy tárolón belüli eltávolításához futtassa a következő parancsot:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Győződjön meg arról, hogy a Azure Portal törli a tárolót.

## <a name="next-steps"></a>Következő lépések

[További tudnivalók a Recovery Services](backup-azure-recovery-services-vault-overview.md) 
 -tárolók Tudnivalók [a Recovery Services-tárolók monitorozásáról és kezeléséről](backup-azure-manage-windows-server.md)
