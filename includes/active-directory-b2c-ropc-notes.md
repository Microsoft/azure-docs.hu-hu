---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "78186995"
---
## <a name="ropc-flow-notes"></a>ROPC-folyamatok megjegyzései
Azure Active Directory B2C (Azure AD B2C) esetén a következő lehetőségek támogatottak:

- **Natív ügyfél**: a hitelesítés során felhasználói interakció történik, ha a kód egy felhasználói oldali eszközön fut. Az eszköz lehet egy natív operációs rendszer, például Android és iOS rendszerű mobil alkalmazás.
- **Nyilvános ügyféloldali folyamat**: a rendszer csak az alkalmazás által összegyűjtött felhasználói hitelesítő adatokat TOVÁBBÍTJA az API-hívásban. Az alkalmazás hitelesítő adatai nem lesznek elküldve.
- **Új jogcímek hozzáadása**: az azonosító jogkivonat tartalma módosítható új jogcímek hozzáadásához.

A következő folyamatok nem támogatottak:

- **Kiszolgálók közötti** kapcsolat: az Identity Protection rendszernek megbízható IP-címet kell begyűjtenie a hívótól (a natív ügyféltől) a beavatkozás részeként. Kiszolgálóoldali API-hívás esetén csak a kiszolgáló IP-címe van használatban. Ha túllépi a sikertelen hitelesítések dinamikus küszöbértékét, az Identity Protection rendszer azonosíthatja az ismétlődő IP-címet támadóként.
- **Bizalmas ügyféloldali folyamat**: az alkalmazás ügyfél-azonosítója érvényesítve van, de az alkalmazás titkos kulcsa nincs érvényesítve.

A ROPC folyamat használatakor vegye figyelembe a következőket:

- A ROPC nem működik, ha megszakad a felhasználói beavatkozást igénylő hitelesítési folyamat. Ha például egy jelszó lejárt vagy meg kell változtatnia, többtényezős hitelesítésre van szükség, vagy ha a bejelentkezés során további adatokat kell gyűjteni (például felhasználói beleegyezik).
- A ROPC csak a helyi fiókokat támogatja. A felhasználók nem jelentkezhetnek be olyan összevont identitás-szolgáltatókkal, mint például a Microsoft, a Google +, a Twitter, az AD-FS vagy a Facebook.
- A munkamenet-kezelés, beleértve a bejelentkezett KMSI is, nem alkalmazható.
