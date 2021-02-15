---
title: Azure Key Vault Secrets – Azure Key Vault
description: Azure Key Vault titkok áttekintése.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 55814dff5cba572e2e22b5a0f9971bc920e32372
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526637"
---
# <a name="about-azure-key-vault-secrets"></a>Tudnivalók a Azure Key Vault titkairól

A [Key Vault](../general/overview.md) az általános titkok, például a jelszavak és az adatbázis-kapcsolatok karakterláncok biztonságos tárolását teszi lehetővé.

Fejlesztői szempontból Key Vault API-k elfogadják és visszaadják a titkos értékeket karakterláncként. Belsőleg Key Vault a titkokat az oktettek (8 bites bájtok) sorozatának megfelelően tárolja és kezeli, és a 25k maximális mérete (bájt). A Key Vault szolgáltatás nem biztosít szemantikai adatokat a titkokhoz. Csupán elfogadja az adatot, titkosítja, tárolja, és visszaadja a titkos azonosítót ("id"). Az azonosító segítségével később is lekérheti a titkos kulcsot.  

A szigorúan bizalmas adatokhoz az ügyfeleknek ajánlott további adatvédelmi rétegeket is használni. Ez lehet például az adatok külön védelmi kulccsal történő titkosítása a Key Vaultba helyezés előtt.  

A Key Vault a Secrets (contentType) mezőt is támogatja. Az ügyfelek megadhatják a titkos kód tartalomtípusát, hogy segítséget nyújtsanak a titkos adatokat a beolvasás során. A mező maximális hossza 255 karakter. Nincsenek előre definiált értékek. A javasolt használat a titkos adatok értelmezésére utal. Előfordulhat például, hogy egy implementáció titkos kulcsként tárolja a jelszavakat és a tanúsítványokat, majd ezt a mezőt használja a megkülönböztetéshez. Nincsenek előre definiált értékek.  

## <a name="encryption"></a>Titkosítás

A Key Vault összes titkát titkosítva tárolja a rendszer. Ez a titkosítás transzparens, és nem igényel műveletet a felhasználótól. A Azure Key Vault szolgáltatás titkosítja a titkot, amikor hozzáadja őket, és automatikusan visszafejti azokat a beolvasás során. A titkosítási kulcs egyedi az egyes kulcstartók esetében.

## <a name="secret-attributes"></a>Titkos attribútumok

A titkos adatok mellett a következő attribútumok is megadhatók:  

