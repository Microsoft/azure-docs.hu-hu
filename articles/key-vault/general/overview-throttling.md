---
title: Az Azure Key Vaultra vonatkozó szabályozási irányelvek
description: Key Vault szabályozás korlátozza az erőforrások túlzott mennyiségének megelőzésére irányuló egyidejű hívások számát.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7bdc3ac517df6b73fba7231cfe0fdc9855803782
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175753"
---
# <a name="azure-key-vault-throttling-guidance"></a>Az Azure Key Vaultra vonatkozó szabályozási irányelvek

A szabályozás olyan folyamat, amely az erőforrások túlzott használatának megakadályozása érdekében korlátozza az Azure-szolgáltatás egyidejű hívásait. A Azure Key Vault (AKV) nagy mennyiségű kérelem kezelésére szolgál. Ha a kérések túlnyomó száma meghaladja a kérelmeket, az ügyfél kéréseinek szabályozása segít a AKV szolgáltatás optimális teljesítményének és megbízhatóságának fenntartásában.

A szabályozás korlátai a forgatókönyv alapján változnak. Ha például nagy mennyiségű írást végez, a szabályozás lehetősége magasabb, mint ha csak olvasást végez.

## <a name="how-does-key-vault-handle-its-limits"></a>Hogyan kezeli a Key Vault a korlátait?

A szolgáltatás korlátai a Key Vault meggátolják az erőforrások felhasználását, és biztosítják az összes Key Vault ügyfelének minőségi szolgáltatását. Ha túllépi a szolgáltatás küszöbértékét, Key Vault korlátozza az ügyféltől érkező további kérelmeket egy adott időtartamra, a 429-as HTTP-állapotkódot (túl sok kérés) adja vissza, és a kérés meghiúsul. Az 429-et visszaadó sikertelen kérelmek nem számítanak bele a Key Vault által követett szabályozási korlátokba. 

A Key Vault eredetileg a titkok tárolására és lekérésére szolgáltak a központi telepítés ideje alatt.  A világ fejlődött, és a Key Vault futtatáskor használatban van a titkok tárolására és beolvasására, és gyakran az alkalmazások és a szolgáltatások használni szeretnék a Key Vaultt, például az adatbázist.  Az aktuális határértékek nem támogatják a nagy átviteli sebességet.

A Key Vault eredetileg a [Azure Key Vault szolgáltatás korlátaiban](service-limits.md)megadott korlátozásokkal lett létrehozva.  Ahhoz, hogy a lehető leghatékonyabban Key Vault a saját átviteli sebességét, az alábbi ajánlott irányelvek és ajánlott eljárások a teljesítmény maximalizálása érdekében:
1. Győződjön meg arról, hogy a szabályozás érvényben van.  Az ügyfélnek meg kell felelnie a 429-re vonatkozó exponenciális visszatartási szabályzatoknak, és gondoskodnia kell arról, hogy az alábbi útmutatás szerint próbálkozzon újra.
1. Ossza meg Key Vault forgalmát több tároló és különböző régió között.   Használjon külön tárolót minden egyes biztonsági/rendelkezésre állási tartományhoz.   Ha öt alkalmazással rendelkezik, amelyek mindegyike két régióban található, javasoljuk, hogy az alkalmazás és a régió számára egyedi titkokat tartalmazó 10 tárolót.  Az összes tranzakciótípus esetében az előfizetésre vonatkozó korlát ötszöröse az egyes kulcstartók maximális száma. Például HSM – az egyes előfizetésekhez tartozó egyéb tranzakciók 10 másodpercen belül 5 000 tranzakcióra korlátozódnak. Érdemes lehet gyorsítótárba venni a titkos kulcsot a szolgáltatáson vagy az alkalmazáson belül, hogy közvetlenül a Key vaultra csökkentse az RPS-t és/vagy a burst-alapú forgalmat.  A forgalmat a különböző régiók között is feloszthatja, hogy csökkentse a késést, és használjon egy másik előfizetést/tárat.  Ne küldjön egynél több előfizetési korlátot a Key Vault szolgáltatásnak egyetlen Azure-régióban.
1. Gyorsítótárazza a Azure Key Vault memóriából beolvasott titkokat, és amikor csak lehetséges, a memóriából újra felhasználhatja őket.  Azure Key Vault csak akkor olvassa újra a rendszer, ha a gyorsítótárazott másolat leáll (például azért, mert az a forrásnál van elforgatva). 
1. Key Vault a saját szolgáltatásainak titkára lett tervezve.   Ha az ügyfelek titkos kulcsait (különösen a nagy átviteli sebességű kulcsok tárolási forgatókönyvei esetében) tárolja, fontolja meg a kulcsok egy adatbázisban vagy a Storage-fiókban való elhelyezését titkosítással, és csak a főkulcsot tárolja Azure Key Vaultban.
1. A nyilvános kulcsú műveletek titkosítása, becsomagolása és ellenőrzése a Key Vaulthoz való hozzáférés nélkül végezhető el, ami nemcsak csökkenti a szabályozás kockázatát, hanem javítja a megbízhatóságot is (feltéve, hogy a nyilvános kulcsú anyagokat megfelelően gyorsítótárazza).
1. Ha Key Vault használatával tárolja a szolgáltatás hitelesítő adatait, ellenőrizze, hogy a szolgáltatás támogatja-e az Azure AD-hitelesítést közvetlenül a hitelesítéshez. Ez csökkenti a Key Vault terhelését, javítja a megbízhatóságot, és leegyszerűsíti a kódot, mivel Key Vault mostantól az Azure AD-tokent is használhatja.  Számos szolgáltatás az Azure AD-hitelesítés használatával lett áthelyezve.  Tekintse [meg az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)aktuális listáját.
1. Ügyeljen arra, hogy a terhelést/üzembe helyezést hosszabb idő alatt, a jelenlegi RPS-korlátokon belül is el lehessen osztani.
1. Ha az alkalmazás több olyan csomópontot tartalmaz, amelyeknek ugyanazokat a titkos kulcsokat kell beolvasniuk, akkor érdemes kipróbálni a mintázatot, ahol az egyik entitás beolvassa a titkos kulcsot a Key Vaultból, és kiveszi a rajongókat az összes csomópontra.   Csak a memóriában gyorsítótárazza a beolvasott titkokat.
Ha úgy látja, hogy a fentiek továbbra sem felelnek meg az igényeinek, töltse ki az alábbi táblázatot, és vegye fel velünk a kapcsolatot, és határozza meg, hogy milyen további kapacitás vehető fel (példa csak szemléltető célokra).

