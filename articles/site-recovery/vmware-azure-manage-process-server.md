---
title: Kezelheti a folyamatkiszolgáló vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure-bA az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk azt ismerteti, felügyelhetők a folyamatkiszolgálók állítható be a vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálóról az Azure Site Recovery használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0a0b6c83f800c0a479ba7a16c91b497d1a11da9e
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62732485"
---
# <a name="manage-process-servers"></a>Folyamatkiszolgálók kezelése

Alapértelmezés szerint a folyamatkiszolgáló, amikor replikál a VMware virtuális gépek vagy fizikai kiszolgálók Azure-bA a helyszíni konfigurációs kiszolgáló gépen telepítve van. Van néhány kell állíthat be különálló folyamatkiszolgálót példányok:

- Nagyméretű környezetekben szükség lehet további helyszíni folyamatkiszolgálók kapacitást.
- Feladat-visszavételt, szüksége lesz egy ideiglenes folyamatkiszolgáló állítsa be az Azure-ban. Ez a virtuális gép feladat-visszavétel végeztével törölhetők. 

Ez a cikk összegzi az alábbi további folyamatkiszolgálók a tipikus felügyeleti feladatokhoz.

## <a name="upgrade-a-process-server"></a>Frissítse a folyamatkiszolgálót

Frissítse a folyamatkiszolgálót, a helyszínen vagy az Azure-ban (feladat-visszavétel célokra) fut a következő:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
>   Általában az Azure Gallery-Image használatával folyamatkiszolgáló létrehozása az Azure-ban feladat-visszavétel az alkalmazásában, ha fut a legújabb elérhető verzió. A Site Recovery csapatok kiadás javításokat és rendszeres időközönként fejlesztések, és azt javasoljuk, tartsa naprakészen a folyamatkiszolgálók.

## <a name="balance-the-load-on-process-server"></a>A folyamatkiszolgáló a terhelés kiegyenlítése

Folyamat két kiszolgáló közötti terhelés kiegyenlítése érdekében

1. Navigáljon a **Recovery Services-tároló** > **kezelése** > **Site Recovery-infrastruktúra** > **számára A VMware és fizikai gépek** > **konfigurációs kiszolgálók**.
2. Kattintson a konfigurációs kiszolgálón, amelyhez a folyamatkiszolgálók regisztrált.
3. A konfigurációs kiszolgáló regisztrálva folyamatkiszolgálók listája az oldalon érhetők el.
4. Kattintson a folyamatkiszolgáló, amelyre szeretné módosítani a számítási feladatok.

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

5. Használhatja **terheléselosztás** vagy **kapcsoló** lehetőségek, kövesse az alábbi utasításokat, a követelmény alapján.

### <a name="load-balance"></a>Terheléselosztás

Ez a beállítás keresztül egy vagy több virtuális gépet választhat ki, és átviheti őket egy másik folyamatkiszolgálóra.

