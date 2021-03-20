---
title: Windows rendszerű virtuális asztali RDP-Shortpath (előzetes verzió)
titleSuffix: Azure
description: Az RDP-Shortpath (előzetes verzió) beállítása a Windows rendszerű virtuális asztali gépekhez.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 295a46f6d1074ddf8422233ea3ccfa4d65c28fd8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571597"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Windows rendszerű virtuális asztali RDP-Shortpath (előzetes verzió)

> [!IMPORTANT]
> Az RDP-Shortpath jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az RDP Shortpath a Windows rendszerű virtuális asztalok egyik funkciója, amely közvetlen UDP-alapú átvitelt hoz létre Távoli asztal ügyfél és a munkamenet-állomás között. Az RDP ezt a továbbítást használja a Távoli asztal és a RemoteApp továbbítására, miközben jobb megbízhatóságot és konzisztens késést biztosít.

## <a name="key-benefits"></a>Főbb előnyök

* Az RDP-Shortpath átvitele a nagymértékben hatékony  [Universal Rate Control Protocol (URCP)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/)alapján történik. A URCP a hálózati feltételek aktív figyelésével fokozza az UDP-t, és valós és teljes körű kapcsolat-kihasználtságot biztosít. A URCP az Távoli asztal által igénybe veszik az alacsony késést és a veszteségi szinteket. A URCP a hálózati paraméterek dinamikus megismerése és a protokoll és a díjszabás-vezérlési mechanizmus biztosítása révén a legjobb teljesítményt biztosítja.
* Az RDP-Shortpath közvetlen kapcsolatot létesít Távoli asztal ügyfél és a munkamenet-állomás között. A közvetlen kapcsolat csökkenti a Windows rendszerű virtuális asztali átjárók függőségét, javítja a kapcsolat megbízhatóságát, és növeli az egyes felhasználói munkamenetek számára elérhető sávszélességet.
* A további továbbítások eltávolítása csökkenti az oda-és bevezetési időt, ami javítja a felhasználói élményt a késésre érzékeny alkalmazásokkal és a bemeneti módszerekkel.
* Az RDP-Shortpath támogatja a szolgáltatásminőség [(QoS) prioritásának konfigurálását](./rdp-quality-of-service-qos.md) az RDP-kapcsolatokhoz a differenciált szolgáltatási kódok (DSCP) jelek használatával.
* Az RDP-Shortpath átvitele lehetővé teszi a [kimenő hálózati forgalom korlátozását](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) az egyes munkamenetek sávszélesség-szabályozási arányának megadásával.

## <a name="connection-security"></a>Kapcsolatbiztonság

Az RDP-Shortpath kiterjeszti az RDP több átviteli képességet. Nem helyettesíti a fordított kapcsolat átvitelét, hanem kiegészíti azt. Az összes kezdeti munkamenet-közvetítés a Windows rendszerű virtuális asztali infrastruktúrán keresztül felügyelhető.

Az 3390-es UDP-port csak a fordított kapcsolaton keresztül hitelesített bejövő Shortpath-forgalomhoz használatos. Az RDP-Shortpath figyelő figyelmen kívül hagyja a figyelő összes kapcsolódási kísérletét, kivéve, ha azok megfelelnek a fordított csatlakozási munkamenetnek.

Az RDP-Shortpath TLS-kapcsolatot használ az ügyfél és a munkamenet-állomás között a munkamenet-gazdagép tanúsítványainak használatával. Alapértelmezés szerint az RDP-titkosításhoz használt tanúsítványt az operációs rendszer saját maga hozza létre a telepítés során. Ha kívánja, az ügyfelek központilag felügyelt tanúsítványokat telepíthetnek a vállalati hitelesítésszolgáltató által kiadott tanúsítványok alapján. A tanúsítvány-konfigurációkról további információt a [Windows Server dokumentációjában](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)talál.

## <a name="rdp-shortpath-connection-sequence"></a>RDP Shortpath-kapcsolati folyamat

A [fordított kapcsolat átvitelének](./network-connectivity.md)telepítése után az ügyfél és a munkamenet-gazdagép létrehozza az RDP-kapcsolatot, és egyezteti a több átviteli sebességű szolgáltatásokat. Az alábbiakban ismertetett további lépések:

1. A munkamenet-gazdagép elküldi a saját és nyilvános IPv4-és IPv6-címeinek listáját az ügyfélnek.
2. Az ügyfél elindítja a háttérben futó szálat, hogy egy párhuzamos UDP-alapú átvitelt hozzon létre közvetlenül a gazdagép IP-címeinek egyikéhez.
3. Habár az ügyfél a megadott IP-címeket is kiszolgálja, az a kezdeti kapcsolat létesítését folytatja a fordított kapcsolat átvitelével, hogy ne legyen késés a felhasználói kapcsolaton.
4. Ha az ügyfél közvetlen keresési vonallal rendelkezik, és a tűzfal konfigurációja helyes, az ügyfél biztonságos TLS-kapcsolatot létesít a munkamenet-állomással.
5. Az Shortpath-átvitel létrehozása után az RDP áthelyezi az összes dinamikus virtuális csatornát (DVCs), beleértve a távoli grafikát, a bemenetet és az eszközök átirányítását az új átvitelre.
6. Ha a tűzfal vagy a hálózati topológia megakadályozza, hogy az ügyfél közvetlen UDP-kapcsolatot hozzon létre, az RDP egy fordított csatlakozási átvitelsel folytatja a kapcsolatot.

Az alábbi ábra áttekintést nyújt az RDP Shortpath hálózati kapcsolatról.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="RDP-Shortpath hálózati kapcsolatainak ábrája" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Követelmények

Az RDP-Shortpath támogatásához a Windows rendszerű virtuális asztali ügyfélnek közvetlen rámutatnia kell a munkamenet-gazdagépre. A következő technológiák egyikének használatával közvetlen áttekintést kaphat:

* [Privát ExpressRoute](../expressroute/expressroute-circuit-peerings.md)
* [Helyek közötti VPN (IPsec-alapú)](../vpn-gateway/tutorial-site-to-site-portal.md)
* [Pont – hely típusú VPN (IPsec-alapú)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Nyilvános IP-cím hozzárendelése](../virtual-network/virtual-network-public-ip-address.md)

Ha más VPN-típusokat használ az Azure-beli virtuális hálózathoz való csatlakozáshoz, javasoljuk, hogy használjon UDP-alapú VPN-t a legjobb eredmények eléréséhez. Míg a TCP-alapú VPN-megoldások többsége minden IP-csomagot magában foglal, beleértve az UDP-t, a TCP-torlódási szabályozás örökölt terhelését, amely lelassítja az RDP-teljesítményt.

A közvetlen Rámutatás azt jelenti, hogy a tűzfalak nem blokkolják az 3390-es UDP-portot, és az ügyfél közvetlenül kapcsolódhat a munkamenet-gazdagéphez.

## <a name="enabling-rdp-shortpath-preview"></a>RDP-Shortpath engedélyezése – előzetes verzió

Az RDP-Shortpath előzetes verziójában való részvételhez engedélyeznie kell az RDP Shortpath-figyelőt a munkamenet-gazdagépen. Az RDP-Shortpath engedélyezheti a környezetben használt tetszőleges számú munkamenet-gazdagépen. Az RDP-Shortpath nem kell engedélyeznie a készletben lévő összes gazdagépen.
A Shortpath-figyelő engedélyezéséhez konfigurálnia kell a következő beállításjegyzék-értékeket:

> [!WARNING]
> Súlyos problémák merülhetnek fel, ha a beállításjegyzéket helytelenül módosítja a Rendszerleíróadatbázis-szerkesztővel vagy más módszert használ. Ezek a problémák szükségessé tehetik az operációs rendszer újratelepítését. A Microsoft nem garantálja, hogy ezek a problémák megoldhatók. A beállításjegyzéket csak saját felelősségére módosítsa.

1. A munkamenet-gazdagépen indítsa el Regedit.exe, majd navigáljon a következő helyre:

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. Hozzon létre egy új, **fUseUdpPortRedirector** nevű **DWORD** értéket, és állítsa **1** (decimális) értékre.
3. Hozzon létre egy új, **UdpPortNumber** nevű **DWORD** értéket, és állítsa **3390** (decimális) értékre.
4. Lépjen ki a Beállításszerkesztőből.
5. Munkamenet-gazdagép újraindítása

A következő parancsmagokat egy emelt szintű PowerShell-ablakban is futtathatja a beállításjegyzék értékeinek megadásához:

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

A csoportházirend konfigurálásához a PowerShellt is használhatja

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>A fokozott biztonságú Windows Defender-tűzfal konfigurálása

Az RDP-Shortpath bejövő hálózati forgalmának engedélyezéséhez használja a Csoportházirend Management MMC beépülő modul fokozott biztonságú Windows Defender-tűzfal csomópontját a tűzfalszabályok létrehozásához.

