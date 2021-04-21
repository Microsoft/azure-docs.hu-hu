---
title: A Azure Key Vault – Azure Key Vault
description: A titkos Azure Key Vault áttekintése.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: dc4368075e1cecb43887c9fab21e55b23208dbab
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814927"
---
# <a name="about-azure-key-vault-secrets"></a>Információk az Azure Key Vault titkos kódjairól

[Key Vault](../general/overview.md) általános titkos kulcsok, például jelszavak és adatbázis-kapcsolati sztringek biztonságos tárolását biztosítja.

A fejlesztők szemszögéből nézve a Key Vault API-k sztringként fogadják el és ják vissza a titkos értékeket. Belsőleg a Key Vault tárolja és kezeli a titkos kódokat oktettek sorozataiként (8 bites bájtok), amelyek mérete legfeljebb 25 bájt lehet. A Key Vault szolgáltatás nem biztosít szemantikát a titkos kulcsokhoz. Csupán fogadja az adatokat, titkosítja és tárolja azokat, és egy titkos azonosítót ("id") ad vissza. Az azonosítóval később lekérheti a titkos adatokat.  

A szigorúan bizalmas adatokhoz az ügyfeleknek ajánlott további adatvédelmi rétegeket is használni. Ez lehet például az adatok külön védelmi kulccsal történő titkosítása a Key Vaultba helyezés előtt.  

Key Vault a titkos kulcsok contentType mezőjét is támogatja. Az ügyfelek megadhatják a titkos adat tartalomtípusát, amely segít értelmezni a titkos adatokat a lekéréskor. A mező maximális hossza 255 karakter. Nincsenek előre definiált értékek. A javasolt használat a titkos adatok értelmezésének segítő tippe. Előfordulhat például, hogy egy implementáció titkos kulcsokként tárolja a jelszavakat és a tanúsítványokat, majd ezt a mezőt használja a megkülönböztetni. Nincsenek előre definiált értékek.  

## <a name="encryption"></a>Titkosítás

A tárolóban minden titkos Key Vault titkosítva van tárolva. Ez a titkosítás transzparens, és a felhasználó beavatkozását nem igényli. A Azure Key Vault a titkos kódokat a hozzáadáskor titkosítja, és automatikusan visszafejti azokat az olvasáskor. A titkosítási kulcs minden kulcstartó egyedi.

## <a name="secret-attributes"></a>Titkos attribútumok

A titkos adatok mellett a következő attribútumok is meg lehetnek adva:  

