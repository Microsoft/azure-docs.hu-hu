---
author: baanders
description: DefaultAzureCredential-fájl belefoglalása az Azure Digital Twins-mintákba – Megjegyzés
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8aa3cbb2a037e49a694192c9852eeae0215c089c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211782"
---
>[!NOTE]
> Ez a példa a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (a könyvtár részét képező `Azure.Identity` ) használatával hitelesíti a felhasználókat az Azure Digital Twins-példánnyal a helyi gépen való futtatásakor. Ilyen típusú hitelesítés esetén a minta az Azure-beli hitelesítő adatokat fogja keresni a helyi környezetben, például egy helyi [Azure parancssori](/cli/azure/install-azure-cli) felületről vagy a Visual Studio/Visual Studio Code-ban.
>
> További információ a használatáról `DefaultAzureCredential` és egyéb hitelesítési lehetőségekről [*: útmutató: az alkalmazás-hitelesítési kód írása*](../articles/digital-twins/how-to-authenticate-client.md).