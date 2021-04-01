---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/30/2020
ms.author: glenga
ms.openlocfilehash: f89383c1b7d0ccce82f35d91acbe1ff8c902db29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84448910"
---
::: zone pivot="programming-language-python"  
## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Virtuális környezet létrehozása és aktiválása

Egy megfelelő mappában futtassa a következő parancsokat a nevű virtuális környezet létrehozásához és aktiválásához `.venv` . Ügyeljen arra, hogy a Azure Functions által támogatott Python 3,8, 3,7 vagy 3,6-ot használja.

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Ha a Python nem telepítette a venv csomagot a Linux-disztribúcióban, futtassa a következő parancsot:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Az összes további parancsot futtatja ebben az aktivált virtuális környezetben.   
::: zone-end
