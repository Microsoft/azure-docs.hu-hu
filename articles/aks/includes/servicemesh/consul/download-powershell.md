---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8931ba97cf72891c78e20389be10178dd2c0317a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "77594258"
---
A Windows PowerShell-alapú rendszerhéjában a paranccsal `Invoke-WebRequest` töltse le a Consul Helm chart kiadást, majd a következő módon bontsa ki a `Expand-Archive` t:

```powershell
# Specify the Consul Helm chart version that will be leveraged throughout these instructions
$CONSUL_HELM_VERSION="0.10.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/hashicorp/consul-helm/archive/v$CONSUL_HELM_VERSION.zip" -OutFile "consul-helm-$CONSUL_HELM_VERSION.zip"
Expand-Archive -Path "consul-helm-$CONSUL_HELM_VERSION.zip" -DestinationPath .
Move-Item -Path consul-helm-$CONSUL_HELM_VERSION -Destination consul-helm
```