1. Kattintson a **terheléselosztása**, folyamat-célkiszolgáló elemet a legördülő menüből válassza a lefelé. Kattintson az **OK** gombra

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Kattintson a **gépek kijelölése**, válassza ki a virtuális gépeket szeretne áthelyezni a jelenlegi folyamat kiszolgálóról az a folyamat-célkiszolgáló. Átlagos változás részletei jelennek meg minden egyes virtuális gépek ellen.
3. Kattintson az **OK** gombra. A feladat előrehaladásának figyeléséhez **Recovery Services-tároló** > **figyelés** > **Site Recovery-feladatok**.
4. A közzététel sikeres befejezését a művelet megfelelően módosítások 15 percet vesz igénybe vagy [frissítse a konfigurációs kiszolgáló](vmware-azure-manage-configuration-server.md#refresh-configuration-server) az azonnali hatállyal.

### <a name="switch"></a>Kapcsoló

Keresztül ezt a beállítást a folyamatkiszolgáló védett teljes munkaterhelés került egy másik folyamatkiszolgálót.

1. Kattintson a **kapcsoló**, válassza ki a cél folyamatkiszolgáló, kattintson a **OK**.

    ![Kapcsoló](media/vmware-azure-manage-process-server/Switch.PNG)

2. A feladat előrehaladásának figyeléséhez **Recovery Services-tároló** > **figyelés** > **Site Recovery-feladatok**.
3. A közzététel sikeres befejezését a művelet megfelelően módosítások 15 percet vesz igénybe vagy [frissítse a konfigurációs kiszolgáló](vmware-azure-manage-configuration-server.md#refresh-configuration-server) az azonnali hatállyal.

## <a name="process-server-selection-guidance"></a>Kiszolgáló kiválasztása útmutatást feldolgozása

Az Azure Site Recovery automatikusan annak jelzése, hogy a Folyamatkiszolgáló közeledik a használati korlátok. Útmutató ismerteti, amikor létrehozása egy kibővíthető folyamatkiszolgáló.

|Állapotadatok  |Magyarázat  | Erőforrás rendelkezésre állása  | Ajánlás|
|---------|---------|---------|---------|
| Kifogástalan (zöld)    |   Folyamatkiszolgáló csatlakozik, és kifogástalan      |CPU és memória kihasználtságáról nem éri a 80 %. Rendelkezésre álló szabad hely a 30 % felett van| A folyamatkiszolgálóhoz további kiszolgálók védelméhez használható. Győződjön meg arról, hogy az új számítási feladatok belül van-e a [folyamat kiszolgálói korlátok definiált](vmware-azure-set-up-process-server-scale.md#sizing-requirements).
|Figyelmeztetés (narancs)    |   Folyamatkiszolgáló csatlakozik, de bizonyos erőforrások készül, hogy eléri a maximális korlát  |   CPU és memória kihasználtságáról van 80 % - 95 % között; Rendelkezésre álló szabad hely van közötti 25 – 30 %       | A folyamatkiszolgáló használati küszöbértékeket közelében van. Új kiszolgálók hozzáadása a folyamatkiszolgáló azonos lesz a küszöbértékek átlépését, és hatással lehet a meglévő védett elemek. Javasolt [beállítása kibővíthető folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md#before-you-start) új replikációk számára.
|Figyelmeztetés (narancs)   |   Folyamatkiszolgáló csatlakozik, de nem történt adatfeltöltés az Azure-bA az elmúlt 30 perc  |   Erőforrás-felhasználás küszöbértéke határértékeken belül van       | Hibaelhárítás [adatok feltöltési hibák](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) új számítási feladatok hozzáadása előtt **vagy** [beállítása kibővíthető folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md#before-you-start) az új replikációk.
|Kritikus (piros)    |     A folyamatkiszolgáló lecsatlakozhat  |  Erőforrás-felhasználás küszöbértéke határértékeken belül van      | Hibaelhárítás [a Folyamatkiszolgáló kapcsolódási problémái](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) vagy [beállítása kibővíthető folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md#before-you-start) az új replikációk.
|Kritikus (piros)    |     Erőforrás-felhasználása elért küszöbérték-korlátok |  CPU és memória kihasználtságáról 95 %-os; felett van Rendelkezésre álló szabad hely, 25 %-nál kisebb.   | Új számítási feladatok ugyanazon folyamatkiszolgáló hozzáadása le van tiltva, már teljesülnek-e korlátok erőforrás küszöbértéket. Tehát [beállítása kibővíthető folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md#before-you-start) új replikációk számára.
Kritikus (piros)    |     Nem történt adatfeltöltés az Azure-bA az elmúlt 45 perc. |  Erőforrás-felhasználás küszöbértéke határértékeken belül van      | Hibaelhárítás [adatok feltöltési hibák](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) új számítási feladatok ugyanazon folyamatkiszolgáló való felvétele előtt vagy [horizontális felskálázási folyamatkiszolgáló beállítása](vmware-azure-set-up-process-server-scale.md#before-you-start)

## <a name="reregister-a-process-server"></a>A folyamatkiszolgáló regisztrálása

Ha szeretné a helyszínen futó folyamatkiszolgáló regisztrálása, vagy az Azure-ban, a konfigurációs kiszolgálóval, tegye a következőket:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

A beállítások mentését követően tegye a következőket:

1. A folyamatkiszolgáló nyisson meg egy rendszergazdai parancssort.
2. Keresse meg a mappa **%PROGRAMDATA%\ASR\Agent**, és futtassa a parancsot:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>A helyszíni folyamatkiszolgálót proxybeállításainak módosítása

Ha a folyamatkiszolgáló csatlakozni az Azure Site Recovery egy proxyt használ, ez az eljárás használható, ha a meglévő proxybeállításokkal módosítani kell.

1. Jelentkezzen be a folyamat kiszolgáló gép. 
2. Nyisson meg egy rendszergazdai PowerShell-parancsablakot, és futtassa a következő parancsot:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Keresse meg a mappa **%PROGRAMDATA%\ASR\Agent**, és futtassa a következő parancsot:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Távolítsa el a folyamatkiszolgáló

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="manage-anti-virus-software-on-process-servers"></a>Víruskereső szoftver a folyamatkiszolgálók kezelése

Ha víruskereső szoftver egy különálló folyamatkiszolgálót vagy a fő célkiszolgáló aktív, a következő mappák kizárása víruskereső műveletek:


- C:\Program Files\Microsoft Azure Recovery Services-ügynök
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Folyamat kiszolgáló telepítési mappájában, például: C:\Program Files (x86)\Microsoft Azure Site Recovery