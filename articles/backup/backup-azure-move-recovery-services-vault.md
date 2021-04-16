---
title: Recovery Services Azure Backup tárolók áthelyezése
description: Útmutatás a Recovery Services-tárolók Áthelyezéséhez Azure-előfizetések és -erőforráscsoportok között.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 49d6782af5a9c946eaf92147dab22e4605195d89
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514767"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Recovery Services-tároló áthelyezése Azure-előfizetések és erőforráscsoportok között

Ez a cikk azt ismerteti, hogyan lehet áthelyezni egy Azure Backup konfigurált Recovery Services-tárolót azure-előfizetések között vagy egy másik erőforráscsoportba ugyanabban az előfizetésben. A helyreállítási tár Azure Portal Vagy a PowerShell használatával áthelyezhet egy Recovery Services-tárolót.

## <a name="supported-regions"></a>Támogatott régiók

Minden nyilvános régió és szuverén régió támogatott, kivéve Közép-Franciaország, Dél-Franciaország, Északkelet-Németország, Közép-Németország, Észak-Kína, Észak-Kína2, észak-Kelet-Kína és Kelet-Kína2.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>A Recovery Services-tároló áthelyezésének előfeltételei

- A tároló erőforráscsoportok közötti áthelyezése során a forrás- és a cél-erőforráscsoport zárolva van, ami megakadályozza az írási és törlési műveleteket. További információkért tekintse meg [ezt a cikket](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Csak a rendszergazdai előfizetés rendelkezik a tároló áthelyezéséhez szükséges engedélyekkel.
- A tárolók előfizetések közötti áthelyezéséhez a cél-előfizetésnek ugyanabban a bérlőben kell lennie, mint a forrás-előfizetésnek, és az állapotát engedélyezni kell. Tároló másik Azure AD-címtárba való áthelyezéséhez lásd: [Előfizetés](../role-based-access-control/transfer-subscription.md) áthelyezése másik címtárba és Recovery Service-tároló – [gyakori kérdések.](/backup-azure-backup-faq.yml#recovery-services-vault)
- A cél erőforráscsoporton írási műveletek végrehajtásához engedéllyel kell rendelkeznie.
- A tároló mozgatása csak az erőforráscsoportot módosítja. A Recovery Services-tároló ugyanazon a helyen található, és nem módosítható.
- Egyszerre csak egy Recovery Services-tárolót lehet áthelyezni régiónként.
- Ha egy virtuális gép nem mozog a Recovery Services-tárolóval az előfizetések között, vagy egy új erőforráscsoportba, az aktuális virtuális gép helyreállítási pontjai a lejáratukig érintetlenek maradnak a tárolóban.
- Akár áthelyezi a virtuális gépet a tárolóval, akár nem, a tárolóban tárolt biztonsági mentési előzményekből mindig visszaállíthatja a virtuális gépet.
- A Azure Disk Encryption, hogy a kulcstartó és a virtuális gépek ugyanabban az Azure-régióban és -előfizetésben találhatók.
- A felügyelt lemezeket tároló virtuális gépek áthelyezését ebben a cikkben [olvashatja el.](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- A klasszikus modellel üzembe helyezett erőforrások áthelyezési lehetőségei eltérőek attól függően, hogy egy előfizetésen belül vagy egy új előfizetésbe helyez át erőforrásokat. További információkért tekintse meg [ezt a cikket](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- A tárolóhoz definiált biztonsági mentési szabályzatokat a rendszer megőrzi, miután a tárolót áthelyezte az előfizetések között vagy egy új erőforráscsoportba.
- Csak olyan tárolót lehet áthelyezni, amely a következő típusú biztonsági mentési elemek bármelyikét tartalmazza. Az alább fel nem sorolt biztonsági mentési elemeket le kell állítani, és a tároló áthelyezéséhez véglegesen törölni kell az adatokat.
  - Azure Virtual Machines
  - Microsoft Azure Recovery Services- (MARS-) ügynök
  - Microsoft Azure Backup (MABS)
  - Data Protection Manager (DPM)
- Ha egy virtuális gép biztonsági mentési adatait tartalmazó tárolót áthelyez az előfizetések között, a virtuális gépeket ugyanoda az előfizetésbe kell áthelyeznie, és ugyanazt a cél virtuálisgép-erőforráscsoport-nevet kell használnia (mint a régi előfizetésben), hogy folytatja a biztonsági mentéseket.

> [!NOTE]
> A Recovery Services-tárolók Azure Backup Azure-régiók között nem támogatottak.<br><br>
> Ha virtuális gépeket (Azure IaaS, Hyper-V, VMware) vagy fizikai gépeket konfigurált vészhelyreállításra a **Azure Site Recovery** használatával, az áthelyezési művelet le lesz tiltva. Ha tárolókat szeretne áthelyezni a Azure Site Recovery, [](../site-recovery/move-vaults-across-regions.md) tekintse át ezt a cikket a tárolók manuális áthelyezésének elsajátításért.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>A Azure Portal Recovery Services-tároló áthelyezése másik erőforráscsoportba

Recovery Services-tároló és a hozzá tartozó erőforrások áthelyezése egy másik erőforráscsoportba:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg a **Recovery Services-tárolók listáját,** és válassza ki az áthelyezni kívánt tárolót. Amikor megnyílik a tároló irányítópultja, az az alábbi képen látható módon jelenik meg.

   ![Recovery Services-tároló megnyitása](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Ha nem látja a tároló **Alapvető** szolgáltatások adatait, válassza a legördülő ikont. Most meg kell jelenni a tároló Alapvető szolgáltatások információinak.

   ![Alapvető adatok lap](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. A tároló áttekintési menüjében válassza a **módosítás** lehetőséget az Erőforráscsoport mellett az Erőforrások **áthelyezése panel megnyitásához.**

   ![Erőforráscsoport módosítása](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Az Erőforrások **áthelyezése panelen** a kiválasztott tárolóhoz ajánlott áthelyezni a nem kötelező kapcsolódó erőforrásokat a jelölőnégyzet bejelölve az alábbi képen látható módon.

   ![Előfizetés áthelyezése](./media/backup-azure-move-recovery-services/move-resource.png)

5. A cél erőforráscsoport hozzáadásához  az Erőforráscsoport legördülő listában válasszon ki egy meglévő erőforráscsoportot, vagy válassza az Új csoport **létrehozása** lehetőséget.

   ![Erőforrás létrehozása](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Az erőforráscsoport hozzáadása után győződjön meg arról, hogy megértettem, hogy az áthelyezett erőforrásokhoz társított eszközök és szkriptek nem fognak működni, amíg nem frissítem őket, hogy új erőforrás-értékeket használjanak, majd kattintson az **OK** gombra a tároló áthelyezésének **befejezéséhez.**

   ![Megerősítő üzenet](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>A Azure Portal Recovery Services-tároló másik előfizetésbe való áthelyezéséhez használja a következőt:

A Recovery Services-tárolót és a hozzá tartozó erőforrásokat áthelyezheti egy másik előfizetésbe

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg a Recovery Services-tárolók listáját, és válassza ki az áthelyezni kívánt tárolót. Amikor megnyílik a tároló irányítópultja, az az alábbi képen látható módon jelenik meg.

    ![Recovery Services-tároló megnyitása](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Ha nem látja a tároló **Alapvető** szolgáltatások adatait, válassza a legördülő ikont. Most meg kell jelenni a tároló Alapvető szolgáltatások információinak.

    ![Alapvető adatok lap](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. A tároló áttekintési  menüjében válassza az Előfizetés melletti módosítás lehetőséget **az** Erőforrások áthelyezése **panel megnyitásához.**

   ![Előfizetés módosítása](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Válassza ki az áthelyezni kívánt erőforrásokat.  Itt javasoljuk, hogy az Összes kijelölése lehetőséggel válassza ki az összes felsorolt választható erőforrást.

   ![erőforrás áthelyezése](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Válassza ki a  cél-előfizetést az Előfizetés legördülő listából, ahol át szeretné áthelyezni a tárolót.
6. A cél erőforráscsoport hozzáadásához  az Erőforráscsoport legördülő listában válasszon ki egy meglévő erőforráscsoportot, vagy válassza az Új csoport **létrehozása** lehetőséget.

   ![Előfizetés hozzáadása](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Jelölje **be a Megértettem, hogy** az áthelyezett erőforrásokhoz társított eszközök és szkriptek nem fognak működni, amíg nem frissítem őket, hogy új erőforrás-adja meg a beállításokat a megerősítéshez, majd kattintson az OK **gombra.**

> [!NOTE]
> Az előfizetések közötti biztonsági mentés (az RS-tároló és a védett virtuális gépek eltérő előfizetésben vannak) nem támogatott forgatókönyv. Emellett a tárredundania lehetősége a helyi redundáns tárolástól (LRS) a globális redundáns tárolásig (GRS) és fordítva nem módosítható a tároló áthelyezési művelete során.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Recovery Services-tároló áthelyezése a PowerShell használatával

Egy Recovery Services-tároló másik erőforráscsoportba való áthelyezéshez használja a `Move-AzureRMResource` parancsmagot. `Move-AzureRMResource` erőforrásnévre és erőforrástípusra van szükség. Mindkettőt a `Get-AzureRmRecoveryServicesVault` parancsmagból kaphatja meg.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Az erőforrások másik előfizetésbe való áthelyezéséhez foglalja bele a `-DestinationSubscriptionId` paramétert.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

A fenti parancsmagok végrehajtása után meg kell erősítenie, hogy át szeretné-e áthelyezni a megadott erőforrásokat. A **megerősítéshez írja be az Y** parancsot. A sikeres ellenőrzés után az erőforrás áthelyeződik.

## <a name="use-cli-to-move-recovery-services-vault"></a>Recovery Services-tároló áthelyezése a cli használatával

Egy Recovery Services-tároló másik erőforráscsoportba való áthelyezéshez használja a következő parancsmagot:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Új előfizetésre való áthelyezéshez adja meg a `--destination-subscription-id` paramétert.

## <a name="post-migration"></a>Migrálás után

1. Állítsa be/ellenőrizze az erőforráscsoportok hozzáférés-vezérlését.  
2. Az áthelyezés befejezése után újra konfigurálni kell a biztonsági mentési jelentéskészítési és monitorozási funkciót a tárolóhoz. Az előző konfiguráció az áthelyezési művelet során elveszik.

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Helyezzen át egy Azure-beli virtuális gépet egy másik helyreállítási tárba. 

Ha olyan Azure-beli virtuális gépet szeretne áthelyezni, amelyen engedélyezve van az Azure Backup, két lehetőség közül választhat. Az üzleti követelményektől függnek:

- [Nem kell megőriznie a korábbi biztonságimentett adatokat](#dont-need-to-preserve-previous-backed-up-data)
- [Meg kell őriznie a korábbi biztonságimentett adatokat](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Nem kell megőriznie a korábbi biztonságimentett adatokat

Az új tárolókban lévő számítási feladatok védelméhez törölni kell az aktuális védelmet és adatokat a régi tárolóból, és a biztonsági mentést újra kell konfigurálni.

>[!WARNING]
>A következő művelet romboló, és nem vonható vissza. A védett kiszolgálóhoz társított összes biztonsági mentési adat és biztonsági mentési elem véglegesen törölve lesz. Legyen óvatos.

**Állítsa le és törölje a régi tároló aktuális védelmét:**

1. Tiltsa le a soft delete parancsot a tároló tulajdonságai között. A [következő lépésekkel tiltsa](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) le a nem végleges törlést.

2. Állítsa le a védelmet, és törölje a biztonsági másolatokat az aktuális tárolóból. A Tároló irányítópult menüjében válassza a Biztonsági **mentési elemek lehetőséget.** Az itt felsorolt elemeket az új tárolóba kell áthelyezni, és a biztonsági mentési adataikat is el kell távolítani. Tekintse meg, hogyan törölhet védett elemeket [a felhőben,](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) és hogyan törölheti a [védett elemeket a helyszínen.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

3. Ha AFS-t (Azure-fájlmegosztásokat), SQL-kiszolgálókat vagy SAP HANA kiszolgálókat tervez áthelyezni, akkor a regisztrációt is meg kell majd mondania. A tároló irányítópultjának menüjében válassza a Biztonsági **mentési infrastruktúra lehetőséget.** Tekintse meg az [SQL-kiszolgáló](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)regisztrációjának, az Azure-fájlmegosztásokkal társított tárfiók regisztrációjának és a SAP HANA [](manage-afs-backup.md#unregister-a-storage-account) [regisztrációját.](sap-hana-db-manage.md#unregister-an-sap-hana-instance)

4. Miután eltávolította őket a régi tárolóból, továbbra is konfigurálhatja a számítási feladat biztonsági mentését az új tárolóban.

### <a name="must-preserve-previous-backed-up-data"></a>Meg kell őriznie a korábbi biztonságimentett adatokat

Ha meg kell tartania az aktuális védett adatokat a régi tárolóban, és egy új tárolóban kell folytatnia a védelmet, néhány számítási feladathoz korlátozott lehetőségek állnak rendelkezésre:

- A MARS-hoz [leállíthatja](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) a védelmet az adatok megőrzésével, és regisztrálhatja az ügynököt az új tárolóban.

  - Azure Backup szolgáltatás továbbra is megőrzi a régi tároló összes meglévő helyreállítási pontjait.
  - Fizetnie kell azért, hogy a helyreállítási pontok a régi tárolóban maradnak.
  - A biztonsági másolatban tárolt adatokat csak a régi tároló nem lekért helyreállítási pontjaira tudja visszaállítani.
  - Létre kell hoznunk az adatok új kezdeti replikáját az új tárolón.

- Azure-beli virtuális [](backup-azure-manage-vms.md#stop-protecting-a-vm) gépeknél leállíthatja a védelmet a régi tárolóban található virtuális gép adatainak megőrzésével, áthelyezheti a virtuális gépet egy másik erőforráscsoportba, majd megvédheti a virtuális gépet az új tárolóban. Tekintse [meg a virtuális gépek](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) másik erőforráscsoportba való áthelyezésének útmutatóját és korlátozását.

  Egy virtuális gép egyszerre csak egy tárolóban védható. Az új erőforráscsoportban található virtuális gép azonban védve lehet az új tárolón, mivel más virtuális gépnek számít.

  - Azure Backup szolgáltatás megőrzi a régi tárolón tárolt helyreállítási pontokat.
  - Fizetnie kell azért, hogy a helyreállítási pontok a régi tárolóban maradnak [(Azure Backup a](azure-backup-pricing.md) részletekért).
  - Szükség esetén visszaállíthatja a virtuális gépet a régi tárolóból.
  - Az új erőforrás virtuális gépének új tárolón található első biztonsági mentése egy kezdeti replika lesz.

## <a name="next-steps"></a>Következő lépések

Számos különböző típusú erőforrást áthelyezhet az erőforráscsoportok és az előfizetések között.

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).