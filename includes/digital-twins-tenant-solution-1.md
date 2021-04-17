---
author: baanders
description: tartalmazza a bérlők közötti korlátozás jogkivonat-megoldását leíró fájlt a Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 5bc7e8af356ebe5968fe8a74783edc215788e5b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589340"
---
Ennek egyik módja a következő parancssori felületi parancs használata, ahol a az azure ad-bérlő azonosítója, amely a Azure Digital Twins `<home-tenant-ID>` tartalmazza:

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

A kérelem kérése után az identitás megkapja az Azure AD-erőforráshoz kiadott jogkivonatot, amely egyező bérlőazonosító-jogcímet kap a *https://digitaltwins.azure.net* Azure Digital Twins példányhoz. Ennek a jogkivonatnak az API-kérelmekben vagy a kóddal való használatával az összevont identitás hozzáférhet a Azure Digital Twins `Azure.Identity` erőforráshoz.