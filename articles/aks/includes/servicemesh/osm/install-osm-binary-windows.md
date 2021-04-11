---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 8ac70027f7483fbf0131c31a5ba3631ed1d4ff9a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080229"
---
## <a name="download-and-install-the-osm-client-binary"></a>Az OSM-ügyfél bináris fájljának letöltése és telepítése

A Windows PowerShell-alapú rendszerhéjában a használatával `Invoke-WebRequest` töltse le a Istio-kiadást, majd a következő módon bontsa ki a kivonatot `Expand-Archive` :

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.8.2"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-windows-amd64.zip&quot; -OutFile &quot;osm-$OSM_VERSION.zip"
Expand-Archive -Path "osm-$OSM_VERSION.zip" -DestinationPath .
```

Az `osm` ügyfél bináris fájlja fut az ügyfélszámítógépen, és lehetővé teszi az OSM-vezérlő felügyeletét az AK-fürtben. A következő parancsokkal telepítheti az OSM `osm` -ügyfél bináris fájlját a Windows PowerShell-alapú rendszerhéján. Ezek a parancsok az `osm` ügyfél bináris fájljait egy OSM-mappába másolják, majd azonnal elérhetővé teszik (a jelenlegi rendszerhéjban) és véglegesen (a rendszerhéj-újraindítások között) a használatával `PATH` . A parancsok futtatásához nincs szükség emelt szintű (rendszergazdai) jogosultságokra, és nem kell újraindítani a rendszerhéjat.

```powershell
# Copy osm.exe to C:\OSM
New-Item -ItemType Directory -Force -Path "C:\OSM"
Move-Item -Path .\windows-amd64\osm.exe -Destination "C:\OSM\"

# Add C:\OSM to PATH.
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\OSM\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\OSM\"
```
