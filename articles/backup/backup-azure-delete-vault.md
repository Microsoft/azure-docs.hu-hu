---
title: Helyreállítási tár Microsoft Azure törlése
description: Ebből a cikkből megtudhatja, hogyan távolíthat el függőségeket, majd hogyan törölhet Azure Backup Recovery Services-tárolóból.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: bb6be070ac0fb408ac37c8ae7b003b54da5d6dea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773656"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Helyreállítási tár Azure Backup törlése

Ez a cikk bemutatja, hogyan törölhet egy [Azure Backup](backup-overview.md) Recovery Services-tárolót. Útmutatásokat tartalmaz a függőségek eltávolításához, majd egy tároló törléséhez.

## <a name="before-you-start"></a>Előkészületek

Az alábbi függőségek egyike esetén sem törölhet Recovery Services-tárolót:

- Védett adatforrásokat (például IaaS virtuális gépeket, SQL-adatbázisokat, Azure-fájlmegosztásokat) tartalmazó tároló nem törölhető.
- Biztonsági mentési adatokat tartalmazó tároló nem törölhető. A biztonságimásolat-adatok a törlést követően az adatok állapota helyreállíthatóan törölt lesz.
- Nem törölhet olyan tárolót, amely a biztonsági másolatok adatait soft törölt állapotban tartalmazza.
- A regisztrált tárfiókokkal rendelkező tárolók nem törölhetők.

Ha a függőségek eltávolítása nélkül próbálja törölni a tárolót, az alábbi hibaüzenetek egyike jelenik meg:

- A tár nem törölhető, mert létező erőforrásokat tartalmaz. Győződjön meg arról, hogy nincsenek a tárolóhoz társított biztonsági mentési elemek, védett kiszolgálók vagy biztonsági mentési felügyeleti kiszolgálók. A törlés előtt törölje a tárolóhoz társított következő tárolók regisztrációját.

- A helyreállítási tár nem törölhető, mivel a tárolóban helyreállítható módon törölt biztonsági másolati elemek vannak. A 14 napos törlési művelet után a törölt elemek véglegesen törlődnek. Próbálja meg törölni a tárolót, miután a biztonsági másolat elemei véglegesen törölve vannak, és a tárolóban már nem maradt visszagátlott elem. További információ: [Soft delete for Azure Backup.](./backup-azure-security-feature-cloud.md)

## <a name="proper-way-to-delete-a-vault"></a>Tároló törlésének megfelelő módja

>[!WARNING]
>A következő művelet kipusztító, és nem vonható vissza. A védett kiszolgálóhoz társított összes biztonsági mentési adat és biztonsági mentési elem véglegesen törölve lesz. Legyen óvatos.

A tároló megfelelő törléséhez az alábbi sorrendben kell követnie a lépéseket:

- **1. lépés:** A soft delete funkció letiltása. [A soft](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) delete letiltásához szükséges lépéseket itt láthatja.

- **2. lépés:** A soft delete letiltása után ellenőrizze, hogy vannak-e korábban olyan elemek, amelyek a korábban törölt állapotban maradtak. Ha vannak olyan elemek, amelyek törölt állapotban vannak, akkor törölnie és *törölnie* kell *őket.* [Az alábbi lépésekkel](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) megkereshet és véglegesen törölhet elemeket.

