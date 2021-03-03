---
title: A Windows rendszerű virtuális asztali ügynökkel kapcsolatos problémák elhárítása – Azure
description: Az ügynökök és a kapcsolatok általános problémáinak elhárítása.
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: b0fc5bd16aaa455ce3f6d634ce35e9a389a6f13b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732581"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>A Windows rendszerű virtuális asztali ügynökkel kapcsolatos gyakori problémák elhárítása

A Windows rendszerű virtuális asztali ügynök több tényező miatt is okozhat kapcsolati problémákat:
   - Hiba a közvetítőn, amely az ügynököt a szolgáltatás leállítására készteti.
   - Problémák a frissítésekkel.
   - Az ügynök telepítése során felmerülő problémák, amelyek megszakítják a kapcsolódást a munkamenet-gazdagéphez.

Ez a cikk végigvezeti a gyakori forgatókönyvek megoldásain, valamint a kapcsolódási problémák megoldásán.

>[!NOTE]
>A munkamenet-kapcsolattal és a Windows rendszerű virtuális asztali ügynökkel kapcsolatos problémák elhárításához javasoljuk, hogy tekintse át az eseménynaplókat **Eseménynapló**  >  **Windows-naplók**  >  **alkalmazásban**. Keresse meg azokat az eseményeket, amelyek az alábbi források egyikével rendelkeznek a probléma azonosításához:
>
>- WVD-Agent
>- WVD-Agent-Updater
>- RDAgentBootLoader
>- MsiInstaller

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>Hiba: a RDAgentBootLoader és/vagy Távoli asztal ügynök betöltője leállt

Ha a következő problémák valamelyikét látja, ez azt jelenti, hogy a rendszerindító betöltő, amely betölti az ügynököt, nem tudta megfelelően telepíteni az ügynököt, és az ügynök szolgáltatás nem fut:
- A **RDAgentBootLoader** leállt vagy nem fut.
- Nincs állapot a **Távoli asztal ügynök betöltője** számára.

A probléma megoldásához indítsa el a RDAgent rendszerindítási betöltőjét:

1. A szolgáltatások ablakban kattintson a jobb gombbal **Távoli asztal ügynök betöltő** elemre.
2. Válassza az **Indítás** elemet. Ha ez a beállítás szürkén jelenik meg, nem rendelkezik rendszergazdai engedélyekkel, és a szolgáltatás elindításához le kell kérnie őket.
3. Várjon 10 másodpercet, majd kattintson a jobb gombbal **Távoli asztal ügynök betöltő** elemre.
4. Válassza a **frissítés** lehetőséget.
5. Ha a szolgáltatás a megkezdése és frissítése után leáll, előfordulhat, hogy regisztrációs hiba történt. További információ: [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token).

## <a name="error-invalid_registration_token"></a>Hiba: INVALID_REGISTRATION_TOKEN

Nyissa meg **Eseménynapló**  >  **Windows-naplók**  >  **alkalmazást**. Ha a 3277 AZONOSÍTÓJÚ eseményt látja, a leírásban **INVALID_REGISTRATION_TOKEN** szerepel, a regisztrációs jogkivonat nem ismerhető fel érvényesként.

A probléma megoldásához hozzon létre egy érvényes regisztrációs jogkivonatot:

