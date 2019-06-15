---
title: Leképezési manager-támogatás – Azure Batch
description: Az Azure használatához megjelenítése az Azure Batch renderelési manager integrációjának használatával
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: bcc66a73e3d7986b177b13eb309ad664a006b960
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118586"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Azure Batch renderelési farm vezetők alá tartozó használatával

Használata egy meglévő helyi farm, renderelni, akkor nagyon valószínű, hogy egy renderelési manager a renderelési farm kapacitás szabályozza, és a renderelési feladatok.

Az Azure beépített támogatást vagy a bővítményeket kínál népszerű renderelési kezelők. Ezután adja hozzá, és távolítsa el az Azure virtuális gépekhez, beleértve a használatalapú esetében használható az alkalmazáslicenceket rendelkező virtuális gépek és az alacsony prioritású virtuális gépek.

Az alábbi renderelési kezelők támogatottak:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal renderelési](http://www.royalrender.de/)
* [Thinkbox határidő](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>Az Azure-PipelineFX Qube

Parancsfájlok és útmutatást ahhoz, hogy az Azure Batch-készlet Qube munkavállalók a használni kívánt virtuális gépeket [a GitHub-adattár](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Az Azure-Királyi renderelési

Royal renderelési rendelkezik beépített, az Azure és az Azure Batch integrációja lehetővé teszi, hogy terjessze ki az Azure-alapú virtuális gépeket a renderelési farm. Egy összefoglaló, lásd: [a súgófájlok](http://www.royalrender.de/help8/index.html?Cloudrendering.html).

Az Azure-beli integrációs Royal jelennek meg Microsoft-példa: a [Jellyfish képek vásárlói beszámolónk](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Az Azure-Thinkbox határidő

Parancsfájlok és útmutatást ahhoz, hogy az Azure Batch-készlet határidő alkiszolgálók a használni kívánt virtuális gépeket [a GitHub-adattár](https://github.com/Azure/azure-deadline).

## <a name="next-steps"></a>További lépések

Próbálja ki az Azure Batch-integráció segítségével a megfelelő, a renderelési kezelője beépülő modul és útmutatás a Githubon, ahol lehetséges.