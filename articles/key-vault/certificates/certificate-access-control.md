---
title: Tudnivalók Azure Key Vault tanúsítványok hozzáférés-vezérléséről
description: A Azure Key Vault tanúsítványok hozzáférés-vezérlésének áttekintése
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92128640"
---
# <a name="certificate-access-control"></a>Tanúsítvány Access Control

 A tanúsítványok hozzáférés-vezérlését a Key Vault kezeli, és az adott tanúsítványokat tartalmazó Key Vault biztosítja. A tanúsítványok hozzáférés-vezérlési házirendje különbözik az azonos Key Vault található kulcsok és titkok hozzáférés-vezérlési házirendjeitől. A felhasználók egy vagy több tárolót hozhatnak létre a tanúsítványok tárolására, a forgatókönyvek megfelelő szegmentálásának és a tanúsítványok kezelésének fenntartása érdekében.  

 A következő engedélyek a Key Vault Secrets hozzáférés-vezérlési bejegyzésében, és a titkos objektumon engedélyezett műveletek részletes tükrözése céljából használhatók:  

- Tanúsítványkezelő műveletekhez szükséges engedélyek
  - **beolvasás**: a tanúsítvány aktuális verziójának vagy bármely verziójának lekérése
  - **lista**: a tanúsítvány aktuális tanúsítványait vagy verzióit sorolja fel.  
  - **frissítés**: tanúsítvány frissítése
  - **Létrehozás**: Key Vault tanúsítvány létrehozása
  - **Importálás**: tanúsítvány importálása Key Vault tanúsítványba
  - **Törlés**: tanúsítvány törlése, házirendje és minden verziója  
  - **helyreállítás**: törölt tanúsítvány helyreállítása
  - **biztonsági mentés**: tanúsítvány biztonsági mentése kulcstartóban
  - **visszaállítás**: biztonsági másolat készítése a tanúsítványról egy kulcstartóra
  - **managecontacts**: Key Vault tanúsítványok kapcsolatainak kezelése  
  - **manageissuers**: Key Vault hitelesítésszolgáltatók/kiállítók kezelése
  - **getissuers**: tanúsítvány hitelesítő szerveinek/kiállítóinak beszerzése
  - **listissuers**: a tanúsítvány hatóságainak/kiállítóinak listázása  
  - **setissuers**: Key Vault tanúsítvány szerveinek/kiállítóinak létrehozása vagy frissítése  
  - **deleteissuers**: Key Vault tanúsítvány hatóságainak/kiállítóinak törlése  
 
- Jogosultsági szintű műveletek engedélyei
  - **kiürítés**: törölt tanúsítvány törlése (végleges törlése)

További információkért tekintse meg a [tanúsítványok műveleteit a Key Vault REST API-referenciában](/rest/api/keyvault). További információ az engedélyek létrehozásáról: tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="troubleshoot"></a>Hibaelhárítás
A hiányzó hozzáférési szabályzat miatt hiba jelenhet meg. A ```Error type : Access denied or user is unauthorized to create certificate``` hiba elhárításához például tanúsítványokat vagy létrehozási engedélyt kell hozzáadnia.

## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a Key Vaultról](../general/overview.md)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](../general/about-keys-secrets-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
