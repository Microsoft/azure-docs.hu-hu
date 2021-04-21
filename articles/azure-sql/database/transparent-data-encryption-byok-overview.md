---
title: Ügyfél által felügyelt transzparens adattitkosítás (TDE)
description: Bring Your Own Key (BYOK) támogatása a transzparens adattitkosítás (TDE) Azure Key Vault a SQL Database és Azure Synapse Analytics. A BYOK-val való TDE áttekintése, előnyei, működése, szempontjai és javaslatai.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 02/01/2021
ms.openlocfilehash: b3403558cbc07d152bbae7e901464a8aa4a8e4d2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812767"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL transzparens adattitkosítás ügyfél által kezelt kulccsal
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL transzparens adattitkosítás kulcsokkal [(TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) való használata lehetővé teszi az Bring Your Own Key (BYOK) forgatókönyvének az adatokkal való védelmét, valamint lehetővé teszi a szervezetek számára a kulcsok és adatok kezelése során a feladatok elkülönítését. Az ügyfél által felügyelt transzparens adattitkosítással az ügyfél felelős a kulcs életciklusának kezeléséért (kulcs létrehozása, feltöltése, rotációja, törlése), a kulcshasználati engedélyekért és a kulcsokon végzett műveletek naplózásáért.

Ebben a forgatókönyvben a TDE-védőnek nevezett adatbázistitkosítási kulcs (DEK) titkosításához használt kulcs egy ügyfél által felügyelt aszimmetrikus kulcs, amely egy ügyfél által birtokolt és ügyfél által felügyelt [Azure Key Vault (AKV)](../../key-vault/general/security-features.md)felhőalapú külső kulcskezelő rendszerben van tárolva. Key Vault RSA titkosítási kulcsokhoz magas rendelkezésre áll és skálázható biztonságos tárolást biztosít, igény szerint a FIPS 140-2 2. szintje szerint ellenőrzött hardveres biztonsági modulok (HSM-ek) segítségével. Nem engedélyezi a tárolt kulcsok közvetlen hozzáférését, de titkosítási/visszafejtési szolgáltatásokat biztosít a kulcs használatával az engedélyezett entitások számára. A kulcsot a kulcstartó generálhatja, importálhatja vagy átviheti a kulcstartóba egy- vagy egy [on-prem HSM-eszközről.](../../key-vault/keys/hsm-protected-keys.md)

A Azure SQL Database és Azure Synapse Analytics A TDE-védő a kiszolgáló szintjén van beállítva, és a kiszolgálóhoz társított összes titkosított adatbázis örökli. A Azure SQL Managed Instance A TDE-védő a példány szintjén van beállítva, és a példány összes titkosított adatbázisa örökli. A *kiszolgáló kifejezés* a dokumentum SQL Database és Azure Synapse kiszolgálóira, valamint a dokumentumban SQL Managed Instance felügyelt példányra is vonatkozik, hacsak másként nincs megszabadulva.

> [!IMPORTANT]
> Azok számára, akik szolgáltatás által felügyelt TDE-t használnak, akik az ügyfél által felügyelt TDE-t szeretnék használni, az adatok titkosítva maradnak az átváltás során, és nem áll le és nem történik meg az adatbázisfájlok újratitkosítása. A szolgáltatás által felügyelt kulcsról egy ügyfél által felügyelt kulcsra való váltáshoz csak az adattitkosítási kulcs újratitkosítása szükséges, amely egy gyors és online művelet.

> [!NOTE]
> <a id="doubleencryption"></a> Annak érdekében, Azure SQL az ügyfelek számára két réteg titkosítást biztosítson az adatáttitkosításhoz az (AES-256 titkosítási algoritmust használó) infrastruktúra-titkosítás a platform által felügyelt kulcsokkal. Ez egy további titkosítási réteget biztosít az adattitkosítással és a TDE-vel, valamint az ügyfél által kezelt kulcsokkal, amely már elérhető. A Azure SQL Database és a felügyelt példányok esetében minden adatbázis, beleértve a master adatbázist és más rendszeradatbázisokat is, titkosítva lesz, ha az infrastruktúra-titkosítás be van kapcsolva. Jelenleg az ügyfeleknek hozzáférést kell kérniük ehhez a funkcióhoz. Ha érdekli ez a képesség, lépjen kapcsolatba a következővel: AzureSQLDoubleEncryptionAtRest@service.microsoft.com .

## <a name="benefits-of-the-customer-managed-tde"></a>Az ügyfél által felügyelt TDE előnyei

Az ügyfél által felügyelt TDE a következő előnyöket biztosítja az ügyfél számára:

- A TDE-védő használatának és felügyeletének teljes és részletes vezérlése;

- A TDE-védő használatának átláthatósága;

- A szervezeten belüli kulcsok és adatok kezelésével kapcsolatos feladatok elkülönítésének megvalósítása;

- Key Vault rendszergazda visszavonhatja a kulcselérési engedélyeket, hogy a titkosított adatbázis ne legyen elérhető;

- Kulcsok központi kezelése az AKV-ban;

- Megbízhatóbbak a végfelhasználók, mivel az AKV úgy lett kialakítva, hogy a Microsoft ne lát és ne bontsa ki a titkosítási kulcsokat;

## <a name="how-customer-managed-tde-works"></a>Az ügyfél által felügyelt TDE működése

![Az ügyfél által felügyelt TDE beállítása és működése](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

Ahhoz, hogy a kiszolgáló használni tudja az AKV-ban tárolt TDE-védőt az adattitkosítási kulcs titkosításához, a Key Vault rendszergazdájának a következő hozzáférési jogosultságokat kell adnia a kiszolgálónak az egyedi Azure Active Directory- (Azure AD-) identitásával:

- **get** – a nyilvános rész és a kulcs tulajdonságainak leolvasása a Key Vault

- **wrapKey** – az adattitkos kulcs védelme (titkosítása)

- **unwrapKey** – a dekkulccsal való védelem feloldása (visszafejtése)

A Key Vault rendszergazdája a Key [Vault naplózási](../../azure-monitor/insights/key-vault-insights-overview.md)eseményeinek naplózását is engedélyezheti, hogy később naplót is kaphatnak.

Ha a kiszolgáló úgy van konfigurálva, hogy az AKV-ból származó TDE-védőt használjon, a kiszolgáló elküldi az egyes TDE-kompatibilis adatbázisok adattitkosítási kulcsát a kulcstartónak titkosításra. A Key Vault visszaadja a titkosított ADATTITK-t, amelyet aztán a felhasználói adatbázisban tárol.

Szükség esetén a kiszolgáló a kulcstartóba küldi a védett DEK-t a visszafejtés érdekében.

Az auditorok a Azure Monitor a Key Vault AuditEvent naplóit, ha a naplózás engedélyezve van.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Az ügyfél által felügyelt TDE konfigurálásával kapcsolatos követelmények

### <a name="requirements-for-configuring-akv"></a>Az AKV konfigurálásával kapcsolatos követelmények

- A Kulcstartónak és SQL Database/felügyelt példánynak ugyanannak a bérlőnek kell Azure Active Directory lennie. A több-bérlős kulcstartó- és kiszolgáló-interakciók nem támogatottak. Az erőforrások későbbi áthelyezéshez újra kell konfigurálni az AKV-vel együtt a TDE-t. További információ az erőforrások [áthelyezésről.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

- [Az adatvesztéssel](../../key-vault/general/soft-delete-overview.md) (vagy kulcstartóval) való véletlen törlés elkerülése érdekében engedélyezni kell a kulcstartóban a soft-delete funkciót. A helyreállíthatóan törölt erőforrások 90 napig maradnak meg, kivéve, ha az ügyfél addig helyreállítja vagy kiüríti őket. A *helyreállító* és *véglegesen kiürítő* műveletek saját engedélyekkel vannak társítva a kulcstartó hozzáférési szabályzatában. A soft-delete funkció alapértelmezés szerint ki van kapcsolva, és a [PowerShell vagy](../../key-vault/general/key-vault-recovery.md?tabs=azure-powershell) a parancssori felület [használatával engedélyezhető.](../../key-vault/general/key-vault-recovery.md?tabs=azure-cli) Nem engedélyezhető a következő Azure Portal.  

- Adjon hozzáférést a kiszolgálónak vagy a felügyelt példánynak a kulcstartóhoz (get, wrapKey, unwrapKey) annak Azure Active Directory identitásával. Az Azure AD Azure Portal automatikusan létrejön az Azure AD-identitás. A PowerShell vagy a parancssori felület használata esetén explicit módon kell létrehozni az Azure AD-identitást, és ellenőrizni kell a befejezést. Részletes útmutatást a PowerShell használatával SQL Managed Instance TDE konfigurálása BYOK-val és A TDE konfigurálása [BYOK-val.](transparent-data-encryption-byok-configure.md) [](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md)

- Ha tűzfalat használ az AKV-vel, engedélyeznie kell a Megbízható Microsoft-szolgáltatások a *tűzfal megkerülését* beállítást.

### <a name="requirements-for-configuring-tde-protector"></a>A TDE-védő konfigurálásával kapcsolatos követelmények

- A TDE-védő csak aszimmetrikus, RSA- vagy RSA HSM-kulcs lehet. A támogatott kulcshossz 2048 bájt és 3072 bájt.

- A kulcs aktiválási dátumának (ha be van állítva) egy múltbeli dátumnak és időpontnak kell lennie. A lejárati dátumnak (ha be van állítva) jövőbeli dátumnak és időpontnak kell lennie.

- A kulcsnak Engedélyezve állapotban *kell* lennie.

- Ha meglévő kulcsot importál a kulcstartóba, ügyeljen arra, hogy a támogatott fájlformátumokban adja meg azt ( `.pfx` , `.byok` vagy `.backup` ).

> [!NOTE]
> Azure SQL mostantól támogatja a Managed HSM-ben TDE-védőként tárolt RSA-kulcsokat. Ez a funkció nyilvános előzetes **verzióban érhető el.** Azure Key Vault Managed HSM egy teljes körűen felügyelt, magas rendelkezésre álló, egybérlős, szabványnak megfelelő felhőszolgáltatás, amely lehetővé teszi a felhőalkalmazások titkosítási kulcsának védelmét a FIPS 140-2 3. szintje szerint ellenőrzött HSM-ekkel. További információ a [Managed HSM-ről.](../../key-vault/managed-hsm/index.yml)


## <a name="recommendations-when-configuring-customer-managed-tde"></a>Javaslatok az ügyfél által felügyelt TDE konfigurálásakor

### <a name="recommendations-when-configuring-akv"></a>Javaslatok az AKV konfigurálásakor

- Akár 500 általános célú vagy 200 üzletileg kritikus-adatbázis társítása egyetlen előfizetésben található kulcstartóval a magas rendelkezésre állás biztosítása érdekében, amikor a kiszolgáló hozzáfér a kulcstartó TDE-védőjéhez. Ezek az értékek a Key Vault szolgáltatási korlátaiban dokumentált [tapasztalatokon alapulnak.](../../key-vault/general/service-limits.md) A cél a kiszolgáló feladatátvétele utáni problémák megelőzése, mivel az annyi kulcsműveletet indít el a tárolón, amennyit a kiszolgálón adatbázisok is vannak.

- Állítson be egy erőforrás-zárolást a kulcstartón annak szabályozása érdekében, hogy ki törölheti ezt a kritikus erőforrást, és megelőzheti a véletlen vagy jogosulatlan törlést. További információ az [erőforrás-zárolásról.](../../azure-resource-manager/management/lock-resources.md)

- Naplózás és jelentéskészítés engedélyezése az összes titkosítási kulcson: A Key Vault olyan naplókat biztosít, amelyek könnyen bevethetőek más biztonsági információkba és eseménykezelő eszközökbe. Az Operations Management Suite [Log Analytics](../../azure-monitor/insights/key-vault-insights-overview.md) egy példa egy már integrált szolgáltatásra.

- A titkosított adatbázisok magas rendelkezésre állásának biztosítása érdekében csatolja az egyes kiszolgálókat két különböző régióban található kulcstartóval, amelyek ugyanazon a kulcsanyagon találhatók. Csak a kulcstartó kulcsát jelölje meg ugyanabban a régióban, mint a TDE-védőt. A rendszer automatikusan átvált a távoli régióban található kulcstartóra, ha az ugyanabban a régióban található kulcstartót érintő szolgáltatáskimaradás van.

### <a name="recommendations-when-configuring-tde-protector"></a>Javaslatok a TDE-védő konfigurálásakor

- Őrizheti meg a TDE-védő másolatát egy biztonságos helyen, vagy helyezze el a biztonsági másolati szolgáltatásban.

- Ha a kulcs a kulcstartóban jön létre, hozzon létre egy biztonsági másolatot a kulcsról, mielőtt első alkalommal használja a kulcsot az AKV-ban. A biztonsági mentés csak egy Azure Key Vault állítható vissza. További információ a [Backup-AzKeyVaultKey parancsról.](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Hozzon létre egy új biztonsági másolatot minden alkalommal, amikor módosításokat történik a kulcson (például kulcsattribútumok, címkék, ACL-ek).

- **Kulcsok rotált** kulcsának használata során tartsa meg a kulcs korábbi verzióit, hogy visszaállíthatóak legyen az adatbázis régebbi biztonsági másolatai. Ha egy adatbázis TDE-védője módosul, az adatbázis  régi biztonsági másolatai nem frissülnek a legújabb TDE-védő használatára. A visszaállításkor minden biztonsági mentéshez szükség van arra a TDE-védőre, amely a létrehozáskor titkosítva volt. A kulcsrotációkat a Következő útmutató utasításai szerint hajthatja végre: A transzparens adattitkosítás [a PowerShell használatával.](transparent-data-encryption-byok-key-rotation.md)

- A szolgáltatás által kezelt kulcsokra való váltás után is tartsa meg az összes korábban használt kulcsot az AKV-ban. Ez biztosítja, hogy az adatbázis biztonsági másolatai az AKV-ban tárolt TDE-védőkkel állíthatók vissza.  A Azure Key Vault létrehozott TDE-védőket mindaddig fenn kell tartani, amíg az összes fennmaradó tárolt biztonsági mentés létre nem jön a szolgáltatás által felügyelt kulcsokkal. Készítse el a kulcsok helyreállítható biztonsági másolatait a [Backup-AzKeyVaultKey használatával.](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Ha adatvesztés kockázata nélkül szeretne eltávolítani egy potenciálisan feltört kulcsot egy biztonsági incidens során, kövesse a Potenciálisan feltört kulcs [eltávolítása lépéseit.](transparent-data-encryption-byok-remove-tde-protector.md)

## <a name="inaccessible-tde-protector"></a>Nem elérhető TDE-védő

Ha a transzparens adattitkosítás ügyfél által felügyelt kulcs használatára van konfigurálva, az adatbázis online állapotban maradása érdekében folyamatos hozzáférésre van szükség a TDE-védőhöz. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt TDE-védőhöz az AKV-ben, egy adatbázis legfeljebb 10 percen belül elkezd megtagadni a megfelelő hibaüzenettel kapcsolatos összes kapcsolatot, és elérhetetlenné teszi az *állapotát.* Az egyetlen művelet, amely egy elérhetetlen állapotban van egy adatbázison, az annak törlése.

> [!NOTE]
> Ha az adatbázis időszakos hálózatkimaradás miatt nem érhető el, nincs szükség beavatkozásra, és az adatbázisok automatikusan újra online állapotba kerülnek.

A kulcshoz való hozzáférés visszaállítása után az adatbázis online állapotba való visszaállítása további időt és lépéseket igényel, amelyek a kulcshoz való hozzáférés nélkül eltelt időtől és az adatbázisban található adatok méretétől függően változhatnak:

- Ha a kulcshoz való hozzáférés 8 órán belül helyreáll, az adatbázis a következő órán belül automatikusan helyreáll.

- Ha a kulcshoz való hozzáférés több mint 8 óra után áll vissza, az automatikus helyreállítás nem lehetséges, és az adatbázis visszaállítása további lépéseket igényel a portálon, és az adatbázis méretétől függően jelentős időt is igénybe vehet. Ha az adatbázis ismét elérhető, a korábban konfigurált kiszolgálószintű beállítások, például a feladatátvételi csoport konfigurációja, az időponthoz megadott visszaállítási előzmények és a címkék **elvesznek.** [](auto-failover-group-overview.md) Ezért ajánlott olyan értesítési rendszert használni, amely 8 órán belül lehetővé teszi a mögöttes kulcs hozzáférési problémáinak azonosítását és megoldását.

Az alábbiakban megtekintheti a portálon a nem elérhető adatbázisok online állapotba hozása érdekében szükséges további lépéseket.

![TDE BYOK – Elérhetetlen adatbázis](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>A TDE-védő véletlen hozzáférés-visszavonása

Előfordulhat, hogy valaki, aki megfelelő hozzáférési jogosultságokkal rendelkezik a kulcstartóhoz, véletlenül letiltja a kiszolgáló hozzáférését a kulcshoz a következővel:

- a kulcstartó get *,* *wrapKey*, *unwrapKey* engedélyeinek visszaírása a kiszolgálóról

- kulcs törlése

- kulcstartó törlése

- a kulcstartó tűzfalszabályainak módosítása

- a kiszolgáló felügyelt identitásának törlése a Azure Active Directory

További információ az adatbázis elérhetetlenné vált [gyakori okairól.](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current&preserve-view=true#common-errors-causing-databases-to-become-inaccessible)

## <a name="monitoring-of-the-customer-managed-tde"></a>Az ügyfél által felügyelt TDE figyelése

Az adatbázis állapotának monitorozásához és a TDE-védő hozzáférésének elvesztésével kapcsolatos riasztások engedélyezéséhez konfigurálja a következő Azure-szolgáltatásokat:

- [Azure Resource Health:](../../service-health/resource-health-overview.md). Egy elérhetetlen adatbázis, amely nem rendelkezik hozzáféréssel a TDE-védőhez, "Nem érhető el" lesz, miután a rendszer megtagadta az első kapcsolódást az adatbázishoz.
- [Tevékenységnapló,](../../service-health/alerts-activity-log-service-notifications-portal.md) ha az ügyfél által felügyelt kulcstartó TDE-védőjéhez való hozzáférés meghiúsul, a rendszer bejegyzéseket ad a tevékenységnaplóhoz.  Ha riasztásokat hoz létre ezekhez az eseményekhez, a lehető leghamarabb visszaállítja a hozzáférést.
- [Műveletcsoportok](../../azure-monitor/alerts/action-groups.md) definiálhatók úgy, hogy a beállítások alapján értesítéseket és riasztásokat küldjenek, például Email/SMS/Push/Voice, Logic App, Webhook, ITSM vagy Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Adatbázis biztonsági mentése és visszaállítása ügyfél által felügyelt TDE-val

Miután egy adatbázisTDE-titkosítást használt a biztonsági Key Vault kulcsával, az újonnan létrehozott biztonsági másolatok is ugyanazokkal a TDE-védővel vannak titkosítva. A TDE-védő módosulása esetén az adatbázis  régi biztonsági másolatai nem frissülnek a legújabb TDE-védő használatára.

Ha egy TDE-védővel titkosított biztonsági másolatot Key Vault, győződjön meg arról, hogy a kulcsanyag elérhető a célkiszolgáló számára. Ezért javasoljuk, hogy tartsa meg a TDE-védő összes korábbi verzióját a Key Vaultban, hogy az adatbázis biztonsági mentései visszaállíthatók legyenek.

> [!IMPORTANT]
> Egy kiszolgálóhoz jelenleg nem lehet egynél több TDE-védő készlet. Ez a kulcs az "A kulcs legyen az alapértelmezett TDE-védő" jelöléssel a Azure Portal panelen. Azonban több további kulcs is csatolható egy kiszolgálóhoz anélkül, hogy TDE-védőként jelölné meg őket. Ezek a kulcsok nem a DEK védelmére használatosak, de biztonsági másolatból való visszaállításkor használhatók, ha a biztonsági mentési fájl a megfelelő ujjlenyomattal együtt a kulccsal van titkosítva.

Ha a biztonsági másolat visszaállításához szükséges kulcs már nem érhető el a célkiszolgáló számára, a következő hibaüzenet jelenik meg a visszaállítási próbálkozáskor: "A célkiszolgáló nem rendelkezik hozzáféréssel a és a között létrehozott `<Servername>` összes AKV URI-hoz. \<Timestamp #1> \<Timestamp #2> Újrapróbálkozási művelet az összes AKV URI visszaállítása után."

A probléma megoldásához futtassa a [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) parancsmagot a célkiszolgálóhoz vagy a [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) parancsmagot a cél felügyelt példányhoz, amely visszaadja az elérhető kulcsok listáját, és azonosítja a hiányzó kulcsokat. Annak érdekében, hogy az összes biztonsági másolat visszaállítható legyen, győződjön meg arról, hogy a visszaállítás célkiszolgálója hozzáfér az összes szükséges kulcshoz. Ezeket a kulcsokat nem kell TDE-védőként megjelölni.

A biztonsági másolatok biztonsági mentésének helyreállításával kapcsolatos SQL Database lásd: [Adatbázis helyreállítása a SQL Database.](recovery-using-backups.md) A dedikált SQL-készlet biztonsági mentésének helyreállításával kapcsolatos további Azure Synapse Analytics [lásd: Dedikált SQL-készlet helyreállítása.](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md) A SQL Server natív biztonsági mentését/visszaállítását a SQL Managed Instance: Rövid [útmutató:](../managed-instance/restore-sample-database-quickstart.md) Adatbázis visszaállítása a SQL Managed Instance

A naplófájlok további megfontolása: A naplófájlok biztonsági másolata az eredeti TDE-védővel titkosítva marad, még akkor is, ha az adatbázis már új TDE-védőt használ.  A visszaállításkor mindkét kulcsra szükség lesz az adatbázis visszaállításához.  Ha a naplófájl egy, a Azure Key Vault-ban tárolt TDE-védőt használ, erre a kulcsra a visszaállításkor lesz szükség, még akkor is, ha az adatbázist időközben úgy módosították, hogy szolgáltatás által felügyelt TDE-t használjon.

## <a name="high-availability-with-customer-managed-tde"></a>Magas rendelkezésre állás ügyfél által felügyelt TDE-vel

Még akkor is, ha nincs konfigurált georedundania a kiszolgálóhoz, erősen ajánlott úgy konfigurálni a kiszolgálót, hogy két különböző kulcstartót használjon két különböző régióban ugyanazokkal a kulcsokkal. A másik régió másodlagos kulcstartója nem lehet TDE-védőként megjelölve, és még csak nem is engedélyezett. Ha egy szolgáltatáskimaradás érinti az elsődleges kulcstartót, és csak akkor, a rendszer automatikusan átvált a másik, ugyanannak az ujjlenyomattal a másodlagos kulcstartóban található csatolt kulcsra, ha az már létezik. Vegye figyelembe, hogy a váltás nem történik meg, ha a TDE-védő a visszavont hozzáférési jogosultságok miatt nem érhető el, vagy ha egy kulcs vagy kulcstartó törölve lett, mivel ez azt jelezheti, hogy az ügyfél szándékosan korlátozni akarja a kiszolgáló hozzáférését a kulcshoz. Ha ugyanazt a kulcsanyagot különböző régiókban található két kulcstartóhoz biztosítja, akkor a kulcstartón kívül is létre lehet hozva, és mindkét kulcstartóba importálható. 

Alternatív megoldásként a kulcsot a kiszolgálóval azonos régióban található elsődleges kulcstartóval kell generálni, és a kulcsot egy másik Azure-régióban található kulcstartóba klónozhatja. A [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/Backup-AzKeyVaultKey) parancsmag használatával lekérje a kulcsot titkosított formában az elsődleges kulcstartóból, majd használja a [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) parancsmagot, és adjon meg egy kulcstartót a második régióban a kulcs klónozásához. Másik lehetőségként a Azure Portal a kulcs biztonsági mentését és visszaállítását. A kulcs biztonsági mentési/visszaállítási művelete csak az azonos Azure-előfizetésen belüli kulcstartók és az Azure földrajzi [régiója között engedélyezett.](https://azure.microsoft.com/global-infrastructure/geographies/)  

![Single-Server HA](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR és ügyfél által felügyelt TDE

Az aktív georeplikáció és a feladatátvételi csoportok forgatókönyvében minden érintett kiszolgálóhoz külön kulcstartóra van szükség, amely a kiszolgálóval azonos [Azure-régióban](active-geo-replication-overview.md) található. [](auto-failover-group-overview.md) Az ügyfél felelős azért, hogy a kulcstartókban lévő kulcsfontosságú anyagok konzisztensek legyenek, így a földrajzi másodlagos adatbázis szinkronban van, és átveheti ugyanezt a kulcsot a helyi kulcstartóból, ha az elsődleges elérhetetlenné válik a régióban lévő kimaradás és feladatátvétel kiváltása miatt. Legfeljebb négy másodperces rendszer is konfigurálható, és a láncolás (a másodpercek második binárisai) nem támogatott.

A hiányos kulcsú anyagok miatt a georeplikáció során vagy a létrehozása során a problémák elkerülése érdekében fontos, hogy az ügyfél által felügyelt TDE konfigurálásakor kövesse ezeket a szabályokat:

- Az érintett kulcstartóknak azonos tulajdonságokkal és azonos hozzáférési jogosultságokkal kell rendelkeznek a megfelelő kiszolgálókhoz.

- Minden érintett kulcstartónak azonos kulcsanyagot kell tartalmaznia. Nem csak az aktuális TDE-védőre vonatkozik, hanem a biztonsági mentési fájlokban használható összes korábbi TDE-védőre is.

- A TDE-védő kezdeti beállítását és rotációját először a másodlagoson, majd az elsődlegesen kell beállítani.

![Feladatátvételi csoportok és geo-dr](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

A feladatátvétel teszteléséhez kövesse az Aktív georeplikáció [áttekintése című témakör lépéseit.](active-geo-replication-overview.md) Rendszeresen kell tesztelni a feladatátvételt annak ellenőrzésére, hogy SQL Database rendelkezik-e hozzáférési engedéllyel mindkét kulcstartóhoz.

## <a name="next-steps"></a>Következő lépések

Az alábbi PowerShell-példaszk szkripteket is ellenőrizheti az ügyfél által felügyelt TDE gyakori műveleteihez:

- [A transzparens adattitkosítás védő elforgatása SQL Database PowerShell használatával](transparent-data-encryption-byok-key-rotation.md)

- [A transzparens adattitkosítás (TDE) védő eltávolítása SQL Database PowerShell használatával](transparent-data-encryption-byok-remove-tde-protector.md)

- [A transzparens adattitkosítás kezelése SQL Managed Instance saját kulccsal a PowerShell használatával](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)