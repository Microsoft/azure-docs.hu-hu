---
title: Azure Site Recovery-tároló törlése
description: Megtudhatja, hogyan törölhet Azure Site Recoveryhoz konfigurált Recovery Services-tárolót
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: sideeksh
ms.openlocfilehash: a33e04a24013d5450c98b91048fa418958d16886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89426384"
---
# <a name="delete-a-site-recovery-services-vault"></a>Site Recovery Services-tár törlése

Ez a cikk a Site Recovery Recovery Services-tárolójának törlését ismerteti. A Azure Backupban használt tár törléséhez lásd: [Backup-tároló törlése az Azure-ban](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Előkészületek

A tároló törlése előtt el kell távolítania a regisztrált kiszolgálókat és a tárolóban lévő elemeket. Az eltávolítandó replikálási forgatókönyvek a telepítéshez szükséges replikációtól függenek. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Tár törlése – Azure-beli virtuális gép az Azure-ba

1. Az összes védett virtuális gép törléséhez kövesse az [alábbi utasításokat](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) .
2. Ezután törölje a tárolót.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Tár – VMware virtuális gép törlése az Azure-ba

1. Az összes védett virtuális gép törléséhez kövesse az [alábbi utasításokat](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) .
2. Az összes replikációs házirend törléséhez kövesse az [alábbi lépéseket](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) .
3. Törölje a vCenter mutató hivatkozásokat az [alábbi lépésekkel](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).
4. A konfigurációs kiszolgáló leszereléséhez kövesse [az alábbi utasításokat](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) .
5. Ezután törölje a tárolót.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Tár-Hyper-V virtuális gép (VMM) törlése az Azure-ba

1. A System Center VMM által felügyelt Hyper-V virtuális gépek törléséhez kövesse az [alábbi lépéseket](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) .
2. Szüntesse meg az összes replikációs házirend hozzárendelését és törlését. Ezt a tárolóban > **site Recovery infrastruktúra**  >  **a System Center VMM**  >  **replikációs házirendek** esetében.
3. A csatlakoztatott VMM-kiszolgálók regisztrációjának megszüntetéséhez kövesse [az alábbi lépéseket](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) .
4. Ezután törölje a tárolót.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Tároló-Hyper-V virtuális gép törlése az Azure-ba

1. Az összes védett virtuális gép törléséhez kövesse az [alábbi lépéseket](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) .
2. Szüntesse meg az összes replikációs házirend hozzárendelését és törlését. Ezt a tárolóban > a   >  **Hyper-V-helyek**  >  **replikációs házirendjeihez** site Recovery infrastruktúrát.
3. A Hyper-V-gazdagépek regisztrációjának megszüntetéséhez kövesse az [alábbi utasításokat](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) .
4. Törölje a Hyper-V-helyet.
5. Ezután törölje a tárolót.


## <a name="use-powershell-to-force-delete-the-vault"></a>A tároló törlésének kényszerítése a PowerShell használatával 

> [!Important]
> Ha teszteli a terméket, és nem aggódik az adatvesztéssel kapcsolatban, akkor a kényszerített törlési módszer használatával gyorsan eltávolíthatja a tárolót és annak összes függőségét.
> A PowerShell-parancs törli a tár összes tartalmát, és **nem vonható** vissza.

Ha védett elemek is vannak, akkor a Site Recovery-tároló törléséhez használja a következő parancsokat:

```azurepowershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName "XXXXX"

$vault = Get-AzRecoveryServicesVault -Name "vaultname"

Remove-AzRecoveryServicesVault -Vault $vault
```

További információ a [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)és a [Remove-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
