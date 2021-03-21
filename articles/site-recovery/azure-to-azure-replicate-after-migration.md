---
title: A vész-helyreállítás beállítása az Azure-ba való Migrálás után Azure Site Recovery
description: Ez a cikk bemutatja, hogyan készítheti elő a gépeket az Azure-régiók közötti vész-helyreállítás beállításához az Azure-ba való Migrálás után Azure Site Recovery használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a71e476a214c44514c7d57c54a09a38218ad6d2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135680"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Az Azure-beli virtuális gépek vészhelyreállításának beállítása Azure-ba történő migrálás után 


Kövesse ezt a cikket, ha a [site Recovery](site-recovery-overview.md) szolgáltatás használatával [áttelepítette a helyszíni gépeket az Azure-beli virtuális gépekre](./migrate-tutorial-on-premises-azure.md) , és most szeretné lekérni azokat a virtuális gépeket, amelyek a másodlagos Azure-régióba való helyreállításhoz lettek beállítva. A cikk azt ismerteti, hogyan biztosítható, hogy az Azure-beli virtuálisgép-ügynök telepítve legyen az áttelepített virtuális gépekre, és hogy miként távolítható el az áttelepítés után már nem szükséges Site Recovery mobilitási szolgáltatás.



## <a name="verify-migration"></a>Áttelepítés ellenőrzése

A vész-helyreállítás beállítása előtt győződjön meg arról, hogy az áttelepítés a várt módon fejeződött be. Az áttelepítés sikeres befejezéséhez a feladatátvétel után válassza a **teljes áttelepítési** lehetőséget minden áttelepíteni kívánt gépen. 

## <a name="verify-the-azure-vm-agent"></a>Az Azure VM-ügynök ellenőrzése

Minden egyes Azure-beli virtuális gépnek telepítve kell lennie az Azure virtuálisgép- [ügynöknek](../virtual-machines/extensions/agent-windows.md) . Az Azure-beli virtuális gépek replikálásához Site Recovery telepít egy bővítményt az ügynökön.

- Ha a gép a Site Recovery mobilitási szolgáltatás 9.7.0.0 vagy újabb verzióját futtatja, a mobilitási szolgáltatás automatikusan telepíti az Azure-beli virtuálisgép-ügynököt a Windows rendszerű virtuális gépeken. A mobilitási szolgáltatás korábbi verzióiban az ügynököt manuálisan kell telepíteni.
- Linux rendszerű virtuális gépek esetén manuálisan kell telepítenie az Azure virtuálisgép-ügynököt. Csak akkor kell telepítenie az Azure virtuálisgép-ügynököt, ha az áttelepített gépen telepített mobilitási szolgáltatás a v 9.6 vagy régebbi verzió.


### <a name="install-the-agent-on-windows-vms"></a>Az ügynök telepítése Windows rendszerű virtuális gépekre

Ha a 9.7.0.0-nál korábbi Site Recovery mobilitási szolgáltatás verzióját futtatja, vagy az ügynököt manuálisan kell telepítenie, tegye a következőket:  

1. Győződjön meg arról, hogy rendszergazdai jogosultságokkal rendelkezik a virtuális gépen.
2. Töltse le a [VM-ügynök telepítőjét](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Futtassa a telepítőfájlt.

#### <a name="validate-the-installation"></a>A telepítés ellenőrzése
Az ügynök telepítésének ellenőrzését:

1. Az Azure-beli virtuális gépen, a C:\WindowsAzure\Packages mappában megjelenik a WaAppAgent.exe fájl.
2. Kattintson a jobb gombbal a fájlra, majd a **Tulajdonságok** területen válassza a **részletek** lapot.
3. Ellenőrizze, hogy a **termék verziója** mezőben a 2.6.1198.718 vagy a magasabb érték látható-e.

[További](../virtual-machines/extensions/agent-windows.md) információ a Windows rendszerhez készült ügynök telepítéséről.

### <a name="install-the-agent-on-linux-vms"></a>Az ügynök telepítése Linux rendszerű virtuális gépekre

Telepítse manuálisan az [Azure Linux VM](../virtual-machines/extensions/agent-linux.md) -ügynököt az alábbiak szerint:

1. Győződjön meg arról, hogy rendszergazdai jogosultságokkal rendelkezik a gépen.
2. Javasoljuk, hogy a Linux rendszerű virtuális gép ügynökét egy RPM vagy egy DEB-csomag használatával telepítse a terjesztési csomag adattárában. Az összes [támogatott terjesztési szolgáltató](../virtual-machines/linux/endorsed-distros.md) integrálja az Azure Linux-ügynök csomagját a lemezképbe és a tárházba.
    - Javasoljuk, hogy csak terjesztési tárházon keresztül frissítse az ügynököt.
    - Nem javasoljuk, hogy közvetlenül a GitHubról telepítse a Linux VM-ügynököt, és frissítse azt.
    -  Ha a disztribúcióhoz tartozó legújabb ügynök nem érhető el, a telepítésével kapcsolatos útmutatásért forduljon az elosztási támogatáshoz. 

#### <a name="validate-the-installation"></a>A telepítés ellenőrzése 

1. Futtassa ezt a parancsot: **PS-e** , és győződjön meg arról, hogy az Azure-ügynök fut a Linux rendszerű virtuális gépen.
2. Ha a folyamat nem fut, indítsa újra a következő parancsok használatával:
    - Ubuntu esetén: **Service walinuxagent Start**
    - Egyéb disztribúciók esetén: **szolgáltatás waagent indítása**


## <a name="uninstall-the-mobility-service"></a>A mobilitási szolgáltatás eltávolítása

1. Manuálisan távolítsa el a mobilitási szolgáltatást az Azure-beli virtuális gépről az alábbi módszerek egyikének használatával. 
    - Windows esetén a Vezérlőpulton > programok telepítése **/törlése**, **Microsoft Azure site Recovery mobilitási szolgáltatás/fő célkiszolgáló** eltávolítása. Futtassa a következő parancsot egy rendszergazda jogú parancssorban:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux esetén jelentkezzen be root felhasználóként. A terminálban nyissa meg a **/User/local/ASR**, és futtassa a következő parancsot:
        ```
        ./uninstall.sh -Y
        ```
2. A replikáció konfigurálása előtt indítsa újra a virtuális gépet.

## <a name="next-steps"></a>Következő lépések

[Tekintse át](site-recovery-extension-troubleshoot.md) az Azure virtuálisgép-ügynök site Recovery bővítményének hibaelhárítását.
[Gyorsan replikálhat](azure-to-azure-quickstart.md) egy Azure-beli virtuális gépet egy másodlagos régióba.