- *exp:* IntDate, optional, default is **forever**. Az *exp* (lejárati idő) attribútum azonosítja azt a lejárati időt, amely után a titkos adatokat NEM SZABAD lekérni, kivéve bizonyos [helyzetekben.](#date-time-controlled-operations) Ez a mező csak **tájékoztató** jellegű, mivel tájékoztatja a felhasználókat a Key Vault szolgáltatásról egy adott titkos kulcs nem használható. Az értékének egy IntDate értéket tartalmazó számnak kell lennie.   
- *nbf:* IntDate, optional, default is **now**. Az *nbf* (nem előtte) attribútum azonosítja azt az időt, amely előtt a titkos adatok NEM olvashatók be, kivéve bizonyos [helyzetekben.](#date-time-controlled-operations) Ez a mező csak **tájékoztató jellegű.** Az értékének egy IntDate értéket tartalmazó számnak kell lennie. 
- *enabled*: boolean, optional, default is **true**. Ez az attribútum határozza meg, hogy lekértek-e titkos adatokat. Az engedélyezett attribútum az *nbf* és *az exp* értékkel együtt használatos, ha egy művelet *az nbf* és *az exp* között történik, csak akkor lesz engedélyezett, ha az engedélyezve van **true (igaz) értékkel.** Az *nbf és* *az exp* ablakon kívüli műveletek automatikusan le vannak vonva, kivéve bizonyos [helyzetekben.](#date-time-controlled-operations)  

A válaszban további csak olvasható attribútumok is szerepelnek, amelyek titkos attribútumokat tartalmaznak:  

- *created*: IntDate, nem kötelező. A létrehozott attribútum jelzi, hogy mikor jött létre a titkos fájl ezen verziója. Ez az érték null értékű az attribútum összeadása előtt létrehozott titkos kulcsok esetén. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  
- *updated*: IntDate, nem kötelező. A frissített attribútum jelzi, hogy mikor frissült a titkos fájl ezen verziója. Ez az érték null értékű az attribútum összeadása előtt utoljára frissített titkos kulcsok esetén. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.

Az egyes Key Vault-objektumtípusokkal kapcsolatos gyakori attribútumokkal kapcsolatos információkért lásd a Azure Key Vault kulcsok, titkos kulcsok és tanúsítványok [áttekintését.](../general/about-keys-secrets-certificates.md)

### <a name="date-time-controlled-operations"></a>Dátum és idő által vezérelt műveletek

A titkos kód **get művelete** még nem érvényes és lejárt titkos kulcsokhoz is működik az *nbf*  /  *exp ablakon* kívül. A titkos kód get **műveletének** hívása még nem érvényes titkos kód esetén tesztelési célokra használható. Lejárt titkos kulcs **beolvasása**(leolvasása) helyreállítási műveletekhez használható.

## <a name="secret-access-control"></a>Titkoskulcs-hozzáférés vezérlése

Access Control által felügyelt titkos Key Vault a titkos kulcsokhoz a titkos adatokat tartalmazó Key Vault szintjén található meg. A titkos kulcsok hozzáférés-vezérlési szabályzata eltér az ugyanabban a kulcsban található kulcsok hozzáférés-vezérlési Key Vault. A felhasználók létrehozhatnak egy vagy több tárolót a titkos kulcsokhoz, és a forgatókönyv megfelelő szegmentálásának és a titkos kulcsok felügyeletének fenntartásához szükségesek.   

A következő engedélyek használhatók rendszerbiztonsági tagonként egy tároló titkos kulcs hozzáférés-vezérlési bejegyzésében, és szorosan tükrözik a titkos objektumon engedélyezett műveleteket:  

- A titkos tok kezeléséhez szükséges műveletek engedélyei
  - *get:* Titkos információ olvasása  
  - *list*: List the secrets or versions of a secret stored in a Key Vault  
  - *set:* Titkos adat létrehozása  
  - *delete:* Titkos adatokat törölhet  
  - *recover:* Törölt titkos adatok helyreállítása
  - *backup:* Titkos kulcs biztonsági mentése egy kulcstartóban
  - *restore:* Biztonsági másolatban tárolt titkos kulcs visszaállítása egy kulcstartóba

- Jogosultsági szintű műveletekre vonatkozó engedélyek
  - *purge:* Törölt titkos adatokat véglegesen törölhet (véglegesen törölhet)

További információ a titkos kulcsokról: Titkos kulcsok [műveletei a Key Vault REST API referenciában.](/rest/api/keyvault) További információ az engedélyek létrehozásáról: [Tárolók – Létrehozás](/rest/api/keyvault/vaults/createorupdate) vagy frissítés és tárolók [– Hozzáférési szabályzat frissítése.](/rest/api/keyvault/vaults/updateaccesspolicy) 

Útmutató a hozzáférés vezérléshez a Key Vault:
- [Hozzáférési szabályzat Key Vault a CLI használatával](../general/assign-access-policy-cli.md)
- [Új hozzáférési Key Vault hozzárendelése a PowerShell használatával](../general/assign-access-policy-powershell.md)
- [Hozzáférés-Key Vault hozzárendelése a Azure Portal](../general/assign-access-policy-portal.md)
- [Hozzáférés Key Vault kulcsokhoz, tanúsítványokhoz és titkos kulcsokhoz egy Azure-beli szerepköralapú hozzáférés-vezérléssel](../general/rbac-guide.md)

## <a name="secret-tags"></a>Titkos címkék  
További alkalmazásspecifikus metaadatokat is megadhat címkék formájában. Key Vault legfeljebb 15 címkét támogat, amelyek mindegyikének lehet 256 karakteres neve és 256 karakteres értéke.  

>[!Note]
>A címkéket a hívók akkor olvashatják, ha rendelkezik a *listával* vagy *kapják meg az* engedélyt.

## <a name="azure-storage-account-key-management"></a>Azure Storage-fiókkulcsok kezelése

Key Vault [Azure-tárfiókkulcsokat:](../../storage/common/storage-account-overview.md)

- Belsőleg a Key Vault azure-tárfiókkal listába (szinkronizálhatja) a kulcsokat. 
- Key Vault újragenerálja (elforgatja) a kulcsokat.
- A hívónak nem ad vissza kulcsértékeket.
- Key Vault a tárfiókok és a klasszikus tárfiókok kulcsait is kezeli.

További információkért lásd:
- [Tárfiók hozzáférési kulcsa](../../storage/common/storage-account-keys-manage.md)
- [Tárfiókkulcsok kezelése a Azure Key Vault](../secrets/overview-storage-keys.md))


## <a name="storage-account-access-control"></a>Tárfiók hozzáférés-vezérlése

A következő engedélyeket használhatja, amikor jogosultságot ad egy felhasználónak vagy alkalmazásnévnek arra, hogy műveleteket hajtson végre egy felügyelt tárfiókon:  

- Felügyelt tárfiók- és SaS-definíciós műveletek engedélyei
  - *get:* Egy tárfiókkal kapcsolatos információk lekérte 
  - *list:* List storage accounts managed by a Key Vault
  - *update*: Tárfiók frissítése
  - *delete:* Tárfiók törlése  
  - *recover*: Törölt tárfiók helyreállítása
  - *backup:* Tárfiók biztonsági mentése
  - *restore*: Biztonsági másolatból biztonsági másolatból biztonsági másolatba Key Vault
  - *set:* Tárfiók létrehozása vagy frissítése
  - *regeneratekey:* Tárfiók megadott kulcsértékének újragenerálása
  - *getsas:* Információ lekért egy tárfiók SAS-definíciójára
  - *listsas:* Storage SAS-definíciók listái egy tárfiókhoz
  - *deletesas:* SAS-definíció törlése egy tárfiókból
  - *setsas:* Tárfiók új SAS-definíciójának/attribútumainak létrehozása vagy frissítése

- Jogosultsági szintű műveletekre vonatkozó engedélyek
  - *purge*: Felügyelt tárfiók végleges törlése

További információért tekintse meg a tárfiókműveleteket [a Key Vault REST API referenciában.](/rest/api/keyvault) További információ az engedélyek létrehozásáról: [Tárolók – Létrehozás](/rest/api/keyvault/vaults/createorupdate) vagy frissítés és tárolók [– Hozzáférési szabályzat frissítése.](/rest/api/keyvault/vaults/updateaccesspolicy)

Útmutató a hozzáférés-vezérléshez a Key Vault:
- [Hozzáférési szabályzat Key Vault a CLI használatával](../general/assign-access-policy-cli.md)
- [Új hozzáférési Key Vault hozzárendelése a PowerShell használatával](../general/assign-access-policy-powershell.md)
- [Hozzáférés-Key Vault hozzárendelése a Azure Portal](../general/assign-access-policy-portal.md)
- [Hozzáférés Key Vault kulcsokhoz, tanúsítványokhoz és titkos kulcsokhoz egy Azure-beli szerepköralapú hozzáférés-vezérléssel](../general/rbac-guide.md)


## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a Key Vaultról](../general/overview.md)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](../general/about-keys-secrets-certificates.md)
- [Információ a kulcsokról](../keys/about-keys.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Biztonságos hozzáférés egy kulcstartóhoz](../general/security-features.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)