1. Nyissa meg a Csoportházirend-kezelő konzolt a [fokozott biztonságú Windows Defender-tűzfalon](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security).
2. A navigációs ablaktáblán válassza a **Bejövő szabályok** lehetőséget.
3. Válassza a **művelet**, majd az **új szabály** lehetőséget.
4. Az új bejövő szabály varázsló **szabály típusa** lapján válassza az **Egyéni** lehetőséget, majd kattintson a **tovább** gombra.
5. A **program** lapon válassza ki **a program elérési útját**, és írja be a **következőt**: "% systemroot% \system32\svchost.exe", majd válassza a tovább lehetőséget.
6. A **protokollok és portok** lapon válassza az UDP protokoll típusát. A **helyi porton** válassza a "megadott portok" lehetőséget, és írja be a következőt: 3390.
7. A **hatókör** lapon megadhatja, hogy a szabály csak az ezen a lapon megadott IP-címekre vonatkozó hálózati forgalomra vonatkozzon. Konfigurálja megfelelően a tervezéshez, majd kattintson a **tovább** gombra.
8. A **művelet** lapon válassza **a kapcsolat engedélyezése** lehetőséget, majd kattintson a **tovább** gombra.
9. A **profil** lapon válassza ki a hálózati hely azon típusait, amelyekre ez a szabály vonatkozik, majd válassza a **tovább** lehetőséget.
10. A **név** lapon írja be a szabály nevét és leírását, majd kattintson a **Befejezés gombra**.

Ellenőrizze, hogy az új szabály megegyezik-e az alábbi képernyőképekkel: :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="képernyőkép az általános lap tűzfal-konfigurációjának RDP Shortpath hálózati kapcsolataihoz" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="Képernyőfelvétel a programok és szolgáltatások lapról az RDP Shortpath hálózati kapcsolatainak tűzfal-konfigurációjához" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="Az RDP-Shortpath hálózati kapcsolatainak tűzfal-konfigurációjának protokollok és portok lapjának képernyőképe" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

A Windows tűzfalat a PowerShell használatával is konfigurálhatja:

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>A Windows Defender tűzfal konfigurálása a PowerShell használatával

