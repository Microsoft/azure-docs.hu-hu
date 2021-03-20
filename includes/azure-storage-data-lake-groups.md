---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 9750eabf2aa5af4f431f2db17e113b07d3bce863
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017677"
---
Az ACL-bejegyzések mindig az [Azure ad biztonsági csoportjait használják hozzárendelt rendszerbiztonsági csoportként](../articles/active-directory/fundamentals/active-directory-manage-groups.md) . Lehetősége van arra, hogy közvetlenül rendeljen hozzá egyéni felhasználókat vagy egyszerű szolgáltatásokat. Ezzel a struktúrával felhasználókat vagy egyszerű szolgáltatásokat adhat hozzá és távolíthat el anélkül, hogy újra kellene alkalmaznia az ACL-eket egy teljes címtár-struktúrára. Ehelyett egyszerűen hozzáadhat vagy eltávolíthat felhasználókat és egyszerű szolgáltatásokat a megfelelő Azure AD biztonsági csoportból. 

A csoportok számos különböző módon állíthatók be. Tegyük fel például, hogy rendelkezik egy **/LogData** nevű címtárral, amely a kiszolgáló által generált naplózási adatait tartalmazza. Azure Data Factory (ADF) betölti az adott mappába az adatot. A Service Engineering csapata adott felhasználói feltölthetik a naplókat, és kezelik a mappa más felhasználóit, és a különböző Databricks-fürtök elemzik majd a naplókat a mappából. 

A tevékenységek engedélyezéséhez létrehozhat egy `LogsWriter` csoportot és egy `LogsReader` csoportot. Ezután a következő módon rendelhet hozzá engedélyeket:

- Adja hozzá a `LogsWriter` csoportot a **/LOGDATA** könyvtár ACL-jéhez, amely `rwx` engedélyekkel rendelkezik.
- Adja hozzá a `LogsReader` csoportot a **/LOGDATA** könyvtár ACL-jéhez, amely `r-x` engedélyekkel rendelkezik.
- Adja hozzá a szolgáltatás egyszerű objektumát vagy Managed Service Identity (MSI) Az ADF-hez a `LogsWriters` csoporthoz.
- Vegyen fel felhasználókat a Service Engineering csapatba a `LogsWriter` csoportba.
- Adja hozzá a Databricks tartozó egyszerű szolgáltatásnév-objektumot vagy MSI-t a `LogsReader` csoporthoz.

Ha a Service Engineering csapat egyik felhasználója elhagyja a vállalatot, akkor csak távolítsa el őket a `LogsWriter` csoportból. Ha nem adta hozzá a felhasználót egy csoporthoz, hanem egy dedikált ACL-bejegyzést adott a felhasználóhoz, akkor el kell távolítania az ACL-bejegyzést a **/LogData** könyvtárból. Emellett el kell távolítania a bejegyzést a **/LogData** könyvtárának teljes címtár-hierarchiájában található összes alkönyvtárból és fájlból. 

Csoport létrehozásához és tagok hozzáadásához tekintse meg [az alapszintű csoport létrehozása és a tagok hozzáadása a Azure Active Directory használatával](../articles/active-directory/fundamentals/active-directory-groups-create-azure-portal.md)című témakört.