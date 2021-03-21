---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760656"
---
## <a name="retrying-after-errors-and-exceptions"></a>Próbálkozás a hibák és kivételek után

A MSAL meghívásakor várhatóan saját újrapróbálkozási szabályzatokat kell megvalósítani. A MSAL HTTP-hívásokat végez az Azure AD szolgáltatásban, és időnként hibák léphetnek fel. Például a hálózat lemehet, vagy a kiszolgáló túlterhelt.  

### <a name="http-429"></a>HTTP 429

Ha a szolgáltatás-jogkivonat-kiszolgáló (STS) túl sok kéréssel túl van terhelve, a 429 HTTP-hibát adja vissza, amely azt jelzi, hogy mennyi ideig nem próbálkozhat újra a `Retry-After` Válasz mezőjében.