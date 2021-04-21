---
title: Adattitkosítás ügyfél által felügyelt kulccsal – Azure Database for PostgreSQL – Egyetlen kiszolgáló
description: Azure Database for PostgreSQL felhasználó által felügyelt kulccsal az egykiszolgálós adattitkosítás lehetővé teszi, hogy az Bring Your Own Key (BYOK) segítségével védelmet biztosítsunk az adatok számára. Emellett lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésével járó feladatok elkülönítését.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 08c66fe33da78d9b07931c37653b07ba07b22746
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813793"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Azure Database for PostgreSQL kiszolgáló adattitkosításának kezelése ügyfél által felügyelt kulccsal

Az Azure PostgreSQL alapértelmezés szerint az [Azure Storage-titkosítás használatával](../storage/common/storage-service-encryption.md) titkosítja az adatokat a Microsoft által kezelt kulcsokkal. Az Azure PostgreSQL-felhasználók számára nagyon hasonló a transzparens adattitkosítás (TDE) más adatbázisokban, például a SQL Server. Számos vállalatnak teljes körű vezérlésre van szüksége az adatokhoz való hozzáféréshez egy ügyfél által felügyelt kulccsal. Az adattitkosítás ügyfél által felügyelt kulcsokkal Azure Database for PostgreSQL egykiszolgálós kiszolgálón lehetővé teszi, hogy saját kulcsot (BYOK) használ az adatvédelemhez. Emellett lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésével járó feladatok elkülönítését. Az ügyfél által felügyelt titkosítással Ön felel a kulcs életciklusáért, a kulcs használati engedélyeiért, és a kulcsokkal végzett műveletek naplózásáért.

Az egykiszolgálós Azure Database for PostgreSQL ügyfél által kezelt kulcsokkal való adattitkosítás kiszolgálószinten van beállítva. Egy adott kiszolgálóhoz egy ügyfél által kezelt kulcs, a kulcstitkosítási kulcs (KEK) szolgál a szolgáltatás által használt adattitkosítási kulcs (DEK) titkosítására. A KEK egy aszimmetrikus kulcs, amely egy ügyfél tulajdonában lévő és ügyfél által felügyelt Azure Key Vault [tárol.](../key-vault/general/security-features.md) A kulcstitkosítási kulcsot (KEK) és az adattitkosítási kulcsot (DEK) a cikk későbbi, későbbi, részletes leírása ismerteti.

Key Vault egy felhőalapú, külső kulcskezelő rendszer. Magas rendelkezésre áll, és skálázható, biztonságos tárolást biztosít az RSA titkosítási kulcsok számára, opcionálisan a FIPS 140-2 2. szintje szerint ellenőrzött hardveres biztonsági modulok (HSM-ek) segítségével. Nem engedélyezi a tárolt kulcsokhoz való közvetlen hozzáférést, de titkosítási és visszafejtési szolgáltatásokat biztosít az engedélyezett entitások számára. Key Vault létrehozhatja, importálhatja, vagy átviheti egy helyszíni [HSM-eszközről.](../key-vault/keys/hsm-protected-keys.md)

