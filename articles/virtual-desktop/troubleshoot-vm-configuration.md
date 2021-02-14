---
title: Windows rendszerű virtuális asztali munkamenetgazda – Azure
description: A Windows rendszerű virtuális asztali munkamenetgazda virtuális gépek konfigurálásakor felmerülő problémák megoldása.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f1f2caa194972bb870dc58cfa0bd6ccb2f8db053
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099914"
---
# <a name="session-host-virtual-machine-configuration"></a>Munkamenetgazda virtuális gép konfigurációja

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/troubleshoot-vm-configuration-2019.md).

Ez a cikk a Windows rendszerű virtuális asztali munkamenetgazda virtuális gépek (VM-EK) konfigurálásakor felmerülő problémák elhárítására használható.

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , és beszéljen a Windows Virtual Desktop szolgáltatásról a termék csapatával és az aktív közösség tagjaival.

## <a name="vms-are-not-joined-to-the-domain"></a>A virtuális gépek nem csatlakoznak a tartományhoz

Kövesse ezeket az utasításokat, ha problémákat tapasztal a virtuális gépek tartományhoz való csatlakoztatásakor.

- Csatlakoztassa manuálisan a virtuális gépet a [Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](../active-directory-domain-services/join-windows-vm.md) vagy a [tartományhoz való csatlakozás sablonjának](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)használatával.
- Próbálja pingelni a tartománynevet egy parancssorból a virtuális gépen.
- Tekintse át a tartományhoz való [Csatlakozás hibaüzeneteit a következő témakörben](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx): tartományi csatlakozási hibaüzenetek.

### <a name="error-incorrect-credentials"></a>Hiba: helytelen hitelesítő adatok

**OK:** A hitelesítő adatoknak a Azure Resource Manager sablon felületén megjelenő javításokban való megadásának elírása történt.

**Javítás:** A megoldáshoz hajtsa végre az alábbi műveletek egyikét.

