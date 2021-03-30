---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "77593722"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Töltse le és telepítse a Linkerd Linkerd-ügyfél bináris fájlját

A MacOS rendszerű bash-alapú rendszerhéjban `curl` a következő módon töltheti le az Linkerd-kiadást:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

Az `linkerd` ügyfél bináris fájlja az ügyfélszámítógépen fut, és lehetővé teszi a Linkerd szolgáltatás hálójának kezelését. A következő parancsokkal telepítheti a Linkerd `linkerd` -ügyfél bináris fájljait egy bash-alapú rendszerhéjba MacOS rendszeren. Ezek a parancsok az `linkerd` ügyfél bináris fájljait az általános jogú felhasználói program mappájába másolják `PATH` .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
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