1. Új regisztrációs jogkivonat létrehozásához kövesse az [új regisztrációs kulcs létrehozása a virtuális gép számára](#step-3-generate-a-new-registration-key-for-the-vm) című szakaszban ismertetett lépéseket.
2. Nyissa meg a Beállításszerkesztőt. 
3. Nyissa meg **HKEY_LOCAL_MACHINE**  >  **Software**  >  **Microsoft**  >  **RDInfraAgent**.
4. Válassza a **IsRegistered** lehetőséget. 
5. Az **érték adat:** bejegyzés mezőbe írja be a **0** értéket, és kattintson **az OK gombra**. 
6. Válassza a **RegistrationToken** lehetőséget. 
7. Az **érték:** bevitel mezőben illessze be a regisztrációs jogkivonatot az 1. lépésben. 

   > [!div class="mx-imgBorder"]
   > ![Képernyőfelvétel a IsRegistered 0-ról](media/isregistered-token.png)

8. Nyisson meg egy parancssort rendszergazdaként.
9. Adja meg a **net stop RDAgentBootLoader**. 
10. Adja meg a **net start RDAgentBootLoader**. 
11. Nyissa meg a Beállításszerkesztőt.
12. Nyissa meg **HKEY_LOCAL_MACHINE**  >  **Software**  >  **Microsoft**  >  **RDInfraAgent**.
13. Ellenőrizze, hogy a **IsRegistered** értéke 1, és nincs-e semmi a **RegistrationToken** adatoszlopában. 

   > [!div class="mx-imgBorder"]
   > ![Képernyőfelvétel az 1. IsRegistered](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form"></a>Hiba: az ügynök nem tud kapcsolódni a közvetítőhöz INVALID_FORM

Nyissa meg **Eseménynapló**  >  **Windows-naplók**  >  **alkalmazást**. Ha olyan eseményt lát a 3277 AZONOSÍTÓval, amely a leírásban a "INVALID_FORM" kifejezést írja, akkor hiba történt az ügynök és a közvetítő közötti kommunikáció során. Az ügynök nem tud csatlakozni a közvetítőhöz, vagy bizonyos tűzfal vagy DNS-beállítások miatt nem érhető el egy adott URL-cím.

A probléma megoldásához tekintse meg, hogy elérhető-e a BrokerURI és a BrokerURIGlobal:
1. Nyissa meg a beállításszerkesztőt. 
2. Nyissa meg **HKEY_LOCAL_MACHINE**  >  **Software**  >  **Microsoft**  >  **RDInfraAgent**. 
3. Jegyezze fel a **BrokerURI** és a **BrokerURIGlobal** értékeit.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a Broker URI-ról és a globális ügynök URI-ról](media/broker-uri.png)

 
4. Nyisson meg egy böngészőt, és lépjen az *\<BrokerURI\> API/Health* szolgáltatáshoz. 
   - Ügyeljen arra, hogy a 3. lépésben szereplő értéket használja a **BrokerURI**. A jelen szakasz példája a következő: <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health> .
5. Nyisson meg egy másik fület a böngészőben, és nyissa meg az *\<BrokerURIGlobal\> API/Health* lapot. 
   - Ügyeljen arra, hogy a 3. lépésben szereplő értéket használja a **BrokerURIGlobal** hivatkozásban. A jelen szakasz példája a következő: <https://rdbroker.wvd.microsoft.com/api/health> .
6. Ha a hálózat nem blokkolja a közvetítői kapcsolatokat, mindkét oldal sikeresen betöltődik, és egy üzenet jelenik meg, amely szerint a **"távoli asztali ügynök kifogástalan"** állapotú, ahogy az alábbi képernyőképeken is látható. 

   > [!div class="mx-imgBorder"]
   > ![Az ügynök URI-hozzáférésének sikeres betöltését bemutató képernyőkép](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![A bróker globális URI-hozzáférésének sikeres betöltését bemutató képernyőkép](media/broker-global.png)
 

7. Ha a hálózat blokkolja a közvetítői kapcsolatokat, az oldalak nem töltődnek be, ahogy az alábbi képernyőképen is látható. 

   > [!div class="mx-imgBorder"]
   > ![A sikertelenül betöltött Broker-hozzáférés képernyőképe](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a sikertelenül betöltött Broker globális eléréséről](media/unsuccessful-broker-global.png)

8. Ha a hálózat blokkolja ezeket az URL-címeket, akkor fel kell oldania a szükséges URL-címek blokkolását. További információ: a [szükséges URL-címek listája](safe-url-list.md).
9. Ha ez nem oldja meg a problémát, győződjön meg arról, hogy nem rendelkezik olyan csoportházirend-titkosítással, amely letiltja az ügynököt a közvetítőhöz való kapcsolódásra. A Windows virtuális asztal ugyanazt a TLS 1,2 titkosítási algoritmust használja, mint az [Azure bejárati ajtó](../frontdoor/front-door-faq.MD#what-are-the-current-cipher-suites-supported-by-azure-front-door). További információ: a [kapcsolatbiztonsági](network-connectivity.md#connection-security).

## <a name="error-3703"></a>Hiba: 3703

Nyissa meg **Eseménynapló**  >  **Windows-naplók**  >  **alkalmazást**. Ha a leírásban "RD-átjáró URL: nem érhető el" értékkel 3703 rendelkező eseményt lát, az ügynök nem tudja elérni az átjáró URL-címeit. Ahhoz, hogy sikeresen csatlakozhasson a munkamenet-gazdagéphez, és engedélyezze a végpontok számára a hálózati forgalmat a korlátozások megkerülése érdekében, fel kell oldania a [szükséges URL](safe-url-list.md)-címek listáját. Győződjön meg arról is, hogy a tűzfal vagy a proxy beállításai nem blokkolja ezeket az URL-címeket. Az URL-címek blokkolásának feloldása szükséges a Windows virtuális asztal használatához.

A probléma megoldásához ellenőrizze, hogy a tűzfal és/vagy a DNS-beállítások nem blokkolja-e ezeket az URL-címeket:
1. [A Azure Firewall használatával biztosíthatja a Windows rendszerű virtuális asztali környezetek központi telepítését.](../firewall/protect-windows-virtual-desktop.md)
2. Konfigurálja a [Azure Firewall DNS-beállításait](../firewall/dns-settings.md).

## <a name="error-3019"></a>Hiba: 3019

Nyissa meg **Eseménynapló**  >  **Windows-naplók**  >  **alkalmazást**. Ha a 3019 AZONOSÍTÓJÚ eseményt látja, ez azt jelenti, hogy az ügynök nem tudja elérni a web socket Transport URL-címeket. Ahhoz, hogy sikeresen csatlakozhasson a munkamenet-gazdagéphez, és lehetővé tegye a hálózati forgalom számára a korlátozások megkerülését, fel kell oldania a [szükséges URL-címek listájában](safe-url-list.md)felsorolt URL-címeket. Az Azure hálózati csapatával gondoskodhat arról, hogy a tűzfal, a proxy és a DNS-beállítások ne blokkolja ezeket az URL-címeket. A hálózati nyomkövetési naplókat is megtekintheti annak azonosításához, hogy a Windows rendszerű virtuális asztali szolgáltatás hol legyen blokkolva. Ha támogatási kérelmet nyit meg erre a hibára vonatkozóan, ügyeljen arra, hogy a hálózati nyomkövetési naplókat csatolja a kérelemhez.

## <a name="error-installationhealthcheckfailedexception"></a>Hiba: InstallationHealthCheckFailedException

Nyissa meg **Eseménynapló**  >  **Windows-naplók**  >  **alkalmazást**. Ha olyan eseményt lát, amelynek azonosítója 3277, amely a leírásban a "InstallationHealthCheckFailedException" kifejezést jelzi, az azt jelenti, hogy a verem-figyelő nem működik, mert a terminálkiszolgáló bekapcsolta a verem-figyelő beállításkulcsot.

A probléma megoldása:
1. Ellenőrizze, hogy működik-e [a verem-figyelő](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
2. Ha a verem-figyelő nem működik, [manuálisan távolítsa el, majd telepítse újra a verem összetevőt](#error-vms-are-stuck-in-unavailable-or-upgrading-state).

## <a name="error-endpoint_not_found"></a>Hiba: ENDPOINT_NOT_FOUND

Nyissa meg **Eseménynapló**  >  **Windows-naplók**  >  **alkalmazást**. Ha olyan eseményt lát, amelynek azonosítója 3277, ami azt jelenti, hogy a (z) "ENDPOINT_NOT_FOUND" szerepel a leírásban, ami azt jelenti, hogy a közvetítő nem talált végpontot a kapcsolat létesítéséhez. Ez a probléma a következő okok egyike miatt fordulhat elő:

- Nincsenek virtuális gépek a gazdagépen
- A gazdagép-készletben lévő virtuális gépek nem aktívak
- A gazdagép-készletben lévő összes virtuális gép túllépte a maximális munkamenet-korlátot
- A gazdagép-készletben lévő virtuális gépek egyike sem fut az ügynök szolgáltatásban

A probléma megoldása:

1. Győződjön meg arról, hogy a virtuális gép be van kapcsolva, és nem lett eltávolítva a gazdagép-készletből.
2. Győződjön meg arról, hogy a virtuális gép nem lépte túl a maximális munkamenet-korlátot.
3. Győződjön meg arról, hogy az [ügynök szolgáltatás fut](#error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running) , és a [verem-figyelő működik](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
4. Győződjön [meg arról, hogy az ügynök csatlakozni tud a közvetítőhöz](#error-agent-cannot-connect-to-broker-with-invalid_form).
5. Győződjön [meg arról, hogy a virtuális gép rendelkezik érvényes regisztrációs jogkivonattal](#error-invalid_registration_token).
6. Győződjön meg arról, hogy [a virtuális gép regisztrációs jogkivonata nem járt le](faq.md#how-often-should-i-turn-my-vms-on-to-prevent-registration-issues). 

## <a name="error-installmsiexception"></a>Hiba: InstallMsiException

Nyissa meg **Eseménynapló**  >  **Windows-naplók**  >  **alkalmazást**. Ha a 3277 AZONOSÍTÓJÚ eseményt látja, amely a leírásban szerepel a **InstallMsiException** , a telepítő már fut egy másik alkalmazásban az ügynök telepítése közben, vagy egy házirend blokkolja a msiexec.exe program futtatását.

A probléma megoldásához tiltsa le a következő szabályzatot:
   - Windows Installer kikapcsolása  
      - Kategória elérési útja: Számítógép konfigurációja \ felügyeleti sablonok \ következő Windows-összetevők Installer
   
>[!NOTE]
>Ez nem egy átfogó lista a szabályzatokról, amelyek jelenleg is tisztában vannak.

Házirend letiltásához:
1. Nyisson meg egy parancssort rendszergazdaként.
2. Adja meg és futtassa az **RSoP. msc parancsot**.
3. Az **eredő házirend** ablakban, amely megjelenik, lépjen a kategória elérési útjára.
4. Válassza ki a szabályzatot.
5. Válassza a **Letiltva** lehetőséget.
6. Kattintson az **Alkalmaz** gombra.   

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a Windows Installer szabályzatról az eredő házirendben](media/gpo-policy.png)

## <a name="error-win32exception"></a>Hiba: Win32Exception

Nyissa meg **Eseménynapló**  >  **Windows-naplók**  >  **alkalmazást**. Ha a 3277 AZONOSÍTÓJÚ eseményt látja, amely a leírásban a **InstallMsiException** -t tartalmazza, a szabályzat blokkolja cmd.exe indítását. A program blokkolása megakadályozza a konzol ablakának futtatását, amely a szolgáltatás újraindításához szükséges, amikor az ügynök frissül.

A probléma megoldásához tiltsa le a következő szabályzatot:
   - Parancssorhoz való hozzáférés megakadályozása   
      - Kategória elérési útja: Felhasználó konfigurációja \ Sablonok\rendszer elemre
    
>[!NOTE]
>Ez nem egy átfogó lista a szabályzatokról, amelyek jelenleg is tisztában vannak.

Házirend letiltásához:
1. Nyisson meg egy parancssort rendszergazdaként.
2. Adja meg és futtassa az **RSoP. msc parancsot**.
3. Az **eredő házirend** ablakban, amely megjelenik, lépjen a kategória elérési útjára.
4. Válassza ki a szabályzatot.
5. Válassza a **Letiltva** lehetőséget.
6. Kattintson az **Alkalmaz** gombra.   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>Hiba: a verem-figyelő nem működik a Windows 10 2004 virtuális gépen

Futtassa a **qwinsta** parancsot a parancssorban, és jegyezze fel az **RDP-SxS** mellett megjelenő verziószámot. Ha nem jelenik meg az **RDP-TCP** és az **RDP-SxS** összetevők, a mellettük lévő **figyelés** mellett, vagy egyáltalán nem jelennek meg a **qwinsta** futtatása után, az azt jelenti, hogy van egy verem-probléma. A verem frissítései az ügynök frissítéseivel együtt települnek, és ha a telepítés elszakad, a Windows rendszerű virtuális asztali figyelő nem fog működni.

A probléma megoldása:
1. Nyissa meg a Beállításszerkesztőt.
2. Nyissa meg a **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**.
3. A **WinStations** alatt több mappát is láthat a különböző stack-verziókhoz, és válassza ki azt a mappát, amely megfelel a **qwinsta** futtatásakor a parancssorban megjelenő verzió információinak.
4. Keresse meg a **fReverseConnectMode** , és győződjön meg róla, hogy az adatértéke **1**. Győződjön meg arról is, hogy a **fEnableWinStation** értéke **1**.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a fReverseConnectMode](media/fenable-2.png)

5. Ha a **fReverseConnectMode** értéke nem **1**, válassza a **fReverseConnectMode** lehetőséget, és adja meg az **1** értéket az érték mezőben. 
6. Ha a **fEnableWinStation** értéke nem **1**, válassza a **fEnableWinStation** lehetőséget, és adja meg az **1** értéket az érték mezőjében.
7. Indítsa újra a virtuális gépet. 

>[!NOTE]
>Ha egyszerre több virtuális gép **fReverseConnectMode** vagy **fEnableWinStation** módját szeretné módosítani, az alábbi két dolog közül választhat:
>
>- Exportálja a beállításkulcsot a gépről, amelyet már dolgozott, és importálja azokat a többi olyan gépre, amelyeknek szükségük van erre a módosításra.
>- Hozzon létre egy általános csoportházirend-objektumot (GPO), amely a módosítást igénylő gépek beállításkulcs értékét állítja be.

7. Nyissa meg a **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **ClusterSettings**.
8. A **ClusterSettings** területen keresse meg a **SessionDirectoryListener** , és győződjön meg róla, hogy az adatértéke **RDP-SxS..**..
9. Ha a **SessionDirectoryListener** nem **RDP-SxS...** értékre van állítva, kövesse az [ügynök és a rendszerindítási betöltő eltávolítása](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) szakasz lépéseit az ügynök, a rendszerindító betöltő és a stack-összetevők eltávolításához, majd [telepítse újra az ügynököt és a rendszerindítási betöltőt](#step-4-reinstall-the-agent-and-boot-loader). Ezzel újra fogja telepíteni a párhuzamos veremet.

## <a name="error-heartbeat-issue-where-users-keep-getting-disconnected-from-session-hosts"></a>Hiba: a szívveréssel kapcsolatos probléma, hogy a felhasználók megszakítják a munkamenet-gazdagépek leválasztását

Ha a kiszolgáló nem vesz fel szívverést a Windows rendszerű virtuális asztali szolgáltatásból, módosítania kell a szívverés küszöbértékét. Kövesse az ebben a szakaszban található utasításokat, ha az alábbi esetek közül egy vagy több vonatkozik Önre:

- **CheckSessionHostDomainIsReachableAsync** -hibát kapott
- **ConnectionBrokenMissedHeartbeatThresholdExceeded** -hibát kapott
- **ConnectionEstablished kapott: UnexpectedNetworkDisconnect** hiba
- A felhasználói ügyfelek továbbra is le vannak választva
- A felhasználók a munkamenet-gazdagépekről leválasztva maradnak

A szívverési küszöbérték módosítása:
1. Nyissa meg a parancssort rendszergazdaként.
2. Adja meg a **qwinsta** parancsot, és futtassa.
3. Két verem-összetevőnek kell megjelennie: **RDP-TCP** és **RDP-SxS**. 
   - A használt operációs rendszer verziójától függően előfordulhat, hogy az **RDP-SxS** a Build száma követi. Ha igen, ügyeljen rá, hogy később jegyezze fel ezt a számot.
4. Nyissa meg a Beállításszerkesztőt.
5. Nyissa meg a **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**.
6. A **WinStations** alatt több mappát is láthat a különböző stack-verziókhoz. Válassza ki azt a mappát, amely megfelel a 3. lépésben szereplő verziószámnak.
7. Hozzon létre egy új beállításjegyzékbeli DWORD-t úgy, hogy a jobb gombbal a beállításjegyzék-szerkesztőre kattint, majd kiválasztja az **új**  >  **DWORD (32 bites) értéket**. A DWORD létrehozásakor adja meg a következő értékeket:
   - HeartbeatInterval: 10000
   - HeartbeatWarnCount: 30 
   - HeartbeatDropCount: 60 
8. Indítsa újra a virtuális gépet.

>[!NOTE]
>Ha a szívverési küszöbérték módosítása nem oldja meg a problémát, lehet, hogy egy mögöttes hálózati probléma merülhet fel, hogy kapcsolatba kell lépnie az Azure hálózati csapatával.

## <a name="error-downloadmsiexception"></a>Hiba: DownloadMsiException

Nyissa meg **Eseménynapló**  >  **Windows-naplók**  >  **alkalmazást**. Ha a 3277 AZONOSÍTÓJÚ eseményt látja, amely a leírásban **DownloadMsiException** , nincs elég hely a lemezen a RDAgent.

A probléma megoldásához a következő lépésekkel szabadítson fel lemezterületet a lemezen:
   - A felhasználó által már nem használt fájlok törlése
   - A virtuális gép tárolókapacitásának növelése

## <a name="error-agent-fails-to-update-with-missingmethodexception"></a>Hiba: az ügynök nem tud frissíteni a MissingMethodException

Nyissa meg **Eseménynapló**  >  **Windows-naplók**  >  **alkalmazást**. Ha a leírásban "MissingMethodException: metódus nem található" értékkel 3389 rendelkező eseményt lát, ez azt jelenti, hogy a Windows rendszerű virtuális asztali ügynök frissítése nem sikerült, és egy korábbi verzióra lett visszaváltva. Ennek az lehet az oka, hogy a virtuális gépeken jelenleg telepített .NET-keretrendszer verziószáma alacsonyabb, mint 4.7.2. A probléma megoldásához frissítenie kell a .NET-et 4.7.2 vagy újabb verzióra a [.NET-keretrendszer dokumentációjának](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)telepítési utasításait követve.


## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>Hiba: a virtuális gépek nem állnak rendelkezésre vagy verziófrissítés állapotban vannak

Nyisson meg egy PowerShell-ablakot rendszergazdaként, és futtassa a következő parancsmagot:

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

Ha a munkamenet-gazdagép vagy a gazdagépen lévő gazdagépek állapota mindig "nem érhető el" vagy "verziófrissítés", az ügynök vagy a verem nem lett sikeresen telepítve.

A probléma megoldásához telepítse újra az egymás melletti verem:
1. Nyisson meg egy parancssort rendszergazdaként.
2. Adja meg a **net stop RDAgentBootLoader**. 
3. Válassza a **Vezérlőpult**  >  **programok**  >  **programok és szolgáltatások elemét**.
4. Távolítsa el a **Távoli asztali szolgáltatások SxS hálózati verem** legújabb verzióját, vagy a   >    >  ReverseConnectListener alatt HKEY_LOCAL_MACHINE System **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations** felsorolt verziót.
5. Nyisson meg egy konzolablak-konzolt rendszergazdaként, és lépjen a **Program Files**  >  **Microsoft RDInfra**.
6. Válassza ki a **SxSStack** összetevőt, vagy futtassa az **msiexec/i SxSStack- <version> . msi** parancsot az MSI telepítéséhez.
8. Indítsa újra a virtuális gépet.
9. Lépjen vissza a parancssorba, és futtassa a **qwinsta** parancsot.
10. Győződjön meg arról, hogy a 6. lépésben telepített verem-összetevő a mellette lévő **figyelést** mondja.
   - Ha igen, írja be a **net start RDAgentBootLoader** parancsot a parancssorba, és indítsa újra a virtuális gépet.
   - Ha nem, akkor [újra regisztrálnia kell a virtuális gépet, és újra kell telepítenie az ügynök](#your-issue-isnt-listed-here-or-wasnt-resolved) összetevőt.

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>Hiba: a kapcsolódás nem található: a RDAgent nem rendelkezik aktív hozzáféréssel a közvetítőhöz

Előfordulhat, hogy a virtuális gépek a kapcsolati korláton vannak, így a virtuális gép nem tud új kapcsolatokat fogadni.

A probléma megoldása:
   - Csökkentse a munkamenetek maximális számát. Ez biztosítja, hogy az erőforrások egyenletesen oszlanak el a munkamenet-gazdagépek között, és megakadályozza az erőforrások kimerülését.
   - Növelje a virtuális gépek erőforrás-kapacitását.

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>Hiba: Pro virtuális gép vagy más nem támogatott operációs rendszer

A párhuzamos verem csak a Windows Enterprise vagy a Windows Server SKU által támogatott, ami azt jelenti, hogy az operációs rendszerek, például a Pro virtuális gép nem. Ha nem rendelkezik vállalati vagy kiszolgálói SKU-val, a verem a virtuális gépre lesz telepítve, de nem aktiválódik, így a **qwinsta** futtatásakor nem jelenik meg a parancssorban.

A probléma megoldásához hozzon létre egy Windows Enterprise vagy Windows Server rendszerű virtuális gépet.
1. Nyissa meg a [virtuális gép adatait](create-host-pools-azure-marketplace.md#virtual-machine-details) , és kövesse az 1-12-es lépéseket a következő ajánlott rendszerképek egyikének beállításához:
   - Windows 10 Enterprise multi-session, 1909-es verzió
   - Windows 10 Enterprise multi-session, Version 1909 + Microsoft 365 alkalmazások
   - Windows Server 2019 Datacenter
   - Windows 10 Enterprise multi-session, 2004-es verzió
   - Windows 10 Enterprise multi-session, Version 2004 + Microsoft 365 alkalmazások
2. Válassza **a felülvizsgálat és létrehozás** lehetőséget.

## <a name="error-name_already_registered"></a>Hiba: NAME_ALREADY_REGISTERED

A virtuális gép neve már regisztrálva van, és valószínűleg duplikált.

A probléma megoldása:
1. Kövesse a [munkamenet-gazdagép eltávolítása a gazdagép-készletből](#step-2-remove-the-session-host-from-the-host-pool) című szakasz lépéseit.
2. [Hozzon létre egy másik virtuális gépet](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal). Ügyeljen arra, hogy egyedi nevet adjon a virtuális gépnek.
3. Lépjen a [Azure Portalra](https://portal.azure.com) , és nyissa meg a gazdagép azon készletének **Áttekintés** lapját, amelyen a virtuális gép volt. 
4. Nyissa meg a **munkamenet-gazdagépek** lapot, és ellenőrizze, hogy az összes munkamenet-gazdagép szerepel-e az adott alkalmazáskészletben.
5. Várjon 5-10 percet, amíg a munkamenet-gazdagép állapota **elérhetővé válik**.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép az elérhető munkamenet-gazdagépről](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>A probléma nem szerepel a listán, vagy nem lett feloldva

Ha nem találja a problémát ebben a cikkben, vagy az utasítások nem segítenek, javasoljuk, hogy távolítsa el, telepítse újra, majd regisztrálja újra a Windows rendszerű virtuális asztali ügynököt. Az ebben a szakaszban található utasítások azt mutatják be, hogyan lehet újra regisztrálni a virtuális GÉPET a Windows rendszerű virtuális asztali szolgáltatásba az összes ügynök, rendszerindító betöltő és verem-összetevő eltávolításával, a munkamenet-gazdagép a gazdagépről való eltávolításával, a virtuális gép új regisztrációs kulcsának létrehozásával, valamint az ügynök és a rendszerindító betöltő újratelepítésével. Ha az alábbi esetek közül egy vagy több vonatkozik Önre, kövesse az alábbi utasításokat:
- A virtuális gép **frissítése** vagy nem **érhető** el
- A verem-figyelő nem működik, és Windows 10 1809, 1903 vagy 1904 rendszeren fut
- **EXPIRED_REGISTRATION_TOKEN** hibaüzenetet kap
- Nem jelenik meg a virtuális gépek listája a munkamenet-gazdagépek listáján
- Nem látja a **Távoli asztal ügynök betöltőjét** a szolgáltatások ablakban
- Nem látja a **RdAgentBootLoader** összetevőt a Feladatkezelő programban
- A **kapcsolatkezelő nem tudta érvényesíteni a beállítási hibát az** egyéni Rendszerképbeli virtuális gépeken
- A cikkben szereplő utasítások nem oldották meg a problémát

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>1. lépés: az összes ügynök, rendszerindítási betöltő és stack Component program eltávolítása

Az ügynök, a rendszerindító betöltő és a verem újratelepítése előtt el kell távolítania a meglévő összetevő-programokat a virtuális gépről. Az összes ügynök, rendszerindítási betöltő és stack Component program eltávolítása:
1. Jelentkezzen be a virtuális gépre rendszergazdaként. 
2. Válassza a **Vezérlőpult**  >  **programok**  >  **programok és szolgáltatások elemét**.
3. Távolítsa el a következő programokat:
   - Távoli asztal ügynök rendszerindítási betöltő
   - Távoli asztali szolgáltatások infrastruktúra-ügynök
   - Távoli asztali szolgáltatások infrastruktúra-Genfi ügynök
   - Távoli asztali szolgáltatások SxS hálózati verem
   
>[!NOTE]
>Előfordulhat, hogy a programok több példánya is megjelenik. Győződjön meg róla, hogy az összeset eltávolítja.

   > [!div class="mx-imgBorder"]
   > ![Képernyőfelvétel a programok eltávolításáról](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>2. lépés: a munkamenet-gazdagép eltávolítása a gazdagépről

Amikor eltávolítja a munkamenet-gazdagépet a gazdagépről, a munkamenet-gazdagép már nem lesz regisztrálva a gazdagépen. Ez a munkamenet-gazdagép regisztrációjának alaphelyzetbe állítása. A munkamenet-gazdagép eltávolítása a gazdagépről:
1. Nyissa meg az **áttekintő** oldalt a virtuális gép által használt gazdagéphez, a [Azure Portal](https://portal.azure.com). 
2. A **munkamenet** -gazdagépek lapon megtekintheti az adott alkalmazáskészletben lévő összes munkamenet-gazdagép listáját.
3. Tekintse meg a munkamenet-gazdagépek listáját, és válassza ki azt a virtuális gépet, amelyet el szeretne távolítani.
4. Válassza az **Eltávolítás** lehetőséget.  

   > [!div class="mx-imgBorder"]
   > ![A virtuális gép gazdagép-készletből való eltávolításának képernyőképe](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>3. lépés: új regisztrációs kulcs létrehozása a virtuális géphez

Új regisztrációs kulcsot kell megadnia, amely a virtuális gép a gazdagépen és a szolgáltatásban való újbóli regisztrálására szolgál. Új regisztrációs kulcs létrehozása a virtuális géphez:
1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és lépjen a szerkeszteni kívánt virtuális gép gazdagép-készletének **Áttekintés** lapjára.
2. Válassza a **regisztrációs kulcs** lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a regisztrációs kulcsról a portálon](media/reg-key.png)

3. Nyissa meg a **regisztrációs kulcs** lapot, és válassza az **új kulcs előállítása** lehetőséget.
4. Adja meg a lejárati dátumot, majd kattintson **az OK gombra**.  

>[!NOTE]
>A lejárati dátum nem lehet rövidebb, mint egy óra, és a létrehozási időponttól számított 27 napnál hosszabb időt is igénybe vehet. Javasoljuk, hogy a lejárati dátumot a 27 napos maximális értékre állítsa be.

5. Másolja az újonnan létrehozott kulcsot a vágólapra. Később szüksége lesz erre a kulcsra.

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>4. lépés: az ügynök és a rendszerindítási betöltő újratelepítése

Az ügynök és a rendszerindítási betöltő legújabb verziójának újratelepítésével a párhuzamos verem és a Genfi figyelési ügynök is automatikusan települ. Az ügynök és a rendszerindító betöltő újratelepítése:
1. Jelentkezzen be a virtuális gépre rendszergazdaként, és használja az ügynök telepítőjének megfelelő verzióját a központi telepítéshez attól függően, hogy a virtuális gép melyik verzióját futtatja. Ha Windows 10-es virtuális géppel rendelkezik, kövesse a [virtuális gépek regisztrálása](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) a **Windows rendszerű virtuális asztali ügynök** és a **Windows rendszerű virtuális asztali ügynök betöltője** letöltéséhez című témakör útmutatását. Ha Windows 7 rendszerű virtuális géppel rendelkezik, kövesse a [virtuális gépek regisztrálása](deploy-windows-7-virtual-machine.md#configure-a-windows-7-virtual-machine) a **Windows rendszerű virtuális asztali ügynök** és a **Windows rendszerű virtuális asztali ügynök kezelőjének** letöltéséhez című cikk 13-14. lépéseit.

   > [!div class="mx-imgBorder"]
   > ![Az ügynök és a rendszerbetöltő letöltési oldalának képernyőképe](media/download-agent.png)

2. Kattintson a jobb gombbal az ügynökre, és töltse le a letöltött rendszerbetöltő telepítőket.
3. Válassza ki a **Tulajdonságok** elemet.
4. Válassza a **Letiltás feloldása** lehetőséget.
5. Kattintson az **OK** gombra.
6. Futtassa az ügynök telepítőjét.
7. Amikor a telepítő megkérdezi a regisztrációs jogkivonatot, illessze be a regisztrációs kulcsot a vágólapról. 

   > [!div class="mx-imgBorder"]
   > ![A beillesztett regisztrációs jogkivonat képernyőképe](media/pasted-agent-token.png)

8. Futtassa a rendszerindítási betöltő telepítőjét.
9. Indítsa újra a virtuális gépet. 
10. Lépjen a [Azure Portalra](https://portal.azure.com) , és nyissa meg annak a gazdagépnek az **Áttekintés** lapját, amelyhez a virtuális gép tartozik.
11. A **munkamenet** -gazdagépek lapon megtekintheti az adott alkalmazáskészletben lévő összes munkamenet-gazdagép listáját.
12. Ekkor látnia kell a gazdagépen regisztrált munkamenet-gazdagépet az **elérhető** állapottal. 

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép az elérhető munkamenet-gazdagépről](media/hostpool-portal.png)

## <a name="next-steps"></a>Következő lépések

Ha a probléma továbbra is fennáll, hozzon létre egy támogatási esetet, és adja meg a problémával kapcsolatos részletes információkat, valamint azokat a műveleteket, amelyeket el kell végeznie a megoldásához. A következő lista a Windows rendszerű virtuális asztali környezetben felmerülő problémák elhárításához használható egyéb erőforrásokat tartalmaz.

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [környezet és az Alkalmazáskészletek létrehozása](troubleshoot-set-up-issues.md)című témakört.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali szolgáltatások kapcsolatai](troubleshoot-service-connection.md)című témakört.
- Távoli asztal-ügyfelekkel kapcsolatos problémák elhárításához tekintse meg [a távoli asztal-ügyfél hibaelhárítása](troubleshoot-client.md)című témakört.
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerű virtuális asztali környezet](environment-setup.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](../azure-resource-manager/management/view-activity-logs.md).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](../azure-resource-manager/templates/deployment-history.md).
