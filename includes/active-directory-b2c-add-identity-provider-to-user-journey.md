---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674232"
---
## <a name="add-a-user-journey"></a>Felhasználói út hozzáadása 

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a bejelentkezési oldalakon még nem érhető el. Ha nem rendelkezik saját egyéni felhasználói útra, hozzon létre egy másolatot egy meglévő sablon felhasználói utazásról, máskülönben folytassa a következő lépéssel. 

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
1. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
1. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
1. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
1. Nevezze át a felhasználói út azonosítóját. Például: `Id="CustomSignUpSignIn"`.

## <a name="add-the-identity-provider-to-a-user-journey"></a>Az identitás-szolgáltató hozzáadása felhasználói útra 

Most, hogy van egy felhasználói útja, adja hozzá az új identitás-szolgáltatót a felhasználói útra. Először vegyen fel egy bejelentkezési gombot, majd csatolja a gombot egy művelethez. A művelet a korábban létrehozott technikai profil.

1. Megkeresheti a előkészítési lépés elemét, amely tartalmazza a `Type="CombinedSignInAndSignUp"` vagy a `Type="ClaimsProviderSelection"` felhasználói utat. Ez általában az első előkészítési lépés. A **ClaimsProviderSelections** elem tartalmazza azon identitás-szolgáltatók listáját, amelyekkel a felhasználó be tud jelentkezni. Az elemek sorrendje vezérli a felhasználó számára bemutatott bejelentkezési gombok sorrendjét. Adjon hozzá egy **ClaimsProviderSelection** XML-elemet. Állítsa a **TargetClaimsExchangeId** értékét egy rövid névre.

1. A következő előkészítési lépésben adjon hozzá egy **ClaimsExchange** elemet. Állítsa be az **azonosítót** a cél jogcím Exchange-azonosító értékére. frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott technikai profil azonosítójára.

A következő XML azt mutatja be, hogy a felhasználói utazás első két lépését az identitás-szolgáltató biztosítja: