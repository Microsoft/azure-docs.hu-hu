---
author: baanders
description: tartalmazza a bérlők közötti korlátozás kódmegoldását leíró fájlt a Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 2702f3c70d9e6f1a0bdad8695414e2d5fab02411
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589342"
---
Az alábbi példa bemutatja, hogyan állíthatja be a bérlőazonosító értékét a `InteractiveBrowserTenantId` `DefaultAzureCredential` beállításokban:

:::image type="content" source="../articles/digital-twins/media/troubleshoot-error-404/defaultazurecredentialoptions.png" alt-text="Képernyőkép a DefaultAzureCredentialOptions metódust megjelenítő kódról. Az InteractiveBrowserTenantId értéke egy bérlőazonosító-mintaértékre van beállítva.":::

Hasonló beállítások érhetők el a bérlők hitelesítéséhez a Visual Studio és Visual Studio Code-hoz. Az elérhető beállításokkal kapcsolatos további információkért tekintse meg a [DefaultAzureCredentialOptions dokumentációját.](/dotnet/api/azure.identity.defaultazurecredentialoptions?view=azure-dotnet&preserve-view=true)