---
title: Ismert problémák a felügyelt identitásokkal kapcsolatban – Azure Active Directory
description: Ismert problémák az Azure-erőforrások felügyelt identitásával kapcsolatban.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8d2b6323a15595e57e7e89c6a83f9f718422e1d7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226922"
---
# <a name="known-issues-with-managed-identities"></a>Ismert problémák a felügyelt identitásokkal kapcsolatban

Ez a cikk néhány, a felügyelt identitások és a velük kapcsolatos probléma megoldását ismerteti. A felügyelt identitásokkal kapcsolatos gyakori kérdéseket a [gyakori](managed-identities-faq.md) kérdések című cikkben találja.
## <a name="vm-fails-to-start-after-being-moved"></a>A virtuális gép áthelyezése után nem indul el 

Ha egy adott virtuális gépet egy erőforráscsoport vagy előfizetés egy futó állapotában helyez át, az áthelyezés során továbbra is fut. Az áthelyezést követően azonban, ha a virtuális gép leáll és újraindul, nem fog elindulni. Ez a probléma azért fordul elő, mert a virtuális gép nem frissíti az Azure-erőforrások identitásához tartozó felügyelt identitásokra mutató hivatkozást, és továbbra is a régi erőforráscsoporthoz mutat.

**Áthidaló megoldás** 
 
Indítson el egy frissítést a virtuális gépen, hogy helyes értékeket kapjon az Azure-erőforrások felügyelt identitásai számára. A virtuális gép tulajdonságainak módosításával frissítheti az Azure-erőforrások identitásához tartozó felügyelt identitásokra mutató hivatkozást. Beállíthat például egy új címke értéket a virtuális gépen a következő paranccsal:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Ez a parancs egy "fixVM" nevű új címkét állít be a virtuális gépen 1 értékkel. 
 
Ennek a tulajdonságnak a beállításával a virtuális gép a megfelelő felügyelt identitásokkal frissíti az Azure-erőforrások erőforrás URI azonosítóját, majd el kell tudnia indítani a virtuális gépet. 
 
A virtuális gép elindítása után a címkét a következő parancs használatával távolíthatja el:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>Előfizetés továbbítása az Azure AD-címtárak között

A felügyelt identitások nem frissülnek, ha egy előfizetést áthelyeznek/átvisznek egy másik könyvtárba. Ennek eredményeképpen minden létező, rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitás megszakad. 

Megkerülő megoldás a felügyelt identitások számára egy másik könyvtárba áthelyezett előfizetésben:

 - A rendszerhez rendelt felügyelt identitások esetében: letiltás és újbóli engedélyezés. 
 - A felhasználóhoz rendelt felügyelt identitások esetében: törölje, hozza létre újra, majd csatolja újra a szükséges erőforrásokhoz (például virtuális gépekhez)

További információ: [Azure-előfizetés átadása egy másik Azure AD-címtárba](../../role-based-access-control/transfer-subscription.md).


## <a name="next-steps"></a>Következő lépések

Tekintse át a [felügyelt identitásokat támogató szolgáltatásokat](services-support-managed-identities.md) és a [gyakran ismételt kérdéseket](managed-identities-faq.md) ismertető cikket.
