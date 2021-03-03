---
title: Adattitkosítás az ügyfél által felügyelt kulccsal – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Azure Database for PostgreSQL egy ügyfél által felügyelt kulccsal rendelkező egykiszolgálós adattitkosítás lehetővé teszi, hogy Bring Your Own Key (BYOK) a REST-alapú adatvédelem érdekében. Emellett lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésével járó feladatok elkülönítését.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 66faa2b3e6d24c264e2fe26ab42eeaffd48384f6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732836"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Egyetlen kiszolgálóból álló adattitkosítás Azure Database for PostgreSQL ügyfél által felügyelt kulccsal

Az Azure PostgreSQL a Microsoft által felügyelt kulcsok használatával alapértelmezés szerint titkosítja az [Azure Storage-titkosítást](../storage/common/storage-service-encryption.md) a REST-adatok titkosításához. Az Azure PostgreSQL-felhasználók esetében nagyon hasonlít a más adatbázisok (például a SQL Server) transzparens adattitkosítás (TDE). Számos szervezet az ügyfél által felügyelt kulccsal teljes hozzáférést igényel az adathoz való hozzáféréshez. Az adatok titkosítása az ügyfél által felügyelt kulcsokkal Azure Database for PostgreSQL egyetlen kiszolgálón lehetővé teszi a saját kulcs (BYOK) használatát a REST-alapú adatvédelem érdekében. Emellett lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésével járó feladatok elkülönítését. Az ügyfél által felügyelt titkosítással Ön felel a kulcs életciklusáért, a kulcs használati engedélyeiért, és a kulcsokkal végzett műveletek naplózásáért.

Az ügyfél által felügyelt kulcsokkal rendelkező adattitkosítást Azure Database for PostgreSQL egyetlen kiszolgáló esetén a kiszolgáló szintjén kell beállítani. Egy adott kiszolgáló esetében a szolgáltatás által használt adattitkosítási kulcs (ADATTITKOSÍTÁSI kulcsot) titkosítására a Key encryption Key (KEK) nevű ügyfél által felügyelt kulcs szolgál. A KEK egy, az ügyfél és az ügyfél által felügyelt [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) -példányban tárolt aszimmetrikus kulcs. A kulcs titkosítási kulcsát (KEK) és az adattitkosítási kulcsot (ADATTITKOSÍTÁSI kulcsot) a cikk későbbi részében részletesebben ismertetjük.

A Key Vault egy felhőalapú, külső kulcsokat kezelő rendszer. Magas rendelkezésre állású, és méretezhető, biztonságos tárolást biztosít az RSA titkosítási kulcsokhoz, opcionálisan a FIPS 140-2 2-es szintű, ellenőrzött hardveres biztonsági modulok (HSM-k) által támogatottak. Nem engedélyezi a közvetlen hozzáférést egy tárolt kulcshoz, de biztosítja a titkosítási és visszafejtési szolgáltatásokat a jogosult entitások számára. Key Vault a kulcsot létrehozhatja, importálhatja, vagy [áthelyezheti egy helyszíni HSM-eszközről](../key-vault/keys/hsm-protected-keys.md).

