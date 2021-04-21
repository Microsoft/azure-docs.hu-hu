---
title: Adattitkosítás ügyfél által felügyelt kulccsal – Azure Database for PostgreSQL – Egyetlen kiszolgáló
description: Azure Database for PostgreSQL felhasználó által felügyelt kulccsal az egykiszolgálós adattitkosítás lehetővé teszi, hogy az Bring Your Own Key (BYOK) segítségével védelmet biztosítsunk az adatok számára. Emellett lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésével járó feladatok elkülönítését.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: fa52327225667bd84047e74a89e3b1394964b22c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769984"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Azure Database for PostgreSQL kiszolgáló adattitkosításának kezelése ügyfél által felügyelt kulccsal

Az Azure PostgreSQL alapértelmezés szerint az [Azure Storage-titkosítás használatával](../storage/common/storage-service-encryption.md) titkosítja az adatokat a Microsoft által kezelt kulcsokkal. Az Azure PostgreSQL-felhasználók számára nagyon hasonló a transzparens adattitkosítás (TDE) más adatbázisokban, például a SQL Server. Számos vállalatnak teljes körű vezérlésre van szüksége az adatokhoz való hozzáféréshez egy ügyfél által felügyelt kulccsal. Az adattitkosítás ügyfél által felügyelt kulcsokkal Azure Database for PostgreSQL egykiszolgálós kiszolgálón lehetővé teszi, hogy saját kulcsot (BYOK) használ az adatvédelemhez. Emellett lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésével járó feladatok elkülönítését. Az ügyfél által felügyelt titkosítással Ön felel a kulcs életciklusáért, a kulcs használati engedélyeiért, és a kulcsokkal végzett műveletek naplózásáért.

Az egykiszolgálós Azure Database for PostgreSQL ügyfél által kezelt kulcsokkal való adattitkosítás kiszolgálószinten van beállítva. Egy adott kiszolgálóhoz egy ügyfél által kezelt kulcs, a kulcstitkosítási kulcs (KEK) szolgál a szolgáltatás által használt adattitkosítási kulcs (DEK) titkosítására. A KEK egy aszimmetrikus kulcs, amely egy ügyfél tulajdonában lévő és ügyfél által felügyelt Azure Key Vault [tárol.](../key-vault/general/security-overview.md) A kulcstitkosítási kulcsot (KEK) és az adattitkosítási kulcsot (DEK) a cikk későbbi, későbbi, részletes leírása ismerteti.

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

Ahhoz, hogy egy PostgreSQL-kiszolgáló az Key Vault-ban tárolt, ügyfél által kezelt kulcsokat használva titkosítja az adattitkosítási kulcsot, egy Key Vault-rendszergazda a következő hozzáférési jogosultságokat biztosítja a kiszolgálóhoz:

* **get**: A kulcstartó nyilvános részének és tulajdonságainak leolvasása.
* **wrapKey:** Az adattitkos kulcs titkosításához. A titkosított adattitkos kulcs tárolása a Azure Database for PostgreSQL.
* **unwrapKey:** A DEK visszafejtéséhez. Azure Database for PostgreSQL a visszafejtött adattitkos kulcsra az adatok titkosításához/visszafejtéséhez

A Kulcstartó rendszergazdája a naplózási események naplózását [Key Vault](../azure-monitor/insights/key-vault-insights-overview.md)is engedélyezheti, hogy később naplózást végezhet.

Ha a kiszolgáló úgy van konfigurálva, hogy a kulcstartóban tárolt, ügyfél által kezelt kulcsot használja, a kiszolgáló elküldi az adattitkosítási kulcsot a kulcstartónak titkosításra. Key Vault a felhasználói adatbázisban tárolt titkosított ADATTITK-t adja vissza. Szükség esetén a kiszolgáló a kulcstartónak is elküldi a védett DEK-et a visszafejtés érdekében. Ha a naplózás engedélyezve Azure Monitor, az auditorok Key Vault naplók áttekintésére is használhatók.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Az adattitkosítás konfigurálásával kapcsolatos követelmények Azure Database for PostgreSQL kiszolgálón

A következő követelmények vonatkoznak a Key Vault:

* Key Vault Azure Database for PostgreSQL kiszolgálónak ugyanannak az Azure AD Azure Active Directory bérlőnek kell lennie. A több-Key Vault és kiszolgáló-interakciók nem támogatottak. A Key Vault erőforrást később újra kell konfigurálni az adattitkosításhoz.
* A kulcstartót 90 nappal kell beállítani a "Törölt tárolók megőrzése napokban" beállításhoz. Ha a meglévő kulcstartó alacsonyabb számmal lett konfigurálva, létre kell hoznia egy új kulcstartót, mivel az a létrehozás után nem módosítható.
* Engedélyezze a kulcstartó soft-delete funkcióját az adatvesztés elleni védelem érdekében, ha véletlenül kulcs (vagy Key Vault) történik. A helyreállíthatóan törölt erőforrások 90 napig maradnak meg, kivéve, ha a felhasználó addig is helyreállítja vagy kiüríti azokat. A helyreállító és véglegesen kiürítő műveletek saját engedélyekkel vannak társítva egy Key Vault hozzáférési szabályzatban. A soft-delete funkció alapértelmezés szerint ki van kapcsolva, de a PowerShell vagy az Azure CLI használatával engedélyezheti (vegye figyelembe, hogy nem engedélyezheti az Azure Portal). 
* Végleges törlés elleni védelem engedélyezése a törölt tárolók és tárolóobjektumok kötelező megőrzési időszakának kényszerítése érdekében
* Adjon hozzáférést Azure Database for PostgreSQL-kiszolgálónak a kulcstartóhoz get, wrapKey és unwrapKey engedélyekkel az egyedi felügyelt identitásával. A Azure Portal egyedi "Service" identitás automatikusan létrejön, ha az adattitkosítás engedélyezve van az egykiszolgálós PostgreSQL-kiszolgálón. Az [adattitkosítással Azure Database for PostgreSQL](howto-data-encryption-portal.md) az Azure Portal használatával való adattitkosítással kapcsolatos cikk részletes, részletes útmutatást nyújt az Azure Portal.

Az ügyfél által felügyelt kulcs konfigurálásához a következő követelmények vonatkoznak:

* A DEK titkosításához használt, felhasználó által kezelt kulcs csak aszimmetrikus RSA 2048 lehet.
* A kulcs aktiválási dátumának (ha be van állítva) egy múltbeli dátumnak és időpontnak kell lennie. A lejárati dátumnak (ha be van állítva) jövőbeli dátumnak és időpontnak kell lennie.
* A kulcsnak Engedélyezve állapotban *kell* lennie.
* Ha meglévő kulcsot importál [a](/rest/api/keyvault/ImportKey/ImportKey) kulcstartóba, győződjön meg arról, hogy a kulcs a támogatott fájlformátumokban (, , ) van `.pfx` `.byok` `.backup` megszabadva.

## <a name="recommendations"></a>Javaslatok

Ha ügyfél által felügyelt kulccsal használ adattitkosítást, az Key Vault:

* Állítson be erőforrás-zárolást a Key Vault szabályozhatja, hogy ki törölheti ezt a kritikus erőforrást, és hogyan előzheti meg a véletlen vagy jogosulatlan törlést.
* Naplózás és jelentéskészítés engedélyezése az összes titkosítási kulcson. Key Vault olyan naplókat biztosít, amelyek könnyen bevethetőek más biztonsági információkba és eseménykezelő eszközökbe. Azure Monitor Log Analytics egy példa a már integrált szolgáltatásokra.
* Győződjön meg Key Vault, hogy Azure Database for PostgreSQL kiszolgáló ugyanabban a régióban található, így gyorsabb hozzáférést biztosít a DEK-burkolókhoz és a kiírási műveletekhez.
* Zárolja az Azure KeyVaultot a privát végpontra és a kiválasztott hálózatokra, és csak megbízható *Microsoft-szolgáltatások* számára engedélyezze az erőforrások biztonságát. 

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

Az ügyfél által felügyelt kulcsok konfigurálásához a következő javaslatok ajánlásokat tartalmaznak:

* Az ügyfél által kezelt kulcs másolatát tartsa biztonságos helyen, vagy helyezze el a escrow szolgáltatásban.

* Ha Key Vault hozza létre a kulcsot, hozzon létre egy biztonsági másolatot a kulcsról, mielőtt első alkalommal használ ilyet. A biztonsági mentést csak a korábbi Key Vault. A biztonsági mentési paranccsal kapcsolatos további információkért lásd: [Backup-AzKeyVaultKey.](/powershell/module/az.keyVault/backup-azkeyVaultkey)

## <a name="inaccessible-customer-managed-key-condition"></a>Nem érhető el az ügyfél által felügyelt kulcs feltétele

Ha ügyfél által felügyelt kulccsal konfigurálja az adattitkosítást a Key Vault, a kiszolgáló online állapotban maradása érdekében folyamatos hozzáférésre van szükség ehhez a kulcshoz. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt kulcshoz a Key Vault, a kiszolgáló 10 percen belül elkezdi megtagadni az összes kapcsolatot. A kiszolgáló egy megfelelő hibaüzenetet ad ki, és elérhetetlenné teszi *a kiszolgáló állapotát.* Néhány ok, amiért a kiszolgáló elérheti ezt az állapotot:

* Ha létrehozunk egy időponthoz időben visszaállító kiszolgálót az Azure Database for PostgreSQL kiszolgálóhoz, amelyen engedélyezve van az adattitkosítás, az újonnan létrehozott kiszolgáló *Elérhetetlen állapotban* lesz. A kiszolgáló állapotát a következővel javíthatja: [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) [cli.](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)
* Ha létrehozunk egy olvasási replikát az Azure Database for PostgreSQL kiszolgálóhoz, amelyen engedélyezve van az adattitkosítás, a replikakiszolgáló *Elérhetetlen állapotban* lesz. A kiszolgáló állapotát a következővel javíthatja: [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) [cli.](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)
* Ha törli a KeyVaultot, az Azure Database for PostgreSQL-kiszolgáló nem fog tudni hozzáférni a kulcshoz, és *Elérhetetlen állapotba* lép. A kiszolgáló [Key Vault](../key-vault/general/key-vault-recovery.md) helyre, majd ellenőrizze újra az adattitkosítást, hogy elérhetővé tegye a *kiszolgálót.*
* Ha töröljük a kulcsot a KeyVaultból, az Azure Database for PostgreSQL-kiszolgáló nem fog tudni hozzáférni a kulcshoz, és *elérhetetlen állapotba* lép. A kulcs [helyreállításával](../key-vault/general/key-vault-recovery.md) és az adattitkosítás újrajavalidálva elérhetővé teszi a *kiszolgálót.*
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

Miután Azure Database for PostgreSQL kiszolgáló az ügyfél felügyelt kulcsával van titkosítva, Key Vault kiszolgáló minden újonnan létrehozott példánya is titkosítva lesz. Ezt az új másolatot helyi vagy georedukciós művelettel, vagy olvasási replikákon keresztül is készítse el. A másolat azonban módosítható úgy, hogy tükrözze az új ügyfél titkosítási kulcsát. Ha az ügyfél által kezelt kulcsot megváltoztatják, a kiszolgáló régi biztonsági másolatai a legújabb kulcsot használják.

Az ügyfél által felügyelt adattitkosítás visszaállítása vagy olvasása során a problémák elkerülése érdekében fontos, hogy kövesse az alábbi lépéseket az elsődleges és a visszaállított/replikakiszolgálókon:

* Indítsa el a visszaállítási vagy olvasási replika-létrehozási folyamatot az Azure Database for PostgreSQL elsődleges kiszolgálóról.
* Tartsa elérhetetlen állapotban az újonnan létrehozott (visszaállított/replika) kiszolgálót, mert az egyedi identitása még nem kapott engedélyt a Key Vault.
* A visszaállított/replikakiszolgálón érvényesítse újra az ügyfél által felügyelt kulcsot az adattitkosítási beállításokban. Ez biztosítja, hogy az újonnan létrehozott kiszolgáló wrap és unwrap engedélyeket kap a kiszolgálón tárolt Key Vault.

## <a name="limitations"></a>Korlátozások

A Azure Database for PostgreSQL az ügyfelek által felügyelt kulccsal (CMK) használt, az ügyfelek által kezelt adatok titkosításának támogatása néhány korlátozással rendelkezik:

* Ennek a funkciónak a támogatása a **általános célú** **és** a Memóriaoptimalált tarifacsomagra korlátozódik.
* Ez a funkció csak olyan régiókban és kiszolgálókon érhető el, amelyek legfeljebb 16 TB-nyi tárterületet támogatnak. A legfeljebb 16 TEB tárhelyet támogató Azure-régiók listájáért tekintse meg a tárolási szakaszt az itt található [dokumentációban](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - A fent felsorolt régiókban létrehozott összes új PostgreSQL-kiszolgáló elérhető az ügyfélkezelői kulcsokkal való **titkosításhoz.** Az időponthoz időben visszaállított (PITR) kiszolgáló vagy olvasási replika elméletileg nem fog megfelelőnek minősülni.
    > - Annak ellenőrzéséhez, hogy a kiépített kiszolgáló támogatja-e a 16 TEB-t, a portál tarifacsomag paneljére használhatja, és láthatja a kiépített kiszolgáló által támogatott maximális tárterületméretet. Ha a csúszkát 4 TEB-re tudja áthelyezni, előfordulhat, hogy a kiszolgáló nem támogatja az ügyfél által kezelt kulcsokkal való titkosítást. Az adatok titkosítása azonban mindig szolgáltatás által kezelt kulcsokkal történik. Ha kérdése AskAzureDBforPostgreSQL@service.microsoft.com van, forduljon a következő e-hoz: .

* A titkosítás csak RSA 2048 titkosítási kulccsal támogatott.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan állíthatja be az adattitkosítást egy ügyfél által felügyelt kulccsal az egykiszolgálós [Azure-adatbázishoz](howto-data-encryption-portal.md)a Azure Portal.
