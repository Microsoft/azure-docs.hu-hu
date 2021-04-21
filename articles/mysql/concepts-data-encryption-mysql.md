---
title: Adattitkosítás ügyfél által felügyelt kulccsal – Azure Database for MySQL
description: Azure Database for MySQL felhasználó által felügyelt kulccsal való adattitkosítás lehetővé teszi, hogy Bring Your Own Key (BYOK) használatával védelmet biztosítson az adatok számára az adatok számára. Emellett lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésével járó feladatok elkülönítését.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: d8e40cf9dac496266f67ad94e1e65db01e42f9d2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816835"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Azure Database for MySQL felhasználó által kezelt kulccsal való adattitkosítás

Az Azure Database for MySQL ügyfél által felügyelt kulcsokkal történő titkosítása lehetővé teszi a saját kulcs használatát (BYOK) az inaktív adatok védelméhez. Emellett lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésével járó feladatok elkülönítését. Az ügyfél által felügyelt titkosítással Ön felel a kulcs életciklusáért, a kulcs használati engedélyeiért, és a kulcsokkal végzett műveletek naplózásáért.

Az ügyfél által felügyelt kulcsokkal való adattitkosítás Azure Database for MySQL a kiszolgáló szintjén van beállítva. Egy adott kiszolgálóhoz egy ügyfél által kezelt kulcs, a kulcstitkosítási kulcs (KEK) szolgál a szolgáltatás által használt adattitkosítási kulcs (DEK) titkosítására. A KEK egy aszimmetrikus kulcs, amely egy ügyfél tulajdonában lévő és ügyfél által felügyelt Azure Key Vault [tárol.](../key-vault/general/security-features.md) A kulcstitkosítási kulcsot (KEK) és az adattitkosítási kulcsot (DEK) a cikk későbbi, későbbi, részletes leírása ismerteti.

Key Vault egy felhőalapú, külső kulcskezelő rendszer. Magas rendelkezésre áll, és skálázható, biztonságos tárolást biztosít az RSA titkosítási kulcsok számára, opcionálisan a FIPS 140-2 2. szintje szerint ellenőrzött hardveres biztonsági modulok (HSM-ek) segítségével. Nem engedélyezi a tárolt kulcsokhoz való közvetlen hozzáférést, de titkosítási és visszafejtési szolgáltatásokat biztosít az engedélyezett entitások számára. Key Vault létrehozhatja, importálhatja a kulcsot, vagy átviheti egy helyszíni [HSM-eszközről.](../key-vault/keys/hsm-protected-keys.md)

