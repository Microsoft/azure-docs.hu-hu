---
title: Az Azure Key Vaultra vonatkozó szabályozási irányelvek
description: Key Vault a szabályozás korlátozza az egyidejű hívások számát, hogy megakadályozza az erőforrások túlzott felhasználását.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7a215b53f673a7414f1b3662f519de5c26faaa9d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749532"
---
# <a name="azure-key-vault-throttling-guidance"></a>Az Azure Key Vaultra vonatkozó szabályozási irányelvek

A szabályozás egy olyan folyamat, amely korlátozza az Azure-szolgáltatás egyidejű hívásait az erőforrások túlzott használatának elkerülése érdekében. Azure Key Vault (AKV) nagy mennyiségű kérést képes kezelni. Túl sok kérés esetén az ügyfél kérésének szabályozása segít fenntartani az AKV-szolgáltatás optimális teljesítményét és megbízhatóságát.

A szabályozási korlátok a forgatókönyvtől függően változnak. Ha például nagy mennyiségű írást végez, a szabályozás lehetősége magasabb, mint ha csak olvasási műveleteket hajt végre.

## <a name="how-does-key-vault-handle-its-limits"></a>Hogyan kezeli Key Vault a korlátait?

A szolgáltatáskorlátozások Key Vault megakadályozzák az erőforrásokkal való visszaélést, és biztosítják a szolgáltatásminőséget Key Vault összes ügyfél számára. A szolgáltatás küszöbértékének túllépése esetén a Key Vault korlátozza az adott ügyféltől származó további kéréseket egy időre, a 429-es HTTP-állapotkódot adja vissza (Túl sok kérelem), és a kérés meghiúsul. A 429-es számra visszaérkedő sikertelen kérelmek nem számítanak bele a 429-es hiba által nyomon Key Vault. 

Key Vault eredetileg arra tervezték, hogy az üzembe helyezéskor tárolja és lekéri a titkos adatokat.  A világ fejlődött, Key Vault a rendszer futásidőben használja a titkos kulcsok tárolására és lekérésre, és az alkalmazások és szolgáltatások gyakran Key Vault használni, mint egy adatbázist.  A jelenlegi korlátok nem támogatják a magas átviteli sebességet.

Key Vault a szolgáltatáskorlátok között megadott [korlátokkal Azure Key Vault létre.](service-limits.md)  Az átviteli sebesség Key Vault maximalizálása érdekében íme néhány ajánlott irányelv/ajánlott eljárás az átviteli sebesség maximalizálására:
1. Győződjön meg arról, hogy a szabályozás a helyén van.  Az ügyfélnek be kell elégnie a 429-es évek exponenciális visszacsatlott házirendjeit, és gondoskodnia kell arról, hogy az alábbi útmutatásnak megfelelő újratitkosításokat is el legyen végzve.
1. Ossza el Key Vault forgalmat több tároló és különböző régió között.   Használjon külön tárolót minden biztonsági/rendelkezésre állási tartományhoz.   Ha öt, két régióban található alkalmazással rendelkezik, akkor 10 tárolót ajánlunk, amelyek mind az alkalmazásra és a régióra egyedi titkos kulcsokat tartalmaznak.  Az előfizetésre vonatkozó korlát az összes tranzakciótípusra az egyes kulcstartók korlátszámának ötszörese. Például a HSM-egyéb tranzakciók előfizetésenként 5000 tranzakcióra vannak korlátozva előfizetésenként 10 másodperc alatt. Fontolja meg a titkos kulcs gyorsítótárazást a szolgáltatásban vagy az alkalmazásban, hogy az RPS-t közvetlenül kulcstartóra csökkentse, és/vagy kezelje az adatlok-alapú forgalmat.  A késés minimalizálása és egy másik előfizetés/tároló használata érdekében eloszthatja a forgalmat a különböző régiók között.  Ne küldjön több előfizetési korlátot a Key Vault szolgáltatásba egyetlen Azure-régióban.
1. Gyorsítótárazza a memóriában a Azure Key Vault lekért titkos adatokat, és amikor csak lehetséges, használja újra a memóriából.  Csak akkor olvassa újra a Azure Key Vault, ha a gyorsítótárazott másolat leáll (például azért, mert a forrásnál elforgatták). 
1. Key Vault a saját titkos szolgáltatásaihoz készült.   Ha az ügyfelek titkos kulcsait tárolja (különösen nagy átviteli sebességű kulcstárolási forgatókönyvek esetén), fontolja meg a kulcsok titkosítással történő tárolását egy adatbázisban vagy tárfiókban, és csak a főkulcsot a Azure Key Vault.
1. A nyilvános kulcsos műveletek titkosítása, burkolása és ellenőrzése az Key Vault-hoz való hozzáférés nélkül is elvégezhető, ami nem csupán a szabályozás kockázatát csökkenti, hanem javítja a megbízhatóságot is (feltéve, hogy megfelelően gyorsítótárazza a nyilvános kulcs anyagát).
1. Ha a Key Vault tárol hitelesítő adatokat egy szolgáltatáshoz, ellenőrizze, hogy a szolgáltatás támogatja-e az Azure AD-hitelesítést a közvetlen hitelesítéshez. Ez csökkenti a terhelést a Key Vault, növeli a megbízhatóságot és leegyszerűsíti a kódot, Key Vault mostantól használhatja az Azure AD-jogkivonatot.  Számos szolgáltatás átkerült az Azure AD-hitelesítésre.  Tekintse meg az Azure-erőforrások felügyelt identitását támogató [szolgáltatások aktuális listáját.](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)
1. Fontolja meg a terhelés/üzemelő példány hosszabb időtartamra való eltolását, hogy a jelenlegi RPS-korlátok alatt maradjon.
1. Ha az alkalmazás több csomópontból áll, amelyeknek ugyanazt a titkos secret(s)t kell olvasniuk, érdemes lehet egy ventilátormintát használni, amelyben egy entitás beolvassa a titkos Key Vault- és a ventilátorokat az összes csomópontra.   A lekért titkos kulcsok gyorsítótárazhatóak csak a memóriában.
Ha úgy találja, hogy a fentiek továbbra sem találják megfelelőnek az Ön igényeit, töltse ki az alábbi táblázatot, és vegye fel velünk a kapcsolatot annak megállapításához, hogy milyen további kapacitások hozzáadhatóak (például az alábbiakban csak szemléltető célokat szolgál).

