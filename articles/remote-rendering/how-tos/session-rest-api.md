---
title: A munkamenet-felügyeleti REST API
description: A munkamenetek felügyeletének ismertetése
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 9eb1bb87792dbc61e7c85dbc20c136499f23f67c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530212"
---
# <a name="use-the-session-management-rest-api"></a>A munkamenet-kezelési REST API használata

Az Azure távoli renderelési funkciójának használatához létre kell hoznia egy *munkamenetet*. Az egyes munkamenetek az Azure-ban lefoglalt kiszolgálónak felelnek meg, amelyhez az ügyféleszközök csatlakozni tudnak. Amikor egy eszköz csatlakozik a szolgáltatáshoz, a kiszolgáló megjeleníti a kért adatforrásokat, és a videó streamként szolgáltatja az eredményt. A munkamenet létrehozása során kiválaszthatja, hogy melyik [kiszolgálót](../reference/vm-sizes.md) szeretné futtatni, amely meghatározza a díjszabást. Ha a munkamenet többé nem szükséges, le kell állítani. Ha a munkamenet *címbérleti ideje* lejár, a rendszer automatikusan leállítja, ha nem állítja be manuálisan.

## <a name="rest-api-reference"></a>REST API-referencia

A REST API [hivatkozás itt található, és](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering) [itt](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)láthatók a hencegés definíciói.
Biztosítunk egy PowerShell-szkriptet az [ARR Samples adattárában](https://github.com/Azure/azure-remote-rendering) a *Scripts* mappában, amelyet a *RenderingSession.ps1* nevezünk, amely a szolgáltatás használatát mutatja be. A szkriptet és annak konfigurációját itt találja: [példa PowerShell-parancsfájlok](../samples/powershell-example-scripts.md).
A [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), a Java és a Python SDK-kat is biztosítunk.

> [!IMPORTANT]
> A késés a távoli renderelés használatakor fontos tényező. A legjobb megoldás az Ön számára legközelebb eső régióban lévő munkamenetek létrehozásához. Az [Azure-késési teszt](https://www.azurespeed.com/Azure/Latency) segítségével meghatározhatja, hogy melyik régió legközelebb Önhöz.

> [!IMPORTANT]
> Az ügyféleszközök egy renderelési munkamenethez való kapcsolódásához ARR futtatókörnyezeti SDK szükséges. Ezek az SDK-k a [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering?view=remoterendering) és a [C++](https://docs.microsoft.com/cpp/api/remote-rendering/)nyelven érhetők el. A szolgáltatáshoz való kapcsolódás mellett ezek az SDK-k is használhatók a munkamenetek elindításához és leállításához.

## <a name="next-steps"></a>Következő lépések

* [Az Azure frontend API-k használata a hitelesítéshez](frontend-apis.md)
* [PowerShell-példaszkriptek](../samples/powershell-example-scripts.md)
