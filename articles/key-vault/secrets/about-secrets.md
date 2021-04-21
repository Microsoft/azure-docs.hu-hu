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
ms.openlocfilehash: 6d4f3f744a85c14c42ffef1c894b237081e871f8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752425"
---
# <a name="about-azure-key-vault-secrets"></a>Információk az Azure Key Vault titkos kódjairól

[Key Vault](../general/overview.md) általános titkos kulcsok, például jelszavak és adatbázis-kapcsolati sztringek biztonságos tárolását biztosítja.

A fejlesztők szemszögéből nézve a Key Vault API-k sztringekként fogadják el és ják vissza a titkos értékeket. Belsőleg a Key Vault tárolja és kezeli a titkos kódokat oktettek sorozataiként (8 bites), és legfeljebb 25 bájt méretűek. A Key Vault szolgáltatás nem biztosít szemantikát a titkos kulcsokhoz. Csupán fogadja az adatokat, titkosítja és tárolja azokat, és egy titkos azonosítót ("id") ad vissza. Az azonosítóval később lekérheti a titkos adatokat.  

A szigorúan bizalmas adatokhoz az ügyfeleknek ajánlott további adatvédelmi rétegeket is használni. Ez lehet például az adatok külön védelmi kulccsal történő titkosítása a Key Vaultba helyezés előtt.  

Key Vault a titkos kulcsok contentType mezőjét is támogatja. Az ügyfelek megadhatják a titkos adatok lekérhető titkos adatainak értelmezését segítő titkos adatokat. A mező maximális hossza 255 karakter. Nincsenek előre definiált értékek. A javasolt használat a titkos adatok értelmezésének tippe. Előfordulhat például, hogy egy implementáció titkos kulcsokként tárolja a jelszavakat és a tanúsítványokat, majd ezt a mezőt használja a megkülönböztetésére. Nincsenek előre definiált értékek.  

## <a name="encryption"></a>Titkosítás

A tárolóban tárolt Key Vault titkosítva vannak tárolva. Ez a titkosítás átlátható, és nem igényel műveletet a felhasználótól. A Azure Key Vault titkosítja a titkos kulcsokat, amikor hozzáadja őket, és automatikusan visszafejti azokat az olvasásukkor. A titkosítási kulcs minden kulcstartó egyedi.

## <a name="secret-attributes"></a>Titkos attribútumok

A titkos adatok mellett a következő attribútumok is meg lehetnek adva:  

