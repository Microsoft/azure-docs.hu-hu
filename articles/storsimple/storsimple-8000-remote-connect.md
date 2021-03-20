---
title: Távoli kapcsolódás a StorSimple-eszközhöz
description: Ismerteti, hogyan konfigurálhatja az eszközt a távoli felügyelethez, és hogyan csatlakozhat a Windows PowerShell StorSimple-bővítménye HTTP-vagy HTTPS-kapcsolaton keresztül.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1da688dfb00b26ca6b561d5aa0fb548c221381c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514579"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Távoli kapcsolódás a StorSimple 8000 Series eszközhöz

## <a name="overview"></a>Áttekintés

A Windows PowerShell használatával távolról is csatlakozhat az eszközhöz. Ha így tesz, nem jelenik meg a menü. (Csak akkor jelenik meg a menü, ha a soros konzolt használja az eszközön a kapcsolódáshoz.) A Windows PowerShell távelérési szolgáltatásával egy adott RunSpace csatlakozhat. Megadhatja a megjelenítési nyelvet is.

Ha többet szeretne megtudni arról, hogyan kezelheti az eszközét a Windows PowerShell távelérés használatával, lépjen a [Windows PowerShell StorSimple-bővítménye használatára a StorSimple-eszköz felügyeletéhez](storsimple-8000-windows-powershell-administration.md).

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja az eszközt a távoli felügyelethez, majd hogyan csatlakozhat a Windows PowerShell StorSimple-bővítményehoz. HTTP vagy HTTPS használatával távolról csatlakozhat a Windows PowerShell segítségével. Ha azonban úgy dönt, hogyan csatlakozhat Windows PowerShell StorSimple-bővítményehoz, vegye figyelembe a következő információkat:

* Az eszköz soros konzoljának közvetlen csatlakoztatása biztonságos, de a soros konzol hálózati kapcsolókon keresztül történő csatlakoztatása nem. A hálózati kapcsolókon keresztül az eszköz soros konzolhoz való csatlakozáskor legyen óvatos a biztonsági kockázat.
* A HTTP-munkameneten keresztüli csatlakozás nagyobb biztonságot nyújthat, mint a soros konzol hálózaton keresztüli csatlakoztatása. Bár ez nem a legbiztonságosabb módszer, a megbízható hálózatokon elfogadható.
* Ha egy önaláírt tanúsítvánnyal rendelkező HTTPS-munkameneten keresztül csatlakozik, a legbiztonságosabb és ajánlott lehetőség.

Távolról is csatlakozhat a Windows PowerShell felületéhez. A StorSimple-eszközhöz a Windows PowerShell felületén keresztül történő távoli hozzáférés azonban alapértelmezés szerint nincs engedélyezve. Először engedélyeznie kell a távoli felügyeletet az eszközön, majd az eszköz eléréséhez használt ügyfélen.

A cikkben ismertetett lépések a Windows Server 2012 R2 operációs rendszert futtató gazdagépeken hajthatók végre.

## <a name="connect-through-http"></a>Kapcsolat HTTP-n keresztül

A Windows PowerShell StorSimple-bővítménye HTTP-munkameneten keresztül történő csatlakoztatása nagyobb biztonságot nyújt, mint a StorSimple-eszköz soros konzolján. Bár ez nem a legbiztonságosabb módszer, a megbízható hálózatokon elfogadható.

A Távoli felügyelet konfigurálásához használhatja a Azure Portal vagy a soros konzolt is. Válasszon az alábbi eljárások közül:

