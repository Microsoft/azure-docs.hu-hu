---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 5dc9686e4a9994a085cc9f4a4631e66b05d7949d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91666711"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Töltse le és telepítse a Istio istioctl-ügyfél bináris fájlját

A macOS rendszerű bash-alapú rendszerhéjban `curl` a használatával töltse le a Istio-kiadást, majd a következő módon bontsa ki a kivonatot `tar` :

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Az `istioctl` ügyfél bináris fájlja az ügyfélszámítógépen fut, és lehetővé teszi a Istio telepítését és kezelését az AK-fürtben. A következő parancsokkal telepítheti a Istio `istioctl` -ügyfél bináris fájljait egy bash-alapú rendszerhéjba MacOS rendszeren. Ezek a parancsok az `istioctl` ügyfél bináris fájljait az általános jogú felhasználói program mappájába másolják `PATH` .

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Ha a Istio-ügyfél bináris fájljának a parancssori befejezését szeretné `istioctl` beállítani, a következőképpen állítsa be:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```