---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 2112cde42ee00b78a82962ee46f53110068977c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98187074"
---
## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|Az **Azure Defender for Container** -beállításjegyzékek számlázása [a díjszabási oldalon](../articles/security-center/security-center-pricing.md) látható.|
|Támogatott nyilvántartások és lemezképek:|A nyilvános internetről a rendszerhéj-hozzáféréssel elérhető Linux-rendszerképek az ACR-jegyzékekben|
|Nem támogatott kibocsátásiegység-forgalmi jegyzékek és lemezképek:|Windows-rendszerképek<br>"Privát" nyilvántartások<br>Tűzfal, szolgáltatási végpont vagy privát végpontok, például Azure Private-kapcsolattal korlátozott hozzáférésű kibocsátásiegység-forgalmi jegyzékek<br>A Super-minimalista képek, például a [Docker](https://hub.docker.com/_/scratch/) -rendszerképek, vagy a "eltérítetlen" lemezképek, amelyek csak egy alkalmazást és annak futásidejű függőségeit tartalmazzák csomagkezelő, rendszerhéj vagy operációs rendszer nélkül|
|Szükséges szerepkörök és engedélyek:|**Biztonsági olvasó** és [Azure Container Registry szerepkörök és engedélyek](../articles/container-registry/container-registry-roles.md)|
|Felhők|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Kereskedelmi felhők<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov és Kína gov-csak a leküldéses funkciót jelenleg támogatja. További információ a [beolvasott képekről](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)|
|||