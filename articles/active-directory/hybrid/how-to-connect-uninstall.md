---
title: Az Azure AD Connect eltávolítása
description: Ez a dokumentum leírja, Hogyan távolítható el Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96934184"
---
# <a name="uninstall-azure-ad-connect"></a>Az Azure AD Connect eltávolítása

Ez a dokumentum leírja, Hogyan távolítható el megfelelően a Azure AD Connect.

## <a name="uninstall-azure-ad-connect-from-the-server"></a>Azure AD Connect eltávolítása a kiszolgálóról
Először távolítsa el Azure AD Connect a-kiszolgálóról, amelyen fut.  Ehhez a következő lépések szükségesek:

 1. A Azure AD Connect rendszert futtató kiszolgálón navigáljon a **vezérlőpulthoz**.
 2. Kattintson **a** program eltávolítása program eltávolítása elemre. 
  ![](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. Válassza a **Azure ad Connect** lehetőséget.
 ![Azure AD Connect kiválasztása](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. Ha a rendszer kéri, kattintson az **Igen** gombra a megerősítéshez.
 5. Ez a megerősítés a Azure AD Connect képernyőt hozza létre.  Kattintson az **Eltávolítás** elemre.
 ![Eltávolítás](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. A művelet befejezése után kattintson a **Kilépés** gombra.
 7. ![Kilépés](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. A **Vezérlőpulton** kattintson a **frissítés** elemre, és az összes összetevőt el kell távolítani.


## <a name="next-steps"></a>Következő lépések

- További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
- [Az Azure AD Connect telepítése meglévő ADSync-adatbázis használatával](how-to-connect-install-existing-database.md)
- [Az Azure AD Connect telepítése SQL-lel delegált rendszergazdai engedélyekkel](how-to-connect-install-sql-delegation.md)

