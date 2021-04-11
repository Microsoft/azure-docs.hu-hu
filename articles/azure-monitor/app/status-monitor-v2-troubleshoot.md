---
title: Azure Application Insights-ügynök hibaelhárítása és ismert problémái | Microsoft Docs
description: Application Insights-ügynök ismert problémái és a hibaelhárítási példák. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: cf3d8fd1566f3d71541aab7648680063e85079bf
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121829"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Application Insights ügynök hibaelhárítása (korábbi nevén Állapotmonitor v2)

Ha engedélyezi a figyelést, az adatgyűjtést akadályozó problémák merülhetnek fel.
Ez a cikk felsorolja az összes ismert problémát, és hibaelhárítási példákat tartalmaz.

## <a name="known-issues"></a>Ismert problémák

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Ütköző DLL-fájlok egy alkalmazás bin könyvtárában

Ha a DLL-fájlok bármelyike megtalálható a bin könyvtárban, a figyelés sikertelen lehet:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Ezek a DLL-fájlok a Visual Studio alapértelmezett alkalmazás-sablonjaiba tartoznak, még akkor is, ha az alkalmazás nem használja őket.
A hibakeresési eszközök segítségével megtekintheti a tüneti viselkedést:

- Perfview eszköz
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- Az IISReset és az alkalmazás terhelése (telemetria nélkül). Vizsgálat a Sysinternals-szel (Handle.exe és ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="powershell-versions"></a>PowerShell-verziók
Ezt a terméket a PowerShell v 5.1 használatával írták és tesztelték.
Ez a modul nem kompatibilis a PowerShell 6-os vagy 7-es verziójával.
Javasoljuk, hogy az újabb verziókkal együtt a PowerShell v 5.1-es verzióját használja. További információ: a [PowerShell 7 párhuzamos használata a powershell 5,1](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7#using-powershell-7-side-by-side-with-windows-powershell-51)-mel.

### <a name="conflict-with-iis-shared-configuration"></a>Ütközés az IIS megosztott konfigurációjával

Ha a webkiszolgálók fürtje van, lehet, hogy [megosztott konfigurációt](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)használ.
A HttpModule nem lehet beinjektálni ebbe a megosztott konfigurációba.
Futtassa az Enable parancsot az összes webkiszolgálón a DLL-fájlnak az egyes kiszolgálók GAC-ba történő telepítéséhez.

Az enable parancs futtatása után végezze el a következő lépéseket:
1. Nyissa meg a megosztott konfigurációs könyvtárat, és keresse meg a applicationHost.config fájlt.
2. Adja hozzá ezt a sort a konfiguráció modulok szakaszához:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS beágyazott alkalmazások

Az IIS-ben a 1,0-es verzióban nem található beágyazott alkalmazás.

### <a name="advanced-sdk-configuration-isnt-available"></a>A speciális SDK-konfiguráció nem érhető el.

Az SDK-konfiguráció nem érhető el a végfelhasználó számára az 1,0-es verzióban.

    
    
## <a name="troubleshooting"></a>Hibaelhárítás
    
### <a name="troubleshooting-powershell"></a>A PowerShell hibaelhárítása

#### <a name="determine-which-modules-are-available"></a>Az elérhető modulok meghatározása
A `Get-Module -ListAvailable` parancs használatával meghatározhatja, hogy mely modulok vannak telepítve.

#### <a name="import-a-module-into-the-current-session"></a>Modul importálása az aktuális munkamenetbe
Ha egy modult nem töltöttek be egy PowerShell-munkamenetbe, manuálisan is betöltheti azt a `Import-Module <path to psd1>` parancs használatával.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Az Application Insights Agent modul hibaelhárítása

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>A Application Insights Agent modulban elérhető parancsok listázása
Futtassa a parancsot az `Get-Command -Module Az.ApplicationMonitor` elérhető parancsok beszerzéséhez:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Az Application Insights Agent modul aktuális verziójának meghatározása
Futtassa a `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` parancsot a következő információk megjelenítéséhez a modulról:
   - PowerShell-modul verziója
   - Application Insights SDK-verzió
   - A PowerShell-modul fájlelérési útjai
    
A parancsmag használatának részletes ismertetését az [API-referenciában](status-monitor-v2-api-reference.md) tekintheti meg.


### <a name="troubleshooting-running-processes"></a>Futó folyamatok hibaelhárítása

Megvizsgálhatja a rendszerbe helyezett számítógépek folyamatait annak megállapítására, hogy az összes DLL-fájl be van-e töltve.
Ha a figyelés működik, legalább 12 DLL-t be kell tölteni.

Használja a `Get-ApplicationInsightsMonitoringStatus -InspectProcess` parancsot a DLL-fájlok vizsgálatához.

A parancsmag használatának részletes ismertetését az [API-referenciában](status-monitor-v2-api-reference.md) tekintheti meg.


### <a name="collect-etw-logs-by-using-perfview"></a>ETW-naplók gyűjtése a Perfview eszköz használatával

#### <a name="setup"></a>Beállítás

1. Töltse le PerfView.exe és PerfView64.exe a [githubról](https://github.com/Microsoft/perfview/releases).
2. PerfView64.exe elindítása.
3. Bontsa ki a **Speciális beállítások elemet**.
4. Törölje a jelet a következő jelölőnégyzetekből:
    - **Zip**
    - **Egyesítés**
    - **.NET-szimbólum gyűjteménye**
5. **További szolgáltatók** beállítása:`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Naplók összegyűjtése

1. Rendszergazdai jogosultságokkal rendelkező parancssori konzolon futtassa a `iisreset /stop` parancsot az IIS és az összes webalkalmazás kikapcsolásához.
2. A Perfview eszköz területen válassza a **gyűjtés indítása** elemet.
3. Rendszergazdai jogosultságokkal rendelkező parancssori konzolon futtassa a `iisreset /start` parancsot az IIS elindításához.
4. Próbálja meg megkeresni az alkalmazást.
5. Az alkalmazás betöltése után térjen vissza a Perfview eszköz, és válassza a **gyűjtemény leállítása** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- A kihagyott paraméterek megismeréséhez tekintse át az [API-referenciát](status-monitor-v2-overview.md#powershell-api-reference) .