---
title: Archiválási szint támogatása (előzetes verzió)
description: Tudnivalók az archiválási szint támogatásáról Azure Backup
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 6c597d640f24dc4c680bfd5db16f9df09017ee54
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609852"
---
# <a name="archive-tier-support-preview"></a>Archiválási szint támogatása (előzetes verzió)

Az ügyfelek a biztonsági mentési adatok tárolásához Azure Backup támaszkodnak, beleértve a Long-Term adatmegőrzési (LTR) biztonsági mentési adatok megőrzési igényét a szervezet megfelelőségi szabályainak meghatározásával. A legtöbb esetben a régebbi biztonsági mentési adatszolgáltatások ritkán érhetők el, és csak a megfelelőségi igényeknek megfelelően vannak tárolva.

Azure Backup támogatja a hosszú távú adatmegőrzési pontok biztonsági mentését az archiválási szinten, a pillanatképek és a standard szint mellett.

## <a name="scope-for-preview"></a>Az előzetes verzió hatóköre

Támogatott munkaterhelések:

- Azure-beli virtuális gépek
  - Csak havi és éves helyreállítási pontok. A napi és heti helyreállítási pontok nem támogatottak.
  - Age >= 3 hónap Vault-Standard szinten
  - Megőrzés balra >= 6 hónap
  - Nincs aktív napi és heti függőség
- SQL Server az Azure Virtual Machines szolgáltatásban
  - Csak a teljes helyreállítási pontok. A naplók és a különbségek nem támogatottak.
  - Age >= 45 nap Vault-Standard szinten
  - Megőrzés balra >= 6 hónap
  - Nincsenek függőségek

Támogatott ügyfelek:

- A képesség a PowerShell használatával biztosítható

