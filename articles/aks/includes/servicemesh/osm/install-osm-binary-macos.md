---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: 1c7dbe9bb6cf28f4dee2c4e9aa036410fadbbddf
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080236"
---
## <a name="download-and-install-the-osm-client-binary"></a>Az OSM-ügyfél bináris fájljának letöltése és telepítése

Egy bash-alapú rendszerhéjban a használatával `curl` töltse le az OSM-kiadást, majd bontsa ki a `tar` következőt:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

Az `osm` ügyfél bináris fájlja az ügyfélszámítógépen fut, és lehetővé teszi az OSM felügyeletét az AK-fürtben. Az OSM `osm` -ügyfél bináris fájljainak bash-alapú rendszerhéjba való telepítéséhez használja az alábbi parancsokat. Ezek a parancsok az `osm` ügyfél bináris fájljait az általános jogú felhasználói program mappájába másolják `PATH` .

```bash
sudo mv ./darwin-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

A `osm` következő paranccsal ellenőrizheti, hogy az ügyféloldali függvénytár helyesen lett-e hozzáadva az elérési úthoz és verziószámához.

```
osm version
```