> [!NOTE]
> Ez a funkció minden olyan Azure-régióban elérhető, Azure Database for PostgreSQL egyetlen kiszolgáló támogatja a "általános célú" és a "Memóriaoptimalált" tarifacsomagokat. Egyéb korlátozásokért tekintse meg a korlátozások [szakaszát.](concepts-data-encryption-postgresql.md#limitations)

## <a name="benefits"></a>Előnyök

Az adattitkosítás az egykiszolgálós Azure Database for PostgreSQL ügyfél által kezelt kulcsokkal a következő előnyöket biztosítja:

* Az adatelérést teljes mértékben Ön vezérli, ha eltávolítja a kulcsot, és elérhetetlenné teszi az adatbázist 
*    A kulcs életciklusának teljes körű vezérlése, beleértve a kulcs rotációját a vállalati szabályzatok egymáshoz igazítása érdekében
*    A kulcsok központi kezelése és rendszerkezelése a Azure Key Vault
*    A feladatok elkülönítésének lehetősége a biztonsági rendszergazdák, valamint a DBA és a rendszergazdák között

## <a name="terminology-and-description"></a>Terminológia és leírás

**Adattitkosítási kulcs (DEK):** Szimmetrikus AES256-kulcs, amely egy partíció vagy adatblokk titkosítására használható. Az egyes adatblokkok eltérő kulccsal való titkosítása megnehezíti a titkosításelemzési támadásokat. Az adattitkos kulcsokat az erőforrás-szolgáltatónak vagy egy adott blokkot titkosító és visszafejtő alkalmazáspéldánynak kell elérnie. Ha egy adattitkos kulcsot új kulccsal cserél le, csak a hozzá tartozó blokkban található adatokat kell újratitkosítani az új kulccsal.

**Kulcstitkosítási kulcs (KEK):** Az adattitkosítási kulcsok titkosításához használt titkosítási kulcs. Egy olyan KEK, amely soha Key Vault, lehetővé teszi maguk az adattitkok titkosítását és vezérlését. A KEK-hez hozzáférő entitás különbözhet a dekkot igénylő entitástól. Mivel a KEK az adattitkok visszafejtéséhez szükséges, a KEK lényegében egyetlen pont, amellyel hatékonyan törölhetők az ADATTK-k a KEK törlésével.

A KEK-ekkel titkosított ADATTITK-okat a rendszer külön tárolja. Csak a KEK-hez hozzáféréssel rendelkezik entitás képes visszafejteni ezeket az eltitkokat. További információ: [Biztonság az adattitkosításban.](../security/fundamentals/encryption-atrest.md)

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Hogyan működik az adattitkosítás az ügyfél által felügyelt kulccsal?

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png" alt-text="Az áttekintést bemutató Bring Your Own Key":::

Ahhoz, hogy egy PostgreSQL-kiszolgáló az Key Vault-ban tárolt, ügyfél által kezelt kulcsokat használva titkosítja az adattitkosítási kulcsot, a Key Vault-rendszergazda a következő hozzáférési jogokat biztosítja a kiszolgálóhoz:

* **get**: A kulcs nyilvános részének és tulajdonságainak leolvasása a kulcstartóban.
* **wrapKey:** Az adattitkos kulcs titkosításához. A titkosított adattitkos adattitkos kulcs a Azure Database for PostgreSQL.
* **unwrapKey:** A DEK visszafejtéséhez. Azure Database for PostgreSQL az adatok titkosításához/visszafejtéséhez a visszafejtett DEK-re van szükség

A Kulcstartó rendszergazdája engedélyezheti a naplózási Key Vault [naplózását](../azure-monitor/insights/key-vault-insights-overview.md)is, hogy később is naplót végezhet.

Ha a kiszolgáló úgy van konfigurálva, hogy a kulcstartóban tárolt, ügyfél által kezelt kulcsot használja, a kiszolgáló elküldi a kulcstitkosítási kulcsot a kulcstartónak a titkosításhoz. Key Vault a felhasználói adatbázisban tárolt titkosított ADATTITKot adja vissza. Szükség esetén a kiszolgáló hasonlóképpen elküldi a védett DEK-et a kulcstartónak a visszafejtés érdekében. Ha a naplózás engedélyezve Azure Monitor, az auditorok Key Vault naplók áttekintésére is használhatók.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Az adattitkosítás konfigurálásával kapcsolatos követelmények Azure Database for PostgreSQL kiszolgálón

A következő követelmények vonatkoznak a Key Vault:

* Key Vault és Azure Database for PostgreSQL egykiszolgálós kiszolgálónak ugyanannak a Azure Active Directory (Azure AD-) bérlőnek kell lennie. A bérlők közötti Key Vault és kiszolgáló-interakciók nem támogatottak. A Key Vault erőforrás áthelyezéséhez újra kell konfigurálnia az adattitkosítást.
* A kulcstartót 90 nappal kell beállítani a "Törölt tárolók megőrzésének napjai" beállításhoz. Ha a meglévő kulcstartó alacsonyabb számmal lett konfigurálva, létre kell hoznia egy új kulcstartót, mivel az a létrehozás után nem módosítható.
* Engedélyezze a kulcstartón a soft-delete funkciót, hogy megakadályozza az adatvesztést, ha véletlenül kulcs (vagy Key Vault) történik. A helyreállíthatóan törölt erőforrások 90 napig maradnak meg, kivéve, ha a felhasználó addig is helyreállítja vagy kiüríti azokat. A helyreállító és véglegesen kiürítő műveletek saját engedélyekkel vannak társítva a Key Vault hozzáférési szabályzatban. A soft-delete funkció alapértelmezés szerint ki van kapcsolva, de a PowerShellen vagy az Azure CLI-n keresztül engedélyezheti (vegye figyelembe, hogy nem engedélyezheti az Azure Portal). 
* Végleges törlés elleni védelem engedélyezése a törölt tárolók és tárolóobjektumok kötelező megőrzési időszakának kényszerítése érdekében
* Adjon a Azure Database for PostgreSQL kiszolgálónak hozzáférést a kulcstartóhoz a get, wrapKey és unwrapKey engedélyekkel az egyedi felügyelt identitásával. A Azure Portal egyedi "Szolgáltatás" identitás automatikusan létrejön, ha az adattitkosítás engedélyezve van az egykiszolgálós PostgreSQL-kiszolgálón. Az [egyetlen Azure Database for PostgreSQL](howto-data-encryption-portal.md) az Azure Portal használatával való adattitkosítással kapcsolatos cikk részletes, részletes útmutatást nyújt az Azure Portal.

Az ügyfél által felügyelt kulcs konfigurálásához a következő követelmények vonatkoznak:

* Az adattitkos kulcs titkosításához használt ügyfél által kezelt kulcs csak aszimmetrikus lehet, RSA 2048.
* A kulcs aktiválási dátumának (ha be van állítva) múltbeli dátumnak és időpontnak kell lennie. A lejárati dátumnak (ha be van állítva) jövőbeli dátumnak és időpontnak kell lennie.
* A kulcsnak Engedélyezve állapotban *kell* lennie.
* Ha meglévő kulcsot importál [a](/rest/api/keyvault/ImportKey/ImportKey) kulcstartóba, mindenképpen a támogatott fájlformátumokban adja meg azt ( `.pfx` , , `.byok` `.backup` ).

## <a name="recommendations"></a>Javaslatok

Ha ügyfél által felügyelt kulccsal használ adattitkosítást, a következő javaslatok javaslatokat tartalmaznak az adatok Key Vault:

* Állítson be erőforrás-zárolást a Key Vault szabályozhatja, hogy ki törölheti ezt a kritikus erőforrást, és megelőzheti a véletlen vagy jogosulatlan törlést.
* Naplózás és jelentéskészítés engedélyezése az összes titkosítási kulcson. Key Vault olyan naplókat biztosít, amelyek könnyen bevethetőek más biztonsági információkba és eseménykezelő eszközökbe. Azure Monitor Log Analytics egy példa egy már integrált szolgáltatásra.
* Győződjön meg Key Vault, Azure Database for PostgreSQL az egyetlen kiszolgáló ugyanabban a régióban található, így gyorsabb hozzáférést biztosít a DEK-burkhoz és a kicsomagírási műveletekhez.
* Zárolja az Azure KeyVaultot csak a privát végpontra és a kiválasztott hálózatokra, és csak megbízható *Microsoft-szolgáltatások* számára engedélyezze az erőforrások biztonságát. 

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

Az ügyfél által felügyelt kulcsok konfigurálásához a következő javaslatok javaslatokat íme:

* Az ügyfél által felügyelt kulcs másolatát tartsa biztonságos helyen, vagy helyezze el a biztonsági másolati szolgáltatásban.

* Ha Key Vault hozza létre a kulcsot, először hozzon létre egy biztonsági másolatot a kulcsról. A biztonsági mentést csak a Key Vault. A biztonsági mentési paranccsal kapcsolatos további információkért lásd: [Backup-AzKeyVaultKey.](/powershell/module/az.keyVault/backup-azkeyVaultkey)

## <a name="inaccessible-customer-managed-key-condition"></a>Nem érhető el az ügyfél által felügyelt kulcs feltétele

Ha ügyfél által felügyelt kulccsal konfigurálja az adattitkosítást a Key Vault, a kiszolgáló online állapotban maradása érdekében folyamatos hozzáférésre van szükség ehhez a kulcshoz. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt kulcshoz a Key Vault, a kiszolgáló 10 percen belül elkezd megtagadni minden kapcsolatot. A kiszolgáló egy megfelelő hibaüzenetet ad ki, és elérhetetlenné teszi *a kiszolgáló állapotát.* Néhány ok, amiért a kiszolgáló eléri ezt az állapotot:

* Ha létrehozunk egy időponthoz időben visszaállító kiszolgálót az Azure Database for PostgreSQL kiszolgálóhoz, amelyen engedélyezve van az adattitkosítás, az újonnan létrehozott kiszolgáló *Elérhetetlen állapotban* lesz. A kiszolgáló állapotát a következővel javíthatja: [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) [cli.](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)
* Ha létrehozunk egy olvasási replikát az Azure Database for PostgreSQL kiszolgálóhoz, amelyen engedélyezve van az adattitkosítás, a replikakiszolgáló *Elérhetetlen állapotban* lesz. A kiszolgáló állapotát a következővel javíthatja: [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) [cli.](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)
* Ha törli a KeyVault adatokat, az Azure Database for PostgreSQL-kiszolgáló nem fog tudni hozzáférni a kulcshoz, és *Elérhetetlen állapotba lép.* A kiszolgáló [Key Vault](../key-vault/general/key-vault-recovery.md) helyre, majd az adattitkosítás újrajavalidálva elérhetővé teszi a *kiszolgálót.*
* Ha töröljük a kulcsot a KeyVaultból, az Azure Database for PostgreSQL-kiszolgáló nem fog tudni hozzáférni a kulcshoz, és *Elérhetetlen állapotba* lép. A kulcs [helyreállításával](../key-vault/general/key-vault-recovery.md) és az adattitkosítás újrajavalidálva elérhetővé teszi a *kiszolgálót.*
* Ha az Azure KeyVaultban tárolt kulcs lejár, a kulcs érvénytelenné válik, és az Azure Database for PostgreSQL kiszolgáló elérhetetlen állapotba *kerül.* Terjessze ki a kulcs lejárati dátumát a [CLI](/cli/azure/keyvault/key#az_keyvault_key_set_attributes) használatával, majd ellenőrizze újra az adattitkosítást, hogy elérhetővé tegye a *kiszolgálót.*

### <a name="accidental-key-access-revocation-from-key-vault"></a>Kulcshoz való véletlen hozzáférés visszavonása a Key Vault

Előfordulhat, hogy valaki, aki megfelelő hozzáférési jogosultságokkal rendelkezik Key Vault véletlenül letiltja a kiszolgáló kulcshoz való hozzáférését a következővel:

* A kulcstartó get, wrapKey és unwrapKey engedélyeinek visszaírása a kiszolgálóról.
* A kulcs törlése.
* A kulcstartó törlése.
* A kulcstartó tűzfalszabályainak módosítása.

* A kiszolgáló felügyelt identitásának törlése az Azure AD-ban.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Az ügyfél által felügyelt kulcs figyelése a Key Vault

Az adatbázis állapotának monitorozásához és a transzparens adattitkosítási védők hozzáférésének elvesztésével kapcsolatos riasztások engedélyezéséhez konfigurálja a következő Azure-szolgáltatásokat:

* [Azure Resource Health:](../service-health/resource-health-overview.md)Egy elérhetetlen adatbázis, amely nem rendelkezik hozzáféréssel az ügyfélkulcshoz, elérhetetlenként jelenik meg az adatbázishoz való első kapcsolódás megtagadása után.
* [Tevékenységnapló:](../service-health/alerts-activity-log-service-notifications-portal.md)Ha az ügyfél által felügyelt Key Vault ügyfélkulcshoz való hozzáférés meghiúsul, a rendszer bejegyzéseket ad a tevékenységnaplóhoz. Ha riasztásokat hoz létre ezekhez az eseményekhez, a lehető leghamarabb visszaállíthatja a hozzáférést.

* [Műveletcsoportok:](../azure-monitor/alerts/action-groups.md)Ezeket a csoportokat definiálva értesítéseket és riasztásokat küldhet a beállítások alapján.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Visszaállítás és replikálás az ügyfél felügyelt kulcsával a Key Vault

Miután Azure Database for PostgreSQL kiszolgáló az ügyfél felügyelt kulcsával van titkosítva, Key Vault kiszolgáló minden újonnan létrehozott példánya is titkosítva lesz. Ezt az új másolatot helyi vagy georedukciós művelettel, vagy olvasási replikákon keresztül is készítse el. A másolat azonban módosítható úgy, hogy tükrözze az új ügyfél titkosítási kulcsát. Ha az ügyfél által felügyelt kulcsot megváltoztatják, a kiszolgáló régi biztonsági másolatai a legújabb kulcs használatával indulnak el.

Az ügyfél által felügyelt adattitkosítás visszaállítás vagy olvasási replika létrehozása során való beállítása során a problémák elkerülése érdekében fontos, hogy kövesse az alábbi lépéseket az elsődleges és a visszaállított/replikakiszolgálókon:

* Indítsa el a visszaállítási vagy olvasási replika-létrehozási folyamatot az egyetlen Azure Database for PostgreSQL elsődleges kiszolgálóról.
* Tartsa elérhetetlen állapotban az újonnan létrehozott kiszolgálót (visszaállított/replika), mert az egyedi identitása még nem kapott engedélyt a Key Vault.
* A visszaállított/replikakiszolgálón érvényesítse újra az ügyfél által felügyelt kulcsot az adattitkosítási beállításokban. Ez biztosítja, hogy az újonnan létrehozott kiszolgáló wrap és unwrap engedélyeket kap a Key Vault.

## <a name="limitations"></a>Korlátozások

A Azure Database for PostgreSQL esetében az ügyfelek által felügyelt kulccsal (CMK) használt, az ügyfelek által kezelt adatok titkosításának támogatása néhány korlátozással rendelkezik:

* Ennek a funkciónak a támogatása **a** általános célú  memóriaoptimalitott tarifacsomagra korlátozódik.
* Ez a funkció csak olyan régiókban és kiszolgálókon érhető el, amelyek legfeljebb 16 TB-nyi tárterületet támogatnak. A legfeljebb 16 TEB tárhelyet támogató Azure-régiók listájáért tekintse meg a tárolási szakaszt az itt található [dokumentációban](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - A fent felsorolt régiókban létrehozott összes új PostgreSQL-kiszolgáló elérhető az ügyfélkezelői kulcsokkal való **titkosításhoz.** Az időponthoz időben visszaállított (PITR) kiszolgáló vagy olvasási replika nem fog megfelelőnek minősülni, bár elméletben "újak".
    > - Annak ellenőrzéséhez, hogy a kiépített kiszolgáló támogatja-e a 16 TEB-t, a portál tarifacsomag paneljére használhatja, és láthatja a kiépített kiszolgáló által támogatott maximális tárterületméretet. Ha a csúszkát 4 TEB-re tudja felfelé mozgatni, előfordulhat, hogy a kiszolgáló nem támogatja az ügyfél által kezelt kulcsokkal való titkosítást. Az adatok titkosítása azonban mindig szolgáltatás által felügyelt kulcsokkal történik. Ha kérdése van, forduljon a AskAzureDBforPostgreSQL@service.microsoft.com következő e-hoz: .

* A titkosítás csak az RSA 2048 titkosítási kulccsal támogatott.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan állíthatja be az adattitkosítást egy ügyfél által felügyelt kulccsal az egykiszolgálós [Azure-adatbázishoz](howto-data-encryption-portal.md)a Azure Portal.
