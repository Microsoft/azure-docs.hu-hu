---
title: Az Azure Key Vault hozzáférési szabályzatával kapcsolatos problémák elhárítása
description: Az Azure Key Vault hozzáférési szabályzatával kapcsolatos problémák elhárítása
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: c5fab8b856ff9c82a0de887dc9c322dbf541348b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791407"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Az Azure Key Vault hozzáférési szabályzatával kapcsolatos problémák elhárítása

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="i-am-not-able-to-list-or-get-secretskeyscertificate-i-am-seeing-something-went-wrong-error"></a>Nem tudom kilistázni vagy beolvasni a titkokat/kulcsokat/tanúsítványokat. "Hiba történt." Hiba.
Ha problémája van a (z)/beolvasás/létrehozás vagy a titok elérésekor, győződjön meg arról, hogy rendelkezik a művelet végrehajtásához megadott hozzáférési [szabályzattal: Key Vault hozzáférési házirendek](./assign-access-policy-cli.md)

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Hogyan állapítható meg, hogyan és mikor érhetők el a kulcstartók?

Egy vagy több kulcstartó létrehozása után valószínűleg figyelnie kell a kulcstartók elérésének módját és időpontját. A figyelést a Azure Key Vault naplózásának engedélyezésével végezheti el, részletes útmutató a naplózás engedélyezéséhez. [További információ](./logging.md):.

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Hogyan figyelhető meg a tár rendelkezésre állása, a szolgáltatási késési időszakok vagy más teljesítménymutatók a Key Vault esetében?

A szolgáltatás skálázásának megkezdése után a Key vaultba küldött kérések száma megemelkedik. Az ilyen igénynek lehetősége van arra, hogy növelje a kérések késését és a szélsőséges esetekben, hogy a kérések szabályozása megtörténjen, ami hatással lesz a szolgáltatás teljesítményére. Nyomon követheti a Key Vault teljesítmény-metrikáit, és riasztást kaphat a meghatározott küszöbértékekről, a figyelés konfigurálásának lépésenkénti útmutatójában bővebben [olvashat](./alert.md).

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>Nem tudom módosítani a hozzáférési szabályzatot, hogyan lehet engedélyezni?
A felhasználónak elegendő HRE engedéllyel kell rendelkeznie a hozzáférési szabályzat módosításához. Ebben az esetben a felhasználónak magasabb közreműködő szerepkörrel kell rendelkeznie.

### <a name="i-am-seeing-unknown-policy-error-what-does-that-mean"></a>"Ismeretlen házirend" hibaüzenet jelenik meg. Ez mit jelent?
Az ismeretlen szakaszban két különböző lehetőség van a hozzáférési szabályzat megtekintésére:
* Előfordulhat, hogy egy korábbi felhasználónak van hozzáférése, és valamilyen okból kifolyólag a felhasználó nem létezik.
* Ha a hozzáférési szabályzatot a PowerShell használatával adja hozzá, és a hozzáférési szabályzatot hozzáadja az alkalmazás ObjectId az egyszerű szolgáltatásnév helyett.

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Hogyan lehet hozzáférés-vezérlést rendelni Key Vault-objektumhoz? 

A titkos/kulcs/tanúsítvány hozzáférés-vezérlési funkciójának elérhetősége itt jelenik meg, [További információ](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Hogyan biztosíthatom a Key Vault hitelesítését a hozzáférés-vezérlési házirenddel?

A felhőalapú alkalmazások Key Vaulthoz való hitelesítésének legegyszerűbb módja felügyelt identitás; a részletekért lásd: [hitelesítés Azure Key Vault](authentication.md) .
Ha helyszíni alkalmazást hoz létre, helyi fejlesztést végez, vagy más módon nem tud felügyelt identitást használni, ehelyett manuálisan is regisztrálhat egy egyszerű szolgáltatásnevet, és hozzáférést biztosíthat a kulcstartóhoz egy hozzáférés-vezérlési házirend használatával. Lásd: [hozzáférés-vezérlési házirend társítása](assign-access-policy-portal.md).

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Hogyan biztosíthatom az AD-csoport számára a Key Vault elérését?

Az Azure CLI- `az keyvault set-policy` parancs vagy a Azure PowerShell Set-AzKeyVaultAccessPolicy parancsmag használatával adja meg az ad-csoportnak a kulcstartóhoz tartozó engedélyeket. Lásd: [hozzáférési szabályzat társítása –](assign-access-policy-cli.md) parancssori felület és [hozzáférési szabályzat társítása – PowerShell](assign-access-policy-powershell.md).

Az alkalmazásnak szüksége van legalább egy, a Key vaulthoz hozzárendelt identitás-és hozzáférés-kezelési (IAM) szerepkörre is. Ellenkező esetben nem fog tudni bejelentkezni, és nem lesz megfelelő jogosultsága az előfizetéshez való hozzáféréshez. A felügyelt identitásokkal rendelkező Azure AD-csoportok akár nyolc óráig is megkövetelhetik a tokenek frissítését, és érvénybe lépnek.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Hogyan telepíthetem újra Key Vault az ARM-sablonnal a meglévő hozzáférési szabályzatok törlése nélkül?

Jelenleg Key Vault az újratelepítéskor a rendszer törli az összes hozzáférési házirendet a Key Vault, és azokat a ARM-sablon hozzáférési házirendjével helyettesíti. Nincs növekményes beállítás Key Vault hozzáférési házirendekhez. A hozzáférési szabályzatok Key Vaultban való megőrzéséhez olvassa el a meglévő hozzáférési szabályzatokat a Key Vault, és töltse ki az ARM-sablont ezekkel a szabályzatokkal a hozzáférési kimaradások elkerülése érdekében.

Egy másik lehetőség, amely segíthet ehhez a forgatókönyvhöz, hogy az Azure RBAC és a szerepköröket használja a hozzáférési házirendek alternatívájaként. Az Azure RBAC segítségével újra telepítheti a kulcstartót anélkül, hogy újra meg kellene adnia a szabályzatot. Ebben a megoldásban [itt](./rbac-guide.md)találhat további tudnivalókat.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>A következő típusú hibák ajánlott hibaelhárítási lépései

* HTTP 401: Nem hitelesített kérelem – [Hibaelhárítási lépések](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: Nem megfelelő engedélyek – [Hibaelhárítási lépések](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: Túl sok kérelem – [Hibaelhárítási lépések](rest-error-codes.md#http-429-too-many-requests)
* Ellenőrizze, hogy van-e hozzáférési engedélye a Key vaulthoz: [hozzáférési házirend társítása](assign-access-policy-cli.md)– parancssori felület, [hozzáférési szabályzat társítása – PowerShell](assign-access-policy-powershell.md)vagy [hozzáférési szabályzat hozzárendelésének](assign-access-policy-portal.md)engedélyezése.
* Ha problémába ütközik kulcstartó kódban való hitelesítése során, használja az [Authentication SDK-t](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Milyen bevált eljárásokat kell megvalósítani a Key Vault szabályozásakor?
Kövesse az [itt](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits) dokumentált ajánlott eljárásokat

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan lehet elhárítani a Key Vault hitelesítési hibáit: [Key Vault hibaelhárítási útmutató](rest-error-codes.md).