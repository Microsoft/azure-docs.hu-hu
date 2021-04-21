---
title: Adattitkosítás ügyfél által felügyelt kulccsal – Azure Database for MySQL
description: Azure Database for MySQL felhasználó által kezelt kulccsal való adattitkosítás lehetővé teszi, hogy Bring Your Own Key (BYOK) használatával védelmet biztosítsunk az adatok védelméhez. Emellett lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésével járó feladatok elkülönítését.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 809827fc61cde3c8077484dbe2a5be5037609cd8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748547"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Azure Database for MySQL által kezelt kulccsal való adattitkosítás

Az Azure Database for MySQL ügyfél által felügyelt kulcsokkal történő titkosítása lehetővé teszi a saját kulcs használatát (BYOK) az inaktív adatok védelméhez. Emellett lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésével járó feladatok elkülönítését. Az ügyfél által felügyelt titkosítással Ön felel a kulcs életciklusáért, a kulcs használati engedélyeiért, és a kulcsokkal végzett műveletek naplózásáért.

Az ügyfél által felügyelt kulcsokkal való adattitkosítás Azure Database for MySQL kiszolgálószinten van beállítva. Egy adott kiszolgálóhoz egy ügyfél által felügyelt kulcs, a kulcstitkosítási kulcs (KEK) szolgál a szolgáltatás által használt adattitkosítási kulcs (DEK) titkosítására. A KEK egy aszimmetrikus kulcs, amely egy ügyfél tulajdonában lévő és ügyfél által felügyelt Azure Key Vault [tárol.](../key-vault/general/security-overview.md) A kulcstitkosítási kulcsot (KEK) és az adattitkosítási kulcsot (DEK) a cikk későbbi, későbbi, részletes leírása ismerteti.

Key Vault egy felhőalapú, külső kulcskezelő rendszer. Magas rendelkezésre áll, és skálázható, biztonságos tárolást biztosít az RSA titkosítási kulcsok számára, igény szerint a FIPS 140-2 2. szintje szerint ellenőrzött hardveres biztonsági modulok (HSM-ek) segítségével. Nem engedélyezi a tárolt kulcsok közvetlen hozzáférését, de titkosítási és visszafejtési szolgáltatásokat nyújt az engedélyezett entitások számára. Key Vault létrehozhatja, importálhatja, vagy átviheti egy helyszíni [HSM-eszközről.](../key-vault/keys/hsm-protected-keys.md)

