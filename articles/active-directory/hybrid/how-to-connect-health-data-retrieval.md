---
title: Azure AD Connect Health utasítások adatok beolvasása | Microsoft Docs
description: Ez a lap leírja, hogyan lehet beolvasni az adatok Azure AD Connect Healthból való beolvasását.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89463521"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Az adatok lekérésére vonatkozó utasítások Azure AD Connect Health

Ez a dokumentum azt ismerteti, hogyan használható a Azure AD Connect az adatok Azure AD Connect Healthból való lekéréséhez.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>Az összes, az állapottal kapcsolatos riasztásokhoz konfigurált e-mail cím beolvasása.

Ha a riasztások fogadásához Azure AD Connect Health konfigurált összes felhasználó e-mail-címeit szeretné lekérni, kövesse az alábbi lépéseket.

1.  Indítsa el a Azure Active Directory Connect Health (állapot) panelt, és válassza a bal oldali navigációs sávon a **szolgáltatások szinkronizálása** lehetőséget.
 ![Szinkronizáló szolgáltatások](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  Kattintson a **riasztások** csempére.</br>
 ![Riasztás](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  Kattintson az **értesítési beállítások** elemre.
 ![Értesítés](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  Az **értesítési beállítások** panelen megtalálhatja azokat az e-mail-címeket, amelyek címzettjeiként engedélyezve vannak az állapot riasztási értesítéseiben.
 ![E-mail-címek](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>AD FS hibás jelszavakkal megjelölt fiókok beolvasása

A következő lépésekkel kérhet le olyan fiókokat, amelyek AD FS hibás jelszó-kísérlettel lettek megjelölve.

1.  A Azure Active Directory Health (állapot) paneltől kezdve válassza a **szinkronizálási hibák** elemet.
 ![Szinkronizálási hibák](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  A **szinkronizálási hibák** panelen kattintson az **Exportálás** elemre. Ekkor a rendszer exportálja a rögzített szinkronizálási hibák listáját.
 ![Exportálásálás](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>Következő lépések
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Az Azure AD Connect Health-ügynök telepítése](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health Operations (Az Azure AD Connect Health műveletei)](how-to-connect-health-operations.md)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](reference-connect-health-faq.md)
* [Azure AD Connect Health korábbi verziók](reference-connect-health-version-history.md)