> [!NOTE]
> Ez a funkció minden olyan Azure-régióban elérhető, ahol Azure Database for PostgreSQL egyetlen kiszolgáló támogatja a "általános célú" és a "memória-optimalizálva" árképzési csomagokat. Egyéb korlátozások esetén tekintse meg a [korlátozás](concepts-data-encryption-postgresql.md#limitations) szakaszt.

## <a name="benefits"></a>Előnyök

Az ügyfél által felügyelt kulcsokkal rendelkező Azure Database for PostgreSQL önálló kiszolgálókon az alábbi előnyökkel jár az Adattitkosítás:

* Az adathozzáférést teljes mértékben a rendszer ellenőrzi, hogy el tudja-e távolítani a kulcsot, és elérhetetlenné teszi az adatbázist 
*    Teljes hozzáférés a kulcs-életciklushoz, beleértve a kulcs rotációját a vállalati házirendekkel való összehangoláshoz
*    A Azure Key Vaultban található kulcsok központi felügyelete és szervezete
*    A feladatok elkülönítésének lehetősége a biztonsági tisztviselők, valamint a DBA és a rendszergazdák között

## <a name="terminology-and-description"></a>Terminológia és leírás

**Adattitkosítási kulcs (adattitkosítási kulcsot)**: egy partíció vagy adatblokk titkosításához használt szimmetrikus AES256-kulcs. Az egyes adatblokkok egy másik kulccsal való titkosítása nehezebbé teszi a titkosítási elemzési támadásokat. A DEKs való hozzáférésre az erőforrás-szolgáltató vagy az alkalmazás-példány szükséges, amely egy adott blokk titkosítását és visszafejtését végzi. Amikor új kulccsal cserél le egy ADATTITKOSÍTÁSI kulcsot, csak a hozzá tartozó blokkban lévő adatmennyiséget kell újra titkosítani az új kulccsal.

**Kulcs titkosítási kulcsa (KEK)**: a DEKs titkosításához használt titkosítási kulcs. Egy KEK, amely soha nem hagy Key Vault lehetővé teszi, hogy a DEKs titkosítva és vezérelve legyenek. Előfordulhat, hogy a KEK-hez hozzáférő entitás nem azonos a ADATTITKOSÍTÁSI kulcsot igénylő entitással. Mivel a KEK a DEKs visszafejtéséhez szükséges, a KEK gyakorlatilag egyetlen pont, amellyel a DEKs hatékonyan törölhető a KEK törlésével.

A KEK titkosított DEKs külön tárolja a rendszer. Csak egy KEK-hozzáféréssel rendelkező entitás képes visszafejteni ezeket a DEKs. További információ: [Biztonság a titkosításban a REST-ben](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Az adattitkosítás használata az ügyfél által felügyelt kulcsokkal

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png" alt-text="A Bring Your Own Key áttekintését bemutató diagram":::

Ahhoz, hogy a PostgreSQL-kiszolgáló a ADATTITKOSÍTÁSI kulcsot titkosításához Key Vaultban tárolt ügyfél által felügyelt kulcsokat használjon, a Key Vault rendszergazdája a következő hozzáférési jogosultságokat biztosítja a kiszolgálóhoz:

* **beolvasás: a** Key vaultban lévő kulcs nyilvános részének és tulajdonságainak lekérése.
* **wrapKey**: a adattitkosítási kulcsot titkosítása. A titkosított ADATTITKOSÍTÁSI kulcsot a Azure Database for PostgreSQL tárolja.
* **unwrapKey**: a adattitkosítási kulcsot visszafejtéséhez. Azure Database for PostgreSQL a visszafejtett ADATTITKOSÍTÁSI kulcsot szükséges az adattitkosításhoz/visszafejtéshez

A Key Vault rendszergazdája [engedélyezheti Key Vault naplózási események naplózását](../azure-monitor/insights/key-vault-insights-overview.md)is, így később is naplózhatja őket.

Ha a kiszolgáló a Key vaultban tárolt ügyfél által felügyelt kulcs használatára van konfigurálva, a kiszolgáló elküldi a ADATTITKOSÍTÁSI kulcsot a titkosításhoz a Key vaultba. Key Vault a felhasználói adatbázisban tárolt titkosított ADATTITKOSÍTÁSI kulcsot adja vissza. Hasonlóképpen, ha szükséges, a kiszolgáló a védett ADATTITKOSÍTÁSI kulcsot a Key vaultba küldi a visszafejtéshez. A könyvvizsgálók a Azure Monitor segítségével ellenőrizhetik Key Vault naplózási eseménynaplókat, ha engedélyezve van a naplózás.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Az Azure Database for PostgreSQL egyetlen kiszolgáló adattitkosításának konfigurálására vonatkozó követelmények

A Key Vault konfigurálásának követelményei a következők:

* Key Vault és Azure Database for PostgreSQL egyetlen kiszolgálónak ugyanahhoz a Azure Active Directory (Azure AD) bérlőhöz kell tartoznia. A több-bérlős Key Vault és a kiszolgálói interakciók nem támogatottak. A Key Vault erőforrás áthelyezéséhez ezt követően újra kell konfigurálnia az adattitkosítást.
* A Key vaultot a törölt tárolók megtartásához 90 nappal kell beállítani. Ha a meglévő kulcstartó kisebb számmal lett konfigurálva, létre kell hoznia egy új kulcstartót, mivel a létrehozás után nem módosítható.
* Az adatvesztés elleni védelem érdekében engedélyezze a Soft delete funkciót a Key vaultban, ha véletlen kulcs (vagy Key Vault) törlése történik. A Soft-Deleted erőforrásokat 90 napig őrzi meg a rendszer, hacsak a felhasználó addig nem helyreállítja vagy törli őket. A helyreállítás és törlés műveletekhez saját engedélyek tartoznak egy Key Vault hozzáférési házirendben. A Soft-delete funkció alapértelmezés szerint ki van kapcsolva, de a PowerShell vagy az Azure CLI használatával is engedélyezhető (vegye figyelembe, hogy nem engedélyezheti a Azure Portal). 
* A törlési védelem engedélyezése a törölt tárolók és tároló objektumok kötelező megőrzési időtartamának betartatásához
* Az egyedi felügyelt identitás használatával adja meg az Azure Database for PostgreSQL egyetlen kiszolgáló hozzáférését a Key vaulthoz a Get, a wrapKey és a unwrapKey engedélyekkel. A Azure Portal az egyedi "szolgáltatás" identitás automatikusan létrejön, ha az adattitkosítás engedélyezve van a PostgreSQL-kiszolgálón. A Azure Portal használatakor részletes útmutatásért lásd: [Azure Database for PostgreSQL az egyetlen Azure Portal kiszolgáló adattitkosítása](howto-data-encryption-portal.md) .

Az ügyfél által felügyelt kulcs konfigurálásának követelményei a következők:

* A ADATTITKOSÍTÁSI kulcsot titkosításához használt ügyfél által felügyelt kulcs csak aszimmetrikus, RSA 2048 lehet.
* A kulcs aktiválási dátumát (ha be van állítva) a múltban dátumnak és időpontnak kell lennie. A lejárati dátumnak (ha be van állítva) jövőbeli dátumnak és időpontnak kell lennie.
* A kulcsnak *engedélyezett* állapotban kell lennie.
* Ha [meglévő kulcsot importál](/rest/api/keyvault/ImportKey/ImportKey) a kulcstartóba, győződjön meg arról, hogy a támogatott fájlformátumokban ( `.pfx` , `.byok` ,) meg van-e biztosítva `.backup` .

## <a name="recommendations"></a>Javaslatok

Ha ügyfél által felügyelt kulccsal használja az adattitkosítást, a Key Vault konfigurálására vonatkozó ajánlásokat itt találja:

* Állítsa be Key Vault erőforrás-zárolását annak szabályozására, hogy ki törölheti ezt a kritikus erőforrást, és megelőzheti a véletlen vagy jogosulatlan törlést.
* Az összes titkosítási kulcs naplózásának és jelentéskészítésének engedélyezése. A Key Vault olyan naplókat biztosít, amelyek könnyen beilleszthető más biztonsági információkba és az eseménykezelő eszközeibe. Azure Monitor Log Analytics egy olyan szolgáltatás, amely már integrálva van.
* Győződjön meg arról, hogy az egyetlen kiszolgáló Key Vault és Azure Database for PostgreSQL ugyanabban a régióban található, így biztosítva, hogy a ADATTITKOSÍTÁSI kulcsot-wrap és a kicsomagolási műveletek gyorsabban hozzáférhessenek.
* Az Azure kulcstartó zárolása csak **privát végpontok és kiválasztott hálózatok** számára, és csak *megbízható Microsoft* -szolgáltatások engedélyezése az erőforrások biztonságossá tételéhez.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="megbízható szolgáltatás – AKV":::

Az ügyfél által felügyelt kulcs konfigurálására vonatkozó javaslatok:

* Őrizze meg az ügyfél által felügyelt kulcs másolatát egy biztonságos helyen, vagy helyezze letétbe a letéti szolgáltatásban.

* Ha Key Vault generálja a kulcsot, hozzon létre egy kulcsos biztonsági másolatot, mielőtt első alkalommal használja a kulcsot. A biztonsági mentést csak Key Vaultra állíthatja vissza. A Backup parancsról további információt a [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey)című témakörben talál.

## <a name="inaccessible-customer-managed-key-condition"></a>Nem érhető el az ügyfél által felügyelt kulcs állapota

Ha az adattitkosítást Key Vault ügyfél által felügyelt kulccsal konfigurálja, akkor a kiszolgálóhoz való folyamatos hozzáférés szükséges ahhoz, hogy a kiszolgáló online maradjon. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt kulcshoz Key Vault, a kiszolgáló 10 percen belül megtagadja az összes kapcsolatot. A kiszolgáló kiadja a megfelelő hibaüzenetet, és a kiszolgáló állapotát nem *elérhetőre* módosítja. Néhány ok, amiért a kiszolgáló elérheti ezt az állapotot:

* Ha egy időponthoz tartozó visszaállítási kiszolgálót hoz létre a Azure Database for PostgreSQL egyetlen kiszolgálóhoz, amelynek engedélyezve van az adattitkosítása, az újonnan létrehozott kiszolgáló *elérhetetlen* állapotban lesz. A kiszolgáló állapotát [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) vagy [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)használatával javíthatja.
* Ha olyan olvasási replikát hozunk létre a Azure Database for PostgreSQL egyetlen kiszolgálóhoz, amelynek engedélyezve van az adattitkosítása, a replika kiszolgáló *elérhetetlenné* válik. A kiszolgáló állapotát [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) vagy [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)használatával javíthatja.
* Ha törli a kulcstartót, akkor a Azure Database for PostgreSQL egyetlen kiszolgáló nem fér hozzá a kulcshoz, és nem *elérhető* állapotba kerül. Állítsa helyre a [Key Vault](../key-vault/general/key-vault-recovery.md) , és érvényesítse újra az adattitkosítást, hogy *elérhetővé* tegye a kiszolgálót.
* Ha töröljük a kulcsot a kulcstartóból, akkor a Azure Database for PostgreSQL egyetlen kiszolgáló nem fér hozzá a kulcshoz, és nem *elérhető* állapotba kerül. Állítsa helyre a [kulcsot](../key-vault/general/key-vault-recovery.md) , és érvényesítse újra az adattitkosítást, hogy *elérhetővé* tegye a kiszolgálót.
* Ha az Azure kulcstartóban tárolt kulcs lejár, a kulcs érvénytelenné válik, és a Azure Database for PostgreSQL egy kiszolgáló *elérhetetlenné* válik. Terjessze ki a kulcs lejárati dátumát a [parancssori](/cli/azure/keyvault/key#az-keyvault-key-set-attributes) felület használatával, majd ellenőrizze újra az adattitkosítást, hogy *elérhetővé* tegye a kiszolgálót.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Véletlen kulcs-hozzáférés visszavonása a Key Vault

Előfordulhat, hogy a megfelelő hozzáférési jogokkal rendelkező személy Key Vault véletlenül letiltja a kiszolgáló hozzáférését a kulcshoz:

* A Key Vault Get, wrapKey és unwrapKey engedélyeinek visszavonása a kiszolgálóról.
* A kulcs törlése.
* A Key Vault törlése.
* A Key Vault tűzfalszabályok módosítása.

* A kiszolgáló felügyelt identitásának törlése az Azure AD-ben.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Az ügyfél által felügyelt kulcs figyelése Key Vault

Az adatbázis állapotának figyeléséhez, valamint az átlátható adattitkosítás-hozzáférés elvesztését jelző riasztások engedélyezéséhez konfigurálja a következő Azure-szolgáltatásokat:

* [Azure Resource Health](../service-health/resource-health-overview.md): egy nem elérhető adatbázis, amely nem fér hozzá az ügyfél kulcsához, az adatbázishoz való első kapcsolódás megtagadása után "nem érhető el" érték jelenik meg.
* [Műveletnapló](../service-health/alerts-activity-log-service-notifications-portal.md): Ha az ügyfél által felügyelt Key Vault nem sikerül hozzáférni az ügyfél kulcsához, a rendszer hozzáadja a bejegyzéseket a tevékenységi naplóhoz. Ha riasztásokat hoz létre ezekhez az eseményekhez, a lehető leghamarabb visszaállíthatja a hozzáférést.

* [Műveleti csoportok](../azure-monitor/alerts/action-groups.md): ezeket a csoportokat úgy definiálhatja, hogy a beállítások alapján küldje el az értesítéseket és a riasztásokat.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Visszaállítás és replikálás az ügyfél felügyelt kulcsával Key Vault

Miután Azure Database for PostgreSQL egy kiszolgálót a Key Vault tárolt ügyfél felügyelt kulcsával, a kiszolgáló minden újonnan létrehozott példánya is titkosítva lesz. Ezt az új másolatot helyi vagy geo-visszaállítási művelettel, illetve olvasási replikák használatával is elvégezheti. Azonban a másolat módosítható úgy, hogy az új ügyfél felügyelt kulcsát tükrözze a titkosításhoz. Az ügyfél által felügyelt kulcs megváltozása után a kiszolgáló régi biztonsági mentései a legújabb kulcsot használják.

Ha el szeretné kerülni az ügyfél által felügyelt adattitkosítás beállításakor a visszaállítási vagy az olvasási replika létrehozása során felmerülő problémákat, fontos, hogy kövesse ezeket a lépéseket az elsődleges és a visszaállított/replika kiszolgálókon:

* Kezdeményezzen visszaállítási vagy olvasási replika-létrehozási folyamatot az elsődleges Azure Database for PostgreSQL egyetlen kiszolgálóról.
* Az újonnan létrehozott kiszolgáló (visszaállított/replika) nem elérhető állapotban marad, mert az egyedi identitása még nem kapott engedélyt a Key Vault.
* A visszaállított/replika kiszolgálón ellenőrizze újra az ügyfél által felügyelt kulcsot az adattitkosítási beállításokban. Ez biztosítja, hogy az újonnan létrehozott kiszolgáló becsomagolja és kicsomagolja az engedélyeket a Key Vaultban tárolt kulcshoz.

## <a name="limitations"></a>Korlátozások

Azure Database for PostgreSQL esetén az ügyfelek által felügyelt kulcs (CMK) használatával a REST-adatok titkosításának támogatása néhány korlátozással rendelkezik –

* A funkció támogatása a **általános célú** és a **memória optimalizált** díjszabási szintjeire korlátozódik.
* Ez a funkció csak olyan régiókban és kiszolgálókon érhető el, amelyek legfeljebb 16 TB-nyi tárterületet támogatnak. A 16TB-et támogató Azure-régiók listáját [itt](concepts-pricing-tiers.md#storage) találja a dokumentáció tárolás szakaszában.

    > [!NOTE]
    > - A fent felsorolt régiókban létrehozott összes új PostgreSQL-kiszolgáló **elérhető**. a titkosítás támogatása az ügyfél-kezelő kulcsaival. Az időponthoz visszaállított (PITR) kiszolgáló vagy az olvasási replika nem lesz érvényes, de elméletileg az "új".
    > - Annak ellenőrzéséhez, hogy a kiépített kiszolgáló támogatja-e a 16TB, nyissa meg a portál díjszabási szintje paneljét, és tekintse meg a kiépített kiszolgáló által támogatott maximális tárterületet. Ha a csúszkát akár 4TB is áthelyezheti, előfordulhat, hogy a kiszolgáló nem támogatja a titkosítást az ügyfél által felügyelt kulcsokkal. Az adatforgalom azonban mindig a szolgáltatás által felügyelt kulcsokkal van titkosítva. AskAzureDBforPostgreSQL@service.microsoft.comHa bármilyen kérdése van, lépjen kapcsolatba.

* A titkosítás csak az RSA 2048 titkosítási kulccsal támogatott.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [állíthatja be az adattitkosítást egy ügyfél által felügyelt kulccsal a PostgreSQL-hez készült Azure-adatbázishoz a Azure Portal használatával](howto-data-encryption-portal.md).
