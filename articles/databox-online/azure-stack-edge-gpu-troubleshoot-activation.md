---
title: A Azure Portal segítségével elháríthatja az Azure Stack Edge Pro-val kapcsolatos aktiválási hibákat a GPU-val | Microsoft Docs
description: Ismerteti, hogyan lehet elhárítani a Azure Stack Edge Pro GPU-aktiválást és a Key vaulttal kapcsolatos problémákat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 66e62bffe28cc10bd49e1456fdd6e2ac1faebf6e
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442145"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Az Azure Stack Edge Pro GPU-eszköz aktiválási problémáinak elhárítása 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan lehet elhárítani az aktiválási problémákat az Azure Stack Edge Pro GPU-eszközön. 


## <a name="activation-errors"></a>Aktiválási hibák

A következő táblázat összefoglalja az eszköz aktiválásával és a megfelelő ajánlott megoldással kapcsolatos hibákat.

| Hibaüzenet| Ajánlott megoldás |
|------------------------------------------------------|--------------------------------------|
| Ha az aktiváláshoz használt Azure Key Vault törölve lett, mielőtt az eszköz aktiválva lett az aktiválási kulccsal, akkor ezt a hibaüzenetet kapja. <br> ![Key Vault – 1. hiba](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | Ha a Key Vault törölve lett, akkor helyreállíthatja a kulcstartót, ha a tároló a kiürítési időtartam alatt van. Kövesse a [Key Vault helyreállításának](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates)lépéseit. <br>Ha a kiürítési védelem időtartama eltelt, akkor a kulcstartó nem állítható helyre. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától. |
| Ha a Azure Key Vault törölve lett az eszköz aktiválása után, majd megpróbál végrehajtani bármilyen műveletet, amely magában foglalja a titkosítást, például: **felhasználó hozzáadása**, **megosztás hozzáadása**, **számítás konfigurálása**, majd ezt a hibaüzenetet kapja. <br> ![Key Vault – 2. hiba](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | Ha a Key Vault törölve lett, akkor helyreállíthatja a kulcstartót, ha a tároló a kiürítési időtartam alatt van. Kövesse a Key Vault helyreállításának lépéseit. <br>Ha a kiürítési védelem időtartama eltelt, akkor a kulcstartó nem állítható helyre. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától. |
| Ha a Azure Key Vault csatorna integritási kulcsa törölve van, majd megpróbál végrehajtani bármilyen műveletet, amely magában foglalja a titkosítást, például: **felhasználó hozzáadása**, **megosztás hozzáadása**, **számítás konfigurálása** – ezt a hibaüzenetet fogja kapni. <br> ![Key Vault – 3. hiba](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | Ha a Key vaultban a csatorna integritási kulcsa törlődik, de továbbra is a kiürítés időtartama alá esik, kövesse a [Key Vault-kulcs eltávolításának visszavonása](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)című témakör lépéseit. <br>Ha a kiürítési védelem időtartama eltelt, és ha a kulcs biztonsági mentése megtörtént, akkor a biztonsági másolatból visszaállíthatja, hogy a kulcs nem állítható helyre. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától. |
| Ha az aktiválási kulcs létrehozása valamilyen hiba miatt meghiúsul, akkor ezt a hibaüzenetet kapja. További részletek jelennek meg az értesítésben. <br> ![Key Vault – 4. hiba](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Győződjön meg arról, hogy a tűzfal [mögötti hozzáférési Azure Key Vaultban](../key-vault/general/access-behind-firewall.md) megadott portok és URL-címek nyitva vannak a tűzfalon a Key Vault eléréséhez. Várjon néhány percet, és ismételje meg a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| Ha a felhasználó csak olvasási jogosultsággal rendelkezik, akkor a felhasználó nem jogosult az aktiválási kulcs előállítására, és ez a hiba jelenik meg. <br> ![Key Vault – 5. hiba](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | Ennek oka az lehet, hogy nincs megfelelő hozzáférése, vagy a  *Microsoft.* kulcstartó nincs regisztrálva.<li>Ellenőrizze, hogy rendelkezik-e tulajdonosi vagy közreműködői hozzáféréssel az Azure Stack Edge-erőforráshoz használt erőforráscsoport szintjén.</li><li>Győződjön meg arról, hogy a Microsoft. kulcstartó erőforrás-szolgáltató regisztrálva van. Az erőforrás-szolgáltató regisztrálásához lépjen az Azure Stack Edge-erőforráshoz használt előfizetésre. Lépjen az **erőforrás-szolgáltatók** elemre, keresse meg a *Microsoft.* kulcstartót, és válassza ki és **regisztrálja**.</li> |

## <a name="next-steps"></a>Következő lépések

- További információ az [eszközök problémáinak elhárításáról](azure-stack-edge-gpu-troubleshoot.md).
