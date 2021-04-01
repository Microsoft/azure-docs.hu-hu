---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81736277"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Töltse le és telepítse a Linkerd Linkerd-ügyfél bináris fájlját

A Windows PowerShell-alapú rendszerhéjában a következő paranccsal `Invoke-WebRequest` töltheti le az Linkerd-kiadást:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

Az `linkerd` ügyfél bináris fájlja az ügyfélszámítógépen fut, és lehetővé teszi a Linkerd szolgáltatás hálójának kezelését. A következő parancsokkal telepítheti a Linkerd `linkerd` -ügyfél bináris fájlját egy PowerShell-alapú rendszerhéjban Windows rendszeren. Ezek a parancsok az `linkerd` ügyfél bináris fájljait egy Linkerd mappába másolják, majd azonnal elérhetővé teszik (a jelenlegi rendszerhéjban) és véglegesen (a rendszerhéj újraindításakor) a használatával `PATH` . A parancsok futtatásához nincs szükség emelt szintű (rendszergazdai) jogosultságokra, és nem kell újraindítani a rendszerhéjat.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```