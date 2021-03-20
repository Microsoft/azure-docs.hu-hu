---
title: A render Manager támogatása
description: A Azure Batch Render Manager integrációjának használata. Ismerkedjen meg a népszerű Render managerek beépített támogatásával vagy bővítményeivel.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: c44cbf86d8bf2fe83a6dc91dee1c4f58eec156c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83726451"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Azure Batch használata a render Farm Managerrel

Ha egy meglévő helyszíni Render farmot használ, akkor nagy valószínűséggel a render Manager a farmok renderelési kapacitását és a renderelési feladatokat vezérli.

Az Azure beépített támogatást vagy bővítményeket biztosít a népszerű Render managerekhez. Ezután hozzáadhat és eltávolíthat Azure-beli virtuális gépeket, beleértve a használaton kívüli alkalmazások licencelését és az alacsony prioritású virtuális gépeket is.

A következő Render managerek támogatottak:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox határideje](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render hub

Az Azure Render hub leegyszerűsíti az Azure Render-farmok létrehozását és felügyeletét.  A render hub natív módon támogatja a PipelineFx Qube és a 10. határidőt.  További információkért és részletes utasításokért lásd [a GitHub-tárházat](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Az Azure használata a PipelineFX Qube

Az Azure Render hub támogatja a népszerű Render managereket, beleértve a határidőt is.  A render hub üzembe helyezésével és használatával kapcsolatos utasításokért lásd [a GitHub-tárházat](https://github.com/Azure/azure-render-hub).

Azok a parancsfájlok és utasítások, amelyek lehetővé teszik, hogy Azure Batch készletben lévő virtuális gépek Qube feldolgozóként is használhatók legyenek [a GitHub-tárházban](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Az Azure és a Royal Render használata

A Royal Render rendelkezik az Azure-nal és Azure Batch integrációs szolgáltatással, amely lehetővé teszi a render farmok Azure-alapú virtuális gépekkel való bővítését. Az összegzést [a súgófájlok](https://www.royalrender.de/help8/index.html?Cloudrendering.html)részben tekintheti meg.

Az Azure-integrációt használó Royal Render-ügyfélre például a medúza- [képek vásárlói történetében](https://customers.microsoft.com/story/jellyfishpictures)olvashat.

## <a name="using-azure-with-thinkbox-deadline"></a>Az Azure használata a Thinkbox határidővel

Az Azure Render hub támogatja a népszerű Render managereket, beleértve a határidőt is.  A render hub üzembe helyezésével és használatával kapcsolatos utasításokért lásd [a GitHub-tárházat](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Következő lépések

Próbálja ki a render Manager Azure Batch integrációját a megfelelő beépülő modullal és a GitHubon megjelenő utasításokkal, ahol lehetséges.
