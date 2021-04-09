---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: ab30b19160294b334c8c5abdfbcc1dc0da370f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103495883"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Azure kommunikációs erőforrás létrehozása

Azure kommunikációs szolgáltatások erőforrásának létrehozásához először jelentkezzen be a [Azure Portalba](https://portal.azure.com). A lap bal felső sarkában válassza az **+ erőforrás létrehozása** lehetőséget. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Képernyőfelvétel: az erőforrás létrehozása gomb kiemelése a Azure Portalban.":::

Adja meg a **kommunikációt** a **piactér** vagy a portál tetején található keresési sávon.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="A keresési sávban a kommunikációs szolgáltatások keresését bemutató képernyőkép.":::

Válassza az eredmények között a **kommunikációs szolgáltatások** elemet, majd kattintson a **Hozzáadás** gombra.

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="Képernyőfelvétel: az Azure panel, a Hozzáadás gomb kiemelése.":::

Most már konfigurálhatja a kommunikációs szolgáltatások erőforrását. A létrehozási folyamat első lapján a következőt kell megadnia:

* Az előfizetés
* Az erőforráscsoport (létrehozhat egy újat, vagy kiválaszthat egy meglévő erőforráscsoportot)
* A kommunikációs szolgáltatások erőforrásának neve
* Az a földrajzi hely, amelyhez az erőforrás társítva lesz

A következő lépésben címkéket rendelhet hozzá az erőforráshoz. Az Azure-erőforrások rendszerezéséhez címkéket is használhat. A címkékkel kapcsolatos további információkért tekintse meg az [erőforrás-címkézési dokumentációt](../../../azure-resource-manager/management/tag-resources.md) .

Végezetül áttekintheti a konfigurációt, és **létrehozhatja** az erőforrást. Vegye figyelembe, hogy az üzembe helyezés eltarthat néhány percig.

## <a name="manage-your-communication-services-resource"></a>A kommunikációs szolgáltatások erőforrásának kezelése

A kommunikációs szolgáltatások erőforrásának kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com), és keresse meg és válassza ki az **Azure kommunikációs szolgáltatásokat**.

A **kommunikációs szolgáltatások** lapon válassza ki az erőforrás nevét.

Az erőforrás **Áttekintés** lapja olyan alapszintű felügyeleti lehetőségeket tartalmaz, mint a Tallózás, Leállítás, indítás, újraindítás és törlés. Az erőforrás oldal bal oldali menüjében további konfigurációs beállításokat találhat.