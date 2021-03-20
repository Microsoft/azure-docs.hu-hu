---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "70737491"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Hiba történt a webalkalmazásból való ConditionHeadersNotSupported Azure Files böngészőből való használatával

A ConditionHeadersNotSupported hiba akkor fordul elő, amikor a Azure Files futtatott tartalmat egy olyan alkalmazáson keresztül éri el, amely feltételes fejléceket (például webböngészőt) használ, és a hozzáférés meghiúsul. Ez a hiba azt jelzi, hogy a feltétel fejlécei nem támogatottak.

![Feltételes fejlécek Azure Files hiba](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Ok

A feltételes fejlécek még nem támogatottak. Az azokat megvalósító alkalmazásoknak a fájlhoz való hozzáféréskor minden alkalommal le kell kérniük a teljes fájlt.

### <a name="workaround"></a>Áthidaló megoldás

Új fájl feltöltésekor a Cache-Control tulajdonság alapértelmezés szerint "no-cache". Ha szeretné kényszeríteni az alkalmazást, hogy minden alkalommal igényelje a fájlt, a fájl Cache-Control tulajdonságát frissíteni kell a "no-cache" értékről a "no-cache", a No-Store, a revalidate "műveletre. Ezt [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)használatával lehet megvalósítani.

![A Storage Explorer tartalom-gyorsítótárának módosítása Azure Files feltételes fejlécek esetében](media/storage-files-condition-headers/storage-explorer-cache.png)