* A Távoli felügyelet engedélyezése a Azure Portal használatával HTTP-n keresztül
* [A Távoli felügyelet engedélyezése HTTP-n keresztül a soros konzol használatával](#use-the-serial-console-to-enable-remote-management-over-http)

A távfelügyelet engedélyezése után a következő eljárással készítse elő az ügyfelet egy távoli kapcsolatban.

* [Az ügyfél előkészítése távoli csatlakozásra](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>A Távoli felügyelet engedélyezése a Azure Portal használatával HTTP-n keresztül

A Távoli felügyelet HTTP-n keresztüli engedélyezéséhez hajtsa végre a következő lépéseket a Azure Portalon.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Távoli felügyelet engedélyezése a Azure Portal

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Válassza az **eszközök** lehetőséget, majd válassza ki a távoli felügyelethez konfigurálni kívánt eszközt, majd kattintson rá. Lépjen az **eszközbeállítások > biztonság** menüpontra.
2. A **biztonsági beállítások** panelen kattintson a **Távoli felügyelet** elemre.
3. A **távfelügyelet** panelen állítsa a **távfelügyelet engedélyezése** **Igen értéket**.
4. Mostantól HTTP-n keresztül is csatlakozhat. (Az alapértelmezett beállítás a HTTPS-kapcsolaton keresztüli kapcsolódás.) Győződjön meg arról, hogy a HTTP van kiválasztva.
   
   > [!NOTE]
   > A HTTP-n keresztüli csatlakozást kizárólag megbízható hálózatokon célszerű engedélyezni.
   
5. Kattintson a **Mentés** gombra, és amikor a rendszer megerősítést kér, válassza az **Igen** lehetőséget.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>A Távoli felügyelet engedélyezése HTTP-n keresztül a soros konzol használatával
A Távoli felügyelet engedélyezéséhez hajtsa végre a következő lépéseket az eszköz soros konzolján.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Távoli felügyelet engedélyezése az eszköz soros konzolján
1. A soros konzol menüjében válassza az 1. lehetőséget. Az eszköz soros konzoljának használatával kapcsolatos további információkért nyissa meg [Windows PowerShell StorSimple-bővítménye eszközt a soros konzolon keresztül](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. A parancssorba írja be a következőt: `Enable-HcsRemoteManagement –AllowHttp`
3. Értesítést kap a HTTP-vel való kapcsolódás biztonsági sebezhetőségéről az eszközhöz való kapcsolódáshoz. Ha a rendszer kéri, erősítse meg az **Y** beírásával.
4. Az alábbiak beírásával ellenőrizze, hogy a HTTP engedélyezve van-e: `Get-HcsSystem`
5. Ellenőrizze, hogy a **RemoteManagementMode** mező megjeleníti-e a **HttpsAndHttpEnabled**. A következő ábra ezeket a beállításokat jeleníti meg a PuTTY-ban.
   
     ![Soros HTTPS és HTTP engedélyezve](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Az ügyfél előkészítése távoli csatlakozásra
A Távoli felügyelet engedélyezéséhez hajtsa végre az alábbi lépéseket az ügyfélen.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Az ügyfél előkészítése távoli kapcsolódásra
1. Indítsa el a Windows PowerShell-munkamenetet rendszergazdaként. Ha Windows 10-es ügyfelet használ, alapértelmezés szerint a Rendszerfelügyeleti webszolgáltatás manuális értékre van beállítva. Előfordulhat, hogy a következő beírásával kell elindítania a szolgáltatást:

    `Start-Service WinRM`
    
2. A következő parancs beírásával adja hozzá a StorSimple-eszköz IP-címét az ügyfél megbízható gazdagépek listájához:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Cserélje le a <*device_ip*> az eszköz IP-címére. például: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. A következő parancs beírásával mentse az eszköz hitelesítő adatait egy változóba: 
   
    ```
    $cred = Get-Credential
    ```
    
4. A megjelenő párbeszédpanelen:
   
   1. Írja be a felhasználónevet a következő formátumban: *device_ip \ssadmin*.
   2. Adja meg azt az eszköz-rendszergazdai jelszót, amelyet az eszköz a telepítővarázsló segítségével való konfigurálásakor adott meg. Az alapértelmezett jelszó a *jelszó1*.
5. Indítsa el a Windows PowerShell-munkamenetet az eszközön a következő parancs beírásával:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Ha Windows PowerShell-munkamenetet szeretne létrehozni a StorSimple virtuális eszközzel való használatra, fűzze hozzá a `–Port` paramétert, és határozza meg azt a nyilvános portot, amelyet a StorSimple virtuális készüléken a távelérési szolgáltatásban konfigurált.
   
   
Ezen a ponton aktív távoli Windows PowerShell-munkamenetet kell használnia az eszközhöz.
   
![PowerShell-távelérés HTTP használatával](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Csatlakozási HTTPS-kapcsolaton keresztül

A Windows PowerShell StorSimple-bővítménye a HTTPS-munkameneten keresztüli csatlakozás a legbiztonságosabb és ajánlott módszer a Microsoft Azure StorSimple eszközhöz való távoli csatlakozáshoz. A következő eljárások azt ismertetik, hogyan állíthatja be a soros konzolt és az ügyfélszámítógépeket, hogy a HTTPS használatával kapcsolódjon Windows PowerShell StorSimple-bővítményehoz.

A Távoli felügyelet konfigurálásához használhatja a Azure Portal vagy a soros konzolt is. Válasszon az alábbi eljárások közül:

* A Távoli felügyelet engedélyezése a Azure Portal használatával HTTPS-kapcsolaton keresztül
* [A Távoli felügyelet engedélyezése HTTPS protokollon keresztül a soros konzol használatával](#use-the-serial-console-to-enable-remote-management-over-https)

A Távoli felügyelet engedélyezése után a következő eljárásokkal készítse elő a gazdagépet a távoli felügyelethez, és kapcsolódjon az eszközhöz a távoli gazdagépről.

* [A gazdagép előkészítése a távoli felügyelethez](#prepare-the-host-for-remote-management)
* [Kapcsolódás az eszközhöz a távoli gazdagépről](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>A Távoli felügyelet engedélyezése a Azure Portal használatával HTTPS-kapcsolaton keresztül

Hajtsa végre a következő lépéseket a Azure Portal a Távoli felügyelet engedélyezéséhez a HTTPS protokollon keresztül.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Távoli felügyelet engedélyezése HTTPS-kapcsolaton keresztül a Azure Portal

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Válassza az **eszközök** lehetőséget, majd válassza ki a távoli felügyelethez konfigurálni kívánt eszközt, majd kattintson rá. Lépjen az **eszközbeállítások > biztonság** menüpontra.
2. A **biztonsági beállítások** panelen kattintson a **Távoli felügyelet** elemre.
3. A **Távoli felügyelet engedélyezése** lehetőségnél válassza az **Igen** beállítást.
4. Mostantól a HTTPS használatával is csatlakozhat. (Az alapértelmezett beállítás a HTTPS-kapcsolaton keresztüli kapcsolódás.) Győződjön meg arról, hogy a HTTPS van kiválasztva.
5. Kattintson a... majd kattintson a **távfelügyeleti tanúsítvány letöltése** elemre. Itt adhatja meg a fájl mentésének helyét. Telepítenie kell ezt a tanúsítványt azon az ügyfélen vagy gazdaszámítógépen, amelyet az eszközhöz való kapcsolódáshoz használni fog.
6. Kattintson a **Mentés** , majd az **Igen** gombra, amikor a rendszer megerősítést kér.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>A Távoli felügyelet engedélyezése HTTPS protokollon keresztül a soros konzol használatával

A Távoli felügyelet engedélyezéséhez hajtsa végre a következő lépéseket az eszköz soros konzolján.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Távoli felügyelet engedélyezése az eszköz soros konzolján
1. A soros konzol menüjében válassza az 1. lehetőséget. Az eszköz soros konzoljának használatával kapcsolatos további információkért nyissa meg [Windows PowerShell StorSimple-bővítménye eszközt a soros konzolon keresztül](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. A parancssorba írja be a következőt:
   
     `Enable-HcsRemoteManagement`
   
    Ez lehetővé teszi a HTTPS engedélyezését az eszközön.
3. Ellenőrizze, hogy a HTTPS engedélyezve van-e a beírásával: 
   
     `Get-HcsSystem`
   
    Győződjön meg arról, hogy a **RemoteManagementMode** mező a **HttpsEnabled** jeleníti meg. A következő ábra ezeket a beállításokat jeleníti meg a PuTTY-ban.
   
     ![Soros HTTPS engedélyezve](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. A kimenetében `Get-HcsSystem` másolja az eszköz sorozatszámát, és mentse későbbi használatra.
   
   > [!NOTE]
   > A sorozatszám a tanúsítványban szereplő CN-névre van leképezve.
   
5. A távfelügyeleti tanúsítvány beszerzéséhez írja be a következőket: 
   
     `Get-HcsRemoteManagementCert`
   
    A következőhöz hasonló tanúsítvány fog megjelenni.
   
    ![Távfelügyeleti tanúsítvány beolvasása](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Másolja a tanúsítványban szereplő adatokat **-----BEGIN certificate-----** **-----END Certificate-----** egy szövegszerkesztőbe, például a Jegyzettömbbe, és mentse. cer fájlként. (Ezt a fájlt a gazdagép előkészítésekor fogja másolni a távoli gazdagépre.)
   
   > [!NOTE]
   > Új tanúsítvány létrehozásához használja a `Set-HcsRemoteManagementCert` parancsmagot.
   
### <a name="prepare-the-host-for-remote-management"></a>A gazdagép előkészítése a távoli felügyelethez

Ha a gazdaszámítógépet HTTPS-munkamenetet használó távoli kapcsolatra szeretné felkészíteni, hajtsa végre az alábbi eljárásokat:

* [Importálja a. cer fájlt az ügyfél vagy a távoli gazdagép legfelső szintű tárolójába](#to-import-the-certificate-on-the-remote-host).
* [Adja hozzá az eszköz sorozatszámait a gazdagépek fájljához a távoli gazdagépen](#to-add-device-serial-numbers-to-the-remote-host).

Az előző eljárások mindegyikét alább mutatjuk be.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>A tanúsítvány importálása a távoli gazdagépre
1. Kattintson a jobb gombbal a. cer fájlra, majd válassza a **tanúsítvány telepítése** lehetőséget. Ezzel elindítja a tanúsítvány importálása varázslót.
   
    ![Tanúsítvány importálása varázsló 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Az **áruház helye** területen válassza a **helyi számítógép** lehetőséget, majd kattintson a **tovább** gombra.
3. Jelölje be **az összes tanúsítvány tárolása a következő tárolóban** jelölőnégyzetet, majd kattintson a **Tallózás** gombra. Navigáljon a távoli gazdagép legfelső szintű tárolójához, majd kattintson a **tovább** gombra.
   
    ![Tanúsítvány importálása varázsló 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Kattintson a **Finish** (Befejezés) gombra. Megjelenik egy üzenet, amely tájékoztatja, hogy az importálás sikeres volt.
   
    ![Tanúsítvány importálása varázsló 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Eszközök sorozatszámának hozzáadása a távoli gazdagéphez
1. Indítsa el a Jegyzettömböt rendszergazdaként, majd nyissa meg a \Windows\System32\Drivers\etc. címen található hosts fájlt.
2. Adja hozzá a következő három bejegyzést a gazdagépek fájljához: **adatok 0 IP-cím**, **vezérlő 0 rögzített IP-cím**, és **1. vezérlő rögzített IP-cím**.
3. Adja meg a korábban mentett eszköz sorozatszámát. Képezze le ezt az IP-címhez az alábbi ábrán látható módon. A 0. vezérlő és az 1. vezérlő esetében fűzze hozzá a **Controller0** és a **Controller1** a sorozatszám (CN-név) végén.
   
    ![CN-név hozzáadása a Hosts fájlhoz](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Mentse a Hosts fájlt.

### <a name="connect-to-the-device-from-the-remote-host"></a>Kapcsolódás az eszközhöz a távoli gazdagépről

A Windows PowerShell és a TLS használatával adjon meg egy SSAdmin-munkamenetet az eszközön egy távoli gazdagépről vagy ügyfélről. Az SSAdmin-munkamenet az 1. lehetőségre mutat az eszköz [soros konzol](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) menüjében.

Hajtsa végre az alábbi eljárást azon a számítógépen, amelyről a távoli Windows PowerShell-kapcsolatokat szeretné elvégezni.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>SSAdmin-munkamenet megadása az eszközön a Windows PowerShell és a TLS használatával
1. Indítsa el a Windows PowerShell-munkamenetet rendszergazdaként. Ha Windows 10-es ügyfelet használ, alapértelmezés szerint a Rendszerfelügyeleti webszolgáltatás manuális értékre van beállítva. Előfordulhat, hogy a következő beírásával kell elindítania a szolgáltatást:

    `Start-Service WinRM`

2. Adja hozzá az eszköz IP-címét az ügyfél megbízható gazdagépéhez a következő beírásával:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Ahol a <*device_ip*> az eszköz IP-címe; például: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Új hitelesítő adat létrehozásához írja be a következőt:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Ahol a *célként megadott eszköz <IP-* címe> az eszközre vonatkozó 0. ADATEGYSÉG IP-címe; például a **10.126.173.90** a Hosts fájl előző rendszerképében látható módon. Adja meg az eszköz rendszergazdai jelszavát is.
4. Hozzon létre egy munkamenetet az alábbiak beírásával:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    A parancsmaghoz tartozó-számítógépnév paraméternél adja meg a *céleszköz <sorozatszámát*>. Ezt a sorozatszámot a rendszer a távoli gazdagépen lévő Hosts fájlban lévő 0 adatmennyiség IP-címére rendelte le. például a **SHX0991003G44MT** az alábbi ábrán látható módon.
5. Típus:
   
     `Enter-PSSession $session`
6. Néhány percet várnia kell, majd HTTPS protokollon keresztül fog csatlakozni az eszközhöz. Ekkor megjelenik egy üzenet, amely jelzi, hogy csatlakozik az eszközhöz.
   
    ![PowerShell-távelérés a HTTPS és a TLS használatával](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Következő lépések

* További információ a [Windows PowerShell a StorSimple-eszköz felügyeletéhez való használatáról](storsimple-8000-windows-powershell-administration.md).
* További információ [a StorSimple Eszközkezelő szolgáltatás a StorSimple-eszköz felügyeletéhez való használatáról](storsimple-8000-manager-service-administration.md).

