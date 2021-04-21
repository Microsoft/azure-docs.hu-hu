---
title: A Azure Key Vault tanúsítványok hozzáférés-vezérlésének
description: Az Azure Key Vault tanúsítványok hozzáférés-vezérlésének áttekintése
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 54874f30384d7f4827b13a597a469bfc67bc8fd2
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752479"
---
# <a name="certificate-access-control"></a>Tanúsítvány Access Control

 A tanúsítványok hozzáférés-vezérlését a Key Vault kezeli, és az adott tanúsítványokat tartalmazó Key Vault biztosítja. A tanúsítványok hozzáférés-vezérlési szabályzata eltér az ugyanabban a tanúsítványban található kulcsok és titkos kulcsok hozzáférés-vezérlési Key Vault. A felhasználók létrehozhatnak egy vagy több tárolót a tanúsítványok kezeléséhez a forgatókönyvek megfelelő szegmentálása és a tanúsítványok kezelése érdekében.  

 A következő engedélyek használhatók rendszerbiztonsági tagonként egy kulcstartó titkoskulcs-hozzáférés-vezérlési bejegyzésében, és szigorúan tükrözik a titkos objektumon engedélyezett műveleteket:  

- Engedélyek tanúsítványkezelési műveletekhez
  - **get:** Szerezze be az aktuális tanúsítványverziót vagy a tanúsítvány bármely verzióját
  - **list:** Az aktuális tanúsítványok vagy a tanúsítványok verzióinak felsorolása  
  - **update**: Tanúsítvány frissítése
  - **létrehozás:** Hozzon létre Key Vault tanúsítványt
  - **importálás:** Tanúsítványanyag importálása Key Vault tanúsítványba
  - **delete**: Tanúsítvány, házirend és annak összes verziója törlése  
  - **recover:** Törölt tanúsítvány helyreállítása
  - **biztonsági mentés:** Tanúsítvány biztonsági mentése egy kulcstartóban
  - **visszaállítás:** Biztonsági másolatból biztonsági másolatból visszaállítható egy kulcstartó
  - **managecontacts**: Key Vault-tanúsítványok névjegyének kezelése  
  - **manageissuers:** Key Vault hitelesítésszolgáltató vagy kiállító kezelése
  - **getissuers:** Tanúsítvány hitelesítésszolgáltatóinak/kiállítóinak lekéri
  - **listissuers:** Egy tanúsítvány hitelesítésszolgáltatóinak/kiállítóinak felsorolása  
  - **setissuers:** Hozzon létre vagy frissítsen egy Key Vault hitelesítésszolgáltatóit/kiállítóit  
  - **deleteissuers:** Key Vault hitelesítésszolgáltatói/kiállítóinak törlése  
 
- Jogosultsági szintű műveletekre vonatkozó engedélyek
  - **purge**: Törölt tanúsítvány végleges törlése (végleges törlése)

További információért tekintse meg [a tanúsítványműveleteket a Key Vault REST API referenciában.](/rest/api/keyvault) További információ az engedélyek létrehozásáról: [Tárolók – Hozzáférési szabályzat frissítése.](/rest/api/keyvault/vaults/updateaccesspolicy)

## <a name="troubleshoot"></a>Hibaelhárítás
Hiányzó hozzáférési szabályzat miatt hibaüzenet jelenhet meg. A hiba ```Error type : Access denied or user is unauthorized to create certificate``` elhárításához például tanúsítványokat kell hozzáadnia/engedélyeket kell létrehoznia.

## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a Key Vaultról](../general/overview.md)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](../general/about-keys-secrets-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
