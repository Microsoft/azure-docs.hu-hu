---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94573996"
---
> [!IMPORTANT]
> A Cosmos DB példány egy Microsoft által felügyelt erőforráscsoport keretében jön létre az __előfizetésében__, valamint a szükséges erőforrásokkal együtt. Ez azt jelenti, hogy erre a Cosmos DB példányra számítunk fel díjat. A felügyelt erőforráscsoport formátuma a neve `<AML Workspace Resource Group Name><GUID>` . Ha a Azure Machine Learning-munkaterület privát végpontot használ, a rendszer a Cosmos DB példányhoz is létrehoz egy virtuális hálózatot. Ez a VNet a Cosmos DB és Azure Machine Learning közötti kommunikáció biztonságossá tételére szolgál.
> 
> * Ne __törölje az__ ezt a Cosmos db példányt tartalmazó erőforráscsoportot, vagy a csoportban automatikusan létrehozott összes erőforrást. Ha törölnie kell az erőforráscsoportot, Cosmos DB példányt stb., törölnie kell az azt használó Azure Machine Learning-munkaterületet. A társított munkaterület törlésekor az erőforráscsoport, a Cosmos DB-példány és az egyéb automatikusan létrehozott erőforrások törlődnek.
> * Ehhez a Cosmos DB-fiókhoz az alapértelmezett [__kérési egység__](../articles/cosmos-db/request-units.md) __8000__-kor van beállítva.
> * __Nem adhat meg saját VNet__ a létrehozott Cosmos db-példánnyal való használatra. __A virtuális hálózat nem módosítható__. Például nem módosíthatja az általa használt IP-címtartományt.