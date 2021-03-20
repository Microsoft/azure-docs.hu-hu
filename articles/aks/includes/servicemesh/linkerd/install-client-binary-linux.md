---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: d303def85d66d5c2039b426e48186f90c2b3fff6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86277885"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Töltse le és telepítse a Linkerd Linkerd-ügyfél bináris fájlját

A Linux vagy a [Linux rendszerhez készült Windows alrendszer][install-wsl]bash-alapú rendszerhéjában `curl` a következő módon töltheti le az Linkerd-kiadást:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

Az `linkerd` ügyfél bináris fájlja az ügyfélszámítógépen fut, és lehetővé teszi a Linkerd szolgáltatás hálójának kezelését. A következő parancsokkal telepítheti a Linkerd `linkerd` -ügyfél bináris fájlját egy bash-alapú rendszerhéjban Linuxon vagy [Linuxon futó Windows alrendszeren][install-wsl]. Ezek a parancsok az `linkerd` ügyfél bináris fájljait az általános jogú felhasználói program mappájába másolják `PATH` .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Ha a Linkerd-ügyfél bináris fájljának a parancssori befejezését szeretné `linkerd` beállítani, a következőképpen állítsa be:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