- **3. lépés:** Az alábbi három helyen ellenőriznie kell, hogy vannak-e védett elemek:

  - **Felhőben védett elemek:** A Biztonsági másolati elemek menüben > **meg.** Az itt felsorolt összes elemet el kell távolítani a **Biztonsági** másolatok adatainak leállítása vagy a **Biztonsági** másolat adatainak törlése parancsokkal együtt.  [Az elemek eltávolításához](#delete-protected-items-in-the-cloud) kövesse az alábbi lépéseket.
  - **SQL Server példány:** A Biztonsági mentési infrastruktúra által védett kiszolgálók > meg a tároló  >  **irányítópultjának menüjét.** A Protected Servers (Védett kiszolgálók) mezőben válassza ki a kiszolgálót, amelyről le kell mondania a regisztrációt. A tároló törléséhez törölnie kell az összes kiszolgáló regisztrációját. Kattintson a jobb gombbal a védett kiszolgálóra, és válassza **a Regisztráció törlése lehetőséget.**
  - **MARS által védett kiszolgálók:** A Biztonsági mentési infrastruktúra által védett **kiszolgálók**> meg a tároló  >  **irányítópultjának menüjét.** Ha MARS-védelem alatt lévő kiszolgálókkal is van, akkor az itt felsorolt összes elemet törölni kell a biztonsági mentési adataikkal együtt. [Kövesse az alábbi lépéseket](#delete-protected-items-on-premises) a MARS által védett kiszolgálók törléséhez.
  - **MABS- vagy DPM-felügyeleti** kiszolgálók: A Backup **Infrastructure** Backup Management Servers > meg a tároló  >  **irányítópultjának menüjét.** Ha dpm vagy Azure Backup Server (MABS) is van, akkor az itt felsorolt összes elemet törölni kell, vagy törölni kell a regisztrációt a biztonsági másolat adataival együtt. [A felügyeleti kiszolgálók törléséhez](#delete-protected-items-on-premises) kövesse az alábbi lépéseket.

- **4. lépés:** Gondoskodnia kell arról, hogy az összes regisztrált tárfiók törlődött. A Backup Infrastructure Storage-fiókok > meg a **tároló**  >  **irányítópultjának menüjét.** Ha vannak itt felsorolt tárfiókok, akkor mindegyik regisztrációját meg kell újulni. A fiók regisztrációjának a regisztrációját a tárfiók regisztrációjának [a regisztrációjának a megszabadulásában olvashatja el.](manage-afs-backup.md#unregister-a-storage-account)
- **5. lépés:** Győződjön meg arról, hogy nincsenek privát végpontok létrehozva a tárolóhoz. Ha a tárolóban privát  végponti kapcsolatok vannak létrehozva vagy megkíséreltek létrehozni> válassza a Privát végponti kapcsolatok elemet a Beállítások > ha a tárolóban privát végponti kapcsolatok vannak létrehozva vagy megkíséreltek létrehozni, győződjön meg arról, hogy azok el vannak távolítva, mielőtt folytatná a tároló törlését. 

A lépések befejezése után folytathatja a tároló [törlését.](#delete-the-recovery-services-vault)

Ha a helyszínen vagy a felhőben nincsenek védett elemek, de továbbra is a tárolótörlési hibát kapják, hajtsa végre a [Recovery Services-tároló](#delete-the-recovery-services-vault-by-using-azure-resource-manager) törlése a következő Azure Resource Manager

## <a name="delete-protected-items-in-the-cloud"></a>Védett elemek törlése a felhőben

Először olvassa el az **[Indítás](#before-you-start)** előtt szakaszt, hogy megértse a függőségeket és a tárolótörlési folyamatot.

A védelem leállításhoz és a biztonsági mentési adatok törléséhez hajtsa végre a következő lépéseket:

1. A portálról válassza a **Recovery Services-tárolót,** majd a Biztonsági **mentési elemek elemet.** Ezután a **Biztonságimásolat-kezelés** típusa listában válassza ki a felhőben lévő védett elemeket (például Azure Virtual Machines, Azure Storage [Azure Files szolgáltatás], vagy SQL Server Azure Virtual Machines).

    ![Válassza ki a biztonsági mentés típusát.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Megjelenik a kategória összes elemének listája. Kattintson a jobb gombbal a biztonsági mentési elem kiválasztásához. Attól függően, hogy a biztonsági mentési elem védett-e vagy sem, a menüben megjelenik a **Biztonsági** mentés leállítása vagy a Biztonsági másolat adatainak **törlése** panel.

    - Ha **megjelenik a Biztonsági mentés leállítása** panel, válassza a Biztonsági másolatok adatainak törlése **lehetőséget** a legördülő menüből. Adja meg a biztonságimásolat-elem nevét (ez a mező megkülönbözteti a kis- és nagybetűket), majd válasszon egy okot a legördülő menüből. Ha van megjegyzése, adja meg a megjegyzéseit. Ezután válassza a **Biztonsági mentés leállítása lehetőséget.**

        ![A Biztonsági mentés leállítása panel.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Ha **megjelenik** a Biztonsági másolatok adatainak törlése panel, adja meg a biztonsági mentési elem nevét (ez a mező megkülönbözteti a kis- és nagybetűket), majd válasszon egy okot a legördülő menüből. Ha vannak, adja meg megjegyzéseit. Ezután válassza a **Törlés lehetőséget.**

         ![A Biztonsági másolatok adatainak törlése panel.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

   Ez a beállítás törli az ütemezett és az igény szerinti biztonsági mentéseket is.
3. Jelölje be **az Értesítés** ikont: Az ![ Értesítés ikont.](./media/backup-azure-delete-vault/messages.png) A folyamat befejezése után a szolgáltatás a következő üzenetet jeleníti meg: A biztonsági mentés leállítása és a biztonsági mentési adatok törlése a *következőre: "* Biztonsági mentési elem *"*. *A művelet sikeresen befejeződött.*
4. Válassza **a Biztonsági** másolati elemek **menü** Frissítés elemét, hogy biztosan törölve legyen a biztonsági mentési elem.

      ![A Biztonsági másolati elemek törlése oldal.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Védett elemek törlése a helyszínen

Először olvassa el az **[Before you start](#before-you-start)** (Kezdés előtt) szakaszt a függőségek és a tároló törlési folyamatának a megértenie.

1. A tároló irányítópultjának menüjében válassza a Biztonsági **mentési infrastruktúra lehetőséget.**
2. A helyszíni forgatókönyvtől függően válasszon az alábbi lehetőségek közül:

      - A MARS mezőben válassza a **Védett kiszolgálók lehetőséget,** majd válassza **Azure Backup Agent lehetőséget.** Ezután válassza ki a törölni kívánt kiszolgálót.

        ![A MARS számára válassza ki a tárolót az irányítópult megnyitásához.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - MABS vagy DPM esetén válassza a **Biztonsági mentés felügyeleti kiszolgálók lehetőséget.** Ezután válassza ki a törölni kívánt kiszolgálót.

          ![A MABS vagy a DPM számára válassza ki a tárolót az irányítópult megnyitásához.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. **Megjelenik** a Törlés panel egy figyelmeztető üzenettel.

     ![A Törlés panel.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Tekintse át a figyelmeztető üzenetet és a hozzájárulás jelölőnégyzetében található utasításokat.
    > [!NOTE]
    >
    >- Ha a védett kiszolgáló szinkronizálva van az Azure-szolgáltatásokkal, és léteznek biztonsági mentési elemek, a hozzájárulás jelölőnégyzetben megjelenik a függő biztonsági mentési elemek száma és a biztonsági mentési elemek megtekintésére mutató hivatkozás.
    >- Ha a védett kiszolgáló nincs szinkronizálva az Azure-szolgáltatásokkal, és léteznek biztonsági mentési elemek, a hozzájárulás jelölőnégyzet csak a biztonsági mentési elemek számát jeleníti meg.
    >- Ha nincsenek biztonsági másolati elemek, a jóváhagyás jelölőnégyzet törlést kér.

4. Jelölje be a jóváhagyás jelölőnégyzetet, majd válassza a **Törlés lehetőséget.**

5. Jelölje be az **Értesítés ikon** biztonsági mentési ![ adatok ](./media/backup-azure-delete-vault/messages.png) törlését. A művelet befejezése után a szolgáltatás a következő üzenetet jeleníti meg: Biztonsági mentési elem biztonsági mentésének leállítása és *biztonsági mentési adatok törlése.* *A művelet sikeresen befejeződött.*
6. Válassza **a Biztonsági** másolati elemek menü Frissítés elemét, hogy biztosan törölve legyen a biztonsági mentési elem. 

>[!NOTE]
>Ha egy függőségeket tartalmazó portálról töröl egy helyszíni védett elemet, a következő figyelmeztetés jelenik meg: "A kiszolgáló regisztrációjának törlése kioltó művelet, és nem vonható vissza. Az összes biztonsági mentési adat (az adatok visszaállításához szükséges helyreállítási pontok) és a védett kiszolgálóhoz társított biztonsági mentési elemek véglegesen törlődnek."

A folyamat befejezése után törölheti a biztonsági mentési elemeket a felügyeleti konzolról:

- [Biztonsági mentési elemek törlése a MARS felügyeleti konzolról](#delete-backup-items-from-the-mars-management-console)
- [Biztonságimásolat-elemek törlése a MABS vagy a DPM felügyeleti konzolról](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Biztonsági mentési elemek törlése a MARS felügyeleti konzolról

>[!NOTE]
>Ha a forrásgépet a biztonsági mentés leállítása nélkül törölte vagy elveszítette, a következő ütemezett biztonsági mentés sikertelen lesz. A régi helyreállítási pont a szabályzatnak megfelelően lejár, de az utolsó helyreállítási pontot a rendszer mindig megőrzi, amíg le nemállítja a biztonsági mentést, és nem törli az adatokat. Ezt a szakasz lépéseit követve [használhatja.](#delete-protected-items-on-premises)

1. Nyissa meg a MARS felügyeleti konzolt, nyissa meg a **Műveletek panelt,** és válassza a **Biztonsági mentés ütemezése lehetőséget.**
2. Az Ütemezett **biztonsági mentés módosítása** vagy leállítása lapon válassza a Stop using this backup schedule (Leállítás a biztonsági mentés ütemezésének használatával) lehetőséget, és törölje az összes tárolt biztonsági **mentést.** Ezután válassza a **Tovább lehetőséget.**

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Az Ütemezett **biztonsági mentés befejezése lapon válassza a** Befejezés **lehetőséget.**

    ![Állítson le egy ütemezett biztonsági mentést.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. A rendszer kéri, hogy adja meg a biztonsági PIN-kódot (személyes azonosító számot), amelyet manuálisan kell létrehoznia. Ehhez először jelentkezzen be a Azure Portal.
5. Ugrás a **Recovery Services-tároló**  >  **beállításainak**  >  **tulajdonságaira.**
6. A **Biztonsági PIN-kód alatt** válassza a Generate **(Generálás) lehetőséget.** Másolja ki ezt a PIN-kódot. A PIN-kód csak öt percig érvényes.
7. A felügyeleti konzolon illessze be a PIN-kódot, majd kattintson az **OK gombra.**

    ![Biztonsági PIN-kód létrehozása.](./media/backup-azure-delete-vault/security-pin.png)

8. A Biztonsági **mentés folyamatának** módosítása lapon a következő üzenet jelenik meg: A törölt biztonsági mentési adatok *14 napig lesznek megőrizve. Ezt követően a biztonsági mentési adatok véglegesen törlődnek.*  

    ![Törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-delete-vault/deleted-backup-data.png)

A helyszíni biztonsági mentési elemek törlését követően kövesse a következő lépéseket a portálon.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Biztonsági másolat elemeinek törlése a MABS vagy a DPM felügyeleti konzolról

>[!NOTE]
>Ha a forrásgépet a biztonsági mentés leállítása nélkül törölte vagy elveszítette, a következő ütemezett biztonsági mentés sikertelen lesz. A régi helyreállítási pont a szabályzatnak megfelelően lejár, de az utolsó helyreállítási pontot mindig megőrzi a rendszer, amíg le nemállítja a biztonsági mentést, és nem törli az adatokat. Ezt a szakasz lépéseit követve [használhatja.](#delete-protected-items-on-premises)

A biztonsági mentési elemek a MABS vagy a DPM felügyeleti konzolról való törlésére két módszerrel törölhetők.

#### <a name="method-1"></a>1. módszer

A védelem leállításhoz és a biztonsági mentési adatok törléséhez tegye a következőket:

1. Nyissa meg DPM Felügyeleti konzol panelt, majd válassza a **navigációs sáv Védelem** beállítását.
2. A megjelenítési panelen válassza ki az eltávolítani kívánt védelmicsoport-tagot. Kattintson a jobb gombbal a **Csoporttagok védelmének leállítása lehetőség kiválasztásához.**
3. A Védelem **leállítása** párbeszédpanelen válassza a **Védett** adatok törlése lehetőséget, majd jelölje be a Tároló online törlése **jelölőnégyzetet.** Ezután válassza a **Védelem leállítása lehetőséget.**

    ![A Védelem leállítása panelen válassza a Védett adatok törlése lehetőséget.](./media/backup-azure-delete-vault/delete-storage-online.png)

    A védett tag állapota Inaktív *replikára változik.*

4. Kattintson a jobb gombbal az inaktív védelmi csoportra, és válassza az **Inaktív védelem eltávolítása lehetőséget.**

    ![Inaktív védelem eltávolítása.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Az Inaktív **védelem törlése ablakban** jelölje be az Online **tároló** törlése jelölőnégyzetet, majd kattintson az **OK gombra.**

    ![Online tároló törlése.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>2. módszer

Nyissa meg a **MABS felügyeleti konzolt** vagy **a DPM felügyeleti konzolt.** A **Select data protection method (Adatvédelmi módszer kiválasztása)** alatt törölje a I want online protection  **(Online védelemre van szükségem)** jelölőnégyzet jelölését.

  ![Válassza ki az adatvédelmi módszert.](./media/backup-azure-delete-vault/data-protection-method.png)

A helyszíni biztonsági mentési elemek törlése után kövesse a portálon a következő lépéseket.

## <a name="delete-the-recovery-services-vault"></a>A helyreállítási tár törlése

1. Ha az összes függőség el lett távolítva, görgessen a tároló **menüjének Alapvető** szolgáltatások paneljére.
2. Ellenőrizze, hogy nincsenek-e felsorolva biztonsági mentési elemek, biztonsági mentési felügyeleti kiszolgálók vagy replikált elemek. Ha az elemek továbbra is megjelennek a tárolóban, tekintse meg [az Indítás előtt szakaszt.](#before-you-start)

3. Ha nincs több elem a tárolóban, válassza a **Törlés lehetőséget** a tároló irányítópultján.

    ![A tároló irányítópultján válassza a Törlés lehetőséget.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Válassza **az Igen** lehetőséget annak ellenőrzéséhez, hogy törölni szeretné-e a tárolót. A tároló törlődik. A portál visszatér az Új **szolgáltatás menüre.**

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>A Recovery Services-tároló törlése a PowerShell használatával

Először olvassa el az **[Before you start](#before-you-start)** (Kezdés előtt) szakaszt a függőségek és a tároló törlési folyamatának a megértenie.

A védelem leállítása és a biztonsági mentési adatok törlése:

- Ha SQL-t használ az Azure-beli virtuális gépek biztonsági mentésében, és engedélyezte az automatikus védelmet az SQL-példányokhoz, először tiltsa le az automatikus védelmet.

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

  [További információ](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) a védett elemek védelmének Azure Backup letiltásához.

- Állítsa le a védelmet és törölje a felhőben lévő összes biztonsági mentéssel védett elem adatait (például: IaaS virtuális gép, Azure-fájlmegosztás és így tovább):

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

    [További információ](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   a biztonsági mentéssel védett elemek védelmének letiltása.

- Az Azure-ba Azure Backup agent (MARS) használatával védett helyszíni fájlok és mappák esetén a következő PowerShell-paranccsal törölheti az egyes MARS PowerShell-modulokból származó biztonságimentett adatokat:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Ezt követően megjelenik a következő üzenet:

    *Microsoft Azure Backup Biztosan eltávolítja ezt a biztonsági mentési szabályzatot? A törölt biztonsági mentési adatokat a rendszer 14 napig őrzi meg. Ezt követően a biztonsági mentési adatok véglegesen törlődnek. <br/> [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Súgó (az alapértelmezett érték az "Y":*

- A MABS (Microsoft Azure Backup Server) vagy a DPM (System Center Data Protection Manager) használatával az Azure-ba védett helyszíni gépek esetén a következő paranccsal törölheti a biztonságimentett adatokat az Azure-ban.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Ezt követően megjelenik a következő üzenet:

   *Microsoft Azure Backup* Biztosan eltávolítja ezt a biztonsági mentési szabályzatot? A törölt biztonsági mentési adatokat a rendszer 14 napig őrzi meg. Ez idő leteltével a biztonsági mentés adatai véglegesen törlődnek. <br/>
   [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Súgó (az alapértelmezett érték az "Y"):*

A biztonságimentett adatok törlése után törölje a helyszíni tárolók és felügyeleti kiszolgálók regisztrációját.

- Az Azure-ba biztonsági Azure Backup marsi ügynökkel (MARS) védett helyszíni fájlok és mappák esetén:

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

    [További információ](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) a Windows Server vagy más tárolók tárolóból való regisztrációjának a tárolóból való regisztrációjának a regisztrációról.

- MABS (Microsoft Azure Backup Server) vagy DPM – Azure (System Center Data Protection Kezelés) használatával védett helyszíni gépek esetén:

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

    [További információ](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) a Backup felügyeleti tárolónak a tárolóból való regisztrációját megszűkülő tárolóról.

Miután véglegesen törölte a biztonságimentett adatokat, és törölte az összes tároló regisztrációját, lépjen tovább a tároló törlésére.

Recovery Services-tároló törlése:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[További információ](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) a Recovery Services-tárolók törléséről.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>A Recovery Services-tároló törlése a CLI használatával

Először olvassa el az **[Indítás](#before-you-start)** előtt szakaszt, hogy megértse a függőségeket és a tárolótörlési folyamatot.

> [!NOTE]
> Jelenleg a Azure Backup parancssori felület csak az Azure-beli virtuális gépek biztonsági másolatai felügyeletét támogatja, ezért a tároló törlésére szolgáló alábbi parancs csak akkor működik, ha a tároló Azure-beli virtuális gépek biztonsági másolatokat tartalmaz. Nem törölhet tárolót a cli Azure Backup használatával, ha a tároló az Azure-beli virtuális gépeken kívül bármilyen típusú biztonsági mentési elemet tartalmaz.

A meglévő Recovery Services-tároló törléséhez hajtsa végre a következő lépéseket:

- A védelem leállítása és a biztonsági mentési adatok törlése

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

    További információt ebben a cikkben [talál.](/cli/azure/backup/protection#az_backup_protection_disable)

- Meglévő Recovery Services-tároló törlése:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    További információért tekintse meg ezt a [cikket](/cli/azure/backup/vault)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Törölje a Recovery Services-tárolót a Azure Resource Manager

A Recovery Services-tároló törlésének lehetősége csak akkor ajánlott, ha az összes függőség el lett távolítva, és továbbra is a tároló törlési *hibája jelenik meg.* Próbálja ki az alábbi tippek bármelyikét vagy mindegyikét:

- A tároló **menüjének Alapvető** szolgáltatások paneljéből ellenőrizze, hogy nincsenek-e felsorolva biztonsági mentési elemek, biztonsági mentési felügyeleti kiszolgálók vagy replikált elemek. Ha vannak biztonsági másolati elemek, tekintse meg az [Indítás előtt szakaszt.](#before-you-start)
- Próbálja [meg ismét törölni a tárolót a portálról.](#delete-the-recovery-services-vault)
- Ha az összes függőség el lett távolítva, és továbbra is a tároló törlési hibája jelenik *meg,* használja az ARMClient eszközt a következő lépések végrehajtásához (a megjegyzés után).

1. A Chocolatey [chocolatey.org](https://chocolatey.org/) és telepítése a fájlban. Ezután telepítse az ARMClientet a következő parancs futtatásával:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Jelentkezzen be az Azure-fiókjába, majd futtassa a következő parancsot:

    `ARMClient.exe login [environment name]`

3. A Azure Portal gyűjtse össze a törölni kívánt tároló előfizetés-azonosítóját és erőforráscsoport-nevét.

További információ az ARMClient parancsról: [ARMClient README.](https://github.com/projectkudu/ARMClient/blob/master/README.md)

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Recovery Services Azure Resource Manager-tároló törlése a Azure Resource Manager-ügyfél használatával

1. Futtassa a következő parancsot az előfizetés-azonosító, az erőforráscsoport neve és a tároló neve használatával. Ha nincsenek függőségei, a következő parancs futtatásakor a rendszer törli a tárolót:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Ha a tároló nem üres, a következő hibaüzenet jelenik meg: A tároló nem törölhető, mert a tárolóban meglévő *erőforrások vannak.* Egy tárolóban található védett elem vagy tároló eltávolításához futtassa a következő parancsot:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. A Azure Portal ellenőrizze, hogy a tároló törölve lett-e.

## <a name="next-steps"></a>Következő lépések

[Tudnivalók a Recovery Services-tárolókról](backup-azure-recovery-services-vault-overview.md) 
 [Tudnivalók a Recovery Services-tárolók figyeléséről és kezeléséről](backup-azure-manage-windows-server.md)
