---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d19c656946817b06cd620d8a48073bed8299af7d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502256"
---
A Azure PowerShell [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) parancsmag használatával hozzon létre egy Key Vault az előző lépésben létrehozott erőforráscsoportban. A következő információkat kell megadnia:

- Kulcstartó neve: 3–24 karakter hosszúságú sztring, amely csak számokat (0–9), betűket (a-z, A-Z) és kötőjeleket (-) tartalmazhat

  > [!Important]
  > Minden kulcstartónak egyedi névvel kell lennie. Cserélje <-your-unique-keyvault-name> a kulcstartó nevére a következő példákban.

- Erőforráscsoport neve: **myResourceGroup.**
- A hely: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "East US"
```

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott kulcstartó tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

- **Tároló neve:** A fenti --name paraméterhez megadott név.
- **Tároló** URI-ja: A példában ez &lt; https://-unique-keyvault-name &gt; .vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.
