---
title: B2B együttműködési felhasználó naplózása és jelentése – Azure AD
description: A vendég felhasználói tulajdonságok Azure Active Directory B2B együttműködésben konfigurálhatók.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2f1478391eccaabcc4dbcd150b54376494d3f56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587527"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>B2B csoportmunka-felhasználó naplózása és jelentése
A vendég felhasználóival a felhasználókhoz hasonló naplózási funkciókkal rendelkezhet. 

## <a name="access-reviews"></a>Hozzáférési felülvizsgálatok
A hozzáférési felülvizsgálatok segítségével rendszeres időközönként ellenőrizheti, hogy a vendég felhasználóknak továbbra is szükségük van-e az erőforrásokhoz való hozzáférésre. A **hozzáférési felülvizsgálatok** szolgáltatás a **Azure Active Directory** **külső identitások**  >  **hozzáférési felülvizsgálatok**területén Azure Active Directory érhető el. A "hozzáférési felülvizsgálatok" kifejezésre is rákereshet a Azure Portal **összes szolgáltatásában** . A hozzáférési felülvizsgálatok használatának megismeréséhez lásd: [a vendég hozzáférésének kezelése az Azure ad hozzáférési felülvizsgálatokkal](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Auditnaplók

Az Azure AD-naplók rögzítik a rendszer-és felhasználói tevékenységeket, beleértve a vendég felhasználók által kezdeményezett tevékenységeket is. A naplók eléréséhez **Azure Active Directory**a **figyelés**területen válassza a **naplók**lehetőséget. Íme egy példa a Meghívási és visszaváltási előzményekre a meghívott Sam oogle:

![A napló kimenetét bemutató képernyőkép és példa](./media/auditing-and-reporting/audit-log.png)

Ezeket az eseményeket a részletek megismeréséhez is elsajátíthatja. Tekintse meg például az elfogadás részleteit.

![A tevékenység részleteinek kimenetét bemutató képernyőkép és példa](./media/auditing-and-reporting/activity-details.png)

Ezeket a naplókat az Azure AD-ből is exportálhatja, és az Ön által választott jelentéskészítő eszköz használatával testreszabott jelentéseket kaphat.

### <a name="next-steps"></a>További lépések

- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)

