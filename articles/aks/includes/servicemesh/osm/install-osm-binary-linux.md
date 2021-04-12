---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: f99db628b08084ca750816c00a6892e877e90efc
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080228"
---
## <a name="download-and-install-the-osm-client-binary"></a>Az OSM-ügyfél bináris fájljának letöltése és telepítése

A Linux vagy [Windows rendszerű Linux][install-wsl]rendszeren futó bash-alapú rendszerhéjban az `curl` OSM-kiadás letöltéséhez használja a `tar` következőt:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-linux-amd64.tar.gz" | tar -vxzf -
```

Az `osm` ügyfél bináris fájlja az ügyfélszámítógépen fut, és lehetővé teszi az OSM felügyeletét az AK-fürtben. A következő parancsokkal telepítheti az OSM `osm` -ügyfél bináris fájlját egy bash-alapú rendszerhéjon a Linux vagy a [Linux rendszerhez készült Windows alrendszer][install-wsl]használatával. Ezek a parancsok az `osm` ügyfél bináris fájljait az általános jogú felhasználói program mappájába másolják `PATH` .

```bash
sudo mv ./linux-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

A `osm` következő paranccsal ellenőrizheti, hogy az ügyféloldali függvénytár helyesen lett-e hozzáadva az elérési úthoz és verziószámához.

```
osm version
```

<!-- LINKS - external -->

[install-wsl]: /windows/wsl/install-win10