| Tároló neve | Tároló régiója | Objektumtípus (titkos, kulcs vagy tanúsítvány) | Művelet(nek)* | Kulcs típusa | Kulcshossz vagy görbe | HSM-kulcs?| Stabil állapotra vonatkozó RPS szükséges | Peak RPS needed |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Kulcs | Előjel | EC | P-256 | No | 200 | 1000 |

\*A lehetséges értékek teljes listáját a Azure Key Vault [tartalmazza.](/rest/api/keyvault/key-operations)

Ha további kapacitást hagynak jóvá, vegye figyelembe a következőket a kapacitás növekedése miatt:
1. Az adatkonzisztencia-modell változásai. Ha a tárolót további átviteli kapacitással listázta, az Key Vault szolgáltatás adatkonzisztenciájának változásai garantálják a változásokat (ez szükséges a nagyobb RPS-nek való megfelelőséghez, mivel az alapul szolgáló Azure Storage szolgáltatás nem tud lépést tartani).  Egyshellben:
  1. **Lista engedélyezése nélkül:** A Key Vault szolgáltatás egy írási művelet eredményeit fogja tükrözni (pl. SecretSet, CreateKey) közvetlenül a későbbi hívások során (pl. SecretGet, KeySign).
  1. **Az allow listával:** Key Vault szolgáltatás egy írási művelet eredményeit fogja tükrözni (pl. SecretSet, CreateKey) a következő hívások során 60 másodpercen belül (pl. SecretGet, KeySign).
1. Az ügyfélkódnak tiszteletben kell lennie a 429 újraküldetés-visszacsatolt házirendnek. A Key Vault szolgáltatást hívó ügyfélkód nem próbálkozik Key Vault kérésekkel, amikor 429-es válaszkódot kap.  Az Azure Key Vault itt közzétett szabályozási útmutató exponenciális leépítés használatát javasolja a 429 HTTP-válaszkód fogadásakor.

Ha érvényes üzleti esete van a nagyobb korlátozásokkal kapcsolatban, lépjen kapcsolatba velünk.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Az alkalmazás szolgáltatáskorlátok alapján való korlátozásának beállítása

Az alábbi ajánlott **eljárásokat** érdemes megvalósítani a szolgáltatás szabályozása után:
- Csökkentse a kérésenkénti műveletek számát.
- Csökkentse a kérések gyakoriságát.
- Kerülje az azonnali újrabeküldéseket. 
    - Minden kérés a használati korlátokból ad ki összegeket.

Az alkalmazás hibakezelésének megvalósításakor a 429-es HTTP-hibakóddal észlelheti, hogy szükség van ügyféloldali szabályozásra. Ha a kérés 429-es HTTP-hibakóddal meghiúsul, továbbra is azure-szolgáltatási korlátot kap. Folytassa az ajánlott ügyféloldali szabályozási módszer használatát, és próbálja újra a kérést, amíg sikerrel nem jár.

Az exponenciális leépítést megvalósító kód alább látható. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri("https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


A kód használata egy C#-ügyfélalkalmazásban egyszerű. 

### <a name="recommended-client-side-throttling-method"></a>Ajánlott ügyféloldali szabályozási módszer

A 429-es HTTP-hibakódon kezdje el az ügyfél szabályozását exponenciális leépítési módszer használatával:

1. 1 másodperc várakozás, újrapróbálkozási kérelem
2. Ha továbbra is 2 másodpercet várakozik a szabályozás, újrapróbálkozási kérés
3. Ha továbbra is 4 másodpercet várakozik, újrapróbálkozási kérés
4. Ha továbbra is 8 másodperces várakozást tart, újrapróbálkozási kérés
5. Ha továbbra is 16 másodpercig tart a szabályozás, újrapróbálkozási kérés

Ezen a ponton nem kap HTTP 429-es válaszkódokat.

## <a name="see-also"></a>Lásd még

A Microsoft Cloudban a szabályozás mélyebb tájolását a Szabályozási minta oldalon [láthatja.](/azure/architecture/patterns/throttling)
