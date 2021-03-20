---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: a02c17013a205ccc0da85536b491d467ef72fa48
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91666710"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Töltse le és telepítse a Istio istioctl-ügyfél bináris fájlját

A Windows PowerShell-alapú rendszerhéjában a használatával `Invoke-WebRequest` töltse le a Istio-kiadást, majd a következő módon bontsa ki a kivonatot `Expand-Archive` :

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.7.3"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-win.zip" -OutFile "istioctl-$ISTIO_VERSION.zip"
Expand-Archive -Path "istioctl-$ISTIO_VERSION.zip" -DestinationPath .
```

Az `istioctl` ügyfél bináris fájlja az ügyfélszámítógépen fut, és lehetővé teszi a Istio telepítését és kezelését az AK-fürtben. A következő parancsokkal telepítheti a Istio `istioctl` -ügyfél bináris fájlját egy PowerShell-alapú rendszerhéjban Windows rendszeren. Ezek a parancsok az `istioctl` ügyfél bináris fájljait egy Istio mappába másolják, majd azonnal elérhetővé teszik (a jelenlegi rendszerhéjban) és véglegesen (a rendszerhéj újraindításakor) a használatával `PATH` . A parancsok futtatásához nincs szükség emelt szintű (rendszergazdai) jogosultságokra, és nem kell újraindítani a rendszerhéjat.

```powershell
# Copy istioctl.exe to C:\Istio
New-Item -ItemType Directory -Force -Path "C:\Istio"
Move-Item -Path .\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```