> [!NOTE]
> Ez a funkció minden olyan Azure-régióban elérhető, Azure Database for MySQL támogatja a "általános célú" és a "Memóriaoptimalált" tarifacsomagokat. Egyéb korlátozásokért tekintse meg a korlátozásokkal [kapcsolatban szakaszt.](concepts-data-encryption-mysql.md#limitations)

## <a name="benefits"></a>Előnyök

Az ügyfél által felügyelt kulcsokkal való adattitkosítás Azure Database for MySQL az alábbi előnyökkel jár:

* Az adatelérést teljes mértékben Ön vezérli, ha eltávolítja a kulcsot, és elérhetetlenné teszi az adatbázist 
* A kulcs életciklusának teljes körű vezérlése, beleértve a kulcs rotációját a vállalati szabályzatok egymáshoz igazítása érdekében
* Központi kulcskezelés és -rendszerkezelés a Azure Key Vault
* A feladatok elkülönítésének megvalósítása a biztonsági rendszergazdák, valamint a DBA és a rendszergazdák között


## <a name="terminology-and-description"></a>Terminológia és leírás

**Adattitkosítási kulcs (DEK):** Szimmetrikus AES256-kulcs, amely egy partíció vagy adatblokk titkosítására használható. Az egyes adatblokkok eltérő kulccsal való titkosítása megnehezíti a titkosításelemzési támadásokat. Az adattitkos kulcsokat az erőforrás-szolgáltatónak vagy egy adott blokkot titkosító és visszafejtő alkalmazáspéldánynak kell elérnie. Ha egy adattitkos kulcsot új kulccsal cserél le, csak a hozzá tartozó blokkban található adatokat kell újratitkosítani az új kulccsal.

**Kulcstitkosítási kulcs (KEK):** Az adattitkosítási kulcsok titkosításához használt titkosítási kulcs. Egy olyan KEK, amely soha Key Vault, lehetővé teszi maguk az adattitkok titkosítását és vezérlését. A KEK-hez hozzáférő entitás különbözhet a dekkot igénylő entitástól. Mivel a KEK az adattitkok visszafejtéséhez szükséges, a KEK lényegében egyetlen pont, amellyel hatékonyan törölhetők az ADATTK-k a KEK törlésével.

A KEK-ekkel titkosított ADATTITK-okat a rendszer külön tárolja. Csak a KEK-hez hozzáféréssel rendelkezik entitás képes visszafejteni ezeket az eltitkokat. További információ: [Biztonság az adattitkosításban.](../security/fundamentals/encryption-atrest.md)

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Hogyan működik az adattitkosítás az ügyfél által felügyelt kulccsal?

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="Az áttekintést bemutató Bring Your Own Key":::

Ahhoz, hogy egy MySQL-kiszolgáló az Key Vault-ban tárolt, ügyfél által kezelt kulcsokat használva titkosítja az adattitkosítási kulcsot, egy Key Vault-rendszergazda a következő hozzáférési jogosultságokat biztosítja a kiszolgálóhoz:

* **get**: A kulcstartó nyilvános részének és tulajdonságainak leolvasása.
* **wrapKey:** Az adattitkos kulcs titkosításához. A titkosított adattitkos kulcs tárolása a Azure Database for MySQL.
* **unwrapKey:** A DEK visszafejtéséhez. Azure Database for MySQL a visszafejtött adattitkos kulcsra az adatok titkosításához/visszafejtéséhez

A Kulcstartó rendszergazdája a naplózási események naplózását [Key Vault](../azure-monitor/insights/key-vault-insights-overview.md)is engedélyezheti, hogy később naplózást végezhet.

Ha a kiszolgáló úgy van konfigurálva, hogy a kulcstartóban tárolt, ügyfél által kezelt kulcsot használja, a kiszolgáló elküldi a kulcstitkosítási kulcsot a kulcstartónak a titkosításhoz. Key Vault a felhasználói adatbázisban tárolt titkosított ADATTITKot adja vissza. Szükség esetén a kiszolgáló hasonlóképpen elküldi a védett DEK-et a kulcstartónak a visszafejtés érdekében. Ha a naplózás engedélyezve Azure Monitor, az auditorok Key Vault naplók áttekintésére is használhatók.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Az adattitkosítás konfigurálásával kapcsolatos követelmények Azure Database for MySQL

A következő követelmények vonatkoznak a Key Vault:

* Key Vault és Azure Database for MySQL (Azure AD Azure Active Directory bérlőhöz kell tartozni. A bérlők közötti Key Vault és kiszolgáló-interakciók nem támogatottak. A Key Vault erőforrás áthelyezéséhez újra kell konfigurálnia az adattitkosítást.
* Engedélyezze a [kulcstartón](../key-vault/general/soft-delete-overview.md) a **90** napos megőrzési időtartammal a kulcstartóban a véletlen kulcstörlések (vagy Key Vault) elkerülése érdekében. A rendszer alapértelmezés szerint 90 napig őrzi meg a nem törölt erőforrásokat, kivéve, ha a megőrzési időtartam kifejezetten <=90 napra van állítva. A helyreállító és véglegesen kiürítő műveletek saját engedélyekkel vannak társítva egy Key Vault hozzáférési szabályzatban. A soft-delete funkció alapértelmezés szerint ki van kapcsolva, de a PowerShell vagy az Azure CLI használatával engedélyezheti (vegye figyelembe, hogy nem engedélyezheti az Azure Portal).
* Engedélyezze [a Véglegesen kiürítés elleni](../key-vault/general/soft-delete-overview.md#purge-protection) védelmet a kulcstartón **90 napos megőrzési időtartammal.** A végleges törlés elleni védelem csak akkor engedélyezhető, ha a soft-delete engedélyezve van. Az Azure CLI-n vagy a PowerShellen keresztül lehet bekapcsolni. Ha a végleges törlés elleni védelem be van stb., a törölt állapotban van egy tároló vagy objektum nem törölhető, amíg a megőrzési időszak le nem telt. A helyreállíthatóan törölt tárolók és objektumok továbbra is helyreállíthatóak, így biztosítva a megőrzési szabályzat betartását. 
* Adjon hozzáférést Azure Database for MySQL kulcstartóhoz get, wrapKey és unwrapKey engedélyekkel az egyedi felügyelt identitásával. A Azure Portal egyedi "Szolgáltatás" identitás automatikusan létrejön, ha az adattitkosítás engedélyezve van a MySQL-lel. Az [adattitkosítás a MySQL-hez](howto-data-encryption-portal.md) való konfigurálásával kapcsolatos cikkben részletes, részletes útmutatást Azure Portal.

Az ügyfél által felügyelt kulcs konfigurálásához a következő követelmények vonatkoznak:

* Az adattitkos kulcs titkosításához használt ügyfél által kezelt kulcs csak aszimmetrikus lehet, RSA 2048.
* A kulcs aktiválási dátumának (ha be van állítva) múltbeli dátumnak és időpontnak kell lennie. A lejárati dátum nincs beállítva.
* A kulcsnak Engedélyezve állapotban *kell* lennie.
* A kulcsban a [megőrzési időtartamnak](../key-vault/general/soft-delete-overview.md) **90** napnak kell lennie. Ez implicit módon beállítja a szükséges recoveryLevel kulcsattribútumot: "Helyreállítható". Ha a megőrzési idő < 90 nap, akkor a "CustomizedRecoverable" helyreállítási szint , amely nem követelmény, ezért ügyeljen arra, hogy a **megőrzési idő 90** napra legyen beállítva.
* A kulcson engedélyezve kell lennie [a végleges kiürítés elleni védelemnek.](../key-vault/general/soft-delete-overview.md#purge-protection)
* Ha meglévő kulcsot importál [a](/rest/api/keyvault/ImportKey/ImportKey) kulcstartóba, mindenképpen a támogatott fájlformátumokban adja meg azt ( `.pfx` , , `.byok` `.backup` ).

## <a name="recommendations"></a>Javaslatok

Ha ügyfél által felügyelt kulccsal használ adattitkosítást, a következő javaslatok javaslatokat tartalmaznak az adatok Key Vault:

* Állítson be erőforrás-zárolást a Key Vault szabályozhatja, hogy ki törölheti ezt a kritikus erőforrást, és megelőzheti a véletlen vagy jogosulatlan törlést.
* Naplózás és jelentéskészítés engedélyezése az összes titkosítási kulcson. Key Vault olyan naplókat biztosít, amelyek könnyen bevethetőek más biztonsági információkba és eseménykezelő eszközökbe. Azure Monitor Log Analytics egy példa egy már integrált szolgáltatásra.
* Győződjön meg Key Vault, Azure Database for MySQL és az adatok ugyanabban a régióban találhatók, hogy gyorsabb hozzáférést biztosít a DEK-burkok és a kicsomagírási műveletek számára.
* Zárolja az Azure KeyVaultot a privát végpontra és a kiválasztott hálózatokra, és csak megbízható *Microsoft-szolgáltatások* számára engedélyezze az erőforrások biztonságát. 

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

Az ügyfél által felügyelt kulcsok konfigurálásához a következő javaslatok ajánlásokat tartalmaznak:

* Az ügyfél által felügyelt kulcs másolatát tartsa biztonságos helyen, vagy helyezze el a biztonsági másolati szolgáltatásban.

* Ha Key Vault hozza létre a kulcsot, először hozzon létre egy biztonsági másolatot a kulcsról. A biztonsági mentést csak a Key Vault. A biztonsági mentési paranccsal kapcsolatos további információkért lásd: [Backup-AzKeyVaultKey.](/powershell/module/az.keyVault/backup-azkeyVaultkey)

## <a name="inaccessible-customer-managed-key-condition"></a>Nem érhető el az ügyfél által felügyelt kulcs feltétele

Ha ügyfél által felügyelt kulccsal konfigurálja az adattitkosítást a Key Vault, a kiszolgáló online állapotban maradása érdekében folyamatos hozzáférésre van szükség ehhez a kulcshoz. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt kulcshoz a Key Vault, a kiszolgáló 10 percen belül elkezd megtagadni minden kapcsolatot. A kiszolgáló egy megfelelő hibaüzenetet ad ki, és elérhetetlenné teszi *a kiszolgáló állapotát.* Néhány ok, amiért a kiszolgáló eléri ezt az állapotot:

* Ha létrehozunk egy időponthoz időben visszaállító kiszolgálót a Azure Database for MySQL, amelyen engedélyezve van az adattitkosítás, az újonnan létrehozott kiszolgáló *Elérhetetlen állapotban* lesz. Ezt a probléma megoldását az Azure Portal vagy a CLI [segítségével](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) [oldhatja meg.](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)
* Ha létrehozunk egy olvasási replikát a Azure Database for MySQL, amelyen engedélyezve van az adattitkosítás, a replikakiszolgáló *Elérhetetlen állapotban* lesz. Ezt a probléma megoldását az Azure Portal vagy a CLI [segítségével](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) [oldhatja meg.](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)
* Ha törli a KeyVaultot, a Azure Database for MySQL nem fog tudni hozzáférni a kulcshoz, és *Elérhetetlen állapotba lép.* A kiszolgáló [Key Vault](../key-vault/general/key-vault-recovery.md) helyre, majd az adattitkosítás újrajavalidálva elérhetővé teszi a *kiszolgálót.*
* Ha töröljük a kulcsot a KeyVaultból, a Azure Database for MySQL nem fog tudni hozzáférni a kulcshoz, és *Elérhetetlen állapotba* lép. A kulcs [helyreállítása és](../key-vault/general/key-vault-recovery.md) az adattitkosítás újrajavalidása, hogy a kiszolgáló elérhető *legyen.*
* Ha az Azure KeyVaultban tárolt kulcs lejár, a kulcs érvénytelenné válik, és a Azure Database for MySQL elérhetetlen állapotba *kerül.* Terjessze ki a kulcs lejárati dátumát a [CLI](/cli/azure/keyvault/key#az_keyvault_key_set-attributes) használatával, majd ellenőrizze újra az adattitkosítást, hogy elérhetővé tegye a *kiszolgálót.*

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

Miután Azure Database for MySQL az ügyfél felügyelt kulcsával van titkosítva, Key Vault a kiszolgáló minden újonnan létrehozott példánya is titkosítva lesz. Ezt az új másolatot helyi vagy georedukciós művelettel, vagy olvasási replikákon keresztül is készítse el. A másolat azonban módosítható úgy, hogy tükrözze az új ügyfél titkosítási kulcsát. Ha az ügyfél által kezelt kulcsot megváltoztatják, a kiszolgáló régi biztonsági másolatai a legújabb kulcsot használják.

Az ügyfél által felügyelt adattitkosítás visszaállítás vagy olvasási replika létrehozása során való beállítása során a problémák elkerülése érdekében fontos, hogy kövesse az alábbi lépéseket a forrás- és a visszaállított/replikakiszolgálókon:

* Indítsa el a visszaállítási vagy olvasási replika-létrehozási folyamatot a forrás Azure Database for MySQL.
* Tartsa elérhetetlen állapotban az újonnan létrehozott kiszolgálót (visszaállított/replika), mert az egyedi identitása még nem kapott engedélyt a Key Vault.
* A visszaállított/replikakiszolgálón az adattitkosítási beállításokban újraértékelje újra az ügyfél által felügyelt kulcsot, hogy az újonnan létrehozott kiszolgáló burkot és kicsomagot is kapjon a Key Vault.

## <a name="limitations"></a>Korlátozások

A Azure Database for MySQL esetében az ügyfelek által felügyelt kulccsal (CMK) használt, nem használt adatok titkosításának támogatása néhány korlátozással rendelkezik:

* Ennek a funkciónak a támogatása **a** általános célú  memóriaoptimalitott tarifacsomagra korlátozódik.
* Ez a funkció csak olyan régiókban és kiszolgálókon érhető el, amelyek legfeljebb 16 TB-nyi tárterületet támogatnak. A legfeljebb 16 TEB tárhelyet támogató Azure-régiók listájáért tekintse meg a tárolási szakaszt az itt található [dokumentációban](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - A fent felsorolt régiókban létrehozott összes új MySQL-kiszolgáló, az ügyfélkezelői kulcsokkal való titkosítás támogatása **elérhető.** Az időponthoz időben visszaállított (PITR) kiszolgáló vagy olvasási replika nem fog megfelelőnek minősülni, bár elméletben "újak".
    > - Annak ellenőrzéséhez, hogy a kiépített kiszolgáló támogatja-e a 16 TEB-t, a portál tarifacsomag paneljére használhatja, és láthatja a kiépített kiszolgáló által támogatott maximális tárterületméretet. Ha a csúszkát 4 TEB-re tudja felfelé mozgatni, előfordulhat, hogy a kiszolgáló nem támogatja az ügyfél által kezelt kulcsokkal való titkosítást. Az adatok titkosítása azonban mindig szolgáltatás által felügyelt kulcsokkal történik. Ha kérdése van, forduljon a AskAzureDBforMySQL@service.microsoft.com következő e-hoz: .

* A titkosítás csak az RSA 2048 titkosítási kulccsal támogatott.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan állíthatja be az adattitkosítást egy ügyfél által felügyelt kulccsal a MySQL-hez használt [Azure-adatbázishoz](howto-data-encryption-portal.md) a Azure Portal és az [Azure CLI használatával.](howto-data-encryption-cli.md)
