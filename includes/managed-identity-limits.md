---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 6e5885e076222cd23ba127f3be41c1218f327ca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92379745"
---
- Az egyes felügyelt identitások az Azure ad-bérlőben az [Azure ad szolgáltatás korlátai és korlátozásai](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md)című témakörben leírtak szerint számítanak bele az objektum-kvótára.
-   A felügyelt identitások létrehozásának sebessége a következő korlátozásokkal rendelkezhet:

    1. Azure AD-bérlő/Azure-régió: 200 művelet/20 másodperc.
    2. Azure-előfizetések/Azure-régiók: 40 művelet/20 másodperc.

- Amikor felhasználó által hozzárendelt felügyelt identitásokat hoz létre, csak alfanumerikus karakterek (0-9, a-z és A-Z) és a kötőjel (-) támogatottak. Ahhoz, hogy a hozzárendelés egy virtuális géphez vagy virtuálisgép-méretezési csoporthoz megfelelően működjön, a név legfeljebb 24 karakter hosszú lehet.
