---
title: Kiszolgálók eltávolítása és a védelem letiltása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet törölni a kiszolgálókat egy Site Recovery-tárolóból, és letiltani a virtuális gépek és a fizikai kiszolgálók védelmét.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: sharrai
ms.openlocfilehash: 1356deabd13db1dd2f29ac1b1f088db2120353fe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738854"
---
# <a name="remove-servers-and-disable-protection"></a>Kiszolgálók eltávolítása és a védelem letiltása

Ez a cikk ismerteti, hogyan törölheti a kiszolgálókat egy Recovery Services-tárolóból, és hogyan tilthatja le a Site Recovery által védett gépek védelmét.


## <a name="unregister-a--configuration-server"></a>Konfigurációs kiszolgáló regisztrációjának törlése

Ha VMware virtuális gépeket vagy Windows/Linux rendszerű fizikai kiszolgálókat replikál az Azure-ba, akkor a következő módon törölheti a nem csatlakoztatott konfigurációs kiszolgáló regisztrációját a tárból:

1. [Tiltsa le a virtuális gépek védelmét](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. Replikációs házirendek [hozzárendelése vagy törlése](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) .
3. [A konfigurációs kiszolgáló törlése](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>VMM-kiszolgáló regisztrációjának törlése

1. Állítsa le a virtuális gépek Felhőbeli replikálását az eltávolítani kívánt VMM-kiszolgálón.
2. Törölje a törölni kívánt VMM-kiszolgálón a felhők által használt hálózati leképezéseket. A   >  **System Center VMM**  >  **hálózati leképezés** site Recovery infrastruktúrájában kattintson a jobb gombbal a hálózati leképezés > **Törlés** elemre.
3. Jegyezze fel a VMM-kiszolgáló AZONOSÍTÓját.
4. Törölje a replikációs házirendeket az eltávolítani kívánt VMM-kiszolgálón lévő felhőkből.  **Site Recovery infrastruktúra**  >  **a System Center VMM**-  >   **replikációs házirendek** esetében kattintson duplán a társított házirendre. Kattintson a jobb gombbal a felhőre, > szüntesse meg a **hozzárendelést**.
5. Törölje a VMM-kiszolgálót vagy az aktív csomópontot. A   >  **System Center VMM**  >  **VMM-kiszolgálók** site Recovery infrastruktúrájában kattintson a jobb gombbal a kiszolgálóra > **Törlés** elemre.
6. Ha a VMM-kiszolgáló leválasztott állapotban volt, töltse le és futtassa a [karbantartási parancsfájlt](/samples/browse/?redirectedfrom=TechNet-Gallery) a VMM-kiszolgálón. Nyissa meg a PowerShellt a **Futtatás rendszergazdaként** beállítással, hogy megváltoztassa az alapértelmezett (LocalMachine) hatókör végrehajtási szabályzatát. A parancsfájlban adja meg az eltávolítani kívánt VMM-kiszolgáló AZONOSÍTÓját. A szkript eltávolítja a regisztrációs és a Felhőbeli párosítási információkat a kiszolgálóról.
5. Futtassa a karbantartási parancsfájlt bármely másodlagos VMM-kiszolgálón.
6. Futtassa a karbantartási parancsfájlt minden olyan passzív VMM-fürtön, amelyen telepítve van a szolgáltató.
7. Távolítsa el manuálisan a szolgáltatót a VMM-kiszolgálón. Ha rendelkezik fürttel, távolítsa el az összes csomópontot.
8. Ha a virtuális gépeket az Azure-ba replikálták, el kell távolítania a Microsoft Recovery Services Agent ügynököt a törölt felhőkben lévő Hyper-V-gazdagépekről.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Hyper-V-gazdagép regisztrációjának törlése Hyper-V-helyen

A VMM által nem felügyelt Hyper-V-gazdagépek összegyűjtése Hyper-V-helyre történik. Távolítson el egy gazdagépet a Hyper-V-helyen a következőképpen:

1. Tiltsa le a gazdagépen lévő Hyper-V virtuális gépek replikálását.
2. A Hyper-V-helyhez tartozó házirendek hozzárendelésének megszüntetése. A   >  **Hyper-V-helyek**  >   **replikációs házirendjeinek** site Recovery infrastruktúrájában kattintson duplán a társított házirendre. Kattintson a jobb gombbal a helyre, > a **hozzárendelés** elemre.
3. Hyper-V-gazdagépek törlése. A   >  **Hyper-v-helyek**  >  **Hyper-v-gazdagépek** site Recovery infrastruktúrájában kattintson a jobb gombbal a kiszolgálóra > **Törlés** lehetőségre.
4. Törölje a Hyper-V-helyet, miután az összes gazdagép el lett távolítva belőle. A   >  **Hyper-v helyek** site Recovery infrastruktúrájában a  >  **Hyper-v-helyek** területen kattintson a jobb gombbal a helyre > **delete (Törlés**) elemre.
5. Ha a Hyper-V-gazdagép **leválasztott** állapotban volt, futtassa az alábbi szkriptet minden egyes eltávolított Hyper-v-gazdagépen. A szkript törli a beállításokat a kiszolgálón, és megszünteti a regisztrációt a tárolóból.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>VMware virtuális gép vagy fizikai kiszolgáló védelmének letiltása (VMware – Azure)

1. A **védett elemek**  >  **replikált elemek** területen kattintson a jobb gombbal a gépre > a **replikáció letiltása** lehetőségre.
2. A **replikáció letiltása** lapon válasszon egyet a következő lehetőségek közül:
    - **Replikáció letiltása és eltávolítás (ajánlott)** – ez a lehetőség eltávolítja a replikált elemet a Azure site Recoveryról, és leállítja a gép replikálását. A konfigurációs kiszolgálón a replikációs konfiguráció törlődik, és a védett kiszolgáló Site Recovery számlázása leállt. Vegye figyelembe, hogy ez a beállítás csak akkor használható, ha a konfigurációs kiszolgáló csatlakoztatott állapotban van.
    - **Eltávolítás** – ez a beállítás csak akkor használható, ha a forrás környezet törölve van vagy nem érhető el (nincs csatlakoztatva). Ezzel eltávolítja a replikált elemet a Azure Site Recoveryról (a számlázás leállt). A konfigurációs kiszolgálón a replikálási konfiguráció **nem** lesz törölve. 

> [!NOTE]
> Az Options mobilitási szolgáltatás nem lesz eltávolítva a védett kiszolgálókról, manuálisan kell eltávolítania. Ha azt tervezi, hogy a kiszolgálót ugyanazzal a konfigurációs kiszolgálóval kívánja védetté tenni, akkor kihagyhatja a mobilitási szolgáltatás eltávolítását.

> [!NOTE]
> Ha a virtuális gép feladatátvétele már megtörtént, és az Azure-ban fut, vegye figyelembe, hogy a védelem letiltása nem távolítja el és nem érinti a feladatátvételt használó virtuális gépet.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Azure-beli virtuális gép védelmének letiltása (Azure-ról Azure-ra)

-  A **védett elemek**  >  **replikált elemek** területen kattintson a jobb gombbal a gépre > a **replikáció letiltása** lehetőségre.
> [!NOTE]
> a mobilitási szolgáltatás nem lesz eltávolítva a védett kiszolgálókról, manuálisan kell eltávolítania. Ha azt tervezi, hogy ismét meg szeretné tenni a kiszolgálót, akkor kihagyhatja a mobilitási szolgáltatás eltávolítását.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Hyper-V rendszerű virtuális gép védelmének letiltása (Hyper-V-ről Azure-ra)

> [!NOTE]
> Ezt az eljárást akkor használja, ha VMM-kiszolgáló nélkül replikálja a Hyper-V virtuális gépeket az Azure-ba. Ha a **System Center VMM** használatával replikálja a virtuális gépeket az Azure-ba, kövesse az utasításokat a Hyper-V virtuális gépek védelmének letiltása a System Center VMM és az Azure forgatókönyv használatával.

1. A **védett elemek**  >  **replikált elemek** területen kattintson a jobb gombbal a gépre > a **replikáció letiltása** lehetőségre.
2. A **replikáció letiltása** lapon a következő lehetőségek közül választhat:
   - **Replikáció letiltása és eltávolítás (ajánlott)** – ez a lehetőség eltávolítja a replikált elemet a Azure site Recoveryról, és leállítja a gép replikálását. A helyszíni virtuális gépen a replikálási konfiguráció törlődik, és a védett kiszolgáló számlázása Site Recovery leállt.
   - **Eltávolítás** – ez a beállítás csak akkor használható, ha a forrás környezet törölve van vagy nem érhető el (nincs csatlakoztatva). Ezzel eltávolítja a replikált elemet a Azure Site Recoveryról (a számlázás leállt). A helyszíni virtuális gépen a replikálási konfiguráció **nem** lesz törölve. 

    > [!NOTE]
    > Ha az **Eltávolítás** lehetőséget választotta, akkor futtassa a következő parancsfájlokat a helyszíni Hyper-V kiszolgáló replikációs beállításainak tisztításához.

    > [!NOTE]
    > Ha a virtuális gép feladatátvétele már megtörtént, és az Azure-ban fut, vegye figyelembe, hogy a védelem letiltása nem távolítja el és nem érinti a feladatátvételt használó virtuális gépet.

1. A forrás Hyper-V gazdagép kiszolgálóján a virtuális gép replikálásának eltávolításához. Cserélje le a SQLVM1-t a virtuális gép nevére, és futtassa a szkriptet egy felügyeleti PowerShellből

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Az Azure-ba replikált Hyper-V virtuális gépek védelmének letiltása a System Center VMM és az Azure-forgatókönyv használatával

1. A **védett elemek**  >  **replikált elemek** területen kattintson a jobb gombbal a gépre > a **replikáció letiltása** lehetőségre.
2. A **replikáció letiltása** területen válasszon egyet a következő lehetőségek közül:

   - **Replikáció letiltása és eltávolítás (ajánlott)** – ez a lehetőség eltávolítja a replikált elemet a Azure site Recoveryról, és leállítja a gép replikálását. A helyszíni virtuális gépen a replikálási konfiguráció törlődik, és a védett kiszolgáló Site Recovery számlázása leállt.
   - **Eltávolítás** – ez a beállítás csak akkor használható, ha a forrás környezet törölve van vagy nem érhető el (nincs csatlakoztatva). Ezzel eltávolítja a replikált elemet a Azure Site Recoveryról (a számlázás leállt). A helyszíni virtuális gépen a replikálási konfiguráció **nem** lesz törölve. 

     > [!NOTE]
     > Ha az **Eltávolítás** lehetőséget választotta, a következő szkriptek Kitakarításával törölheti a helyszíni VMM-kiszolgáló replikációs beállításait.
3. Futtassa ezt a parancsfájlt a forrás VMM-kiszolgálón a PowerShell használatával (rendszergazdai jogosultságok szükségesek) a VMM-konzolról. Cserélje le a helyőrző **SQLVM1** a virtuális gép nevére.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. A fenti lépések törlik a replikációs beállításokat a VMM-kiszolgálón. A Hyper-V-gazdagépen futó virtuális gép replikálásának leállításához futtassa ezt a parancsfájlt. Cserélje le a SQLVM1 nevet a virtuális gép nevére, és host01.contoso.com a Hyper-V-kiszolgáló nevével.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>A Hyper-V rendszerű virtuális gépek védelmének letiltása másodlagos VMM-kiszolgálóra replikálás a System Center VMM és a VMM forgatókönyv használatával

1. A **védett elemek**  >  **replikált elemek** területen kattintson a jobb gombbal a gépre > a **replikáció letiltása** lehetőségre.
2. A **replikáció letiltása** területen válasszon egyet a következő lehetőségek közül:

   - **Replikáció letiltása és eltávolítás (ajánlott)** – ez a lehetőség eltávolítja a replikált elemet a Azure site Recoveryról, és leállítja a gép replikálását. A helyszíni virtuális gépen a replikálási konfiguráció törlődik, és a védett kiszolgáló Site Recovery számlázása leállt.
   - **Eltávolítás** – ez a beállítás csak akkor használható, ha a forrás környezet törölve van vagy nem érhető el (nincs csatlakoztatva). Ezzel eltávolítja a replikált elemet a Azure Site Recoveryról (a számlázás leállt). A helyszíni virtuális gépen a replikálási konfiguráció **nem** lesz törölve. Futtassa a következő parancsfájlokat a replikációs beállítások helyszíni virtuális gépek karbantartásához.
     > [!NOTE]
     > Ha az **Eltávolítás** lehetőséget választotta, a következő szkriptek Kitakarításával törölheti a helyszíni VMM-kiszolgáló replikációs beállításait.

3. Futtassa ezt a parancsfájlt a forrás VMM-kiszolgálón a PowerShell használatával (rendszergazdai jogosultságok szükségesek) a VMM-konzolról. Cserélje le a helyőrző **SQLVM1** a virtuális gép nevére.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. A másodlagos VMM-kiszolgálón futtassa ezt a parancsfájlt a másodlagos virtuális gép beállításainak megtisztításához:

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Remove-SCVirtualMachine -VM $vm -Force
    ```

5. A másodlagos VMM-kiszolgálón frissítse a Hyper-V-gazdagépen lévő virtuális gépeket, hogy a másodlagos virtuális gép ismét észlelve legyen a VMM-konzolon.
6. A fenti lépésekkel törölheti a replikációs beállításokat a VMM-kiszolgálón. Ha le szeretné állítani a virtuális gép replikálását, futtassa a következő parancsfájlt az elsődleges és másodlagos virtuális gépeken. Cserélje le a SQLVM1 nevet a virtuális gép nevére.

    ```powershell
    Remove-VMReplication –VMName "SQLVM1"
    ```