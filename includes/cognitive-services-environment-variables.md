---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 95bd83575809f6ecda716ff751b47b7bb499cae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85073259"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Környezeti változó konfigurálása hitelesítéshez

Az alkalmazásoknak hitelesíteniük kell az általuk használt Cognitive Services hozzáférését. A hitelesítéshez javasoljuk, hogy hozzon létre egy környezeti változót az Azure-erőforrások kulcsainak tárolásához. 

Miután elvégezte a kulcsot, írja azt egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje le az értékét az `your-key` erőforrás egyik kulcsára.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Miután hozzáadta a környezeti változót, előfordulhat, hogy újra kell indítania minden futó programot, amelynek szüksége lehet a környezeti változó beolvasására, beleértve a konzolablakot is. Ha például a Visual studiót használja szerkesztőként, indítsa újra a Visual studiót a példa futtatása előtt.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[macOS](#tab/unix)

Módosítsa a .bash_profile fájlt, és adja hozzá a környezeti változót:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

***
