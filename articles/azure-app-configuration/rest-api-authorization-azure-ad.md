---
title: Azure app Configuration REST API – Azure Active Directory engedélyezés
description: Azure Active Directory használata az Azure-alkalmazások konfigurálásához a REST API használatával
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: c028dcf6649da9abd196997d077b58386a5e6dd9
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092796"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory Authorization-REST API referenciája

Ha Azure Active Directory (Azure AD) hitelesítést használ, az engedélyezést szerepköralapú hozzáférés-vezérléssel (RBAC) kezeli a rendszer. A RBAC használatához a felhasználóknak hozzá kell rendelniük a szerepköröket az erőforrásokhoz való hozzáférés biztosítása érdekében. Minden szerepkör olyan műveleteket tartalmaz, amelyeket a szerepkörhöz rendelt felhasználók képesek végrehajtani.

## <a name="roles"></a>Szerepkörök

Alapértelmezés szerint az alábbi szerepkörök érhetők el az Azure-előfizetésekben:

- **Azure-alkalmazás konfigurációs adatának tulajdonosa**: Ez a szerepkör teljes hozzáférést biztosít az összes művelethez.
- **Azure-alkalmazás konfigurációs Adatolvasója**: Ez a szerepkör lehetővé teszi az olvasási műveleteket.

## <a name="actions"></a>Műveletek

A szerepkörök tartalmazzák az adott szerepkörhöz rendelt felhasználók által végrehajtható műveletek listáját. Az Azure app Configuration a következő műveleteket támogatja:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Ez a művelet lehetővé teszi az alkalmazás-konfigurációs kulcs-érték erőforrások, például a/KV és a/labels. olvasási hozzáférését
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Ez a művelet lehetővé teszi az írási hozzáférést az alkalmazás konfigurációs kulcs-érték erőforrásaihoz.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Ez a művelet lehetővé teszi az alkalmazások konfigurációs kulcs-érték erőforrásainak törlését. Vegye figyelembe, hogy egy erőforrás törlése visszaadja a törölt kulcs-értéket.

## <a name="error"></a>Hiba

```http
HTTP/1.1 403 Forbidden
```

**OK:** A kérést készítő résztvevő nem rendelkezik a kért művelet végrehajtásához szükséges engedélyekkel.
**Megoldás:** Rendelje hozzá a kért művelet végrehajtásához szükséges szerepkört a kérést elvégző résztvevőhöz.

## <a name="managing-role-assignments"></a>Szerepkör-hozzárendelések kezelése

A szerepkör-hozzárendeléseket az összes Azure-szolgáltatásban szabványos [Azure RBAC eljárások](../role-based-access-control/overview.md) segítségével kezelheti. Ezt az Azure CLI, a PowerShell és a Azure Portal használatával teheti meg. További információ: Azure- [szerepkörök kiosztása a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).