- Manuálisan adja hozzá a virtuális gépeket egy tartományhoz.
- A sablon újbóli üzembe helyezése a hitelesítő adatok megerősítése után. Lásd: [állomáslista létrehozása a PowerShell](create-host-pools-powershell.md)-lel.
- Csatlakoztassa a virtuális gépeket egy tartományhoz egy olyan sablonnal, amely egy [meglévő Windows-alapú virtuális GÉPET ad tartományhoz csatlakozik](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Hiba: a felhasználói bevitelre való várakozás időtúllépése

**OK:** A tartományhoz való csatlakozás végrehajtásához használt fiók többtényezős hitelesítéssel (MFA) rendelkezhet.

**Javítás:** A megoldáshoz hajtsa végre az alábbi műveletek egyikét.

- Ideiglenesen távolítsa el az MFA-t a fiókhoz.
- Használjon egy szolgáltatásfiókot.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Hiba: a kiépítés során használt fiók nem rendelkezik a művelet végrehajtásához szükséges engedélyekkel

**OK:** A használt fióknak nincs engedélye a virtuális gépek tartományhoz való csatlakoztatására a megfelelőség és a szabályozások miatt.

**Javítás:** A megoldáshoz hajtsa végre az alábbi műveletek egyikét.

- Olyan fiókot használjon, amely a rendszergazda csoport tagja.
- Adja meg a szükséges engedélyeket a használt fiókhoz.

### <a name="error-domain-name-doesnt-resolve"></a>Hiba: a tartománynév nem oldható fel

**1. ok:** A virtuális gépek olyan virtuális hálózaton vannak, amely nincs társítva a virtuális hálózathoz (VNET), ahol a tartomány található.

**1. javítás:** Hozzon létre VNET-társítást a virtuális gépek kiépített VNET és a tartományvezérlőt (DC) futtató VNET között. Lásd: [virtuális hálózati társ-erőforrás-kezelő létrehozása, különböző előfizetések](../virtual-network/create-peering-different-subscriptions.md).

**2. ok:** Azure Active Directory Domain Services (Azure AD DS) használata esetén a virtuális hálózat nem frissült a DNS-kiszolgáló beállításaival, hogy azok a felügyelt tartományvezérlőkre mutassanak.

**2. javítás:** Az Azure AD DSt tartalmazó virtuális hálózat DNS-beállításainak frissítéséhez tekintse meg [Az Azure-beli virtuális hálózat DNS-beállításainak frissítése](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)című témakört.

**3. ok:** A hálózati adapter DNS-kiszolgálójának beállításai nem a virtuális hálózaton lévő megfelelő DNS-kiszolgálóra mutatnak.

**3. javítás:** Hajtsa végre a következő műveletek egyikét a megoldáshoz a [DNS-kiszolgálók módosítása] című témakörben leírtak szerint.
- Módosítsa a hálózati adapter DNS-kiszolgálójának beállításait **Egyéni** értékre a [DNS-kiszolgálók módosítása](../virtual-network/virtual-network-network-interface.md#change-dns-servers) lépésekkel, és a virtuális hálózaton lévő DNS-kiszolgálók magánhálózati IP-címeinek megadásával.
- Módosítsa a hálózati adapter DNS-kiszolgálójának beállításait úgy, hogy azok a **virtuális hálózatról öröklik** a [DNS-kiszolgálók módosításának](../virtual-network/virtual-network-network-interface.md#change-dns-servers)lépéseit, majd módosítsa a virtuális hálózat DNS-KISZOLGÁLÓJÁNAK beállításait a [DNS-kiszolgálók módosítása](../virtual-network/manage-virtual-network.md#change-dns-servers)című témakör lépéseit követve.

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Nincs telepítve a Windows rendszerű virtuális asztali ügynök és a Windows rendszerű virtuális asztali rendszerindító betöltő

A virtuális gépek üzembe helyezésének ajánlott módja a Azure Portal létrehozási sablon használata. A sablon automatikusan telepíti a Windows rendszerű virtuális asztali ügynököt és a Windows virtuális asztali ügynök rendszerindítási betöltőjét.

Kövesse ezeket az utasításokat az összetevők telepítésének megerősítéséhez és a hibaüzenetek ellenőrzéséhez.

1. Ellenőrizze, hogy a két összetevő telepítve van-e a **Vezérlőpult**  >  **programok**  >  **programok és szolgáltatások** elemének ellenőrzésével. Ha a **Windows rendszerű virtuális asztali ügynök** és a **Windows rendszerű virtuális asztali ügynök rendszerindító betöltőprogramja** nem látható, akkor azok nincsenek telepítve a virtuális gépen.
2. Nyissa meg a **fájlkezelőt** , és navigáljon a **C:\Windows\Temp\ScriptLog.log**. Ha a fájl hiányzik, az azt jelzi, hogy a két összetevőt telepítő PowerShell DSC nem tudott futni a megadott biztonsági környezetben.
3. Ha a fájl **C:\Windows\Temp\ScriptLog.log** van, nyissa meg, és ellenőrizze a hibaüzeneteket.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Hiba: hiányzik a Windows virtuális asztali ügynök és a Windows rendszerű virtuális asztali ügynök rendszerindítási betöltője. A C:\Windows\Temp\ScriptLog.log is hiányzik

**1. ok:** A Azure Resource Manager sablon bemenete során megadott hitelesítő adatok helytelenek voltak, vagy az engedélyek elégtelenek voltak.

**1. javítás:** Manuálisan adja hozzá a hiányzó összetevőket a virtuális gépekhez a [gazdagép létrehozása a PowerShell](create-host-pools-powershell.md)-lel használatával.

**2. ok:** A PowerShell DSC elindítása és végrehajtása sikerült, de nem fejeződött be, mert nem tud bejelentkezni a Windows rendszerű virtuális asztalra, és beszerezni a szükséges információkat.

**2. javítás:** Erősítse meg az alábbi listán szereplő elemeket.

- Győződjön meg arról, hogy a fiók nem rendelkezik MFA-val.
- Ellenőrizze, hogy a gazdagép-készlet neve pontos-e, és létezik-e a Windows rendszerű virtuális asztalon a gazdagép.
- Erősítse meg, hogy a fiók legalább közreműködői engedélyekkel rendelkezik az Azure-előfizetésben vagy az erőforrás-csoportban.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Hiba: a hitelesítés nem sikerült, hiba a C:\Windows\Temp\ScriptLog.log

**OK:** A PowerShell DSC végrehajtása sikerült, de nem tudott csatlakozni a Windows rendszerű virtuális asztalhoz.

**Javítás:** Erősítse meg az alábbi listán szereplő elemeket.

- Manuálisan regisztrálja a virtuális gépeket a Windows Virtual Desktop szolgáltatással.
- A Windows rendszerű virtuális asztalhoz való csatlakozáshoz használt fiók megerősítése engedélyekkel rendelkezik az Azure-előfizetésben vagy az erőforráscsoporthoz a gazdagépek létrehozásához.
- A fiók megerősítése nem rendelkezik MFA-val.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>A Windows rendszerű virtuális asztali ügynök nem regisztrálja magát a Windows rendszerű virtuális asztali szolgáltatással

Ha a Windows rendszerű virtuális asztali ügynök először van telepítve a munkamenet-gazdagép virtuális gépei számára (manuálisan vagy a Azure Resource Manager sablonon és a PowerShell DSC-n keresztül), akkor egy regisztrációs jogkivonatot biztosít. A következő szakasz a Windows rendszerű virtuális asztali ügynökre és a jogkivonatra vonatkozó hibaelhárítási problémákat ismerteti.

### <a name="error-the-status-filed-in-get-azwvdsessionhost-cmdlet-shows-status-as-unavailable"></a>Hiba: Get-AzWvdSessionHost parancsmagban bejelentkezett állapot nem elérhető állapotot jelenít meg.

> [!div class="mx-imgBorder"]
> ![A Get-AzWvdSessionHost parancsmag a nem elérhető állapotot jeleníti meg.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**OK:** Az ügynök nem tudja frissíteni magát egy új verzióra.

**Javítás:** Az ügynök manuális frissítéséhez kövesse az alábbi utasításokat.

1. Töltse le az ügynök új verzióját a munkamenet-gazda virtuális gépre.
2. Indítsa el a Feladatkezelő eszközt, és a szolgáltatás lapon állítsa le a RDAgentBootLoader szolgáltatást.
3. Futtassa a telepítőt a Windows rendszerű virtuális asztali ügynök új verziójához.
4. Ha a rendszer kéri a regisztrációs tokent, távolítsa el a bejegyzést INVALID_TOKEN és nyomja meg a Next (új token nem szükséges) elemet.
5. Fejezze be a telepítővarázsló lépéseit.
6. Nyissa meg a Feladatkezelő eszközt, és indítsa el a RDAgentBootLoader szolgáltatást.

## <a name="error-windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Hiba: a Windows rendszerű virtuális asztali ügynök beállításjegyzékbeli bejegyzése IsRegistered 0 értéket jelenít meg

**OK:** A regisztrációs jogkivonat lejárt.

**Javítás:** Az ügynök beállításjegyzékbeli hibájának kijavításához kövesse az alábbi utasításokat.

1. Ha már van regisztrációs jogkivonat, távolítsa el a Remove-AzWvdRegistrationInfo.
2. Új jogkivonat létrehozásához futtassa a **New-AzWvdRegistrationInfo** parancsmagot.
3. Győződjön meg arról, hogy a *-ExpriationTime* paraméter értéke 3 nap.

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-azwvdsessionhost"></a>Hiba: a Windows rendszerű virtuális asztali ügynök nem jelentett szívverést Get-AzWvdSessionHost futtatásakor

**1. ok:** A RDAgentBootLoader szolgáltatás le lett állítva.

**1. javítás:** Indítsa el a Feladatkezelőt, és ha a szolgáltatás lap leállított állapotot jelez a RDAgentBootLoader szolgáltatáshoz, indítsa el a szolgáltatást.

**2. ok:** Lehet, hogy a 443-es port be van zárva.

**2. javítás:** A 443-es port megnyitásához kövesse az alábbi utasításokat.

1. Győződjön meg arról, hogy a 443-es port meg van nyitva a PSPing eszköz a [Sysinternal Tools](/sysinternals/downloads/psping/)webhelyről való letöltés
2. Telepítse a PSPing a munkamenet-gazda virtuális gépre, amelyen az ügynök fut.
3. Nyissa meg a parancssort rendszergazdaként, és adja ki az alábbi parancsot:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Győződjön meg arról, hogy a PSPing adatokat kapott a RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>A Windows rendszerű virtuális asztalok egymás melletti veremével kapcsolatos hibák elhárítása

A Windows rendszerű virtuális asztali párhuzamos verem automatikusan települ a Windows Server 2019-es verzióra. A Microsoft Installer (MSI) használatával telepítse a párhuzamos stacket a Microsoft Windows Server 2016 vagy a Windows Server 2012 R2 rendszerre. A Microsoft Windows 10 rendszerben a Windows rendszerű virtuális asztali párhuzamos verem **enablesxstackrs.ps1tel** van engedélyezve.

A munkamenet-gazdagépen futó virtuális gépeken a következő három fő módszert kell telepíteni vagy engedélyezni:

- A Azure Portal létrehozási sablonnal
- A fő rendszerképbe való felvétel és engedélyezés
- Manuálisan telepítve vagy engedélyezve az egyes virtuális gépeken (vagy bővítményekkel/PowerShell-lel)

Ha problémákat tapasztal a Windowsos virtuális asztal egymás melletti veremben, írja be a **qwinsta** parancsot a parancssorból annak megerősítéséhez, hogy a párhuzamos verem telepítve vagy engedélyezve van.

A **qwinsta** kimenete az **RDP-SxS** fogja listázni a kimenetben, ha a párhuzamos verem telepítve van és engedélyezve van.

> [!div class="mx-imgBorder"]
> ![Az egymás melletti verem telepítve van vagy engedélyezve van a kimenetben RDP-SxS-ként felsorolt qwinsta.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Vizsgálja meg az alább felsorolt beállításjegyzék-bejegyzéseket, és ellenőrizze, hogy az értékek egyeznek-e. Ha a beállításkulcsok hiányoznak, vagy az értékek nem egyeznek, győződjön meg arról, hogy [támogatott operációs rendszert](troubleshoot-agent.md#error-operating-a-pro-vm-or-other-unsupported-os)futtat. Ha Ön, kövesse a [gazdagép létrehozása a PowerShell használatával](create-host-pools-powershell.md) című témakör utasításait a párhuzamos verem újratelepítéséhez.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Hiba: O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![O_REVERSE_CONNECT_STACK_FAILURE hibakód.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**OK:** A párhuzamos verem nincs telepítve a munkamenet-gazdagép virtuális gépén.

**Javítás:** Kövesse ezeket az utasításokat a párhuzamos verem telepítéséhez a munkamenet-gazda virtuális gépen.

1. A RDP protokoll (RDP) használatával közvetlenül a munkamenet-gazda virtuális gépre kerül a helyi rendszergazdaként.
2. Telepítse a párhuzamos veremet a [gazdagép létrehozása a PowerShell](create-host-pools-powershell.md)használatával című részből.

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>A Windows rendszerű virtuális asztalok egymás melletti veremének kijavítása

Vannak olyan ismert körülmények, amelyek az egymás melletti verem meghibásodását okozhatják:

- Nem követi a lépések helyes sorrendjét a párhuzamos verem engedélyezéséhez
- Automatikus frissítés a Windows 10 továbbfejlesztett sokoldalú lemezére (EVD)
- Hiányzik a Távoli asztal munkamenet-gazdagép (RDSH) szerepkör
- enablesxsstackrc.ps1 futtatása többször
- enablesxsstackrc.ps1 futtatása helyi rendszergazdai jogosultságokkal nem rendelkező fiókban

Az ebben a szakaszban található utasítások segítséget nyújtanak a Windows rendszerű virtuális asztal egymás melletti verem eltávolításához. A párhuzamos verem eltávolítása után nyissa meg a "virtuális gép regisztrálása a Windows rendszerű virtuális asztali készlettel" című részt a [gazdagép létrehozása a PowerShell](create-host-pools-powershell.md) -lel című részből a párhuzamos verem újratelepítéséhez.

A szervizelés futtatásához használt virtuális gépnek ugyanazon az alhálózaton és tartományban kell lennie, mint a virtuális gépnek, amely a meghibásodott párhuzamos verem.

Az alábbi utasításokat követve futtassa a szervizelést ugyanarról az alhálózatról és tartományról:

1. Kapcsolódjon a standard RDP protokoll (RDP) szolgáltatáshoz a virtuális géphez, ahonnan a javítást alkalmazni fogja.
2. Töltse le a PsExec a alkalmazásból https://docs.microsoft.com/sysinternals/downloads/psexec .
3. Bontsa ki a letöltött fájlt.
4. Indítsa el a parancssort helyi rendszergazdaként.
5. Navigáljon a mappához, ahol a PsExec ki lett csomagolva.
6. A parancssorban használja a következő parancsot:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >A VMname a virtuális gép számítógépneve, amely a meghibásodott párhuzamos verem.

7. Az elfogadás gombra kattintva fogadja el a PsExec-licencszerződést.

    > [!div class="mx-imgBorder"]
    > ![Szoftverlicenc-szerződés képernyőképe](media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >Ez a párbeszédpanel csak a PsExec első futtatásakor fog megjelenni.

8. Miután a parancssor-munkamenet megnyílik a virtuális gépen a meghibásodott párhuzamos verem használatával, futtassa a qwinsta, és győződjön meg arról, hogy az RDP-SxS nevű bejegyzés elérhető. Ha nem, egy párhuzamos verem nem található a virtuális gépen, így a probléma nem a párhuzamos veremhez van kötve.

    > [!div class="mx-imgBorder"]
    > ![Rendszergazdai parancssor](media/AdministratorCommandPrompt.png)

9. Futtassa a következő parancsot, amely felsorolja a virtuális gépen telepített Microsoft-összetevőket a meghibásodott párhuzamos verem használatával.

    ```cmd
        wmic product get name
    ```

10. Futtassa az alábbi parancsot a fenti lépésből származó termékek neveivel.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Távolítsa el az összes olyan terméket, amely a következővel kezdődik: "Távoli asztal."

12. A Windows rendszerű virtuális asztali összetevők eltávolítása után kövesse az operációs rendszerének utasításait:

13. Ha az operációs rendszer a Windows Server, indítsa újra azt a virtuális gépet, amely a (Azure Portal vagy a PsExec eszközből származó) hibás működésű párhuzamos veremmel rendelkezik.

Ha az operációs rendszer Microsoft Windows 10, folytassa az alábbi utasításokkal:

14. A PsExec-t futtató virtuális gépről nyissa meg a fájlkezelőt, és másolja disablesxsstackrc.ps1 a virtuális gép rendszermeghajtóján a meghibásodott párhuzamos verem használatával.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >A VMname a virtuális gép számítógépneve, amely a meghibásodott párhuzamos verem.

15. Az ajánlott folyamat: a PsExec eszközből indítsa el a PowerShellt, és navigáljon az előző lépésben található mappára, és futtassa disablesxsstackrc.ps1. Azt is megteheti, hogy a következő parancsmagokat futtatja:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Ha a parancsmagok futása befejeződött, indítsa újra a virtuális gépet a meghibásodott párhuzamos verem használatával.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>Távoli asztal licencelési mód nincs konfigurálva

Ha rendszergazdai fiókkal jelentkezik be a Windows 10-es nagyvállalati munkamenetbe, előfordulhat, hogy a "Távoli asztal licencelési mód nincs konfigurálva, Távoli asztali szolgáltatások X nap múlva nem fog működni. A Csatlakozáskezelő kiszolgálón a Kiszolgálókezelő használatával adhatja meg a Távoli asztal licencelési módot. "

Ha lejár az időkorlát, megjelenik egy hibaüzenet, amely azt jelzi, hogy "a távoli munkamenet le lett választva, mert nincs elérhető Távoli asztal ügyfél-hozzáférési licenc ehhez a számítógéphez."

Ha ezeket az üzeneteket látja, ez azt jelenti, hogy a lemezképen nincsenek telepítve a legújabb Windows-frissítések, vagy a csoportházirenden keresztül állítja be a Távoli asztal licencelési módot. A következő szakaszokban ismertetett lépéseket követve ellenőrizheti a csoportházirend-beállítást, azonosíthatja a Windows 10 Enterprise több munkamenet verzióját, és telepítheti a megfelelő frissítést.

>[!NOTE]
>A Windows rendszerű virtuális asztali számítógépeken csak a távoli asztali szolgáltatások ügyféllicencei (CAL) szükségesek, ha a gazdagép Windows Server-munkamenet-gazdagépeket tartalmaz. A RDS CAL konfigurálásának megismeréséhez tekintse meg [az RDS-telepítés licence az ügyfél-hozzáférési licencekkel](/windows-server/remote/remote-desktop-services/rds-client-access-license/)című témakört.

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>A Távoli asztal licencelési mód csoportházirend-beállításának letiltása

A csoportházirend-beállítás megadásával nyissa meg a csoportházirend-szerkesztőt a virtuális gépen, és navigáljon **Felügyeleti sablonok**  >  **Windows-összetevőkhöz**  >  **Távoli asztali szolgáltatások**  >  **Távoli asztal a munkamenet-gazdagép**  >  **licencelése**  >  **Beállítja a távoli asztal licencelési módját**. Ha a csoportházirend-beállítás **engedélyezve** van, módosítsa a szolgáltatást **Letiltva** értékre. Ha már le van tiltva, akkor hagyja a következőképpen:.

>[!NOTE]
>Ha a csoportházirendet a tartományon keresztül állítja be, tiltsa le ezt a beállítást a Windows 10 Enterprise több munkamenetet használó virtuális gépeket megcélozó házirendekben.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Annak meghatározása, hogy a Windows 10 Enterprise több munkamenet melyik verzióját használja

Ha szeretné megtekinteni, hogy a Windows 10 Enterprise több munkamenetének melyik verziója van:

1. Jelentkezzen be a rendszergazdai fiókjával.
2. Adja meg a "Névjegy" kifejezést a Start menü melletti keresési sávon.
3. Válassza **a számítógép névjegye** elemet.
4. A "version" felirat melletti szám bejelölése A számnak "1809" vagy "1903" értékűnek kell lennie, ahogy az alábbi képen is látható.

    > [!div class="mx-imgBorder"]
    > ![A Windows-specifikációk ablak képernyőképe. A verziószám kék színnel van kiemelve.](media/windows-specifications.png)

Most, hogy már ismeri a verziószámát, ugorjon a megfelelő szakaszra.

### <a name="version-1809"></a>1809-es verzió

Ha a verziószáma a "1809" értéket adja meg, telepítse [a KB4516077 frissítését](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>1903-es verzió

Telepítse újra a gazdagép operációs rendszerét a Windows 10 1903-es verziójú lemezképének legújabb verziójával az Azure Gallery-ből.

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Biztonsági hiba miatt nem sikerült csatlakozni a távoli számítógéphez

Ha a felhasználók egy biztonsági hiba miatt nem sikerült csatlakozni a távoli számítógéphez, a következő hibaüzenet jelenik meg:. Ha ez továbbra is fennáll, kérje a rendszergazda vagy a technikai támogatás segítségét, "az alapértelmezett RDP-engedélyeket módosító meglévő házirendek érvényesítése. Egy házirend, amely a hiba megjelenését okozhatja a "bejelentkezés engedélyezése Távoli asztali szolgáltatások biztonsági házirendben".

A szabályzattal kapcsolatos további információkért lásd: [bejelentkezés engedélyezése távoli asztali szolgáltatásokon](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services).

## <a name="next-steps"></a>Következő lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [környezet és az Alkalmazáskészletek létrehozása](troubleshoot-set-up-issues.md)című témakört.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A Windows rendszerű virtuális asztali ügynökkel vagy a munkamenet-kapcsolattal kapcsolatos problémák elhárításához lásd: a [Windows rendszerű virtuális asztali ügynökkel kapcsolatos gyakori problémák elhárítása](troubleshoot-agent.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali szolgáltatások kapcsolatai](troubleshoot-service-connection.md)című témakört.
- Távoli asztal-ügyfelekkel kapcsolatos problémák elhárításához tekintse meg [a távoli asztal-ügyfél hibaelhárítása](troubleshoot-client.md) című témakört.
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerű virtuális asztali környezet](environment-setup.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](../azure-resource-manager/management/view-activity-logs.md).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](../azure-resource-manager/templates/deployment-history.md).
