---
title: Korlátozások
description: Az SDK-funkciók kódjainak korlátai
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: f62a07ef7109c669c6abc90d4e1b4a2ee83ece23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100530246"
---
# <a name="limitations"></a>Korlátozások

Számos szolgáltatás mérete, száma vagy egyéb korlátai vannak.

## <a name="azure-frontend"></a>Azure-előtérbeli felület

A következő korlátozások vonatkoznak a frontend API-ra (C++ és C#):
* [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient) -példányok összesen: 16.
* [RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession) -példányok összesen [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient): 16.

## <a name="objects"></a>Objektumokat

* Egyetlen típus összes engedélyezett objektuma ([entitás](../concepts/entities.md), [CutPlaneComponent](../overview/features/cut-planes.md)stb.): 16 777 215.
* Engedélyezett aktív kivágott síkok összesen: 8.

## <a name="geometry"></a>Geometria

* **Animáció:** Az animációk csak a [game Objects](../concepts/entities.md)egyes átalakításait animálják. A kinyúzási vagy Vertex-animációkkal rendelkező csontváz-animációk nem támogatottak. A forrásként szolgáló fájlból származó animációs zeneszámok nem őrződnek meg. Ehelyett az objektum-átalakítási animációkat az ügyfél kódjának kell megadnia.
* **Egyéni shaderek:** Az egyéni shaderek készítése nem támogatott. Csak beépített [színanyagokat](../overview/features/color-materials.md) vagy [pbr-anyagokat](../overview/features/pbr-materials.md) lehet használni.
* Az adategységben lévő **különböző anyagok maximális száma** : 65 535. További információ az anyagok automatikus számának csökkentéséről: az [anyag-ismétlődési](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) fejezet.
* **Egyetlen textúra maximális mérete**: 16 384 x 16 384. Az átalakítási folyamat a nagyobb forrású textúrák méretét is csökkenti.

### <a name="overall-number-of-polygons"></a>Sokszögek összesített száma

Az összes betöltött modell számára engedélyezett számú sokszög a virtuális gép méretétől függ a [munkamenet-felügyeleti REST API](../how-tos/session-rest-api.md):

| Kiszolgáló mérete | Sokszögek maximális száma |
|:--------|:------------------|
|Standard| 20 000 000 |
|prémium szintű| korlátlan |

A korlátozással kapcsolatos részletes információkért tekintse meg a [kiszolgáló mérete](../reference/vm-sizes.md) című fejezetet.

## <a name="platform-limitations"></a>A platformra vonatkozó korlátozások

**Windows 10 asztali verzió**

* A Win32/x64 az egyetlen támogatott Win32 platform. A Win32/x86 nem támogatott.

**HoloLens 2**

* A [renderelés a PV-kamerából](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) funkció nem támogatott.