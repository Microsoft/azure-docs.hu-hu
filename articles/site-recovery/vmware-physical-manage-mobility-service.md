---
title: A VMware/fizikai kiszolgálók mobilitási ügynökének kezelése Azure Site Recovery
description: A mobilitási szolgáltatás ügynökének kezelése a VMware virtuális gépek és fizikai kiszolgálók az Azure-ba való vész-helyreállításához a Azure Site Recovery szolgáltatás használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: d921bddf90c415cb244e2cc9ad98354392a537ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90530148"
---
# <a name="manage-the-mobility-agent"></a>A Mobility-ügynök kezelése 

Ha Azure Site Recoveryt használ a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba történő helyreállításához, akkor a mobilitási ügynököt a kiszolgálón kell beállítania. A mobilitási ügynök koordinálja a védett számítógép, a konfigurációs kiszolgáló/kibővíthető folyamat kiszolgálója közötti kommunikációt, és kezeli az adatreplikációt. Ez a cikk összefoglalja a mobilitási ügynök telepítés utáni általános feladatait.

>[!TIP]
>Egy adott OS/Linux-disztribúcióhoz tartozó telepítő letöltéséhez tekintse meg az útmutatást [itt](vmware-physical-mobility-service-overview.md#locate-installer-files). A portálról történő automatikus frissítéshez nem kell letöltenie a telepítőt. Az [ASR automatikusan beolvassa a telepítőt a konfigurációs kiszolgálóról, és frissíti az ügynököt](#update-mobility-service-from-azure-portal).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Mobilitási szolgáltatás frissítése Azure Portal

1. Mielőtt elkezdené, győződjön meg arról, hogy a konfigurációs kiszolgáló, a kibővíthető folyamat-kiszolgálók és a telepítés részét képező fő célkiszolgáló frissítése megtörtént, mielőtt frissíti a mobilitási szolgáltatást a védett gépeken.
    1. A 9,36-es verziótól kezdődően, SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5, a Debian 7 esetében győződjön meg arról, hogy a legújabb telepítő [elérhető a konfigurációs kiszolgálón és a kibővíthető folyamat kiszolgálóján](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).
1. A portálon nyissa meg a tároló > **replikált elemeket**.
1. Ha a konfigurációs kiszolgáló a legújabb verzió, megjelenik egy értesítés, amely beolvassa az "új site Recovery replikációs ügynök frissítése" című témakört. Kattintson ide a telepítéshez. "

     ![Replikált elemek ablak](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Kattintson az értesítésre, majd az **ügynök frissítése** területen válassza ki azokat a gépeket, amelyeken frissíteni kívánja a mobilitási szolgáltatást. Ezután kattintson az **OK** gombra.

     ![Replikált elemek virtuálisgép-listája](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. A mobilitási szolgáltatás frissítése feladata minden kiválasztott gépen elindul. A mobilitási ügynök frissítve lett a konfigurációs kiszolgáló verziójára. Ha például a konfigurációs kiszolgáló a 9,33-es verzióban található, a védett virtuális gépen lévő mobilitási ügynök a 9,33-es verzióra is frissül.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Mobilitási szolgáltatás frissítése PowerShell-parancsfájllal a Windows Serveren

Mielőtt elkezdené, győződjön meg arról, hogy a konfigurációs kiszolgáló, a kibővíthető folyamat-kiszolgálók és a telepítés részét képező fő célkiszolgáló frissítése megtörtént, mielőtt frissíti a mobilitási szolgáltatást a védett gépeken.

A következő szkripttel frissítheti a mobilitási szolgáltatást a kiszolgálón a Power Shell parancsmag használatával

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Mobilitási szolgáltatás manuális frissítése minden védett kiszolgálón

1. Mielőtt elkezdené, győződjön meg arról, hogy a konfigurációs kiszolgáló, a kibővíthető folyamat-kiszolgálók és a telepítés részét képező fő célkiszolgáló frissítése megtörtént, mielőtt frissíti a mobilitási szolgáltatást a védett gépeken.

2. [Keresse meg az ügynök telepítőjét](vmware-physical-mobility-service-overview.md#locate-installer-files) a-kiszolgáló operációs rendszere alapján.

>[!IMPORTANT]
> Ha az Azure IaaS virtuális gépet egy Azure-régióból egy másikba replikálja, ne használja ezt a metódust. Az összes elérhető lehetőségről az [útmutatóban](azure-to-azure-autoupdate.md) tájékozódhat.

3. Másolja a telepítési fájlt a védett gépre, és futtassa a mobilitási ügynök frissítéséhez.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>A mobilitási szolgáltatás leküldéses telepítéséhez használt fiók frissítése

Site Recovery üzembe helyezése esetén a mobilitási szolgáltatás leküldéses telepítésének engedélyezéséhez olyan fiókot adott meg, amelyet a Site Recovery Process Server a gépek eléréséhez használ, és telepíti a szolgáltatást, amikor a replikáció engedélyezve van a gépen. Ha frissíteni szeretné a fiók hitelesítő adatait, kövesse az [alábbi utasításokat](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Mobilitási szolgáltatás eltávolítása

### <a name="on-a-windows-machine"></a>Windows rendszerű gépen

Távolítsa el a felhasználói felületet vagy a parancssorból.

- **A felhasználói felületen**: a gép Vezérlőpultján válassza a **programok** lehetőséget. Válassza a **Microsoft Azure site Recovery mobilitási szolgáltatás/fő célkiszolgáló**  >  **eltávolítása** lehetőséget.
- **Parancssorból**: nyisson meg egy parancssori ablakot rendszergazdaként a gépen. Futtassa az alábbi parancsot: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Linux rendszerű gépen
1. A Linux rendszerű gépen jelentkezzen be **root** felhasználóként.
2. A terminálban nyissa meg a/usr/local/ASR
3. Futtassa az alábbi parancsot:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Site Recovery VSS-szolgáltató telepítése a forrásoldali gépen

Azure Site Recovery VSS-szolgáltatóra van szükség a forrásoldali gépen az alkalmazás konzisztencia-pontjainak létrehozásához. Ha a szolgáltató telepítése nem sikerült a leküldéses telepítéssel, az alábbi útmutatást követve manuálisan telepítheti azt.

1. Nyissa meg a rendszergazda cmd ablakot.
2. Navigáljon a mobilitási szolgáltatás telepítési helyére. (Például – C:\Program Files (x86) \Microsoft Azure site Recovery\agent)
3. Futtassa a InMageVSSProvider_Uninstall. cmd parancsfájlt. Ez a művelet eltávolítja a szolgáltatást, ha az már létezik.
4. Futtassa a InMageVSSProvider_Install. cmd parancsfájlt a VSS-szolgáltató manuális telepítéséhez.

## <a name="next-steps"></a>Következő lépések

- [Vész-helyreállítás beállítása VMware virtuális gépekhez](vmware-azure-tutorial.md)
- [A fizikai kiszolgálók vész-helyreállításának beállítása](physical-azure-disaster-recovery.md)
