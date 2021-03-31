---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760697"
---
## <a name="conditional-access-and-claims-challenges"></a>A feltételes hozzáférés és a jogcímek kihívásai

A tokenek csendes lekérése esetén előfordulhat, hogy az alkalmazás hibát jelez, ha egy olyan API-val kapcsolatos [feltételes hozzáférési jogcímet](../articles/active-directory/develop/v2-conditional-access-dev-guide.md) igényel, mint például az MFA-szabályzat.

A hiba kezelésére szolgáló minta a jogkivonat interaktív beszerzése a MSAL használatával. Ez megkéri a felhasználót, és lehetőséget biztosít számukra a kötelező feltételes hozzáférési szabályzat kielégítésére.

Bizonyos esetekben, amikor feltételes hozzáférést igénylő API-t hív meg, az API-val kapcsolatos hiba esetén a jogcím-kihívás is megjelenhet. Ha például a feltételes hozzáférési házirend felügyelt eszközzel (Intune) rendelkezik, a hiba a következőhöz hasonló lesz [: AADSTS53000: az eszköznek az erőforrás eléréséhez](../articles/active-directory/develop/reference-aadsts-error-codes.md) vagy hasonlóhoz kell tartoznia. Ebben az esetben átadhatja a jogcímeket a beszerzési jogkivonat hívásában, hogy a rendszer kérje a felhasználótól a megfelelő szabályzat teljesítését.
