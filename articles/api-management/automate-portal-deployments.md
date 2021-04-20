---
title: A fejlesztői portál üzembe helyezésének automatizálása
titleSuffix: Azure API Management
description: Ismerje meg, hogyan miolheti automatikusan a saját üzemeltetett fejlesztői portál tartalmát két különböző API Management között.
author: erikadoyle
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e189a9339f6ca3bc81148b86206ddd052392074f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741738"
---
# <a name="automate-developer-portal-deployments"></a>A fejlesztői portál üzembe helyezésének automatizálása

A API Management fejlesztői portál támogatja a tartalmak szoftveres hozzáférését. Lehetővé teszi az adatok importálását vagy exportálását egy API Management szolgáltatásból a [tartalomkezelési REST API.](/rest/api/apimanagement/) A REST API hozzáférés a felügyelt és a saját üzemeltetett portálok számára is működik.

## <a name="automated-migration-script"></a>Automatizált migrálási szkript

Az API-val automatizálhatja a tartalom migrálását két API Management-szolgáltatás – például egy tesztkörnyezetben található szolgáltatás és egy éles környezetben található szolgáltatás – között. A `scripts.v3/migrate.js` githubos fejlesztői portálon [](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js) API Management szkript leegyszerűsíti ezt az automatizálási folyamatot.

> [!WARNING]
> A szkript eltávolítja a fejlesztői portál tartalmát a célhelyen API Management szolgáltatásban. Ha aggódik, mindenképpen végezzen biztonsági mentést.

> [!NOTE]
> Ha egy saját üzemeltetett portált használ explicit módon meghatározott egyéni tárfiókkal médiafájlok tárolására (azaz a konfigurációs fájlban kell meghatároznia a beállítást), akkor az eredeti szkriptet kell `blobStorageUrl` `config.design.json` `scripts/migrate.js` [használnia.](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js) Az eredeti szkript nem működik a felügyelt vagy saját üzemeltetett portálok, valamint a felügyelt médiatárfiókkal API Management. Ebben az esetben használja inkább a mappából `/scripts.v3` származó szkriptet.

A szkript a következő lépéseket hajtja végre:

1. Rögzítse a portál tartalmát és adathordozóját a API Management szolgáltatásból.
1. Távolítsa el a portál tartalmát és adathordozóját a API Management szolgáltatásból.
1. Töltse fel a portál tartalmát és adathordozóját a API Management célhelyre.
1. Ha szükséges, és csak felügyelt portálok esetén – automatikusan közzéteheti a portált.

A szkript sikeres végrehajtása után a célként API Management szolgáltatásnak ugyanazt a portáltartalmat kell tartalmaznia, mint a forrásszolgáltatásnak, és rendszergazdaként fogja látni.

* Ha felügyelt portált használ, beállíthatja a szkriptet úgy, hogy automatikusan tegye közzé a célportált, hogy az áttelepített verzió automatikusan elérhető legyen a látogatók számára. 
* Ha saját maga által üzemeltetett portált használ, manuálisan kell közzétennie a célportált. Kövesse az oktatóanyag közzétételi és üzemeltetési utasításait egy saját üzemeltetésű fejlesztői [portál beállításhoz.](developer-portal-self-host.md)

## <a name="next-steps"></a>Következő lépések

További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál áttekintése](api-management-howto-developer-portal.md)
- [A fejlesztői portál önálló gazdagépe](developer-portal-self-host.md)