- *exp:* IntDate, optional, default is **forever**. Az *exp* (lejárati idő) attribútum azonosítja azt a lejárati időt, amely után a titkos adatokat NEM SZABAD lekérni, kivéve bizonyos [helyzeteket.](#date-time-controlled-operations) Ez a mező csak **tájékoztató** jellegű, mivel tájékoztatja a felhasználókat a Key Vault szolgáltatásról, hogy egy adott titkos kulcsot nem lehet használni. Az értékének egy IntDate értéket tartalmazó számnak kell lennie.   
- *nbf:* IntDate, optional, default is **now**. Az *nbf* (nem előtte) attribútum azonosítja azt az időt, amely előtt a titkos adatok NEM olvashatók be, kivéve bizonyos [helyzetekben.](#date-time-controlled-operations) Ez a mező csak **tájékoztató jellegű.** Az értékének egy IntDate értéket tartalmazó számnak kell lennie. 
- *enabled*: boolean, optional, default is **true**. Ez az attribútum határozza meg, hogy lekérhetők-e a titkos adatok. Az engedélyezett attribútum az *nbf* és *az exp* értékkel együtt használatos, ha egy művelet *az nbf* és *az exp* között történik, csak akkor engedélyezett, ha az engedélyezve van true **(igaz) értékkel.** Az *nbf* és *az exp* ablakon kívüli műveletek automatikusan le vannak vonva, kivéve bizonyos [helyzetekben.](#date-time-controlled-operations)  

A válaszban további csak olvasható attribútumok is szerepelnek, amelyek titkos attribútumokat is tartalmaznak:  

- *created*: IntDate, nem kötelező. A létrehozott attribútum jelzi, hogy mikor lett létrehozva a titkos fájl ezen verziója. Ez az érték null értékű az attribútum összeadása előtt létrehozott titkos kulcsok esetén. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  
- *updated*: IntDate, nem kötelező. A frissített attribútum jelzi, hogy mikor frissült a titkos fájl ezen verziója. Ez az érték null értékű az attribútum összeadása előtt utoljára frissített titkos kulcsok esetén. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.

Az egyes Key Vault-objektumtípusokkal kapcsolatos gyakori attribútumokkal kapcsolatos információkért lásd a Azure Key Vault kulcsokat, titkos kulcsokat és [tanúsítványokat áttekintő témakört.](../general/about-keys-secrets-certificates.md)

### <a name="date-time-controlled-operations"></a>Dátum és idő által vezérelt műveletek

A titkos kód **get művelete** az *nbf* exp ablakon kívül működik a még nem érvényes és lejárt  /  *titkos kódokkal.* A titkos kód get **műveletének** hívása egy még nem érvényes titkos kódhoz tesztelési célokra használható. Lejárt titkos kulcs **leolvasása**(leolvasása) helyreállítási műveletekhez használható.

## <a name="secret-access-control"></a>Titkoskulcs-hozzáférés vezérlése

Access Control által felügyelt titkos kulcsok Key Vault a titkos adatokat tartalmazó titkos Key Vault szintjén biztosítanak. A titkos kulcsok hozzáférés-vezérlési szabályzata eltér az ugyanabban a kulcsban található kulcsok hozzáférés-vezérlési Key Vault. A felhasználók létrehozhatnak egy vagy több tárolót a titkos kulcsokhoz, és a forgatókönyvek megfelelő szegmentálásának és a titkos kulcsok felügyeletének fenntartásához szükségesek.   

A következő engedélyek használhatók rendszerbiztonsági tagonként egy tároló titkos kulcs hozzáférés-vezérlési bejegyzésében, és szorosan tükrözik a titkos objektumon engedélyezett műveleteket:  

- Titkos tok kezeléséhez szükséges engedélyek
  - *get*: Titkos információ olvasása  
  - *list:* List the secrets or versions of a secret stored in a Key Vault  
  - *set*: Titkos adat létrehozása  
  - *delete:* Titkos adatokat törölhet  
  - *recover:* Törölt titkos adatok helyreállítása
  - *biztonsági mentés:* Titkos kulcs biztonsági mentése egy kulcstartóban
  - *restore*: Biztonsági másolatból biztonsági másolatból biztonsági másolatból visszaállt egy kulcstartóra

- Jogosultsági szintű műveletekre vonatkozó engedélyek
  - *purge*: Törölt titkos fájl végleges törlése (végleges törlése)

További információ a titkos kulcsokról: Titkos kulcsok [műveletei a Key Vault REST API referenciában.](/rest/api/keyvault) További információ az engedélyek létrehozásáról: [Tárolók – Létrehozás](/rest/api/keyvault/vaults/createorupdate) vagy frissítés és tárolók [– Hozzáférési szabályzat frissítése.](/rest/api/keyvault/vaults/updateaccesspolicy) 

Útmutató a hozzáférés-vezérléshez a Key Vault:
- [Hozzáférés-Key Vault hozzárendelése a CLI használatával](../general/assign-access-policy-cli.md)
- [Hozzáférés-Key Vault hozzárendelése a PowerShell használatával](../general/assign-access-policy-powershell.md)
- [Hozzáférés-Key Vault hozzárendelése a Azure Portal](../general/assign-access-policy-portal.md)
- [Hozzáférést biztosít Key Vault kulcsokhoz, tanúsítványokhoz és titkos kulcsokhoz egy Azure-beli szerepköralapú hozzáférés-vezérléssel](../general/rbac-guide.md)

## <a name="secret-tags"></a>Titkos címkék  
További alkalmazásspecifikus metaadatokat is megadhat címkék formájában. Key Vault legfeljebb 15 címkét támogat, amelyek mindegyikének lehet 256 karakteres neve és 256 karakteres értéke.  

>[!Note]
>A címkéket a hívók akkor olvashatják, ha rendelkezik a *listával* vagy *kapják meg az* engedélyt.

## <a name="azure-storage-account-key-management"></a>Azure Storage-fiókkulcsok kezelése

Key Vault [Azure Storage-fiókkulcsokat:](../../storage/common/storage-account-overview.md)

- Belsőleg a Key Vault kilistával (szinkronizálhatja) a kulcsokat egy Azure Storage-fiókkal. 
- Key Vault újra létrehozza (elforgatja) a kulcsokat.
- A hívónak adott válaszban a kulcsértékek soha nem ulnak vissza.
- Key Vault a tárfiókok és a klasszikus tárfiókok kulcsait is kezeli.

További információkért lásd:
- [Tárfiók hozzáférési kulcsa](../../storage/common/storage-account-keys-manage.md)
- [Tárfiókkulcsok kezelése a Azure Key Vault](../secrets/overview-storage-keys.md))


## <a name="storage-account-access-control"></a>Tárfiók hozzáférés-vezérlése

A következő engedélyekkel lehet műveleteket végezni egy felügyelt tárfiókon egy felhasználó vagy alkalmazásnév számára:  

- Engedélyek felügyelt tárfiókhoz és SaS-definíciós műveletekhez
  - *get*: Egy tárfiókkal kapcsolatos információk lekérte 
  - *list:* List storage accounts managed by a Key Vault
  - *update*: Tárfiók frissítése
  - *delete:* Tárfiók törlése  
  - *recover:* Törölt tárfiók helyreállítása
  - *biztonsági mentés:* Tárfiók biztonsági mentése
  - *restore*: Biztonsági másolatból biztonsági másolatból biztonsági másolatba Key Vault
  - *set*: Tárfiók létrehozása vagy frissítése
  - *regeneratekey:* Tárfiók megadott kulcsértékének újragenerálása
  - *getsas:* Információ lekért egy tárfiók SAS-definíciójára
  - *listsas:* Tárfiók tároló SAS-definícióinak listázza
  - *deletesas:* SAS-definíció törlése tárfiókból
  - *setsas:* Tárfiók új SAS-definíciójának/attribútumainak létrehozása vagy frissítése

- Jogosultsági szintű műveletekre vonatkozó engedélyek
  - *purge:* Felügyelt tárfiók végleges törlése

További információért tekintse meg a [tárfiókműveleteket a Key Vault REST API között.](/rest/api/keyvault) További információ az engedélyek létrehozásáról: [Tárolók – Létrehozás](/rest/api/keyvault/vaults/createorupdate) vagy frissítés és tárolók [– Hozzáférési szabályzat frissítése.](/rest/api/keyvault/vaults/updateaccesspolicy)

Útmutató a hozzáférés vezérléshez a Key Vault:
- [Hozzáférés-Key Vault hozzárendelése a CLI használatával](../general/assign-access-policy-cli.md)
- [Hozzáférés-Key Vault hozzárendelése a PowerShell használatával](../general/assign-access-policy-powershell.md)
- [Hozzáférés-Key Vault hozzárendelése a Azure Portal](../general/assign-access-policy-portal.md)
- [Hozzáférést biztosít Key Vault kulcsokhoz, tanúsítványokhoz és titkos kulcsokhoz egy Azure-beli szerepköralapú hozzáférés-vezérléssel](../general/rbac-guide.md)


## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a Key Vaultról](../general/overview.md)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](../general/about-keys-secrets-certificates.md)
- [Információ a kulcsokról](../keys/about-keys.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Biztonságos hozzáférés egy kulcstartóhoz](../general/security-overview.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)