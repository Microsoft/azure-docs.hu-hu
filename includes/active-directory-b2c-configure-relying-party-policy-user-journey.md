---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98951526"
---
## <a name="configure-the-relying-party-policy"></a>A függő entitás házirendjének konfigurálása

A függő entitások házirendje (például [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)) Megadja azt a felhasználói utat, amelyet a Azure ad B2C végre fog hajtani. A **DefaultUserJourney** elem megkeresése a [függő entitáson](../articles/active-directory-b2c/relyingparty.md)belül. Frissítse a  **ReferenceId** , hogy az megfeleljen a felhasználói útvonal azonosítójának, amelyben felvette az identitás-szolgáltatót. 

Az alábbi példában a `CustomSignUpOrSignIn` felhasználói útra a **ReferenceId** a következőre van beállítva `CustomSignUpOrSignIn` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Egyéni szabályzat feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza az **egyéni házirend feltöltése** lehetőséget, majd töltse fel a módosított két házirendet, a következő sorrendben: a kiterjesztési szabályzatot, például `TrustFrameworkExtensions.xml` a függő entitás házirendjét, például: `SignUpSignIn.xml` .