A csoportházirend konfigurálásához a PowerShellt is használhatja

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP' -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True -GPOSession $gpoSession
Save-NetGPO -GPOSession $gpoSession
```

## <a name="configuring-azure-network-security-group"></a>Az Azure hálózati biztonsági csoport konfigurálása

Ahhoz, hogy az RDP-Shortpath figyelő hozzáférjen a hálózati biztonsági határokhoz, konfigurálnia kell az Azure hálózati biztonsági csoportot a 3390 bejövő UDP-port engedélyezéséhez.
Kövesse a [hálózati biztonsági csoport dokumentációját](../virtual-machines/windows/nsg-quickstart-portal.md) egy olyan bejövő biztonsági szabály létrehozásához, amely engedélyezi a forgalmat a következő paraméterekkel:

* **Forrás**  -  **Bármely** vagy az az IP-címtartomány, amelyben az ügyfelek tartózkodnak
* **Forrásport-tartományok** - **\***
* **Cél**  -  **Bármilyen**
* **Célport tartományai**  -  **3390**
* **Protokoll**  -  **UDP**
* **Művelet**  -  **Engedélyezés**
* Szükség esetén módosíthatja a **prioritást**. A prioritás befolyásolja a szabályok alkalmazásának sorrendjét: minél kisebb a numerikus érték, annál korábbi a szabály alkalmazása.
* **Név** – **RDP Shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>Az RDP-Shortpath letiltása egy adott alhálózat esetében

Ha egy adott alhálózatot le kell tiltania az RDP Shortpath-átvitel használatával, további hálózati biztonsági csoportokat is beállíthat, amelyek meghatározzák a forrás IP-tartományokat.

## <a name="verifying-the-connectivity"></a>A kapcsolat ellenőrzése

### <a name="using-connection-information-dialog"></a>A kapcsolatbiztonsági adatok párbeszédpanel használata

Annak ellenőrzéséhez, hogy a kapcsolatok RDP-Shortpath használnak-e, nyissa meg a "kapcsolati adatok" párbeszédpanelt a kapcsolat eszköztárának antenna ikonjára kattintva.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Távoli asztali kapcsolat sáv képe":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Távoli asztali kapcsolat információ párbeszédpanel képe":::

### <a name="using-event-logs"></a>Eseménynaplók használata

Annak ellenőrzése, hogy a munkamenet RDP Shortpath-átvitelt használ-e:

1. Kapcsolódjon a virtuális gép asztalához a Windows Virtual Desktop ügyfélprogram használatával.
2. Indítsa el a Eseménynapló, és nyissa meg a következő csomópontot: **alkalmazások és szolgáltatások naplói > Microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational**
3. Annak megállapításához, hogy a rendszer RDP Shortpath-átvitelt használ-e, keresse meg a 131-es AZONOSÍTÓJÚ eseményt.

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>Shortpath-kapcsolat ellenőrzése Log Analytics használatával

Ha [Azure-log Analytics](./diagnostics-log-analytics.md)használ, a [WVDConnections tábla](/azure/azure-monitor/reference/tables/wvdconnections)lekérdezésével figyelheti a kapcsolatokat. A UdpUse nevű oszlop azt jelzi, hogy a Windows Virtual Desktop RDP-verem UDP protokollt használ-e az aktuális felhasználói kapcsolaton.
Lehetséges értékek:

* **0** – a felhasználói kapcsolat nem használ RDP-Shortpath
* **1** – a felhasználói kapcsolat RDP-Shortpath használ
  
A következő lekérdezési lista lehetővé teszi a kapcsolatok adatainak áttekintését. Ezt a lekérdezést a [log Analytics lekérdezés-szerkesztőben](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query)futtathatja. Minden lekérdezésnél cserélje le a helyére a `userupn` megkeresni kívánt felhasználó egyszerű felhasználónevét.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="verify-shortpath-listener"></a>Shortpath-figyelő ellenőrzése

Annak ellenőrzéséhez, hogy az UDP-figyelő engedélyezve van-e, használja a következő PowerShell-parancsot a munkamenet-gazdagépen:

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Ha engedélyezve van, a következőhöz hasonló kimenet jelenik meg:

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

Ütközés esetén azonosíthatja a portot elfoglaló folyamatot a következő parancs használatával.

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>RDP-Shortpath letiltása

Bizonyos esetekben előfordulhat, hogy le kell tiltania az RDP Shortpath-átvitelt. Az RDP-Shortpath a csoportházirend használatával tilthatja le.

### <a name="disabling-rdp-shortpath-on-the-client"></a>Az RDP-Shortpath letiltása az ügyfélen

Egy adott ügyfél RDP-Shortpath letiltásához a következő Csoportházirend segítségével tilthatja le az UDP-támogatást:

1. A-ügyfélen futtassa a **gpedit. msc parancsot**.
2. Navigáljon a **Számítógép konfigurációja > felügyeleti sablonok > Windows-összetevők > Távoli asztali szolgáltatások > távoli asztali kapcsolat-ügyfél elemre**.
3. Állítsa be az **"UDP letiltása az ügyfélen"** beállítást az **engedélyezve** értékre

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>Az RDP-Shortpath letiltása a munkamenet-gazdagépen

Egy adott munkamenet-állomás RDP-Shortpath letiltásához a következő Csoportházirend segítségével tilthatja le az UDP-támogatást:

1. A munkamenet-gazdagépen futtassa a **gpedit. msc parancsot**.
2. Navigáljon a **Számítógép konfigurációja > felügyeleti sablonok > Windows-összetevők > Távoli asztali szolgáltatások > távoli asztali kapcsolat gazdagép > kapcsolatok elemre**.
3. Az **"RDP átviteli protokollok kiválasztása"** beállítás beállítása **csak TCP** értékre

## <a name="public-preview-feedback"></a>Nyilvános előzetes visszajelzés

Ezt a nyilvános előzetes verziót szeretném hallani a tapasztalatairól!
* Kérdések, kérések, megjegyzések és egyéb visszajelzések esetén [használja ezt a visszajelzési űrlapot](https://aka.ms/RDPShortpathFeedback).

## <a name="next-steps"></a>Következő lépések

* A Windows rendszerű virtuális asztali hálózati kapcsolat megismeréséhez tekintse meg a [Windows rendszerű virtuális asztali hálózati kapcsolat ismertetése](network-connectivity.md)című témakört.
* A Windows rendszerű virtuális asztali szolgáltatások szolgáltatásminőség (QoS) szolgáltatásának megkezdéséhez lásd: [a Windows rendszerű virtuális asztali szolgáltatás szolgáltatásminőség (QoS) implementálása](rdp-quality-of-service-qos.md).