---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cbc7c82803115f71db233be94d62a857ba050b63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050486"
---
#### <a name="to-download-hotfixes"></a>Gyorsjavítások letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

1. Indítsa el az Internet Explorert, és navigáljon a gombra [http://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .
2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

    ![Katalógus telepítése](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. A Microsoft Update katalógus keresőmező mezőjébe írja be a letölteni kívánt gyorsjavítás (Tudásbázis) számát, például **4037264**, majd kattintson a **Keresés** gombra.
   
    Megjelenik a gyorsjavítások listája, például a **StorSimple 8000 sorozat összesített szoftvercsomag-frissítése 5,0**.
   
    ![Keresés a katalógusban](./media/storsimple-install-update5-hotfix/update-catalog-search.png)

4. Kattintson a **Letöltés** gombra. Adja meg vagy **tallózással** válassza ki a helyet, ahová a fájlokat le szeretné tölteni. Kattintson a fájlok a megadott helyre és mappára való letöltéséhez. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.
5. Keresse meg a fenti táblázatban felsorolt további gyorsjavításokat (**4037266**), majd töltse le a megfelelő fájlokat az előző táblázatban látható módon az adott mappákba.

> [!NOTE]
> A gyorsjavításoknak mindkét vezérlőből elérhetőnek kell lenniük, hogy észlelje a társ-vezérlő esetleges hibaüzeneteit.
>
> A gyorsjavításokat 3 különböző mappába kell másolni. Például az eszköz szoftver/CIS/MDS-ügynök frissítése a _FirstOrderUpdate_ mappába másolható, az összes többi nem zavaró frissítés a _SecondOrderUpdate_ mappába másolható, és a karbantartási mód frissítései a _ThirdOrderUpdate_ mappába másolhatók.

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Normál módú gyorsjavítások telepítése és ellenőrzése

A normál módú gyorsjavítások telepítéséhez és ellenőrzéséhez hajtsa végre az alábbi lépéseket. Ha már telepítette őket a Azure Portal használatával, ugorjon a [karbantartási mód gyorsjavítások telepítése és ellenőrzése](#to-install-and-verify-maintenance-mode-hotfixes)című lépésre.

1. A gyorsjavítások telepítéséhez nyissa meg a Windows PowerShell felületét a StorSimple-eszköz soros konzoljában. Kövesse [a PuTTY a soros konzolhoz való csatlakozáshoz történő használatát](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console) ismertető részletes útmutatásokat. A parancssorban nyomja le az **Enter** billentyűt.
2. Válassza az 1. lehetőséget, majd **Jelentkezzen be teljes hozzáféréssel**. Javasoljuk, hogy előbb a passzív vezérlőre telepítse a gyorsjavítást.
3. A gyorsjavítás telepítéséhez írja be a következőt a parancssorba:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    A fenti parancsban a megosztás elérési útjában DNS-cím helyett inkább IP-címet adjon meg. A hitelesítési paramétert csak akkor kell használni, ha egy hitelesített megosztáshoz kíván hozzáférni.
   
    Javasoljuk, hogy a megosztások elérése során használja a hitelesítési paramétert. Még a „mindenki” számára elérhető megosztások sem elérhetőek a nem hitelesített felhasználók számára.
   
4. Amikor a rendszer kéri, adja meg a jelszót. Alább látható egy példa az elsőrendű frissítések telepítésének kimenetére. Az első megrendelés frissítéséhez az adott fájlra kell mutatnia.

    >[!NOTE] 
    > Először telepítenie kell a _HcsSoftwareUpdate.exe_ . A telepítés befejezése után telepítse a _CisMdsAgentUpdate.exe_.
   
    ```output
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \FirstOrderUpdate\HcsSoftwareUpdate.exe -Credential contoso\John
   
    Confirm
   
    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
   
    ```
5. Írja be az **Y** karaktert, amikor a rendszer kéri, hogy erősítse meg a gyorsjavítás telepítését.
6. A frissítést a `Get-HcsUpdateStatus` parancsmag használatával figyelheti. A frissítés előbb a passzív vezérlőn lesz végrehajtva. Miután a passzív vezérlő frissítve lett, az azt követő feladatátvétel során a frissítés a másik vezérlőre is alkalmazva lesz. A frissítés akkor fejeződött be, ha mindkét vezérlő frissítve lett.
   
    Az alábbi kimeneti példa azt mutatja, hogy a frissítés még folyamatban van. Az az `RunInprogress` , `True` Amikor a frissítés folyamatban van.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 07/28/2017 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     Az alábbi kimeneti példa azt mutatja, hogy a frissítés befejeződött. Az az `RunInProgress` , `False` Amikor a frissítés befejeződött.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 07/28/2017 9:15:55 AM
    LastUpdateTimestamp : 07/28/2017 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > Alkalmanként a parancsmag `False` értéket jelenít meg, amikor a frissítés még folyamatban van. A gyorsjavítás befejezéséhez várjon néhány percet, majd futtassa újra a parancsot, és ellenőrizze, hogy a `RunInProgress` értéke `False`. Ha így van, a gyorsjavítás befejeződött.

7. A szoftverfrissítés befejeztét követően ellenőrizze a rendszerszoftverek verzióját. Típus:
   
    `Get-HcsSystem`
   
    A következő verziónak kell megjelennie:
   
   * `FriendlySoftwareVersion: StorSimple 8000 Series Update 5.0`
   * `HcsSoftwareVersion: 6.3.9600.17845`
   
     Ha a verziószám nem változik a frissítés telepítését követően, ez azt jelzi, hogy a gyorsjavítás nem lett telepítve. Ha ezt látja, további segítségért forduljon a [Microsoft támogatási szolgálatához](../articles/storsimple/storsimple-8000-contact-microsoft-support.md).
     
     > [!IMPORTANT]
     > A következő frissítés alkalmazása előtt újra kell indítania az aktív vezérlőt a `Restart-HcsController` parancsmagon keresztül.
     
8. Ismételje meg a 3-6-es lépést a _CisMDSAgentupdate.exe_ ügynöknek a _FirstOrderUpdate_ mappába való letöltésének telepítéséhez.
8. Ismételje meg a 3-6 lépést a második megrendelés frissítéseinek telepítéséhez. 

    > [!NOTE] 
    > A második megrendelési frissítések esetében több frissítés is telepíthető a futtatásával, `Start-HcsHotfix cmdlet` és arra a mappára mutat, ahol a második megrendelés frissítései találhatók. A parancsmag végrehajtja a mappában található összes frissítést. Ha valamely frissítés már telepítve lett, a frissítési logika észleli ezt, és nem alkalmazza újra az adott frissítést.

    Miután az összes gyorsjavítás telepítve lett, használja a `Get-HcsSystem` parancsmagot. A verziónak a következőknek kell lennie:
    
    * `CisAgentVersion:  1.0.9724.0`
    * `MdsAgentVersion: 35.2.2.0`
    * `Lsisas2Version: 2.0.78.00`


#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Karbantartási módú gyorsjavítások telepítése és ellenőrzése

A KB4037263 használatával telepítse a lemez belső vezérlőprogram-frissítéseit. Ezek működési zavart okozó frissítések, amelyeknek a végrehajtása körülbelül 30 percet vesz igénybe. Ha úgy dönt, ezeket egy karbantartási időszakban is telepítheti, ha csatlakozik az eszköz soros konzoljához.

> [!NOTE] 
> Ha a lemez belső vezérlőprogramja már naprakész, nem kell telepítenie ezeket a frissítéseket. Futtassa a `Get-HcsUpdateAvailability` parancsmagot az eszköz soros konzoljáról, és ellenőrizze, hogy vannak-e elérhető frissítések, és azok zavart okozó (karbantartási módú) vagy zavart nem okozó (normál módú) frissítések-e.

A lemezfirmware-frissítések telepítéséhez kövesse az alábbi utasításokat.

1. Helyezze az eszközt karbantartási módba. 

    > [!NOTE] 
    > Ne használja a Windows PowerShell távelérést, ha karbantartási módban lévő eszközhöz csatlakozik. Ehelyett futtassa az alábbi parancsmagot az eszközvezérlőn, miközben az eszköz soros konzolján keresztül csatlakozik az eszközhöz.

    A vezérlő karbantartási módba helyezéséhez írja be a következőt:
   
    `Enter-HcsMaintenanceMode`
   
    Az alábbiakban egy példa látható a kimenetre.

    ```output
    Controller0>Enter-HcsMaintenanceMode
    Checking device state...
   
    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y
   
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8600
    Name: Update4-8600-mystorsimple
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------
   
    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
    ```

    Ekkor mindkét vezérlő újraindul karbantartási módban.
2. A lemezfirmware-frissítés telepítéséhez írja be a következőt:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Az alábbiakban egy példa látható a kimenetre.

    ```output
    Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\ThirdOrderUpdates\ -Credential contoso\john
    Enter Password:
    WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
    Confirm
    This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
    [Y] Yes [N] No (Default is "Y"): Y
    WARNING: Installation is currently in progress. This operation can take several minutes to complete.
    ```

3. A telepítési folyamatot a `Get-HcsUpdateStatus` parancs használatával figyelheti. A frissítés akkor fejeződött be, ha a `RunInProgress``False` értékre vált.
4. Miután befejeződött a telepítés, a vezérlő, amelyre a karbantartási módú gyorsjavítás telepítve lett, újraindul. Jelentkezzen be 1. lehetőségként, **Jelentkezzen be a teljes hozzáféréssel**, és ellenőrizze a lemez belső vezérlőprogram-verzióját. Típus:
   
   `Get-HcsFirmwareVersion`
   
   A várt lemezfirmware-verzió a következő:
   
   `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`
   
   Az alábbiakban egy példa látható a kimenetre.

    ```output
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8600
       Name: Update4-8600-mystorsimple
       Software Version: 6.3.9600.17845
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
        ActiveBIOS:0.45.0010
           BackupBIOS:0.45.0006
           MainCPLD:17.0.000b
           ActiveBMCRoot:2.0.001F
           BackupBMCRoot:2.0.001F
           BMCBoot:2.0.0002
           LsiFirmware:20.00.04.00
           LsiBios:07.37.00.00
           Battery1Firmware:06.2C
           Battery2Firmware:06.2C
           DomFirmware:X231600
           CanisterFirmware:3.5.0.56
           CanisterBootloader:5.03
           CanisterConfigCRC:0x9134777A
           CanisterVPDStructure:0x06
           CanisterGEMCPLD:0x19
           CanisterVPDCRC:0x142F7DC2
           MidplaneVPDStructure:0x0C
           MidplaneVPDCRC:0xA6BD4F64
           MidplaneCPLD:0x10
           PCM1Firmware:1.00|1.05
           PCM1VPDStructure:0x05
           PCM1VPDCRC:0x41BEF99C
           PCM2Firmware:1.00|1.05
           PCM2VPDStructure:0x05
           PCM2VPDCRC:0x41BEF99C

        EbodFirmware
           CanisterFirmware:3.5.0.56
           CanisterBootloader:5.03
           CanisterConfigCRC:0xB23150F8
           CanisterVPDStructure:0x06
           CanisterGEMCPLD:0x14
           CanisterVPDCRC:0xBAA55828
           MidplaneVPDStructure:0x0C
           MidplaneVPDCRC:0xA6BD4F64
           MidplaneCPLD:0x10
           PCM1Firmware:3.11
           PCM1VPDStructure:0x03
           PCM1VPDCRC:0x6B58AD13
           PCM2Firmware:3.11
           PCM2VPDStructure:0x03
           PCM2VPDCRC:0x6B58AD13

        DisksFirmware
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
    ```
   
    Futtassa a `Get-HcsFirmwareVersion` parancsot a második vezérlőn annak ellenőrzéséhez, hogy a szoftververzió frissítve lett-e. Ezután kiléphet a karbantartási módból. Ehhez írja be a következő parancsot mindkét eszközvezérlő esetében:
   
   `Exit-HcsMaintenanceMode`

5. A vezérlők újraindulnak, miután kilépett a karbantartási módból. A lemez belső vezérlőprogram-frissítéseinek sikeres alkalmazása után az eszköz kilépett a karbantartási módból, térjen vissza a Azure Portal. Vegye figyelembe, hogy a portálon esetleg még 24 órán keresztül nem jelenik meg, hogy a karbantartási módú frissítések telepítve lettek.

