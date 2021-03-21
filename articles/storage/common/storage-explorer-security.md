---
title: Azure Storage Explorer biztonsági útmutató | Microsoft Docs
description: A Azure Storage Explorer biztonsági útmutatója
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: 283ec9999f9b4362035b6770383984efb0879d49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92783759"
---
# <a name="azure-storage-explorer-security-guide"></a>Azure Storage Explorer biztonsági útmutató

A Microsoft Azure Storage Explorer lehetővé teszi az Azure Storage-adattárolási szolgáltatás biztonságos és biztonságos használatát Windows, macOS és Linux rendszeren. Az irányelvek követésével gondoskodhat róla, hogy az adatai védve legyenek.

## <a name="general"></a>Általános kérdések

- **Mindig a Storage Explorer legújabb verzióját használja.** Storage Explorer kiadások biztonsági frissítéseket is tartalmazhatnak. Az általános biztonság érdekében naprakész maradhat.
- **Csak a megbízható erőforrásokhoz kapcsolódjon.** A nem megbízható forrásból letöltött adatok rosszindulatúak lehetnek, és az adatok nem megbízható forrásba való feltöltése az elveszett vagy ellopott adatok elvesztését eredményezheti.
- **Ha lehetséges, használja a HTTPS-t.** A Storage Explorer alapértelmezés szerint HTTPS-t használ. Egyes forgatókönyvek lehetővé teszik a HTTP használatát, de a HTTP-t csak utolsó megoldásként kell használni.
- **Győződjön meg arról, hogy csak a szükséges engedélyek vannak megadva azok számára, akiknek szükségük van rájuk.** Kerülje a túlzott engedékenység biztosítását, ha bárki számára hozzáférést biztosít az erőforrásokhoz.
- **Kritikus műveletek végrehajtásakor körültekintően járjon el.** Bizonyos műveletek, például a törlés és a felülírás, nem vonhatók vissza, és adatvesztést okozhatnak. A műveletek végrehajtása előtt győződjön meg arról, hogy a megfelelő erőforrásokkal dolgozik.

## <a name="choosing-the-right-authentication-method"></a>A megfelelő hitelesítési módszer kiválasztása

A Storage Explorer különböző lehetőségeket biztosít az Azure Storage-erőforrások elérésére. Bármilyen módszert választ, a következő javaslatokat ajánljuk.

### <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

Az Azure Storage-erőforrások elérésének legegyszerűbb és legbiztonságosabb módja az Azure-fiókkal való bejelentkezés. A bejelentkezés Azure AD-hitelesítést használ, amely a következőket teszi lehetővé:

- Hozzáférés biztosítása adott felhasználókhoz és csoportokhoz.
- Bármikor visszavonhatja az adott felhasználókhoz és csoportokhoz való hozzáférést.
- A hozzáférési feltételek betartatása, például a többtényezős hitelesítés megkövetelése.

Ha lehetséges, javasoljuk az Azure AD-hitelesítés használatát.

Ez a szakasz a tárolási erőforrások biztonságossá tételéhez használható két Azure AD-alapú technológiát ismerteti.

#### <a name="azure-role-based-access-control-azure-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) részletes hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz. Az Azure-szerepköröket és-engedélyeket a Azure Portal lehet felügyelni.

Storage Explorer támogatja az Azure RBAC hozzáférését a Storage-fiókokhoz, a Blobokhoz és a várólistákhoz. Ha hozzáférésre van szüksége a fájlmegosztás vagy a táblák számára, hozzá kell rendelnie az Azure-szerepköröket, amelyek engedélyeket adnak a Storage-fiók kulcsainak listázásához.

#### <a name="access-control-lists-acls"></a>Hozzáférés-vezérlési lista (ACL-ek)

A [hozzáférés-vezérlési listák (ACL-ek)](../blobs/data-lake-storage-access-control.md) lehetővé teszik a fájlok és mappák szintjének hozzáférését ADLS Gen2 blob-tárolókban. Az ACL-eket Storage Explorer használatával kezelheti.

### <a name="shared-access-signatures-sas"></a>Közös hozzáférésű jogosultságkódok (SAS)

Ha nem tudja használni az Azure AD-hitelesítést, javasoljuk, hogy használjon közös hozzáférési aláírásokat. A közös hozzáférésű aláírásokkal a következőket teheti:

- Névtelen korlátozott hozzáférés biztosítása az erőforrások biztonságossá tételéhez.
- Az SAS azonnali visszavonása, ha egy megosztott hozzáférési házirendből (SAP) jön létre.

A közös hozzáférésű aláírásokkal azonban a következőket teheti:

- Korlátozza, hogy kik használhatnak SAS-t. Egy érvényes SAS-t bárki is használhat, aki rendelkezik vele.
- SAS visszavonása, ha nem egy megosztott elérési házirendből (SAP) jön létre.

Ha SAS-t használ a Storage Explorerban, a következő irányelveket javasoljuk:

- **Az SAS-tokenek és az URI-k eloszlásának korlátozása.** Csak az SAS-jogkivonatok és URI-k terjesztése megbízható személyeknek. Az SAS-eloszlás korlátozása csökkenti a SAS lehetséges felhasználható lehetőségét.
- **Csak a megbízható entitásokból származó SAS-jogkivonatokat és URI-ket használjon.**
- **Ha lehetséges, használjon közös hozzáférési házirendeket (SAP) SAS-tokenek és URI-k létrehozásakor.** A megosztott hozzáférési házirenden alapuló SAS biztonságosabb, mint az operációs rendszer nélküli SAS, mert az SAS-t az SAP törlésével lehet visszavonni.
- **A tokenek minimális erőforrás-hozzáféréssel és engedélyekkel hozhatók elő.** A minimális engedélyek korlátozzák a lehetséges kárt, ha egy SAS-t nem használ.
- **Olyan jogkivonatokat állít elő, amelyek csak a szükséges ideig érvényesek.** A rövid élettartam különösen fontos az operációs rendszer nélküli SAS esetében, mert nem vonható vissza a létrehozásuk után.

> [!IMPORTANT]
> Az SAS-tokenek és az URI-k hibaelhárítási célból történő megosztásakor – például a szolgáltatási kérelmekben vagy a hibajelentésekben – mindig kitakarja az SAS aláírási részét.

### <a name="storage-account-keys"></a>Tárfiókkulcsok

A Storage-fiókok kulcsai korlátlan hozzáférést biztosítanak a szolgáltatásokhoz és az erőforrásokhoz a Storage-fiókon belül. Ezért javasoljuk, hogy korlátozza a kulcsok használatát a Storage Explorer erőforrásaihoz való hozzáférésre. Használja helyette az Azure RBAC funkcióit vagy az SAS-t a hozzáférés biztosításához.

Egyes Azure-szerepkörök engedélyt adnak a Storage-fiók kulcsainak lekérésére. Az ezekkel a szerepkörökkel rendelkező személyek hatékonyan kihasználhatják az Azure RBAC által biztosított vagy megtagadott engedélyeket. Javasoljuk, hogy csak akkor adja meg ezt az engedélyt, ha szükséges.

A Storage Explorer a kérések hitelesítéséhez a Storage-fiókok kulcsait fogja használni, ha vannak ilyenek. Ezt a funkciót letilthatja a beállítások (**szolgáltatások > Storage-fiókok > a kulcsok használatának letiltása**). Egyes szolgáltatások nem támogatják az Azure RBAC, például a klasszikus Storage-fiókok használatát. Ezek a funkciók továbbra is kulcsokat igényelnek, és ezt a beállítást nem érinti.

Ha kulcsokat kell használnia a tárolási erőforrások eléréséhez, javasoljuk a következő irányelveket:

- **Ne ossza meg fiókja kulcsait senkivel.**
- **Kezelje a Storage-fiók kulcsait, például a jelszavakat.** Ha a kulcsokat elérhetővé kell tenni, használjon biztonságos tárolási megoldásokat, például [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

> [!NOTE]
> Ha úgy gondolja, hogy a Storage-fiók kulcsa meg lett osztva vagy hiba történt, akkor a Azure Portal új kulcsokat hozhat a Storage-fiókhoz.

### <a name="public-access-to-blob-containers"></a>Nyilvános hozzáférés blob-tárolóhoz

Storage Explorer lehetővé teszi az Azure Blob Storage-tárolók hozzáférési szintjének módosítását. A nem privát blob-tárolók lehetővé teszik, hogy bárki névtelen olvasási hozzáférése legyen ezekben a tárolókban található adattartalomhoz.

A blob-tárolók nyilvános hozzáférésének engedélyezésekor a következő irányelveket javasoljuk:

- **Ne engedélyezze a nyilvános hozzáférést olyan blob-tárolóhoz, amely potenciálisan bizalmas adatokat tartalmazhat.** Győződjön meg arról, hogy a blob-tároló ingyenes az összes magánjellegű adattal.
- **Ne töltsön fel semmilyen potenciálisan bizalmas adatokat blob-tárolóba blob-vagy tároló-hozzáféréssel.** 

## <a name="next-steps"></a>Következő lépések

- [Biztonsági javaslatok](../blobs/security-recommendations.md)