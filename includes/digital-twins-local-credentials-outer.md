---
author: baanders
description: fájl belefoglalása a DefaultAzureCredential helyi hitelesítésének beállításához az Azure Digital Twins-mintákban – bevezetés
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: dcef8030c47e771e5cd771dac09b5f96e3d38abd
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473665"
---
### <a name="set-up-local-azure-credentials"></a>Helyi Azure-beli hitelesítő adatok beállítása

Ez a példa a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (a könyvtár részét képező `Azure.Identity` ) használatával hitelesíti a felhasználókat az Azure Digital Twins-példánnyal a helyi gépen való futtatásakor. További információ az ügyfélalkalmazások az Azure Digital Twins szolgáltatással való hitelesítésének különböző módjairól [*: útmutató: az alkalmazás-hitelesítési kód írása*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]