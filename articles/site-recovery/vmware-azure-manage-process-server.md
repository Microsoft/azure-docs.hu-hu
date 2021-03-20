---
title: A VMware virtuális gépek/fizikai kiszolgáló vész-helyreállításának folyamat-kiszolgálójának kezelése Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan kezelhetők a Process Server a VMware virtuális gépek/fizikai kiszolgálók vész-helyreállításához Azure Site Recovery használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: a547a874c42d06d8453b154847561d8b5f0dabb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019189"
---
# <a name="manage-process-servers"></a>Folyamatkiszolgálók kezelése

Ez a cikk a Site Recovery Process Server felügyeletének általános feladatait ismerteti.

A Process Server replikációs adatfogadásra, optimalizálásra és az Azure-ba történő küldésére szolgál. Emellett elvégzi a mobilitási szolgáltatás leküldéses telepítését a replikálni kívánt VMware virtuális gépeken és fizikai kiszolgálókon, valamint a helyszíni gépek automatikus felderítését. A helyszíni VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba történő replikálásához a Process Server alapértelmezés szerint telepítve van a konfigurációs kiszolgáló számítógépén. 

- Nagyméretű telepítések esetén további helyszíni folyamat-kiszolgálókra lehet szükség a kapacitás skálázásához.
- Az Azure-ból a helyszíni rendszerbe való feladat-visszavételhez be kell állítania egy ideiglenes folyamat-kiszolgálót az Azure-ban. Ezt a virtuális gépet akkor törölheti, ha a feladat-visszavétel befejeződött. 

További információ a Process Serverről.


## <a name="upgrade-a-process-server"></a>Folyamat kiszolgálójának frissítése

Ha helyszíni vagy Azure-beli kiszolgálóként helyez üzembe egy folyamatot a feladat-visszavételhez, a rendszer telepíti a Process Server legújabb verzióját. A Site Recovery csapatok rendszeresen javítanak a javításokon és fejlesztéseken, és javasoljuk, hogy a folyamat-kiszolgálók naprakészen maradjanak. A folyamat-kiszolgálót a következőképpen frissítheti:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Virtuális gépek áthelyezése a folyamat-kiszolgáló terhelésének kiegyensúlyozására

Az alábbiak szerint kiegyensúlyozhatja a terhelést úgy, hogy áthelyezi a virtuális gépeket két folyamat kiszolgálója között:

1. A tárolóban a **kezelés** alatt kattintson **site Recovery infrastruktúra** elemre. **A VMware & fizikai gépek** területen kattintson a **konfigurációs kiszolgálók** elemre.
2. Kattintson arra a konfigurációs kiszolgálóra, amelyen a folyamat-kiszolgálók regisztrálva vannak.
3. Kattintson arra a folyamat-kiszolgálóra, amelynek terheléselosztási forgalmát el szeretné osztani.

    ![A képernyőképen egy olyan folyamat-kiszolgáló látható, amelyen terheléselosztási forgalmat lehet betölteni.](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Kattintson a **terheléselosztás** elemre, válassza ki azt a cél-feldolgozási kiszolgálót, amelyre a gépeket át szeretné helyezni. Ezután kattintson **az OK** gombra.

    ![Képernyőfelvétel: a terheléselosztás ablaktábla kijelölése a cél-feldolgozási kiszolgáló kijelölésével.](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Kattintson a **gépek kiválasztása** lehetőségre, majd válassza ki azokat a gépeket, amelyeket az aktuálisról a cél-feldolgozási kiszolgálóra szeretne áthelyezni. Az átlagos adatváltozás részletei az egyes virtuális gépeken jelennek meg. Ezután kattintson az **OK** gombra. 
3. A tárolóban figyelje a feladat előrehaladását a **figyelés**  >  **site Recovery feladatok** területen.

Körülbelül 15 percet vesz igénybe, hogy a változások tükrözzék a portálon. A gyorsabb hatás érdekében [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Teljes munkaterhelés váltása másik folyamat-kiszolgálóra

Helyezze át a folyamat-kiszolgáló által kezelt teljes munkaterhelést egy másik folyamat-kiszolgálóra, az alábbiak szerint:

1. A tárolóban a **kezelés** alatt kattintson **site Recovery infrastruktúra** elemre. **A VMware & fizikai gépek** területen kattintson a **konfigurációs kiszolgálók** elemre.
2. Kattintson arra a konfigurációs kiszolgálóra, amelyen a folyamat-kiszolgálók regisztrálva vannak.
3. Kattintson arra a folyamat-kiszolgálóra, amelyről át szeretné váltani a munkaterhelést.
4. Kattintson a **kapcsoló** elemre, válassza ki azt a cél-feldolgozási kiszolgálót, amelyre át szeretné helyezni a munkaterhelést. Ezután kattintson **az OK** gombra.

    ![Képernyőfelvétel: a cél-feldolgozási kiszolgáló kiválasztása panel.](media/vmware-azure-manage-process-server/Switch.PNG)

5. A tárolóban figyelje a feladat előrehaladását a **figyelés**  >  **site Recovery feladatok** területen.

Körülbelül 15 percet vesz igénybe, hogy a változások tükrözzék a portálon. A gyorsabb hatás érdekében [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>Fő célkiszolgáló regisztrálása

A fő célkiszolgáló a konfigurációs kiszolgálón és a kibővíthető folyamat-kiszolgálókon található. Regisztrálni kell a konfigurációs kiszolgálóval. Ha a regisztráció során hiba lép fel, az hatással lehet a védett elemek állapotára. A fő célkiszolgáló konfigurációs kiszolgálóval való regisztrálásához jelentkezzen be az adott konfigurációs kiszolgáló/kibővítő folyamat kiszolgálójára, amelyen a regisztráció szükséges. Navigáljon a mappa **%PROGRAMDATA%\ASR\Agent**, és futtassa a következő parancsot a rendszergazdai parancssorban.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Folyamat-kiszolgáló újraregisztrálása

A következő módon regisztrálja újra a helyszíni vagy egy Azure-beli virtuális gépen futó folyamat-kiszolgálót a konfigurációs kiszolgálóval:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

A beállítások mentése után tegye a következőket:

1. Nyisson meg egy rendszergazdai parancssort a Process Server kiszolgálón.
2. Tallózással keresse meg a **%PROGRAMDATA%\ASR\Agent** mappát, és futtassa a következő parancsot:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Helyi folyamat kiszolgálójának proxybeállításait módosítva

Ha egy helyszíni folyamat-kiszolgáló egy proxy használatával csatlakozik az Azure-hoz, a proxybeállításokat a következőképpen módosíthatja:

1. Jelentkezzen be a Process Server-gépre. 
2. Nyisson meg egy rendszergazdai PowerShell-parancssorablakot, és futtassa a következő parancsot:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Tallózással keresse meg a **%PROGRAMDATA%\ASR\Agent** mappát, és futtassa a következő parancsot:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Folyamat kiszolgálójának eltávolítása

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Mappák kizárása a víruskereső szoftverből

Ha a víruskereső szoftver egy kibővíthető folyamat-kiszolgálón (vagy fő célkiszolgálón) fut, akkor zárja ki a következő mappákat a vírusellenőrző műveletekből:


- C:\Program Files\Microsoft Azure Recovery Services ügynök
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- A Process Server telepítési könyvtára. Például: C:\Program Files (x86) \Microsoft Azure Site Recovery