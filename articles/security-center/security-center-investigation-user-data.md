---
title: Azure Security Center vizsgálatban talált felhasználói adatkezelési szolgáltatás kezelése
description: " Megtudhatja, hogyan kezelheti a Azure Security Center vizsgálati funkciójában található felhasználói információkat. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 89d47f1fe941dad6a2450df86bfed2d25a1d8f7d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595665"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Azure Security Center vizsgálatban talált felhasználói adatkezelési szolgáltatás kezelése
Ez a cikk a Azure Security Center vizsgálati funkciójában található felhasználói adatok kezelésével kapcsolatos információkat tartalmazza. A vizsgálati adatvizsgálatok tárolása [Azure monitor naplókban](../azure-monitor/logs/log-query-overview.md) történik, és Security Center elérhető. A felhasználói adatkezelési funkció magában foglalja az adattörlési vagy-exportálási lehetőséget.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Személyes adatok keresése és azonosítása
A Azure Portalban a Security Center vizsgálati funkciója használható a személyes adatkereséshez. A vizsgálati funkció a **biztonsági riasztások** területen érhető el.

A vizsgálat funkció az **entitások** lapon lévő összes entitást, felhasználói információt és adatot megjeleníti.

## <a name="securing-and-controlling-access-to-personal-information"></a>Személyes információk védelme és a hozzáférés szabályozása
Az olvasó, a tulajdonos, a közreműködő vagy a fiók rendszergazdája által hozzárendelt Security Center felhasználó az eszközön belül férhet hozzá az ügyféladatok eléréséhez.

Az olvasói, a tulajdonosi és a közreműködő szerepkörökkel kapcsolatos további tudnivalókért tekintse meg az [Azure szerepköralapú hozzáférés-vezérlés beépített szerepköreit](../role-based-access-control/built-in-roles.md) . Az [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md) további információkat tudhatnak meg a fiók rendszergazdai szerepköréről.

## <a name="deleting-personal-data"></a>Személyes adatok törlése
A tulajdonos, közreműködő vagy fiók rendszergazdája által hozzárendelt Security Center felhasználó törölheti a vizsgálati adatokat.

Egy vizsgálat törléséhez `DELETE` kérelmet küldhet a Azure Resource Manager Rest APIba:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

A `incidentName` bemenet a kérelmeket használó összes incidens felsorolásával érhető el `GET` :

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Személyes adatok exportálása
A tulajdonos, közreműködő vagy fiók rendszergazdája által hozzárendelt Security Center felhasználó exportálhatja a vizsgálati adatokat. A vizsgálati információk exportálásához lépjen az **entitások** lapra a megfelelő információk másolásához és beillesztéséhez.

## <a name="next-steps"></a>Következő lépések
A felhasználói adatok kezelésével kapcsolatos további információkért lásd: [felhasználói adatok kezelése Azure Security Centerban](security-center-privacy.md).
Ha többet szeretne megtudni a személyes adatainak törléséről Azure Monitor naplókban, tekintse meg a [személyes adatainak exportálása és törlése](../azure-monitor/logs/personal-data-mgmt.md#how-to-export-and-delete-private-data)című témakört.