---
title: Korlátozások
description: Az SDK-funkciók kódjainak korlátai
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b500a3777ee24d6615022dae2571d021bd0d675a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201171"
---
# <a name="limitations"></a>Korlátozások

Számos szolgáltatás mérete, száma vagy egyéb korlátai vannak.

## <a name="azure-frontend"></a>Azure-előtérbeli felület

* AzureFrontend-példányok összesen: 16.
* AzureSession-példányok összesen AzureFrontend: 16.

## <a name="objects"></a>Objektumokat

* Egyetlen típus összes engedélyezett objektuma (entitás, CutPlaneComponent stb.): 16 777 215.
* Engedélyezett aktív kivágott síkok összesen: 8.

## <a name="geometry"></a>Geometria

* **Animáció:** Az animációk csak a game Objects egyes átalakításait animálják. A kinyúzási vagy Vertex-animációkkal rendelkező csontváz-animációk nem támogatottak. A forrásként szolgáló fájlból származó animációs zeneszámok nem őrződnek meg. Ehelyett az objektum-átalakítási animációkat az ügyfél kódjának kell megadnia.
* **Egyéni shaderek:** Az egyéni shaderek készítése nem támogatott. Csak beépített [színanyagokat](../overview/features/color-materials.md) vagy [pbr-anyagokat](../overview/features/pbr-materials.md) lehet használni.
* Az adategységben lévő **különböző anyagok maximális száma** : 65 535. További információ az anyagok automatikus számának csökkentéséről: az [anyag-ismétlődési](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) fejezet.
* **Egyetlen textúra maximális mérete**: 16 384 x 16 384. Az átalakítási folyamat a nagyobb forrású textúrák méretét is csökkenti.

### <a name="overall-number-of-polygons"></a>Sokszögek összesített száma

Az összes betöltött modell számára engedélyezett számú sokszög a virtuális gép méretétől függ a [munkamenet-felügyeleti REST API](../how-tos/session-rest-api.md#create-a-session):

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