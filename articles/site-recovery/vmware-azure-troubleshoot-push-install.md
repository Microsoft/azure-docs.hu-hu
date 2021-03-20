---
title: A mobilitási szolgáltatás leküldéses telepítésének hibáinak megoldása Azure Site Recovery
description: A mobilitási szolgáltatások telepítési hibáinak elhárítása, amikor a Azure Site Recovery-vel történő vész-helyreállítási replikációt engedélyez.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 62c8240a4d2e50aa3b584f322baf7d2ee217c6d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98127872"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>A mobilitási szolgáltatás leküldéses telepítésének hibáinak megoldása

A mobilitási szolgáltatás telepítése kulcsfontosságú lépés a replikáció engedélyezéséhez. Ennek a lépésnek a sikere az előfeltételek teljesítése és a támogatott konfigurációk használata. A mobilitási szolgáltatás telepítése során esetlegesen felmerülő leggyakoribb hibák a következők:

* [Hitelesítő adatok/jogosultsági hibák](#credentials-check-errorid-95107--95108)
* [Bejelentkezési hibák](#login-failures-errorid-95519-95520-95521-95522)
* [Csatlakozási hibák](#connectivity-failure-errorid-95117--97118)
* [Fájl-és nyomtatómegosztás – hibák](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Windows Management Instrumentation-(WMI-) hibák](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nem támogatott operációs rendszerek](#unsupported-operating-systems)
* [Nem támogatott rendszerindító konfigurációk](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [A Kötet árnyékmásolata szolgáltatás (VSS) telepítési hibái](#vss-installation-failures)
* [Eszköz neve a GRUB-konfigurációban az eszköz UUID helyett](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Logikai kötet-kezelő (LVM) kötet](#lvm-support-from-920-version)
* [Újraindítási figyelmeztetések](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Ha engedélyezi a replikálást, Azure Site Recovery megkísérli telepíteni a mobilitási szolgáltatás ügynökét a virtuális gépre (VM). A folyamat részeként a konfigurációs kiszolgáló megpróbál kapcsolódni a virtuális géphez, és átmásolja az ügynököt. A sikeres telepítés engedélyezéséhez kövesse a részletes hibaelhárítási útmutatót.

## <a name="credentials-check-errorid-95107--95108"></a>Hitelesítő adatok keresése (ErrorID: 95107 & 95108)

Ellenőrizze, hogy a replikáció engedélyezése során kiválasztott felhasználói fiók érvényes és pontos-e. A Azure Site Recovery a **rendszergazdai jogosultságokkal** rendelkező **főfiókot** vagy felhasználói fiókot kell leküldéses telepítés végrehajtásához. Ellenkező esetben a leküldéses telepítést a rendszer letiltja a forrásoldali gépen.

Windows esetén (**95107**-es hiba) ellenőrizze, hogy a felhasználói fiók rendelkezik-e rendszergazdai hozzáféréssel a forrásszámítógépen, helyi fiókkal vagy tartományi fiókkal. Ha nem használ tartományi fiókot, le kell tiltania a távoli felhasználói hozzáférés-vezérlést a helyi számítógépen.

* Távoli felhasználói hozzáférés-vezérlést letiltó beállításkulcs manuális hozzáadása:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Új hozzáadása `DWORD` : `LocalAccountTokenFilterPolicy`
  * Érték beállítása a következőre `1`

* A beállításkulcs hozzáadásához a parancssorból futtassa a következő parancsot:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Linux esetén (**95108**-es hiba) a mobilitási szolgáltatás ügynökének sikeres telepítéséhez ki kell választania a **legfelső szintű** fiókot. Emellett az SSH File Transfer Protocol (SFTP) szolgáltatásoknak is futniuk kell. Az SFTP alrendszer és a jelszó hitelesítésének engedélyezése a _sshd_config_ fájlban:

1. Jelentkezzen be **gyökér** szintű felhasználóként.
1. Nyissa meg a _/etc/ssh/sshd_config fájlt_, és keresse meg a kezdetű sort `PasswordAuthentication` .
1. Állítsa vissza a sort, és módosítsa az értéket `yes` .
1. Keresse meg a kezdetű sort `Subsystem` , és a sor megjegyzését.
1. Indítsa újra a `sshd` szolgáltatást.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse az [alábbi utasításokat](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Nem megfelelő jogosultságok hiánya (ErrorID: 95517)

Ha a mobilitási ügynök telepítésére kiválasztott felhasználó nem rendelkezik rendszergazdai jogosultságokkal, a konfigurációs kiszolgáló/kibővített folyamat kiszolgálója nem tudja átmásolni a mobilitási ügynök szoftverét a forrásoldali gépre. Ez a hiba egy hozzáférés-megtagadási hiba miatt meghiúsult. Győződjön meg arról, hogy a felhasználói fiók rendszergazdai jogosultságokkal rendelkezik.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse az [alábbi utasításokat](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Nem megfelelő jogosultságok hiánya (ErrorID: 95518)

Ha a tartományi megbízhatósági kapcsolat létrehozása az elsődleges tartomány és a munkaállomás között meghiúsul, miközben megpróbál bejelentkezni a forrásoldali gépre, a mobilitási ügynök telepítése a 95518-es AZONOSÍTÓJÚ hiba miatt meghiúsul. Győződjön meg arról, hogy a mobilitási ügynök telepítéséhez használt felhasználói fiók rendszergazdai jogosultságokkal rendelkezik a forrásszámítógép elsődleges tartományán keresztül történő bejelentkezéshez.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse az [alábbi utasításokat](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Bejelentkezési hibák (ErrorID: 95519, 95520, 95521, 95522)

Ez a szakasz a hitelesítő adatokkal és a bejelentkezési hibákkal kapcsolatos üzeneteket ismerteti.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>A felhasználói fiók hitelesítő adatai le lettek tiltva (ErrorID: 95519)

A replikáció engedélyezése során kiválasztott felhasználói fiók le lett tiltva. A felhasználói fiók engedélyezéséhez tekintse meg [ezt a cikket](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) , vagy futtassa a következő parancsot a szöveges _Felhasználónév_ a tényleges felhasználónévvel való lecserélésével.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>A hitelesítő adatok több sikertelen bejelentkezési kísérlet miatt ki vannak zárva (ErrorID: 95520)

A számítógép elérésére irányuló sikertelen próbálkozások többször is zárolják a felhasználói fiókot. A hiba oka a következő lehet:

* A konfiguráció beállítása során megadott hitelesítő adatok helytelenek.
* A replikáció engedélyezése során kiválasztott felhasználói fiók nem megfelelő.

Módosítsa az [utasítások](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) követésével kiválasztott hitelesítő adatokat, majd próbálja megismételni a műveletet.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>A bejelentkezési kiszolgálók nem érhetők el a forrásoldali gépen (ErrorID: 95521)

Ez a hiba akkor fordul elő, ha a bejelentkezési kiszolgálók nem érhetők el a forrásoldali gépen. Ha a bejelentkezési kiszolgálók nem érhetők el, a bejelentkezési kérések sikertelenek lesznek, és a mobilitási ügynök nem telepíthető. Sikeres bejelentkezés esetén győződjön meg arról, hogy a bejelentkezési kiszolgálók elérhetők a forrásoldali gépen, és indítsa el a Netlogon szolgáltatást. További információ: [Windows bejelentkezési forgatókönyvek](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>A bejelentkezési szolgáltatás nem fut a forrásoldali gépen (ErrorID: 95522)

A bejelentkezési szolgáltatás nem fut a forrásoldali gépen, és a bejelentkezési kérelem meghibásodását okozta. Nem lehet telepíteni a mobilitási ügynököt. A hiba elhárításához használja az alábbi módszerek egyikét a szolgáltatás elindításához `Netlogon` a forrásoldali gépen:

* A szolgáltatás parancssorból történő elindításához `Netlogon` futtassa a parancsot `net start Netlogon` .
* Indítsa el a szolgáltatást a Feladatkezelő segédprogramból `Netlogon` .

## <a name="connectivity-failure-errorid-95117--97118"></a>Csatlakozási hiba (ErrorID: 95117 & 97118)

A konfigurációs kiszolgáló/kibővített folyamat kiszolgálója megpróbál csatlakozni a forrás virtuális géphez a mobilitási ügynök telepítéséhez. Ez a hiba akkor fordul elő, ha a forrásszámítógép nem érhető el, mert hálózati kapcsolati problémák léptek fel.

A hiba elhárítása:

* Ellenőrizze, hogy a felhasználói fiók rendelkezik-e rendszergazdai hozzáféréssel a forrásoldali számítógépen, helyi fiókkal vagy tartományi fiókkal. Ha nem használ tartományi fiókot, le kell tiltania a távoli felhasználói hozzáférés-vezérlést a helyi számítógépen.
  * Távoli felhasználói hozzáférés-vezérlést letiltó beállításkulcs manuális hozzáadása:
    * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
    * Új hozzáadása `DWORD` : `LocalAccountTokenFilterPolicy`
    * Érték beállítása a következőre `1`
  * A beállításkulcs hozzáadásához a parancssorból futtassa a következő parancsot:

    `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

* Győződjön meg arról, hogy a forrásszámítógép pingelése a konfigurációs kiszolgálóról végezhető el. Ha a replikálás engedélyezése során kiválasztotta a kibővíthető folyamat kiszolgálóját, győződjön meg arról, hogy a forráskiszolgáló pingelése megtörtént a folyamat-kiszolgálóról.

* Győződjön meg arról, hogy a fájl-és nyomtatómegosztási szolgáltatás engedélyezve van a virtuális gépen. Tekintse át a lépéseket [itt](vmware-azure-troubleshoot-push-install.md#file-and-printer-sharing-services-check-errorid-95105--95106).

* Győződjön meg arról, hogy a WMI-szolgáltatás engedélyezve van a virtuális gépen. Tekintse át a lépéseket [itt](vmware-azure-troubleshoot-push-install.md#windows-management-instrumentation-wmi-configuration-check-error-code-95103).

* Győződjön meg arról, hogy a virtuális gépen lévő megosztott hálózati mappák elérhetők a folyamat-kiszolgálóról. Tekintse át a lépéseket [itt](vmware-azure-troubleshoot-push-install.md#check-access-for-network-shared-folders-on-source-machine-errorid-9510595523).

* A forrás-kiszolgáló számítógép parancssorában a használatával `Telnet` pingelheti a konfigurációs kiszolgálót vagy a kibővíthető folyamat-kiszolgálót a 135-es HTTPS-porton az alábbi parancsban látható módon. Ez a parancs ellenőrzi, hogy van-e hálózati kapcsolattal kapcsolatos probléma, vagy a tűzfal portja blokkolja a hibákat.

  `telnet <CS/ scale-out PS IP address> <135>`

* Emellett Linux rendszerű virtuális gépek esetén:
  * Ellenőrizze, hogy a legújabb OpenSSH, OpenSSH Server és OpenSSL csomagok telepítve vannak-e.
  * Győződjön meg arról, hogy a Secure Shell (SSH) engedélyezve van, és a 22-es porton fut.
  * Az SFTP-szolgáltatásoknak futniuk kell. Az SFTP alrendszer és a jelszó-hitelesítés engedélyezése a _sshd_config_ fájlban:

    1. Jelentkezzen be **gyökér** szintű felhasználóként.
    1. Nyissa meg a _/etc/ssh/sshd_config_ fájlt, és keresse meg a kezdetű sort `PasswordAuthentication` .
    1. Állítsa vissza a sort, és módosítsa az értéket `yes` .
    1. Keresse meg a kezdetű sort `Subsystem` , és a sor megjegyzését.
    1. Indítsa újra a `sshd` szolgáltatást.

* Sikertelen volt egy kapcsolódási kísérlet, ha nincs megfelelő válasz egy adott időtartam után, vagy egy létesített kapcsolat nem sikerült, mert egy csatlakoztatott gazdagép nem válaszolt.
* Lehet, hogy kapcsolat/hálózat/tartományhoz kapcsolódó probléma van. Az is előfordulhat, hogy a DNS-név megoldja a problémát vagy a TCP-port kimerülési problémáját. Ellenőrizze, hogy vannak-e ilyen ismert problémák a tartományban.

## <a name="connectivity-failure-errorid-95523"></a>Csatlakozási hiba (ErrorID: 95523)

Ez a hiba akkor fordul elő, ha a forrást futtató hálózat nem található, lehet, hogy törölték, vagy már nem érhető el. A hiba elhárításának egyetlen módja a hálózat létezésének biztosítása.

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>Hálózati megosztott mappák hozzáférésének engedélyezése a forrásoldali gépen (ErrorID: 95105, 95523)

Ellenőrizze, hogy a virtuális gépen lévő hálózati megosztott mappák elérhetők-e a Process Server (PS) szolgáltatásból a megadott hitelesítő adatok használatával távolról. A hozzáférés megerősítése: 

1. Jelentkezzen be a Process Server-gépre.
2. Nyissa meg a Fájlkezelőt. A címsorba írja be a értéket, `\\<SOURCE-MACHINE-IP>\C$` majd kattintson az ENTER (bevitel) gombra.

    ![Mappa megnyitása a PS-ben](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. A fájlkezelő kérni fogja a hitelesítő adatokat. Adja meg a felhasználónevet és a jelszót, majd kattintson az OK gombra. <br><br/>

    ![Hitelesítő adatok megadása](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > Ha a forrásoldali számítógép tartományhoz csatlakozik, adja meg a tartománynevet a felhasználónévvel együtt `<domainName>\<username>` . Ha a forrásszámítógép munkahelyi csoportban van, csak a felhasználónevet adja meg.

4. Ha a kapcsolatok sikeresek, a forrásoldali számítógép mappái távolról láthatók lesznek a Process Serverről.

    ![A forrásoldali gépről látható mappák](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

Ha a csatlakozás sikertelen, ellenőrizze, hogy teljesülnek-e az előfeltételek.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Fájl-és nyomtatómegosztás-ellenőrzési szolgáltatások (ErrorID: 95105 & 95106)

A kapcsolat ellenőrzése után ellenőrizze, hogy a fájl-és nyomtatómegosztási szolgáltatás engedélyezve van-e a virtuális gépen. Ezek a beállítások szükségesek a mobilitási ügynök a forrásoldali gépre való másolásához.

**Windows 2008 R2 és korábbi verziók** esetén:

* A fájl-és nyomtatómegosztás Windows tűzfalon való engedélyezéséhez
  1. Nyissa meg a **Vezérlőpult**  >  **rendszer és biztonság**  >  **Windows tűzfal** felületét. A bal oldali panelen válassza a **Speciális beállítások**  >  **Bejövő szabályok** elemet a konzolfán.
  1. Keresse meg a szabályok fájl-és nyomtatómegosztás (NetBIOS-munkamenet) és a fájl-és nyomtatómegosztás (SMB, bejövő) szakaszt.
  1. Mindegyik szabályhoz kattintson a jobb gombbal a szabályra, majd kattintson a **szabály engedélyezése** parancsra.

* Fájlmegosztás engedélyezése Csoportházirend használatával:
  1. Kattintson a **Start** gombra, írja be a parancsot, `gpmc.msc` és keresse meg.
  1. A navigációs ablaktáblán nyissa meg a következő mappákat: **helyi számítógép-házirend**  >  **felhasználói konfiguráció**  >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **hálózati megosztása**.
  1. A részleteket tartalmazó ablaktáblán kattintson duplán a **felhasználók a profilban lévő fájlok megosztásának megakadályozása** lehetőségre.

     Ha le szeretné tiltani a Csoportházirend beállítást, és engedélyezni szeretné a felhasználók számára a fájlok megosztását, válassza a **Letiltva** lehetőséget.

  1. A módosítások mentéséhez kattintson az **OK** gombra.

  További információ: fájlmegosztás [engedélyezése vagy letiltása Csoportházirend](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))használatával.

A Windows vagy Linux későbbi verzióiban a fájl-és nyomtatómegosztás engedélyezéséhez kövesse a [mobilitási szolgáltatás telepítése a VMWare virtuális gépek és fizikai kiszolgálók vész-helyreállításához](vmware-azure-install-mobility-service.md) című témakör utasításait.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) konfigurációs vizsgálat (hibakód: 95103)

A fájl-és nyomtató-szolgáltatások ellenőrzését követően engedélyezze a WMI szolgáltatást a tűzfalon keresztül a privát, nyilvános és tartományi profilokhoz. Ezek a beállítások a forrásszámítógép távoli végrehajtásának befejezéséhez szükségesek.

A WMI engedélyezése:

1. Lépjen a **Vezérlőpult**  >  **Biztonság** elemre, és válassza a **Windows tűzfal** lehetőséget.
1. Válassza a **beállítások módosítása** lehetőséget, majd válassza a **kivételek** lapot.
1. A **kivételek** ablakban jelölje be Windows Management Instrumentation (WMI) jelölőnégyzetét a WMI-forgalom tűzfalon keresztüli engedélyezéséhez.

A WMI-forgalmat a tűzfalon keresztül is engedélyezheti a parancssorból a következő paranccsal:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Más WMI-hibaelhárítási cikkek a következő cikkekben találhatók.

* [Alapszintű WMI-tesztelés](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [WMI-hibaelhárítás](/windows/win32/wmisdk/wmi-troubleshooting)
* [A WMI-parancsfájlokkal és a WMI-szolgáltatásokkal kapcsolatos problémák elhárítása](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Nem támogatott operációs rendszerek

Egy másik gyakori ok, hogy a hiba oka egy nem támogatott operációs rendszer. A mobilitási szolgáltatás sikeres telepítéséhez használja a támogatott operációs rendszer és kernel verzióját. Kerülje a privát javítások használatát.

A Azure Site Recovery által támogatott operációs rendszerek és kernel-verziók listájának megtekintéséhez tekintse meg a [támogatási mátrix dokumentumát](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nem támogatott rendszerindító lemez-konfigurációk (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>A rendszerindító és a rendszerpartíciók/kötetek nem azonos a lemezen (ErrorID: 95309)

Az 9,20-es verzió előtt a rendszerindító és a rendszerpartíciók/kötetek a különböző lemezeken nem támogatottak. A 9,20-es [verziótól](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)kezdve ez a konfiguráció támogatott.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>A rendszerindító lemez nem érhető el (ErrorID: 95310)

Rendszerindító lemezzel nem rendelkező virtuális gép nem védhető. A rendszerindító lemez biztosítja a virtuális gép zökkenőmentes helyreállítását egy feladatátvételi művelet során. A rendszerindító lemez hiánya miatt a gép nem indítható el a feladatátvételt követően. Győződjön meg arról, hogy a virtuális gép tartalmaz egy rendszerindító lemezt, majd próbálja megismételni a műveletet. Emellett ugyanazon a gépen több rendszerindító lemez sem támogatott.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Több rendszerindító lemez található a forrásoldali gépen (ErrorID: 95311)

Egy több rendszerindító lemezzel rendelkező virtuális gép nem [támogatott konfiguráció](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Rendszerpartíció több lemezen (ErrorID: 95313)

Az 9,20-es verzió előtt a több lemezen lévő gyökérszintű partíció vagy kötet beállítása nem támogatott konfiguráció. A 9,20-es [verziótól](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)kezdve ez a konfiguráció támogatott.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>A védelem engedélyezése nem sikerült, mert a GRUB-konfigurációban az UUID helyett az eszköznév szerepel (ErrorID: 95320)

### <a name="possible-cause"></a>Lehetséges ok

A Grand Unified bootloader (GRUB) konfigurációs fájljai (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/GRUB2/grub.cfg_ vagy _/etc/default/grub_) tartalmazhatják a paraméterek **gyökerének** értékét **, és a** tényleges eszköznév helyett az univerzálisan egyedi azonosító (UUID) helyett a tényleges eszközök nevét is felhasználhatják. Site Recovery megbízza az UUID megközelítést, mivel az eszközök nevei megváltozhatnak a virtuális gép újraindításakor. Előfordulhat például, hogy a virtuális gép nem fog ugyanazzal a névvel online állapotba jutni a feladatátvétel során, és problémákba ütközik.

Például:

- A következő sor a GRUB-fájl _/boot/GRUB2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- A következő sor a GRUB-fájl _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> A **grub-sorok** a paraméterek **gyökeréhez** tartozó tényleges eszközök nevét tartalmazzák, és nem az UUID-t.

### <a name="how-to-fix"></a>A javítás módja

Az eszközök nevét a megfelelő UUID-azonosítókra kell cserélni.

1. A parancs végrehajtásával keresse meg az eszköz UUID-azonosítóját `blkid \<device name>` .

   Például:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Most cserélje le az eszköz nevét az UUID formátumára, például: `root=UUID=\<UUID>` . Ha például az _/boot/GRUB2/grub.cfg_, a _/boot/GRUB2/grub.cfg_ vagy a _/etc/default/grub_ fájlban szereplő, a root és a resume paraméterhez tartozó, az UUID értékkel rendelkező eszközök nevét cseréli le, akkor a fájlok sorai a következő sorba hasonlítanak:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Indítsa újra a védelmet.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>A mobilitási szolgáltatás telepítése figyelmeztetéssel fejeződött be (ErrorID: 95265 & 95266)

Site Recovery mobilitási szolgáltatásnak számos összetevője van, amelyek közül az egyik neve szűrő-illesztőprogram. A szűrő-illesztőprogram csak a rendszer újraindításakor töltődik be a rendszermemóriába. Az illesztőprogram-javítások szűrése csak akkor valósítható meg, ha a rendszer újraindításakor új szűrő-illesztőprogram van betöltve.

> [!IMPORTANT]
> Ez egy figyelmeztetés, és a meglévő replikáció az új ügynök frissítése után is működni fog. Dönthet úgy, hogy bármikor újraindítja az új szűrő-illesztőprogram előnyeit, de ha nem indul újra, a régi szűrő-illesztőprogram továbbra is működni fog. Tehát a frissítés újraindítása után a szűrő-illesztőprogram kivételével a **mobilitási szolgáltatás további fejlesztései és javításai is elérhetők**. Bár ajánlott, a frissítés után nem kötelező újraindulni. További információ arról, hogy mikor szükséges újraindítást igényelni, ha a Azure Site Recovery a Service Updates szolgáltatásban a [mobilitási szolgáltatás verziófrissítése](service-updates-how-to.md#reboot-after-mobility-service-upgrade) című szakaszt kell beállítania.

> [!TIP]
>A karbantartási időszakon belüli frissítések ütemezésével kapcsolatos ajánlott eljárásokért tekintse meg a [legújabb operációs rendszer/kernel támogatása](service-updates-how-to.md#support-for-latest-operating-systemskernels) című témakört Azure site Recovery.

## <a name="lvm-support-from-920-version"></a>Az LVM 9,20-es verziójának támogatása

Az 9,20-es verzió előtt a logikai kötetek kezelője (LVM) csak adatlemezek esetében támogatott. A `/boot` partíciónak lemezpartíció és nem LVM kötetnek kell lennie.

Az 9,20-es [verziótól](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)kezdve az [operációsrendszer-lemez az LVM számítógépen](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) támogatott.

## <a name="insufficient-space-errorid-95524"></a>Nincs elég hely (ErrorID: 95524)

Ha a mobilitási ügynököt a forrásoldali gépre másolja, legalább 100 MB szabad terület szükséges. Győződjön meg arról, hogy a forrásszámítógép rendelkezik a szükséges szabad területtel, majd próbálja megismételni a műveletet.

## <a name="low-system-resources"></a>Alacsony rendszererőforrások

A probléma lehetséges hibáinak azonosítói a következők: 95572 és 95573. Ez a probléma akkor fordul elő, ha a rendszer alacsony rendelkezésre állású memóriával rendelkezik, és nem tud memóriát lefoglalni a mobilitási szolgáltatás telepítéséhez. Győződjön meg arról, hogy a telepítés folytatásához és sikeres befejezéséhez elegendő memória van kiszabadítva.

## <a name="vss-installation-failures"></a>VSS-telepítési hibák

A Kötet árnyékmásolata szolgáltatás (VSS) telepítése a mobilitási ügynök telepítésének részét képezi. Ez a szolgáltatás az alkalmazások konzisztens helyreállítási pontjainak létrehozásához használatos. A VSS telepítése során fellépő hibák több okból is előfordulhatnak. A pontos hibák azonosításához tekintse meg a _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. A gyakori hibák és a megoldás lépései a következő szakaszban vannak kiemelve.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-hiba-2147023170 [0x800706BE]-kilépési kód: 511

Ez a probléma leggyakrabban akkor látható, ha a víruskereső szoftver blokkolja a Azure Site Recovery szolgáltatások műveleteit.

A probléma megoldása:

1. Tekintse át a [mappák kizárásának listáját a víruskereső programból](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Kövesse a víruskereső-szolgáltató által közzétett irányelveket a DLL-fájlok Windows rendszerben való regisztrálásának feloldásához.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-hiba 7 [0x7] – 511-es kilépési kód

Ez a hiba olyan futásidejű hiba, amelyet a rendszer azért okozott, mert nincs elég memória a VSS telepítéséhez. A művelet sikeres befejezéséhez növelje a lemezterületet.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS-hiba-2147023824 [0x80070430]-kilépési kód: 517

Ez a hiba akkor fordul elő, ha Azure Site Recovery VSS-szolgáltató szolgáltatás [törlésre van megjelölve](/previous-versions/ms838153(v=msdn.10)). Próbálja meg manuálisan telepíteni a VSS-t a forrásoldali gépen a következő parancs futtatásával:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-hiba-2147023841 [0x8007041F]-kilépési kód: 512

Ez a hiba akkor fordul elő, ha Azure Site Recovery VSS-szolgáltatói szolgáltatás adatbázisa [zárolva](/previous-versions/ms833798(v=msdn.10))van. Próbálja meg manuálisan telepíteni a VSS-t a forrásoldali gépen a következő parancs parancssorból történő futtatásával:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Ha hiba lép fel, ellenőrizze, hogy van-e egy víruskereső program vagy más szolgáltatás **indítási** állapotban. A **kezdő** állapotú folyamat megtarthatja a zárolást az adatbázis-szolgáltatásokban. A VSS-szolgáltató telepítése során fellépő hibákhoz vezet. Győződjön meg arról, hogy egy szolgáltatás nem **kezdő** állapotban van, majd próbálja megismételni a fenti műveletet.

### <a name="vss-exit-code-806"></a>VSS kilépési kód 806

Ez a hiba akkor fordul elő, ha a telepítéshez használt felhasználói fiók nem rendelkezik a parancs végrehajtásához szükséges engedélyekkel `CSScript` . Adja meg a szükséges engedélyeket a felhasználói fióknak a parancsfájl végrehajtásához, majd próbálja megismételni a műveletet.

### <a name="other-vss-errors"></a>Egyéb VSS-hibák

Próbálja meg manuálisan telepíteni a VSS-szolgáltató szolgáltatást a forrásszámítógépen a következő parancs parancssorból történő futtatásával:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS-hiba – 0x8004E00F

Ez a hiba általában a mobilitási ügynök telepítése során fordul elő, `DCOM` és `DCOM` kritikus állapotban van.

A hiba okának megállapításához kövesse az alábbi eljárást.

### <a name="examine-the-installation-logs"></a>A telepítési naplók vizsgálata

1. Nyissa meg a _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_ címen található telepítési naplót.
2. A probléma a következő hiba jelenlétét jelzi:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

A hiba megoldása érdekében:

Forduljon a [Microsoft Windows platform csapatához](https://aka.ms/Windows_Support) , és kérjen segítséget a DCOM-probléma megoldásához.

Ha a DCOM-probléma megoldódott, telepítse újra a Azure Site Recovery VSS-szolgáltatót manuálisan a következő paranccsal a parancssorból:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Ha az alkalmazás konzisztenciája nem kritikus a vész-helyreállítási követelmények esetében, megkerülheti a VSS-szolgáltató telepítését.

A Azure Site Recovery VSS-szolgáltató telepítésének mellőzése és a Azure Site Recovery VSS-szolgáltató manuális telepítése a telepítés után:

1. Telepítse a mobilitási szolgáltatást. A telepítés a következő lépésben sikertelen lesz: a **Telepítés utáni konfiguráció**.
1. A VSS-telepítés megkerülése:
   1. Nyissa meg a Azure Site Recovery mobilitási szolgáltatás telepítési könyvtárát a következő helyen:

      _C:\Program Files (x86) \Microsoft Azure site Recovery\agent_

   1. Módosítsa a Azure Site Recovery VSS-szolgáltató telepítési parancsfájljait _InMageVSSProvider_Install_ és a _InMageVSSProvider_Uninstall. cmd_ fájlt, hogy mindig sikeres legyen a következő sorok hozzáadásával:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Manuálisan kell telepíteni a mobilitási ügynököt.
1. Ha a telepítés sikeres, és a következő lépésre lép, **konfigurálja** a hozzáadott sorokat.
1. A VSS-szolgáltató telepítéséhez nyisson meg egy parancssort rendszergazdaként, és futtassa a következő parancsot:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Ellenőrizze, hogy a Azure Site Recovery VSS-szolgáltató szolgáltatásként van-e telepítve a Windows-szolgáltatásokban. Nyissa meg a Komponensszolgáltatások MMC-t, és ellenőrizze, hogy szerepel-e a VSS-szolgáltató.
1. Ha a VSS-szolgáltató telepítése továbbra is meghiúsul, a technikai támogatási szolgálattal elháríthatja az engedélyek hibáit a titkosítási alkalmazásprogramozási felületen (CAPI2).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>A VSS-szolgáltató telepítése sikertelen, mert a Fürtszolgáltatás nem fürtözött gépen van engedélyezve.

Ez a probléma azt eredményezi, hogy a Azure Site Recovery mobilitási ügynök telepítése meghiúsul a Azure Site Recovery VSS-szolgáltató telepítése során. A hiba oka az, hogy a VSS- `COM+` szolgáltató telepítését megakadályozó probléma merül fel.

### <a name="to-identify-the-issue"></a>A probléma azonosítása

A (z) _C:\ProgramData\ASRSetupLogs\UploadedLogs \<date-time> UA_InstallLogFile. log_ nevű konfigurációs kiszolgálón található naplóban a következő kivétel jelenik meg:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

A hiba megoldása érdekében:

1. Győződjön meg arról, hogy a gép nem fürtözött számítógép, és hogy a fürt összetevői nincsenek használatban.
1. Ha az összetevők nincsenek használatban, távolítsa el a fürt összetevőit a gépről.

## <a name="drivers-are-missing-on-the-source-server"></a>Hiányoznak illesztőprogramok a forráskiszolgálón

Ha a mobilitási ügynök telepítése nem sikerül, vizsgálja meg a _C:\ProgramData\ASRSetupLogs_ területen található naplókat annak megállapításához, hogy egyes vezérlők hiányoznak-e bizonyos szükséges illesztőprogramok.

A hiba megoldása érdekében:

1. Egy Rendszerleíróadatbázis-szerkesztővel `regedit.msc` , például a paranccsal nyissa meg a beállításjegyzéket.
1. Nyissa meg a `HKEY_LOCAL_MACHINE\SYSTEM` csomópontot.
1. A `SYSTEM` csomóponton keresse meg a vezérlőelem-készleteket.
1. Nyissa meg az egyes vezérlőelem-készleteket, és ellenőrizze, hogy a következő Windows-illesztőprogramok találhatók-e:

   * ATAPI
   * VMBus
   * Storflt
   * Storvsc
   * Intelide

1. Telepítse újra a hiányzó illesztőprogramokat.

## <a name="next-steps"></a>Következő lépések

[További](vmware-azure-tutorial.md) információ a VMWare virtuális gépek vész-helyreállításának beállításáról.
