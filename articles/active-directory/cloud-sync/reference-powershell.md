---
title: AADCloudSyncTools PowerShell-modul a Azure AD Connect Cloud Sync szolgáltatáshoz
description: Ez a cikk ismerteti, hogyan telepítheti a Azure AD Connect Cloud kiépítési ügynököt.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa358b0c9d7747584deabe761160d3bcbcde8feb
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593180"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>AADCloudSyncTools PowerShell-modul a Azure AD Connect Cloud Sync szolgáltatáshoz

A AADCloudSyncTools modul olyan hasznos eszközöket biztosít, amelyekkel kezelheti Azure AD Connect felhőalapú szinkronizálási környezetét.

## <a name="pre-requisites"></a>Előfeltételek
A következő előfeltételek szükségesek:

- A modul összes előfeltétele automatikusan telepíthető a következő használatával: `Install-AADCloudSyncToolsPrerequisites`
- Ez a modul MSAL-hitelesítést használ, ezért telepíteni kell a MSAL.PS modult. Az ellenőrzéshez egy PowerShell-ablakban hajtsa végre a következőt: `Get-module MSAL.PS -ListAvailable` . Ha a modul megfelelően van telepítve, a rendszer választ kap. `Install-AADCloudSyncToolsPrerequisites`A segítségével telepítheti a MSAL.PS legújabb verzióját
- Bár a AzureAD PowerShell-modul nem a modul bármely funkciójának előfeltétele, hasznos lehet, ha a használatával is automatikusan települ `Install-AADCloudSyncToolsPrerequisites` .
- A PowerShell-modulok manuális telepítése a TLS 1,2 kényszerítést igényli. A modulok telepítésének biztosításához a következőt kell beállítani a PowerShell-munkamenetben a használata előtt
  ```
   Install-Module:
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>A AADCloudSyncTools PowerShell-modul telepítése
A AADCloudSyncTools modul telepítéséhez és használatához kövesse az alábbi lépéseket:

1. A Windows PowerShell megnyitása rendszergazdai jogosultságokkal
2. Írja be vagy másolja be a következőt: `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`
3. Nyomja meg az ENTER billentyűt.
4. A modul importálásának ellenőrzéséhez írja be vagy másolja és illessze be a következőt: `Get-module AADCloudSyncTools`
5. Ekkor látnia kell a modul információit.
6. Ezután a AADCloudSyncTools modul előfeltételeinek telepítéséhez futtassa a következőt: `Install-AADCloudSyncToolsPrerequisites`
7. Az első futtatáskor a rendszer telepíti a PoweShellGet modult, ha nincs jelen. Az új PowershellGet-modul betöltéséhez zárjuk be a PowerShell ablakot, és nyisson meg egy új PowerShell-munkamenetet rendszergazdai jogosultságokkal. 
8. Importálja újra a modult a 3. lépés használatával.
9. `Install-AADCloudSyncToolsPrerequisites`A MSAL és a AzureAD modulok telepítéséhez futtassa a parancsot
11. Az összes előzetes reqs sikeresen telepíteni kell a ![ telepítési modult](media/reference-powershell/install-1.png)


## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools-parancsmagok
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
A MSAL.PS modul használatával kéri a tokent az Azure AD-rendszergazdának a Microsoft Graph eléréséhez 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
A következő módon exportálja és csomagolja be a tömörített fájlban található összes hibaelhárítási adatmennyiséget:
 1. Elindítja a részletes nyomkövetést a Start-AADCloudSyncToolsVerboseLogs.  Ezeket a nyomkövetési naplókat a következő mappában találja: C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.
 2. 3 percig gyűjt nyomkövetési naplót.
   Megadhat egy másik időpontot a-TracingDurationMins, vagy kihagyhatja a részletes nyomkövetést a-SkipVerboseTrace használatával
 3. Leállítja a részletes nyomkövetést Stop-AADCloudSyncToolsVerboseLogs
 4. Gyűjti Eseménynapló naplókat az elmúlt 24 órában
 5. Tömöríti az összes ügynök naplóit, a részletes naplókat és az eseménynaplókat a felhasználó dokumentumok mappájában található tömörített zip-fájlba. 
 </br>Másik kimeneti mappát is megadhat a-OutputPath \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Az Azure AD-bérlő részleteinek és a belső változók állapotának megjelenítése

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
A Graph használatával beolvassa a AD2AAD egyszerű szolgáltatásait, és visszaadja a szinkronizálási feladatok adatait.
A (z) paraméterként megadott szinkronizálási feladatok AZONOSÍTÓjának használatával is meghívható.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
A Graph használatával beolvassa a AD2AAD egyszerű szolgáltatásait, és visszaadja a szinkronizálási feladatok ütemtervét.
A (z) paraméterként megadott szinkronizálási feladatok AZONOSÍTÓjának használatával is meghívható.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
A Graph használatával beolvassa a AD2AAD egyszerű szolgáltatásait, és visszaadja a szinkronizációs feladatokhoz tartozó sémát.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
A gráf használatával beolvassa a szinkronizációs feladatok sémáját a megadott szinkronizálási feladatokhoz, és az összes szűrő csoport hatókörét megjeleníti.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
A Graph használatával beolvassa a AD2AAD egyszerű szolgáltatásait, és visszaadja a szinkronizálási feladatok beállításait.
A (z) paraméterként megadott szinkronizálási feladatok AZONOSÍTÓjának használatával is meghívható.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
A Graph használatával beolvassa a AD2AAD egyszerű szolgáltatásait, és visszaadja a szinkronizálási feladatok állapotát.
A (z) paraméterként megadott szinkronizálási feladatok AZONOSÍTÓjának használatával is meghívható.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
A Graph használatával beolvassa a AD2AAD és/vagy SyncFabric tartozó egyszerű szolgáltatásnév (ka) t.
Paraméterek nélkül csak a AD2AAD (ka) t fogja visszaadni.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Ellenőrzi a PowerShellGet v 2.2.4.1 vagy újabb, valamint az Azure AD-és MSAL.PS-modulok jelenlétét, és ha hiányzik, telepíti ezeket.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Meghívja a paraméterként megadott URI, metódus és törzs webes kérelmét.

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Az Azure AD PowerShell használatával törli az aktuális fiókot (ha van), és visszaállítja a szinkronizálási fiók hitelesítését egy új szinkronizációs fiókkal az Azure AD-ben.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Újraindítja a teljes szinkronizálást.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Folytatja a szinkronizálást az előző vízjelből.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
A (z) "AADConnectProvisioningAgent.exe.config" módosítása a részletes nyomkövetés engedélyezéséhez és a AADConnectProvisioningAgent szolgáltatás újraindításához a-SkipServiceRestart használatával megakadályozhatja a szolgáltatás újraindítását, de a konfigurációs módosítások nem lépnek életbe.  Ezeket a nyomkövetési naplókat a következő mappában találja: C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Módosítja a "AADConnectProvisioningAgent.exe.config" beállítást a részletes nyomkövetés letiltásához és a AADConnectProvisioningAgent szolgáltatás újraindításához. A-SkipServiceRestart használatával megakadályozhatja a szolgáltatás újraindítását, de a konfigurációs módosítások nem lépnek érvénybe.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Szünetelteti a szinkronizálást.

## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)

