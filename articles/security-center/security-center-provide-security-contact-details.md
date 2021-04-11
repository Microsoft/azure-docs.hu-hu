---
title: E-mail-értesítések konfigurálása Azure Security Center riasztásokhoz
description: Ismerje meg, hogyan végezheti el a biztonsági riasztások Azure Security Center által küldött e-mailek típusának finomhangolását.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 04/07/2021
ms.author: memildin
ms.openlocfilehash: 96a389a581a9ecaddfc418824b3ebe9c780e6bd1
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011587"
---
# <a name="configure-email-notifications-for-security-alerts"></a>E-mail-értesítések konfigurálása biztonsági riasztásokhoz 

A biztonsági riasztásoknak el kell érniük a szervezete megfelelő személyeit. Alapértelmezés szerint Security Center e-mail-előfizetések tulajdonosai, amikor magas súlyosságú riasztást váltanak ki az előfizetéséhez. Ezen az oldalon megtudhatja, hogyan szabhatja testre ezeket az értesítéseket.

Az értesítő e-mailek saját beállításainak megadásához Azure Security Center **e-mail-értesítéseinek** beállításai oldalon a következő lehetőségek közül választhat:

- ***kinek* kell értesítést** küldeni – az e-maileket elküldheti az egyéni felhasználóknak vagy egy adott Azure-szerepkörrel rendelkező felhasználók számára az előfizetéshez. 
- ***mit* kell** kapniuk a szolgáltatásról – módosítsa azokat a súlyossági szinteket, amelyekre Security Center küldi az értesítéseket.

A riasztások fáradtságának elkerülése érdekében Security Center korlátozza a kimenő levelek mennyiségét. Az egyes előfizetésekhez Security Center a következőt küldi el:

- legfeljebb egy e-mail/ **6 óra** (naponta 4 e-mail) a **nagy súlyosságú** riasztásokhoz
- legfeljebb egy e-mail/ **12 óra** (naponta 2 e-mail) a **közepes súlyosságú** riasztásokhoz
- legfeljebb egy e-mail-cím **24 óránként** az **alacsony súlyosságú** riasztásokhoz

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="A biztonsági riasztásokkal kapcsolatos e-maileket fogadó partner adatainak konfigurálása." :::
 
## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|Ingyenes|
|Szükséges szerepkörök és engedélyek:|**Biztonsági rendszergazda**<br>**Előfizetés tulajdonosa** |
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||


## <a name="customize-the-security-alerts-email-notifications-via-the-portal"></a>A biztonsági riasztások e-mail-értesítéseinek testreszabása a portálon keresztül<a name="email"></a>
E-mailes értesítéseket küldhet magánszemélyeknek vagy az adott Azure-szerepkörökkel rendelkező felhasználóknak is.

1. A Security Center **díjszabása & beállítások** területen válassza ki a megfelelő előfizetést, és válassza az **e-mail értesítések** lehetőséget.

1. Adja meg az értesítések címzettjeit az alábbi lehetőségek egyikével vagy mindkettővel:

    - A legördülő listából válassza ki az elérhető szerepkörök közül.
    - Adjon meg konkrét e-mail-címeket vesszővel elválasztva. A megadható e-mail-címek száma nincs korlátozva.

1. A biztonsági kapcsolattartási adatok előfizetésre való alkalmazásához válassza a **Mentés** lehetőséget.

## <a name="customize-the-alerts-email-notifications-through-the-api"></a>A riasztások e-mail-értesítéseinek testreszabása az API-n keresztül
Az e-mailes értesítéseket a megadott REST APIon is kezelheti. A részletekért tekintse meg a [SECURITYCONTACTS API dokumentációját](/rest/api/securitycenter/securitycontacts).

Ez egy példa az PUT-kérelemre a biztonsági kapcsolatfelvételi konfiguráció létrehozásakor:

URI: https://management.azure.com/subscriptions/ <SubscriptionId> /providers/Microsoft.Security/securityContacts/default? API-Version = 2020-01 -01 – előzetes verzió

```json
{
    "properties": {
        "emails": admin@contoso.com;admin2@contoso.com,
        "notificationsByRole": {
            "state": "On",
            "roles": ["AccountAdmin", "Owner"]
        },
        "alertNotifications": {
            "state": "On",
            "minimalSeverity": "Medium"
        },
        "phone": ""
    }
}
```


## <a name="see-also"></a>Lásd még
A biztonsági riasztásokkal kapcsolatos további tudnivalókért tekintse meg a következő lapokat:

- [Biztonsági riasztások – útmutató](alerts-reference.md)a Azure Security Center veszélyforrások elleni védelmi moduljában esetlegesen megjelenő biztonsági riasztások megismeréséhez
- [Biztonsági riasztások kezelése és válaszadás a Azure Security Center-ben](security-center-managing-and-responding-alerts.md)– útmutató a biztonsági riasztások kezeléséhez és megválaszolásához
- [Munkafolyamat-automatizálás](workflow-automation.md)– a riasztásokra adott válaszok automatizálása egyéni értesítési logikával