- *exp*: IntDate, nem kötelező, az alapértelmezett érték **örökre**. Az *exp* (lejárati idő) attribútum azt a lejárati időt határozza meg, amely után a titkos adatok nem kérhetők le, kivéve [bizonyos helyzetekben](#date-time-controlled-operations). Ez a mező csak **tájékoztató** jellegű, mivel a Key Vault szolgáltatás felhasználóit nem használja fel, mert egy adott titok nem használható. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.   
- *NBF*: IntDate, nem kötelező, alapértelmezés szerint **most**. A *NBF* (nem korábban) attribútum azt az időpontot határozza meg, ameddig a titkos adatokat nem lehet lekérni, kivéve [bizonyos helyzetekben](#date-time-controlled-operations). Ez a mező csak **tájékoztató** jellegű. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie. 
- *engedélyezve*: logikai, nem kötelező, az alapértelmezett érték **true (igaz**). Ez az attribútum határozza meg, hogy a titkos adatot lehet-e lekérni. Az enabled attribútum a *NBF* és az *exp* együttes használata esetén használatos, ha a *NBF* és az *exp* közötti művelet történik, akkor csak akkor lesz engedélyezve, ha a beállítás értéke **true (igaz**). A *NBF* és az *exp* ablakon kívüli műveletek automatikusan le lesznek tiltva, kivéve [bizonyos helyzetekben](#date-time-controlled-operations).  

A titkos attribútumokat tartalmazó válaszokban további írásvédett attribútumok is szerepelnek:  

- *Létrehozva*: IntDate, nem kötelező. A létrehozott attribútum azt jelzi, hogy a titkos kulcs ezen verzióját hozta-e létre. Ez az érték null értékű az attribútum hozzáadása előtt létrehozott titkok esetében. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  
- *frissítve*: IntDate, nem kötelező. A frissített attribútum azt jelzi, hogy a titkos kulcs ezen verziója frissítve lett-e. Ez az érték null értékű azoknál a titkoknál, amelyeket az attribútum hozzáadása előtt utoljára frissítettek. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.

A Key Vault-objektumtípusok általános attribútumaival kapcsolatos információkért lásd: [Azure Key Vault kulcsok, titkok és tanúsítványok áttekintése](../general/about-keys-secrets-certificates.md)

### <a name="date-time-controlled-operations"></a>Dátum-idő vezérelt műveletek

A titkos **beolvasási** művelet a *NBF*  /  *exp* ablakon kívül még nem érvényes és lejárt titkokat fog működni. A titkos kód **lekérési** műveletének meghívása tesztelési célokra használható. A lejárt titkos kód beolvasása **(beolvasása**) helyreállítási műveletekhez használható.

## <a name="secret-access-control"></a>Titkoskulcs-hozzáférés vezérlése

A Key Vaultban felügyelt titkok Access Control az adott titkokat tartalmazó Key Vault szintjén vannak megadva. A titkos kulcsok hozzáférés-vezérlési szabályzata különbözik az azonos Key Vault található kulcsok hozzáférés-vezérlési házirendjétől. A felhasználók egy vagy több tárolót hozhatnak létre a titkos kulcsok megőrzése érdekében, és a forgatókönyvek megfelelő szegmentálásához és a titkok kezeléséhez szükségesek.   

A következő engedélyek használhatók a tár Secrets hozzáférés-vezérlési bejegyzésében, valamint a titkos objektumon engedélyezett műveletek részletes tükrözéséhez:  

- A titkos felügyeleti műveletekhez szükséges engedélyek
  - *Get*: titkos kód beolvasása  
  - *lista*: egy Key Vaultban tárolt titkos kód titkainak vagy verzióinak listázása  
  - *beállítás*: titkos kód létrehozása  
  - *Törlés*: titkos kód törlése  
  - *helyreállítás*: törölt titkos kód helyreállítása
  - *biztonsági mentés*: titkos kulcs biztonsági mentése a kulcstartóban
  - *visszaállítás*: biztonsági másolat készítése a titkos kulcsról egy kulcstartóra

- Jogosultsági szintű műveletek engedélyei
  - *kiürítés*: törölt titkos kód kiürítése (végleges törlése)

A titkokkal kapcsolatos további információkért tekintse meg [a Key Vault REST API-referenciában található titkos műveletek](/rest/api/keyvault)című témakört. Az engedélyek létrehozásával kapcsolatos információkért lásd: tárolók [– Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy). 

Útmutatók a Key Vault való hozzáférés vezérléséhez:
- [Key Vault hozzáférési szabályzat kiosztása a parancssori felület használatával](../general/assign-access-policy-cli.md)
- [Key Vault hozzáférési szabályzatok társítása a PowerShell használatával](../general/assign-access-policy-powershell.md)
- [Key Vault hozzáférési szabályzat társítása a Azure Portal használatával](../general/assign-access-policy-portal.md)
- [Hozzáférés biztosítása Key Vault kulcsokhoz, tanúsítványokhoz és titkokhoz egy Azure szerepköralapú hozzáférés-vezérléssel](../general/rbac-guide.md)

## <a name="secret-tags"></a>Titkos Címkék  
További alkalmazásspecifikus metaadatokat is megadhat címkék formájában. A Key Vault legfeljebb 15 címkét támogat, amelyek mindegyike 256 karakterből és 256 karakterből állhat.  

>[!Note]
>A címkék a hívó által olvashatók, ha rendelkeznek a *listával* vagy a *Get* engedéllyel.

## <a name="azure-storage-account-key-management"></a>Azure Storage-fiók kulcsainak kezelése

A Key Vault az [Azure Storage-fiók](../../storage/common/storage-account-overview.md) kulcsait tudja kezelni:

- Belsőleg az Azure Storage-fiókkal Key Vault listázhatja (szinkronizálhatja) a kulcsokat. 
- Key Vault a kulcsok rendszeres újragenerálása (elforgatása).
- A rendszer soha nem adja vissza a kulcs értékeit a hívónak válaszul.
- Key Vault a Storage-fiókok és a klasszikus Storage-fiókok kulcsait kezeli.

További információkért lásd:
- [Tárfiók hozzáférési kulcsa](../../storage/common/storage-account-keys-manage.md)
- [Storage-fiók kulcsainak kezelése Azure Key Vault](../secrets/overview-storage-keys.md))


## <a name="storage-account-access-control"></a>Storage-fiók hozzáférés-vezérlése

A következő engedélyek használhatók, ha egy felhasználó vagy egy alkalmazás egy felügyelt Storage-fiók műveleteinek elvégzését engedélyezi:  

- A felügyelt Storage-fiók és az SaS-definíciós műveletek engedélyei
  - Get: egy Storage-fiók adatainak *beolvasása* 
  - *lista*: Key Vault által kezelt Storage-fiókok listázása
  - *frissítés*: Storage-fiók frissítése
  - *Törlés*: Storage-fiók törlése  
  - *helyreállítás*: törölt Storage-fiók helyreállítása
  - *biztonsági mentés*: Storage-fiók biztonsági mentése
  - *Restore (visszaállítás*): biztonsági másolatba mentett Storage-fiók visszaállítása Key Vault
  - *beállítás*: Storage-fiók létrehozása vagy frissítése
  - *regeneratekey*: a megadott kulcs értékének újralétrehozása egy Storage-fiókhoz
  - *getsas*: a Storage-fiókhoz tartozó sas-definícióval kapcsolatos információk beolvasása
  - *listsas*: a Storage-fiókhoz tartozó tárolási sas-definíciók listázása
  - *deletesas*: sas-definíció törlése egy Storage-fiókból
  - *setsas*: új sas-definíció/-attribútumok létrehozása vagy frissítése egy Storage-fiókhoz

- Jogosultsági szintű műveletek engedélyei
  - *kiürítés*: felügyelt Storage-fiók kiürítése (végleges törlése)

További információ: a [Storage-fiók műveletei a Key Vault REST API-referenciában](/rest/api/keyvault). Az engedélyek létrehozásával kapcsolatos információkért lásd: tárolók [– Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy).

Útmutatók a Key Vault való hozzáférés vezérléséhez:
- [Key Vault hozzáférési szabályzat kiosztása a parancssori felület használatával](../general/assign-access-policy-cli.md)
- [Key Vault hozzáférési szabályzatok társítása a PowerShell használatával](../general/assign-access-policy-powershell.md)
- [Key Vault hozzáférési szabályzat társítása a Azure Portal használatával](../general/assign-access-policy-portal.md)
- [Hozzáférés biztosítása Key Vault kulcsokhoz, tanúsítványokhoz és titkokhoz egy Azure szerepköralapú hozzáférés-vezérléssel](../general/rbac-guide.md)


## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a Key Vaultról](../general/overview.md)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](../general/about-keys-secrets-certificates.md)
- [Információ a kulcsokról](../keys/about-keys.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Biztonságos hozzáférés egy kulcstartóhoz](../general/secure-your-key-vault.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)