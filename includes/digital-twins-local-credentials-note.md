---
author: baanders
description: DefaultAzureCredential-fájl belefoglalása az Azure Digital Twins-mintákba – Megjegyzés
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473686"
---
>[!NOTE]
> Ez a példa a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (a könyvtár részét képező `Azure.Identity` ) használatával hitelesíti a felhasználókat az Azure Digital Twins-példánnyal a helyi gépen való futtatásakor. Ilyen típusú hitelesítés esetén a minta az Azure-beli hitelesítő adatokat fogja keresni a helyi környezetben, például egy helyi [Azure parancssori](/cli/azure/install-azure-cli) felületről vagy a Visual Studio/Visual Studio Code-ban.
>
> További információ a használatáról `DefaultAzureCredential` és egyéb hitelesítési lehetőségekről [*: útmutató: az alkalmazás-hitelesítési kód írása*](../articles/digital-twins/how-to-authenticate-client.md).