>[!NOTE]
>Az Azure-beli virtuális gépek és SQL Server Azure-beli virtuális gépeken történő archiválásának támogatása korlátozott nyilvános előzetes verzióban korlátozott regisztrációval. Az archiválási támogatásra való regisztráláshoz használja ezt a [hivatkozást](https://aka.ms/ArchivePreviewInterestForm).

## <a name="get-started-with-powershell"></a>Bevezetés a PowerShell használatába

1. Futtassa az alábbi parancsot a PowerShellben:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.0.0-preview -AllowPrerelease -force
    ```

1. Kapcsolódjon az Azure-hoz a [Kapcsolódás-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.
1. Jelentkezzen be az előfizetésbe:

   `Set-AzContext -Subscription "SubscriptionName"`

1. A tár beszerzése:

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. A biztonsági mentési elemek listájának beolvasása:

    `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM/AzureWorkload" -WorkloadType "AzureVM/MSSQL"`

1. A biztonsági másolati tétel beolvasása.

    - Azure-beli virtuális gépek esetén:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - SQL Server Azure-beli virtuális gépeken:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>A PowerShell használata

### <a name="check-archivable-recovery-points"></a>Archiválható helyreállítási pontok keresése

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

Ekkor megjelenik egy adott biztonsági mentési elemmel társított összes olyan helyreállítási pont, amely készen áll az archívumba való áthelyezésre.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>Győződjön meg arról, hogy a helyreállítási pont miért nem helyezhető át az archívumba

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Hol `$rp[0]` található a helyreállítási pont, amelyre vonatkozóan meg szeretné nézni, hogy miért nem archiválható.

Példa a kimenetre:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Archiválható pontok ajánlott készletének (csak Azure-beli virtuális gépek esetén)

A virtuális géphez társított helyreállítási pontok növekményes jellegűek. Ha egy adott helyreállítási pontot áthelyez az archívumba, a rendszer a teljes biztonsági mentésbe konvertálja, majd áthelyezi az archívumba. Így az archiválásra való áttéréshez kapcsolódó költségmegtakarítás az adatforrás adatforgalmának függvénye.

Így Azure Backup olyan helyreállítási pontok ajánlott készletével rendelkezik, amelyek együttesen költségmegtakarítást okozhatnak.

>[!NOTE]
>A költségmegtakarítás különböző okoktól függ, és előfordulhat, hogy a két példány esetében nem ugyanaz.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>Áthelyezés az archívumba

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Ez a parancs archiválható helyreállítási pontot helyez át az archívumba. Egy olyan feladatot ad vissza, amellyel nyomon követhető az áthelyezési művelet mind a portál, mind a PowerShell használatával.

### <a name="view-archived-recovery-points"></a>Archivált helyreállítási pontok megtekintése

Ez a parancs az összes archivált helyreállítási pontot visszaadja.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>Visszaállítás a PowerShell-lel

Az archív helyreállítási pontok esetében Azure Backup egy integrált visszaállítási módszertant biztosít.

Az integrált visszaállítás egy kétlépéses folyamat. Az első lépés magában foglalja az archívumban tárolt helyreállítási pontok újraszárítását, és ideiglenesen a tároló standard csomagjában tárolja az időtartamot (más néven a rehidratálás időtartamát), amely egy 10 – 30 napos időszakra terjed ki. Az alapértelmezett érték 15 nap. A rehidratálás két különböző prioritással rendelkezik – standard és magas prioritású. További információ a [rehidratálás prioritásáról](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration#rehydrate-an-archived-blob-to-an-online-tier).

>[!NOTE]
>
>- A kiválasztást követően a rehidratálás időtartama nem módosítható, és a rehidratált helyreállítási pontok a standard szinten maradnak a rehidratálás időtartamára.
>- A rehidratálás további lépése a költségek.

További információ az Azure Virtual Machines különböző visszaállítási módszereiről: Azure-beli virtuális [gép visszaállítása a PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm)-lel.

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

A SQL Server visszaállításához kövesse [az alábbi lépéseket](backup-azure-sql-automation.md#restore-sql-dbs). A további szükséges paraméterek a következők: **RehydrationPriority** és **RehydrationDuration**.

### <a name="view-jobs-from-powershell"></a>Feladatok megtekintése a PowerShellből

Az áthelyezési és visszaállítási feladatok megtekintéséhez használja a következő PowerShell-parancsmagot:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>A portál használata

### <a name="check-archived-recovery-point"></a>Archivált helyreállítási pont keresése

Most már megtekintheti az archiválásra áthelyezett összes helyreállítási pontot.

![Minden helyreállítási pont.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>Visszaállítás a portálon

Az archiválásra áthelyezett helyreállítási pontok esetén a visszaállításhoz hozzá kell adnia a rehidratálás időtartamára és a rehidratálás prioritására vonatkozó paramétereket.

![Visszaállítás a portálon.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>Feladatok megtekintése a portálon

![Feladatok megtekintése a portálon.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>Védelem módosítása

Az adatforrások védelmét kétféleképpen módosíthatja:

- Meglévő szabályzat módosítása
- Az adatforrás védelme új házirenddel

Mindkét esetben a rendszer az új házirendet alkalmazza a standard szintű és az archiválási szinten lévő összes régebbi helyreállítási pontra. Előfordulhat, hogy a régebbi helyreállítási pontok törölve lesznek, ha módosul a házirend.

A helyreállítási pontok archiválásra való áthelyezésekor a rendszer a 180 napos korai törlési időszakra vonatkozik. A díjakat arányosan számítjuk fel. Ha egy olyan helyreállítási pont, amely 180 napig nem maradt archívumban, akkor a standard szinten töltött napok száma mínusz 180.

Azok a helyreállítási pontok, amelyek legalább hat hónapig nem maradtak az archívumban, a törléssel kapcsolatos korai törlési költségekkel járnak.

## <a name="stop-protection-and-delete-data"></a>Védelem leállítása és az adattörlés

A védelem leállítása és az adattörlés törli az összes helyreállítási pontot. Azon helyreállítási pontok esetében, amelyek archiválási szinten nem maradnak meg a 180 napos időtartamon belül, a helyreállítási pontok törlése a korai törlési költségeket eredményezi.

## <a name="error-codes-and-troubleshooting-steps"></a>Hibakódok és hibaelhárítási lépések

Több hibakód is megjelenhet, ha egy helyreállítási pont nem helyezhető át az archívumba.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Hibaüzenet** – a Recovery-Point típusa nem jogosult az archiválás áthelyezésére

**Leírás** – ez a hibakód akkor jelenik meg, ha a kijelölt helyreállítási pont típusa nem jogosult az archívumba való áthelyezésre.

**Javasolt művelet** – [itt](#scope-for-preview) tekintheti meg a helyreállítási pont jogosultságát

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Hibaüzenet** – Recovery-Point, hogy a visszaállításhoz tartozó aktív függőségek nem jogosultak az Archívum áthelyezésére

**Leírás –** A kijelölt helyreállítási pont aktív függőségekkel rendelkezik, ezért nem helyezhető át az archívumba.

**Javasolt művelet** – [itt](#scope-for-preview) tekintheti meg a helyreállítási pont jogosultságát

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Hibaüzenet** – a Recovery-Point nem jogosult az archiválásra, mert az élettartama a tár szabványos szintjénél kisebb, mint a minimálisan szükséges

**Leírás** – a helyreállítási pontnak standard szintűnek kell lennie az Azure-beli virtuális gépekhez legalább három hónapig, az Azure-beli virtuális gépek SQL Server pedig 45 napig kell maradni

**Javasolt művelet** – [itt](#scope-for-preview) tekintheti meg a helyreállítási pont jogosultságát

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Hibaüzenet** – Recovery-Point hátralévő élettartam kisebb a minimálisan szükségesnél.

**Leírás** – a helyreállítási ponthoz az archiválási jogosultsághoz szükséges minimális élettartam hat hónap.

**Javasolt művelet** – [itt](#scope-for-preview) tekintheti meg a helyreállítási pont jogosultságát

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Hibaüzenet** – Recovery-Point nem jogosult az archiválásra, mert már át lett helyezve az archiválási szintre

**Leírás** – a kijelölt helyreállítási pont már szerepel az archívumban. Így nem lehet áthelyezni az archívumba.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Hibaüzenet** – az adatforrás típusa nem jogosult a javaslat API-ra.

**Leírás** – az ajánlási API csak az Azure Virtual Machines esetében alkalmazható. Nem alkalmazható a kiválasztott adatforrás-típusra.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Hibaüzenet** – a helyreállítási pont már rehidratált. A rehidratálás nem engedélyezett ezen az RP-on.

**Leírás** – a kijelölt helyreállítási pont már rehidratált.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Hibaüzenet** – a helyreállítási pont nem jogosult az archiválási áthelyezésre.

**Leírás** – a kijelölt helyreállítási pont nem jogosult az archiválási áthelyezésre.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**Hibaüzenet** **– a** helyreállítási pont archiválása nem történik meg. A rehidratálás befejezése után próbálkozzon újra a visszaállítással az archív RP-on.

**Leírás** – a helyreállítási pont nem rehidratált. A helyreállítási pont újraszárítása után próbálkozzon újra a visszaállítással.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**Hibaüzenet** **– a** rehidratálás csak archiválási helyreállítási pontok esetén támogatott – a rehidratálás csak archív helyreállítási pontok esetén támogatott.

**Leírás** – a kijelölt helyreállítási ponthoz nem engedélyezett a rehidratálás.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Hibaüzenet** – a rehidratálás már In-Progress az archív helyreállítási ponthoz.

**Leírás** – a kijelölt helyreállítási pont rehidratálása már folyamatban van.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Hibaüzenet** – a helyreállítási pont nem helyezhető át az archiválási szintre, mert nem elegendő a házirendben megadott megőrzési időtartam.

**Javasolt művelet** – frissítési szabályzat a védett elemhez megfelelő megőrzési beállítással, majd próbálkozzon újra.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Hibaüzenet** – továbbra is megállapítható, hogy a helyreállítási pont áthelyezhető-e.

**Leírás** – a helyreállítási pont mozgatási készültsége még meg van határozva.

**Javasolt művelet** – egy kis idő elteltével próbálkozzon újra.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>Mi fog történni a helyreállítási pontok archiválásakor, ha leállítja a védelmet, és megtartja az adatok megőrzését?

A helyreállítási pont örökre archiválva marad. További információ: [a védelem leállítása a helyreállítási pontokon](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

## <a name="next-steps"></a>Következő lépések

- [Az Azure Backup díjszabása](azure-backup-pricing.md)