> [!NOTE]
> Ez a funkció minden olyan Azure-régióban elérhető, Azure Database for MySQL támogatja a "általános célú" és a "Memóriaoptimalált" tarifacsomagot. Egyéb korlátozásokért tekintse meg a korlátozások [szakaszát.](concepts-data-encryption-mysql.md#limitations)

## <a name="benefits"></a>Előnyök

Az ügyfél által felügyelt kulcsokkal való adattitkosítás Azure Database for MySQL az alábbi előnyöket biztosítja:

* Az adatelérést teljes mértékben Ön vezérli, ha eltávolítja a kulcsot, és elérhetetlenné teszi az adatbázist 
* A kulcs életciklusának teljes körű vezérlése, beleértve a kulcs rotációját a vállalati szabályzatok egymáshoz igazítása érdekében
* A kulcsok központi kezelése és rendszerkezelése a Azure Key Vault
* A feladatok elkülönítésének lehetősége a biztonsági rendszergazdák, valamint a DBA és a rendszergazdák között


## <a name="terminology-and-description"></a>Terminológia és leírás

**Adattitkosítási kulcs (DEK):** Szimmetrikus AES256-kulcs partíció vagy adatblokk titkosításához. Az egyes adatblokkok eltérő kulccsal való titkosítása megnehezíti a titkosításelemzési támadásokat. Az adattitkos kulcsokat az erőforrás-szolgáltatónak vagy egy adott blokkot titkosító és visszafejtő alkalmazáspéldánynak kell elérnie. Amikor lecserél egy adattitkos kulcsokat egy új kulcsra, csak a hozzá tartozó blokkban található adatokat kell újratitkosítani az új kulccsal.

**Kulcstitkosítási kulcs (KEK):** Az adattitkosítási kulcsok titkosításához használt titkosítási kulcs. Az adattitkos kulcstitkok titkosítását és vezérlését Key Vault KEK teszi lehetővé, hogy a kódtitkok soha ne hagyjanak el. A KEK-hez hozzáférő entitás különbözhet a dekkot igénylő entitástól. Mivel a KEK-nek vissza kell fejtenie az adattitkokat, a KEK lényegében egyetlen pont, amellyel hatékonyan törölhetők az ADATTK-k a KEK törlésével.

A KEK-ekkel titkosított ADATTITK-okat külön tárolja a rendszer. Ezeket a DEK-okat csak a KEK-hez hozzáféréssel felfejthető entitások használhatja. További információ: [Security in encryption in rest](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Hogyan működik az adattitkosítás egy ügyfél által felügyelt kulccsal?

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="Diagram a Bring Your Own Key":::

Ahhoz, hogy egy MySQL-kiszolgáló az Key Vault-ban tárolt, ügyfél által kezelt kulcsokat használva titkosítja az adattitkosítási kulcsot, egy Key Vault-rendszergazda a következő hozzáférési jogokat biztosítja a kiszolgálóhoz:

* **get**: A kulcs nyilvános részének és tulajdonságainak leolvasása a kulcstartóban.
* **wrapKey:** Az adattitkos kulcs titkosításához. A titkosított adattitkos adattitkos kulcs a Azure Database for MySQL.
* **unwrapKey:** A DEK visszafejtéséhez. Azure Database for MySQL az adatok titkosításához/visszafejtéséhez a visszafejtett DEK-re van szükség

A Kulcstartó rendszergazdája engedélyezheti a naplózási Key Vault [naplózását](../azure-monitor/insights/key-vault-insights-overview.md)is, hogy később is naplót végezhet.

Ha a kiszolgáló úgy van konfigurálva, hogy a kulcstartóban tárolt, ügyfél által kezelt kulcsot használja, a kiszolgáló elküldi az adattitkosítási kulcsot a kulcstartónak titkosításra. Key Vault a felhasználói adatbázisban tárolt titkosított ADATTITK-t adja vissza. Szükség esetén a kiszolgáló a kulcstartónak is elküldi a védett DEK-et a visszafejtés érdekében. Ha a naplózás engedélyezve Azure Monitor, az auditorok Key Vault naplók áttekintésére is használhatók.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Az adattitkosítás konfigurálásával kapcsolatos követelmények Azure Database for MySQL

A következő követelmények vonatkoznak a Key Vault:

* Key Vault és Azure Database for MySQL ugyanannak a bérlőnek (Azure AD Azure Active Directory kell lennie. A több-Key Vault és kiszolgáló-interakciók nem támogatottak. Ezt Key Vault erőforrás áthelyezéséhez újra kell konfigurálnia az adattitkosítást.
* Engedélyezze a kulcstartóban a **90** napos megőrzési időtartamú kulcstartó [soft-delete](../key-vault/general/soft-delete-overview.md) funkcióját az adatvesztés elleni védelem érdekében, ha véletlenül kulcstörlésre (vagy Key Vault kerül sor). A rendszer alapértelmezés szerint 90 napig őrzi meg a nem törölt erőforrásokat, kivéve, ha a megőrzési idő kifejezetten <=90 napra van állítva. A helyreállító és véglegesen kiürítő műveletek saját engedélyekkel vannak társítva a Key Vault hozzáférési szabályzatban. A soft-delete funkció alapértelmezés szerint ki van kapcsolva, de a PowerShellen vagy az Azure CLI-n keresztül engedélyezheti (vegye figyelembe, hogy nem engedélyezheti az Azure Portal).
* Engedélyezze a [Véglegesen kiürítés elleni védelem](../key-vault/general/soft-delete-overview.md#purge-protection) funkciót a kulcstartón **90 napos megőrzési időtartammal.** A végleges törlés elleni védelem csak akkor engedélyezhető, ha a soft-delete engedélyezve van. Az Azure CLI-n vagy a PowerShellen keresztül lehet bekapcsolni. Ha a végleges törlés elleni védelem be van va, a törölt állapotban van egy tároló vagy objektum nem törölhető, amíg a megőrzési időtartam el nem telt. A helyreállíthatóan törölt tárolók és objektumok továbbra is helyreállíthatóak, így biztosítva a megőrzési szabályzat betartását. 
* Adjon hozzáférést Azure Database for MySQL kulcstartóhoz get, wrapKey és unwrapKey engedélyekkel az egyedi felügyelt identitásával. A Azure Portal egyedi "Szolgáltatás" identitás automatikusan létrejön, ha az adattitkosítás engedélyezve van a MySQL-hez. Az [adattitkosítás a MySQL-hez](howto-data-encryption-portal.md) való konfigurálásával kapcsolatos cikkben részletes, részletes útmutatást Azure Portal.

Az ügyfél által felügyelt kulcs konfigurálásához a következő követelmények vonatkoznak:

* A DEK titkosításához használt, felhasználó által kezelt kulcs csak aszimmetrikus RSA 2048 lehet.
* A kulcs aktiválási dátumának (ha be van állítva) egy múltbeli dátumnak és időpontnak kell lennie. A lejárati dátum nincs beállítva.
* A kulcsnak Engedélyezve állapotban *kell* lennie.
* A kulcshoz a [megőrzési időtartamnak](../key-vault/general/soft-delete-overview.md) **90** napnak kell lennie. Ez implicit módon beállítja a szükséges recoveryLevel kulcsattribútumot: "Helyreállítható". Ha a megőrzési idő < 90 nap, akkor a recoveryLevel: "CustomizedRecoverable" (Testreszabható) beállítás nem követelmény, ezért ügyeljen arra, hogy a **megőrzési idő 90** napra legyen beállítva.
* A kulcson engedélyezni kell [a véglegesen kiürítés elleni védelmet.](../key-vault/general/soft-delete-overview.md#purge-protection)
* Ha meglévő kulcsot importál [a](/rest/api/keyvault/ImportKey/ImportKey) kulcstartóba, győződjön meg arról, hogy a kulcs a támogatott fájlformátumokban (, , ) van `.pfx` `.byok` `.backup` megszabadva.

## <a name="recommendations"></a>Javaslatok

Ha ügyfél által felügyelt kulccsal használ adattitkosítást, az Key Vault:

* Állítson be erőforrás-zárolást a Key Vault szabályozhatja, hogy ki törölheti ezt a kritikus erőforrást, és hogyan előzheti meg a véletlen vagy jogosulatlan törlést.
* Naplózás és jelentéskészítés engedélyezése az összes titkosítási kulcson. Key Vault olyan naplókat biztosít, amelyek könnyen bevethetőek más biztonsági információkba és eseménykezelő eszközökbe. Azure Monitor Log Analytics egy példa a már integrált szolgáltatásokra.
* Győződjön meg Key Vault, Azure Database for MySQL és az adatok ugyanabban a régióban találhatók, így gyorsabb hozzáférést biztosít a DEK-burkhoz és a kiírási műveletekhez.
* Zárolja az Azure KeyVaultot csak a privát végpontra és a kiválasztott hálózatokra, és csak megbízható *Microsoft-szolgáltatások* számára engedélyezze az erőforrások biztonságát. 

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

Az ügyfél által felügyelt kulcsok konfigurálásához a következő javaslatok javaslatokat íme:

* Az ügyfél által kezelt kulcs másolatát tartsa biztonságos helyen, vagy helyezze el a escrow szolgáltatásban.

* Ha Key Vault hozza létre a kulcsot, hozzon létre egy biztonsági másolatot a kulcsról, mielőtt első alkalommal használ ilyet. A biztonsági mentést csak a korábbi Key Vault. A biztonsági mentési paranccsal kapcsolatos további információkért lásd: [Backup-AzKeyVaultKey.](/powershell/module/az.keyVault/backup-azkeyVaultkey)

## <a name="inaccessible-customer-managed-key-condition"></a>Nem érhető el az ügyfél által felügyelt kulcs feltétele

Ha ügyfél által felügyelt kulccsal konfigurálja az adattitkosítást a Key Vault, a kiszolgáló online állapotban maradása érdekében folyamatos hozzáférésre van szükség ehhez a kulcshoz. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt kulcshoz a Key Vault, a kiszolgáló 10 percen belül elkezdi megtagadni az összes kapcsolatot. A kiszolgáló egy megfelelő hibaüzenetet ad ki, és elérhetetlenné teszi *a kiszolgáló állapotát.* Néhány ok, amiért a kiszolgáló elérheti ezt az állapotot:

* Ha létrehozunk egy időponthoz időben visszaállító kiszolgálót a Azure Database for MySQL, amelyen engedélyezve van az adattitkosítás, az újonnan létrehozott kiszolgáló *Elérhetetlen állapotban* lesz. Ezt a probléma megoldását [a(Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) cli) [segítségével oldhatja meg.](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)
* Ha létrehozunk egy olvasási replikát a Azure Database for MySQL, amelyen engedélyezve van az adattitkosítás, a replikakiszolgáló *Elérhetetlen állapotban* lesz. Ezt a probléma megoldását [a(Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) cli) [segítségével oldhatja meg.](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)
* Ha törli a KeyVaultot, a Azure Database for MySQL nem fog tudni hozzáférni a kulcshoz, és *Elérhetetlen állapotba* lép. A kiszolgáló [Key Vault](../key-vault/general/key-vault-recovery.md) helyre, majd ellenőrizze újra az adattitkosítást, hogy elérhetővé tegye a *kiszolgálót.*
* Ha töröljük a kulcsot a KeyVaultból, a Azure Database for MySQL nem fog tudni hozzáférni a kulcshoz, és *Elérhetetlen állapotba lép.* A kulcs [helyreállításával](../key-vault/general/key-vault-recovery.md) és az adattitkosítás újrajavalidálva elérhetővé teszi a *kiszolgálót.*
* Ha az Azure KeyVaultban tárolt kulcs lejár, a kulcs érvénytelenné válik, és a Azure Database for MySQL elérhetetlen *állapotba* kerül. Terjessze ki a kulcs lejárati dátumát a [CLI](/cli/azure/keyvault/key#az-keyvault-key-set-attributes) használatával, majd ellenőrizze újra az adattitkosítást, hogy elérhetővé tegye a *kiszolgálót.*

### <a name="accidental-key-access-revocation-from-key-vault"></a>Kulcshoz való véletlen hozzáférés visszavonása a Key Vault

Előfordulhat, hogy valaki, aki megfelelő hozzáférési jogosultságokkal rendelkezik Key Vault véletlenül letiltja a kiszolgáló kulcshoz való hozzáférését a következővel:

* A kulcstartó get, wrapKey és unwrapKey engedélyeinek visszaírása a kiszolgálóról.
* A kulcs törlése.
* A kulcstartó törlése.
* A kulcstartó tűzfalszabályainak módosítása.
* A kiszolgáló felügyelt identitásának törlése az Azure AD-ban.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Az ügyfél által felügyelt kulcs figyelése a Key Vault

Az adatbázis állapotának monitorozásához és a transzparens adattitkosítási védő hozzáférés elvesztésével kapcsolatos riasztások engedélyezéséhez konfigurálja a következő Azure-szolgáltatásokat:

* [Azure Resource Health:](../service-health/resource-health-overview.md)Egy elérhetetlen adatbázis, amely nem fér hozzá az ügyfélkulcshoz, elérhetetlenként jelenik meg az adatbázissal való első kapcsolat megtagadása után.
* [Tevékenységnapló:](../service-health/alerts-activity-log-service-notifications-portal.md)Ha az ügyfél által felügyelt Key Vault ügyfélkulcshoz való hozzáférés meghiúsul, a rendszer bejegyzéseket ad hozzá a tevékenységnaplóhoz. Ha riasztásokat hoz létre ezekhez az eseményekhez, a lehető leghamarabb visszaállíthatja a hozzáférést.

* [Műveletcsoportok:](../azure-monitor/alerts/action-groups.md)Adja meg ezeket a csoportokat, hogy a beállítások alapján értesítéseket és riasztásokat küldjenek Önnek.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Visszaállítás és replikálás az ügyfél felügyelt kulcsával a Key Vault

Miután Azure Database for MySQL az ügyfél felügyelt kulcsával van titkosítva, Key Vault a kiszolgáló minden újonnan létrehozott példánya is titkosítva lesz. Ezt az új másolatot helyi vagy georedukciós művelettel, vagy olvasási replikákon keresztül is el lehet készítse. A másolat azonban módosítható úgy, hogy tükrözze az új ügyfél titkosítási kulcsát. Ha az ügyfél által felügyelt kulcsot megváltoztatják, a kiszolgáló régi biztonsági másolatai a legújabb kulcs használatával indulnak el.

Az ügyfél által felügyelt adattitkosítás visszaállítás vagy olvasási replika létrehozása során való beállítása során a problémák elkerülése érdekében fontos, hogy kövesse az alábbi lépéseket a forrás- és a visszaállított/replikakiszolgálókon:

* Indítsa el a replika-létrehozási vagy -visszaállítási folyamatot a forrás-Azure Database for MySQL.
* Tartsa elérhetetlen állapotban az újonnan létrehozott (visszaállított/replika) kiszolgálót, mert az egyedi identitása még nem kapott engedélyt a Key Vault.
* A visszaállított/replikakiszolgálón az adattitkosítási beállítások között újraértékelje az ügyfél által felügyelt kulcsot, hogy az újonnan létrehozott kiszolgáló burkkezelés és kiírás engedélyeket kapjon a Key Vault.

## <a name="limitations"></a>Korlátozások

A Azure Database for MySQL az ügyfelek által felügyelt kulccsal (CMK) használt, az ügyfelek által kezelt adatok titkosításának támogatása korlátozott:

* Ennek a funkciónak a támogatása a **általános célú** **és** a Memóriaoptimalált tarifacsomagra korlátozódik.
* Ez a funkció csak olyan régiókban és kiszolgálókon érhető el, amelyek legfeljebb 16 TB-nyi tárterületet támogatnak. A legfeljebb 16 TEB tárhelyet támogató Azure-régiók listájáért tekintse meg a tárolási szakaszt az itt található [dokumentációban](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - A fent felsorolt régiókban létrehozott összes új MySQL-kiszolgáló, az ügyfélkezelői kulcsokkal való titkosítás támogatása **elérhető.** Az időponthoz időben visszaállított (PITR) kiszolgáló vagy olvasási replika elméletileg nem fog megfelelőnek minősülni.
    > - Annak ellenőrzéséhez, hogy a kiépített kiszolgáló támogatja-e a 16 TEB-t, a portál tarifacsomag paneljére használhatja, és láthatja a kiépített kiszolgáló által támogatott maximális tárterületméretet. Ha a csúszkát 4 TEB-re tudja áthelyezni, előfordulhat, hogy a kiszolgáló nem támogatja az ügyfél által kezelt kulcsokkal való titkosítást. Az adatok titkosítása azonban mindig szolgáltatás által kezelt kulcsokkal történik. Ha kérdése AskAzureDBforMySQL@service.microsoft.com van, forduljon a következő e-hoz: .

* A titkosítás csak RSA 2048 titkosítási kulccsal támogatott.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan állíthatja be az adattitkosítást egy ügyfél által felügyelt kulccsal a MySQL-hez használt [Azure-adatbázishoz](howto-data-encryption-portal.md) a Azure Portal és az [Azure CLI használatával.](howto-data-encryption-cli.md)
