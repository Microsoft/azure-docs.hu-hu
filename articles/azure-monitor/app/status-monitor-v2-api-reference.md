---
title: Azure Application Insights .net-ügynök API-referenciája
description: Application Insights Agent API-referenciája. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7c5c2f1d055ec6270892873548872b20b17b4158
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566897"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure Monitor Application Insights Agent API-referenciája

Ez a cikk olyan parancsmagot ismertet, amely az az [. ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagja.

> [!NOTE] 
> - Első lépésként szükség van egy kialakítási kulcsra. További információt az [erőforrás létrehozása](create-new-resource.md#copy-the-instrumentation-key)című témakörben talál.
> - Ehhez a parancsmaghoz meg kell vizsgálni és el kell fogadnia a licenc-és adatvédelmi nyilatkozatot.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenetre és emelt szintű végrehajtási szabályzatra van szükség. További információ: a [PowerShell futtatása rendszergazdaként emelt szintű végrehajtási házirenddel](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - Ehhez a parancsmaghoz meg kell vizsgálni és el kell fogadnia a licenc-és adatvédelmi nyilatkozatot.
> - A Instrumentation-motor további terhelést hoz létre, és alapértelmezés szerint ki van kapcsolva.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

A rendszerállapot-kezelő motor egyes kulcsainak beállításával engedélyezhető.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

A kialakítási motor a .NET SDK-k által gyűjtött adatokat is kiegészítheti.
Olyan eseményeket és üzeneteket gyűjt, amelyek leírják a felügyelt folyamat végrehajtását. Ezek az események és üzenetek függőségi eredmény kódokat, HTTP-műveleteket és [SQL-parancsok szövegét](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)tartalmazzák.

A rendszerállapot-kezelő motor engedélyezése, ha:
- Már engedélyezte a figyelést az Enable parancsmaggal, de nem engedélyezte a kialakítási motort.
- Manuálisan alakította ki az alkalmazást a .NET SDK-val, és további telemetria szeretne gyűjteni.

### <a name="examples"></a>Példák

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Paraméterek

#### <a name="-acceptlicense"></a>-AcceptLicense
**Választható.** Ezzel a kapcsolóval fogadhatja el a fej nélküli telepítések licencét és adatvédelmi nyilatkozatát.

#### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezt a kapcsolót használja a részletes naplók kinyomtatásához.

### <a name="output"></a>Kimenet


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Példa a Instrumentation-motor sikeres engedélyezésének eredményére

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Engedélyezi az IIS-alkalmazások kód alapú csatolásának figyelését a célszámítógépen.

Ez a parancsmag módosítja az IIS-applicationHost.config, és beállítja a beállításkulcsokat.
Emellett létrehoz egy applicationinsights.ikey.config fájlt is, amely meghatározza az egyes alkalmazások által használt kialakítási kulcsot.
Az IIS betölti a RedfieldModule az indításkor, amely az alkalmazások indításakor beadja a Application Insights SDK-t az alkalmazásokba.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

Miután engedélyezte a figyelést, javasoljuk, hogy [élő metrikák](live-stream.md) használatával gyorsan ellenőrizze, hogy az alkalmazás küldjön-e nekünk telemetria.

### <a name="examples"></a>Példák

#### <a name="example-with-a-single-instrumentation-key"></a>Példa egyetlen kialakítási kulccsal
Ebben a példában az aktuális számítógépen lévő összes alkalmazás egyetlen kialakítási kulcshoz van rendelve.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Példa a kialakítási kulcs megfeleltetésére
Ebben a példában:
- `MachineFilter` az aktuális számítógépnek felel meg a `'.*'` helyettesítő karakter használatával.
- `AppFilter='WebAppExclude'` rendszerállapot- `null` kulcsot biztosít. A megadott alkalmazás nem lesz kialakítva.
- `AppFilter='WebAppOne'` egy egyedi kialakítási kulcsot rendel hozzá a megadott alkalmazáshoz.
- `AppFilter='WebAppTwo'` egy egyedi kialakítási kulcsot rendel hozzá a megadott alkalmazáshoz.
- Végezetül `AppFilter` a helyettesítő karaktert is használja a `'.*'` korábbi szabályoknak nem megfelelő webalkalmazások és az alapértelmezett kialakítási kulcs hozzárendeléséhez.
- A szóközök hozzáadódnak az olvashatósághoz.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```

> [!NOTE]
> Az ebben a kontextusban található AppFilter elnevezése zavaró lehet, `AppFilter` Beállítja az alkalmazásnév regex-szűrőt (üzemeltetési. sitename a .net-on az IIS-ben). `VirtualPathFilter` beállítja a virtuális elérési út regex-szűrőjét (üzemeltetési. ApplicationVirtualPath az IIS-ben .net esetén). Egyetlen alkalmazás esetében a következőképpen használhatja a VirtualPathFilter: `Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap @(@{VirtualPathFilter="^/MyAppName$"; InstrumentationSettings=@{InstrumentationKey='<your ikey>'}})`

### <a name="parameters"></a>Paraméterek

#### <a name="-instrumentationkey"></a>– InstrumentationKey
**Szükséges.** Ezzel a paraméterrel egyetlen rendszerállapot-kulcsot adhat meg a célszámítógépen lévő összes alkalmazás számára.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Szükséges.** Ezzel a paraméterrel több rendszerállapot-kulcs és az egyes alkalmazások által használt rendszerállapot-kulcsok leképezése is megadható.
A beállítással egyetlen telepítési parancsfájlt is létrehozhat több számítógép számára `MachineFilter` .

> [!IMPORTANT]
> Az alkalmazások a szabályok megadásának sorrendjében egyeznek meg a szabályokkal. Ezért a legpontosabb szabályokat kell megadnia az első és a legáltalánosabb szabályok közül.

##### <a name="schema"></a>Séma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- A **MachineFilter** a számítógép vagy a virtuális gép nevének kötelező C# regexje.
    - a ". *" mindennek megfelel
    - A "számítógépnév" csak a pontosan megadott névvel rendelkező számítógépekhez fog megfelelni.
- A **AppFilter** az IIS-hely nevének kötelező C#-alapú regexje. A [Get-iissite](/powershell/module/iisadministration/get-iissite)parancs futtatásával lekérheti a kiszolgálón található helyek listáját.
    - a ". *" mindennek megfelel
    - A "SiteName" csak az IIS-helynek felel meg, amely a pontos nevet adja meg.
- A **InstrumentationKey** az előző két szűrőnek megfelelő alkalmazások figyelésének engedélyezéséhez szükséges.
    - Ha a figyelés kizárásához szabályokat szeretne meghatározni, hagyja üresen ezt az értéket.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Választható.** Ezzel a kapcsolóval engedélyezheti a rendszerállapot-kezelő motornak, hogy eseményeket és üzeneteket gyűjtsön arról, hogy mi történik a felügyelt folyamat végrehajtása során. Ezek az események és üzenetek függőségi eredmény kódokat, HTTP-műveleteket és SQL-parancsok szövegét tartalmazzák.

A kialakítási motor terhelést hoz létre, és alapértelmezés szerint ki van kapcsolva.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Választható.** Ezzel a kapcsolóval fogadhatja el a fej nélküli telepítések licencét és adatvédelmi nyilatkozatát.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Ha webkiszolgálókat tartalmazó fürttel rendelkezik, lehet, hogy [megosztott konfigurációt](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)használ.
A HttpModule nem lehet beinjektálni ebbe a megosztott konfigurációba.
Ez a parancsfájl nem fog működni, ha az üzenetben további telepítési lépések szükségesek.
Ezzel a kapcsolóval figyelmen kívül hagyhatja ezt az ellenőrzés, és folytathatja az előfeltételek telepítését. További információ: [ismert ütközés-az-IIS-Shared-Configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezzel a kapcsolóval részletes naplókat jeleníthet meg.

#### <a name="-whatif"></a>– WhatIf 
**Általános paraméter.** Ezzel a kapcsolóval tesztelheti és érvényesítheti a bemeneti paramétereket a figyelés tényleges engedélyezése nélkül.

### <a name="output"></a>Kimenet

#### <a name="example-output-from-a-successful-enablement"></a>Példa a sikeres engedélyezésből származó kimenetre

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

Letiltja a rendszerállapot-kezelő motort néhány beállításkulcs eltávolításával.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

### <a name="examples"></a>Példák

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Paraméterek 

#### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezt a kapcsolót használja a részletes naplók kinyomtatásához.

### <a name="output"></a>Kimenet


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Példa a Instrumentation-motor sikeres letiltásának kimenetére

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

Letiltja a figyelést a célszámítógépen.
Ez a parancsmag eltávolítja az IIS-applicationHost.config szerkesztését, és eltávolítja a beállításkulcsokat.

### <a name="examples"></a>Példák

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Paraméterek 

#### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezzel a kapcsolóval részletes naplókat jeleníthet meg.

### <a name="output"></a>Kimenet


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Példa a figyelés sikeres letiltására

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Lekéri a konfigurációs fájlt, és kiírja az értékeket a-konzolra.

### <a name="examples"></a>Példák

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Paraméterek

Nincs szükség paraméterre.

### <a name="output"></a>Kimenet


##### <a name="example-output-from-reading-the-config-file"></a>Példa kimenetre a konfigurációs fájl olvasásakor

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

Ez a parancsmag hibaelhárítási információkat biztosít a Állapotmonitorról.
Ezzel a parancsmaggal vizsgálhatja meg a figyelési állapotot, a PowerShell-modul verzióját, valamint ellenőrizheti a futó folyamatot.
Ez a parancsmag a figyeléshez szükséges kulcsfontosságú fájlokkal kapcsolatos információkat és információkat fogja jelenteni.

### <a name="examples"></a>Példák

#### <a name="example-application-status"></a>Példa: alkalmazás állapota

Futtassa a parancsot a webhelyek `Get-ApplicationInsightsMonitoringStatus` figyelési állapotának megjelenítéséhez.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

Ebben a példában;
- A **számítógép-azonosító** egy névtelen azonosító, amely a kiszolgáló egyedi azonosítására szolgál. Ha támogatási kérelmet hoz létre, erre az AZONOSÍTÓra szüksége lesz a kiszolgáló naplófájljainak megkereséséhez.
- Az **alapértelmezett** webhely le van ÁLLÍTVA az IIS-ben
- A **DemoWebApp111** el lett indítva az IIS-ben, de nem kapott kérelmeket. Ez a jelentés azt jeleníti meg, hogy nincs futó folyamat (folyamatazonosító: nem található).
- A **DemoWebApp222** fut, és folyamatban van a figyelése (instrumentum: true). A felhasználói konfiguráció alapján a rendszerállapot-azonosító kulcs XXXXXXXX-XXXX-XXXX-XXXX-xxxxxxxxx123 a helyhez lett egyeztetve.
- A **DemoWebApp333** manuálisan lett kialakítva az Application Insights SDK használatával. Állapotmonitor észlelte az SDK-t, és nem figyeli ezt a helyet.


#### <a name="example-powershell-module-information"></a>Példa: PowerShell-modul adatai

Futtassa a parancsot az `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` aktuális modul adatainak megjelenítéséhez:

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>Példa: futtatókörnyezet állapota

Megvizsgálhatja a folyamatot a műszeres számítógépen, és ellenőrizheti, hogy az összes DLL-fájl be van-e töltve. Ha a figyelés működik, legalább 12 DLL-t be kell tölteni.

Futtassa a `Get-ApplicationInsightsMonitoringStatus -InspectProcess` parancsmagot:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>Paraméterek

#### <a name="no-parameters"></a>(Nincsenek paraméterek)

Alapértelmezés szerint ez a parancsmag a webalkalmazások figyelési állapotát fogja jelenteni.
Ezzel a beállítással ellenőrizheti, hogy az alkalmazás sikeresen létrejött-e.
Azt is megtekintheti, hogy melyik rendszerállapot-kulcs felelt meg a helynek.


#### <a name="-powershellmodule"></a>-PowerShellModule
Nem **kötelező**. Ezzel a kapcsolóval jelentheti a figyeléshez szükséges DLL-ek verziószámait és elérési útját.
Akkor használja ezt a beállítást, ha meg kell határoznia bármely DLL verzióját, beleértve a Application Insights SDK-t is.

#### <a name="-inspectprocess"></a>-InspectProcess

Nem **kötelező**. Ezzel a kapcsolóval jelentheti, hogy az IIS fut-e.
Emellett letölti a külső eszközöket is annak megállapításához, hogy a szükséges DLL-fájlok betöltődik-e az IIS-futtatókörnyezetbe.


Ha a folyamat bármilyen okból meghiúsul, manuálisan is futtathatja ezeket a parancsokat:
- iisreset.exe/status
- [handle64.exe](/sysinternals/downloads/handle) – p W3wp | findstr/I "InstrumentationEngine AI. ApplicationInsights
- [listdlls64.exe](/sysinternals/downloads/listdlls) W3wp | findstr/I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

Nem **kötelező**. Csak a InspectProcess használatával használható. Ezzel a kapcsolóval átugorhatja a további eszközök letöltése előtt megjelenő felhasználói üzenetet.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

A konfigurációs fájl teljes újratelepítés nélkül állítható be.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.


### <a name="examples"></a>Példák

#### <a name="example-with-a-single-instrumentation-key"></a>Példa egyetlen kialakítási kulccsal
Ebben a példában az aktuális számítógépen lévő összes alkalmazás hozzá lesz rendelve egyetlen kialakítási kulcshoz.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Példa a kialakítási kulcs megfeleltetésére
Ebben a példában:
- `MachineFilter` az aktuális számítógépnek felel meg a `'.*'` helyettesítő karakter használatával.
- `AppFilter='WebAppExclude'` rendszerállapot- `null` kulcsot biztosít. A megadott alkalmazás nem lesz kialakítva.
- `AppFilter='WebAppOne'` egy egyedi kialakítási kulcsot rendel hozzá a megadott alkalmazáshoz.
- `AppFilter='WebAppTwo'` egy egyedi kialakítási kulcsot rendel hozzá a megadott alkalmazáshoz.
- Végezetül `AppFilter` a helyettesítő karaktert is használja a `'.*'` korábbi szabályoknak nem megfelelő webalkalmazások és az alapértelmezett kialakítási kulcs hozzárendeléséhez.
- A szóközök hozzáadódnak az olvashatósághoz.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>Paraméterek

#### <a name="-instrumentationkey"></a>– InstrumentationKey
**Szükséges.** Ezzel a paraméterrel egyetlen rendszerállapot-kulcsot adhat meg a célszámítógépen lévő összes alkalmazás számára.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Szükséges.** Ezzel a paraméterrel több rendszerállapot-kulcs és az egyes alkalmazások által használt rendszerállapot-kulcsok leképezése is megadható.
A beállítással egyetlen telepítési parancsfájlt is létrehozhat több számítógép számára `MachineFilter` .

> [!IMPORTANT]
> Az alkalmazások a szabályok megadásának sorrendjében egyeznek meg a szabályokkal. Ezért a legpontosabb szabályokat kell megadnia az első és a legáltalánosabb szabályok közül.

##### <a name="schema"></a>Séma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- A **MachineFilter** a számítógép vagy a virtuális gép nevének kötelező C# regexje.
    - a ". *" mindennek megfelel
    - A "számítógépnév" csak a megadott névvel rendelkező számítógépekhez fog megfelelni.
- A **AppFilter** a számítógép vagy a virtuális gép nevének kötelező C# regexje.
    - a ". *" mindennek megfelel
    - A "ApplicationName" csak a megadott nevű IIS-alkalmazásoknak felel meg.
- A **InstrumentationKey** az előző két szűrőnek megfelelő alkalmazások figyelésének engedélyezéséhez szükséges.
    - Ha a figyelés kizárásához szabályokat szeretne meghatározni, hagyja üresen ezt az értéket.


#### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezzel a kapcsolóval részletes naplókat jeleníthet meg.


### <a name="output"></a>Kimenet

Alapértelmezés szerint nincs kimenet.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Példa részletes kimenetre a konfigurációs fájlnak a-InstrumentationKey használatával történő beállításához

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Példa részletes kimenetre a konfigurációs fájlnak a-InstrumentationKeyMap használatával történő beállításához

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

A [ETW-eseményeket](/windows/desktop/etw/event-tracing-portal) gyűjti a kód-visszacsatolási futtatókörnyezetből. Ez a parancsmag egy alternatív megoldás a [perfview eszköz](https://github.com/microsoft/perfview)futtatására.

Az összegyűjtött események valós időben lesznek kinyomtatva a konzolra, és egy ETL-fájlba lesznek mentve. A kimeneti ETL-fájl a [perfview eszköz](https://github.com/microsoft/perfview) segítségével nyitható meg további vizsgálat céljából.

Ez a parancsmag addig fut, amíg el nem éri az időtúllépés időtartamát (alapértelmezés szerint 5 perc), vagy manuálisan leállt ( `Ctrl + C` ).

### <a name="examples"></a>Példák

#### <a name="how-to-collect-events"></a>Események gyűjtése

Általában azt javasoljuk, hogy gyűjtsön eseményeket, és vizsgálja meg, hogy az alkalmazás miért nincs kialakítva.

A kód-visszacsatolási futtatókörnyezet ETW eseményeket bocsát ki az IIS indításakor és az alkalmazás indításakor.

Az események összegyűjtése:
1. Rendszergazdai jogosultságokkal rendelkező cmd-konzolon hajtsa végre `iisreset /stop` az IIS és az összes webalkalmazás kikapcsolását.
2. A parancsmag végrehajtása
3. Rendszergazdai jogosultságokkal rendelkező cmd-konzolon futtassa a parancsot `iisreset /start` az IIS elindításához.
4. Próbálja meg megkeresni az alkalmazást.
5. Miután az alkalmazás befejezte a betöltést, manuálisan leállíthatja ( `Ctrl + C` ), vagy megvárhatja az időtúllépést.

#### <a name="what-events-to-collect"></a>A gyűjteni kívánt események

Az események gyűjtésére három lehetőség áll rendelkezésre:
1. Használja a kapcsolót a `-CollectSdkEvents` Application INSIGHTS SDK-ból kibocsátott események gyűjtéséhez.
2. A kapcsolóval `-CollectRedfieldEvents` összegyűjtheti Állapotmonitor és a Redfield futtatókörnyezet által kibocsátott eseményeket. Ezek a naplók az IIS és az alkalmazások indításának diagnosztizálásakor hasznosak.
3. Mindkét kapcsolót mindkét eseménytípus összegyűjtésére használhatja.
4. Alapértelmezés szerint, ha nincs megadva kapcsoló, mindkét eseménytípus összegyűjtve lesz.


### <a name="parameters"></a>Paraméterek

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Választható.** Ezzel a paraméterrel állíthatja be, hogy a parancsfájl mennyi ideig gyűjtsön eseményeket. Az alapértelmezett érték 5 perc.

#### <a name="-logdirectory"></a>– LogDirectory
**Választható.** Ezzel a kapcsolóval állíthatja be az ETL-fájl kimeneti könyvtárát. Alapértelmezés szerint ez a fájl a PowerShell-modulok könyvtárban lesz létrehozva. A parancsfájl végrehajtása során a teljes elérési út megjelenik.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Választható.** Ezzel a kapcsolóval Application Insights SDK-eseményeket gyűjthet.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Választható.** Ezzel a kapcsolóval gyűjthet eseményeket a Állapotmonitor és a Redfield futtatókörnyezetből.

#### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezt a kapcsolót használja a részletes naplók kinyomtatásához.



### <a name="output"></a>Kimenet


#### <a name="example-of-application-startup-logs"></a>Alkalmazás-indítási naplók – példa
```powershell
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```

## <a name="next-steps"></a>Következő lépések

  A telemetriai adatok megtekintése:
 - [Ismerje meg a mérőszámokat](../essentials/metrics-charts.md) a teljesítmény és a használat figyeléséhez.
- [Események és naplók keresése](./diagnostic-search.md) a problémák diagnosztizálásához.
- További speciális lekérdezésekhez használja az [elemzést](../logs/log-query-overview.md) .
- [Irányítópultok létrehozása](./overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely elérhető maradjon.
- [Vegyen fel webes ügyfél-telemetria](./javascript.md) a kivételek megjelenítéséhez a weboldali kódból és a nyomkövetési hívások engedélyezéséhez.
- [Adja hozzá a Application INSIGHTS SDK-t a kódhoz](./asp-net.md) , hogy nyomkövetési és naplózási hívásokat helyezzen el.
 
 Több Application Insights-ügynökkel:
 - Az útmutató segítségével Application Insights-ügynököt lehet [elhárítani](status-monitor-v2-troubleshoot.md) .