| Tároló neve | Tár régiója | Objektumtípus (titok, kulcs vagy tanúsítvány) | Művelet (ek) * | Kulcs típusa | Kulcs hossza vagy görbéje | HSM-kulcs?| Stabil állapot RPS szükséges | Az RPS-csúcs szükséges |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Kulcs | Előjel | EC | P-256 | Nem | 200 | 1000 |

\* A lehetséges értékek teljes listáját a [Azure Key Vault műveletek](/rest/api/keyvault/key-operations)című részben tekintheti meg.

Ha további kapacitást hagy jóvá, vegye figyelembe a következőket a kapacitás növekedésének eredményeképpen:
1. Az adatkonzisztencia-modell módosul. Miután a tár engedélyezve van a további adatátviteli kapacitással, a Key Vault szolgáltatás adatkonzisztencia-garanciája módosul (a nagyobb mennyiségű RPS-vel való megfeleléshez szükséges, mivel a mögöttes Azure Storage szolgáltatás nem tud lépést tartani).  Dióhéjban:
  1. Az **engedélyezési lista nélkül**: a Key Vault szolgáltatás egy írási művelet eredményét fogja tükrözni (például SecretSet, CreateKey) azonnal a következő hívásokban (például: SecretGet, előjel).
  1. Az **engedélyezési listával**: a Key Vault szolgáltatás egy írási művelet eredményét fogja tükrözni (például SecretSet, CreateKey) a következő hívásokban (például:) 60 másodpercen belül. SecretGet, előjel).
1. Az ügyfél kódjának vissza kell térnie a 429-re vonatkozó újrapróbálkozási szabályzatra. A Key Vault szolgáltatást hívó ügyfélalkalmazás nem tudja azonnal újrapróbálkozni Key Vault kérések esetén, amikor 429-as kódú választ kap.  Az itt közzétett Azure Key Vault-szabályozási útmutató az exponenciális leállítási alkalmazását javasolja a 429 http-válasz kódjának fogadásakor.

Ha érvényes üzleti esettel rendelkezik a magasabb szintű szabályozási korlátokhoz, vegye fel velünk a kapcsolatot.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Az alkalmazás szabályozása a szolgáltatási korlátokra reagálva

A szolgáltatás szabályozásakor a következő **ajánlott eljárásokat** kell megvalósítani:
- Csökkentse a műveletekre vonatkozó kérelmek számát.
- Csökkentse a kérelmek gyakoriságát.
- Kerülje az azonnali újrapróbálkozásokat. 
    - Az összes kérelem a használati korlátokkal szemben esedékes.

Az alkalmazás hibakezelés megvalósításakor a 429 HTTP-hibakód használatával észlelni kell az ügyféloldali szabályozás szükségességét. Ha a kérelem a HTTP 429 hibakód miatt ismét meghiúsul, akkor továbbra is Azure-szolgáltatási korláttal találkozik. Folytassa a javasolt ügyféloldali szabályozási módszer használatát, és próbálja meg újból végrehajtani a kérést.

Az exponenciális leállítási megvalósító kód alább látható. 
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


A kód használata egy ügyfél C#-alkalmazásban egyszerű. 

### <a name="recommended-client-side-throttling-method"></a>Ajánlott ügyféloldali szabályozási módszer

A 429-as HTTP-hibakódon kezdje el az ügyfél szabályozását egy exponenciális leállítási megközelítés használatával:

1. Várjon 1 másodpercet, próbálkozzon újra a kérelemmel
2. Ha továbbra is 2 másodpercig tart a szabályozás, próbálkozzon újra a kéréssel
3. Ha továbbra is leszabályozza a várakozási időt 4 másodpercig, próbálkozzon újra a kéréssel
4. Ha továbbra is leszabályozza a 8 másodpercet, próbálkozzon újra a kéréssel
5. Ha a szabályozás továbbra is 16 másodpercig tart, próbálkozzon újra a kérelemmel

Ezen a ponton nem kell lekérdezni a HTTP 429-Response kódokat.

## <a name="see-also"></a>Lásd még

A Microsoft Cloud szabályozásának mélyebb tájolását lásd: [szabályozási minta](/azure/architecture/patterns/throttling).
