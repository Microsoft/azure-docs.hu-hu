---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214376"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>Az érzékelő ujjlenyomat-szolgáltatójának konfigurálása

Először hozzon létre és konfiguráljon egy érzékelő ujjlenyomat-szolgáltatót. Az érzékelő ujjlenyomat-szolgáltatója gondoskodik a platform-specifikus érzékelők olvasásáról az eszközön, és a Felhőbeli térbeli horgony-munkamenet által felhasznált közös képviseletre konvertálja az olvasókat.

> [!IMPORTANT]
> [Ügyeljen arra, hogy itt ellenőrizze, hogy](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability) az Ön által engedélyezésre kerülő érzékelők elérhetők-e a platformon.