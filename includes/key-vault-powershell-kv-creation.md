---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 13d4bb94d3efb574895df6cfdf240cdeec77e539
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070194"
---
Az Azure PowerShell [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) parancsmaggal hozzon létre egy Key Vault az erőforráscsoporthoz az előző lépésben. A következő információkat kell megadnia:

- Key Vault neve: 3 – 24 karakterből álló karakterlánc, amely csak számokat (0-9), betűket (a-z, A-z) és kötőjeleket (-) tartalmazhat.

  > [!Important]
  > Minden kulcstartónak egyedi névvel kell rendelkeznie. A következő példákban cserélje le a <az egyedi-kulcstartó-Name> a Key Vault nevét.

- Erőforráscsoport neve: **myResourceGroup**.
- A hely: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "ContosoResourceGroup" -Location "East US"
```

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott kulcstartó tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

- Tár **neve**: a fenti--Name paraméterhez megadott név.
- Tároló **URI-ja**: a példában ez a https:// &lt; az egyedi-kulcstartó-neve &gt; . Vault.Azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.
