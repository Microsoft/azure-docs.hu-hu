---
title: Az ügyfél által felügyelt transzparens adattitkosítás (TDE)
description: Bring Your Own Key (BYOK) transzparens adattitkosítás (TDE) támogatása a SQL Database és az Azure szinapszis Analytics Azure Key Vault használatával. TDE a BYOK áttekintése, előnyei, hogyan működik, megfontolások és javaslatok.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/01/2021
ms.openlocfilehash: e096e21e7d20c992e18634d684f663f149cc3c55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101691246"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL transzparens adattitkosítás ügyfél által kezelt kulccsal
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Az ügyfél által felügyelt kulccsal rendelkező Azure SQL [transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) lehetővé teszi BRING Your Own Key (BYOK) forgatókönyv használatát az inaktív adatok védelméhez, és lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésében felmerülő feladatok elkülönítését. Az ügyfél által felügyelt transzparens adattitkosítással az ügyfél felelős a kulcsfontosságú életciklus-felügyelet (a kulcs létrehozása, feltöltése, elforgatása, törlése), a kulcshasználat engedélyei és a kulcsokon végzett műveletek naplózása felett.

Ebben a forgatókönyvben a TDE Protector nevű adatbázis-titkosítási kulcs (ADATTITKOSÍTÁSI kulcsot) titkosításához használt kulcs az ügyfél által felügyelt aszimmetrikus kulcs, amelyet az ügyfél és az ügyfél által felügyelt [Azure Key Vault (AKV)](../../key-vault/general/secure-your-key-vault.md), a felhőalapú külső kulcsokat kezelő rendszer tárol. Key Vault magas rendelkezésre állású és méretezhető biztonságos tárhely az RSA titkosítási kulcsokhoz, opcionálisan a FIPS 140-2 2-es szintű, ellenőrzött hardveres biztonsági modulok (HSM-EK) által támogatott. Nem engedélyezi a közvetlen hozzáférést egy tárolt kulcshoz, de a titkosítási/visszafejtési szolgáltatásokat biztosít a kulcs használatával a jogosult entitások számára. A kulcsot a Key Vault generálhatja, importálhatja vagy [áthelyezheti a Key vaultba egy helyszíni HSM-eszközről](../../key-vault/keys/hsm-protected-keys.md).

A Azure SQL Database és az Azure szinapszis Analytics esetében a TDE-védő a kiszolgáló szintjén van beállítva, és az adott kiszolgálóhoz társított összes titkosított adatbázis örökli. Az Azure SQL felügyelt példányai esetében a TDE-védő a példány szintjén van beállítva, és az adott példányon található összes titkosított adatbázis örökli. A *kiszolgáló* kifejezés a SQL Database-ben és az Azure szinapszisban, illetve egy felügyelt példányon, az SQL által felügyelt példányon, a jelen dokumentumon belül, kivéve, ha másként van megadva.

> [!IMPORTANT]
> Azok a szolgáltatás által felügyelt TDE használó felhasználók, akik az ügyfél által felügyelt TDE szeretnék használni, az adatváltási folyamat során titkosítva maradnak, és az adatbázisfájlok nem lesznek újra titkosítva. Ha egy szolgáltatás által felügyelt kulcsról egy ügyfél által felügyelt kulcsra vált át, csak a ADATTITKOSÍTÁSI kulcsot újratitkosítására van szükség, amely gyors és online művelet.

> [!NOTE]
> <a id="doubleencryption"></a> A platform által felügyelt kulcsokkal rendelkező Azure SQL-ügyfelek számára az inaktív adatok titkosításának két rétegét, az infrastruktúra-titkosítást (AES-256 titkosítási algoritmus használatával) végezheti el. Ez egy kiegészítő titkosítási réteget is tartalmaz, amely a TDE és az ügyfél által felügyelt kulcsokkal együtt már elérhető. Azure SQL Database és felügyelt példány esetében az infrastruktúra-titkosítás bekapcsolásakor a rendszer az összes adatbázist, beleértve a főadatbázist és az egyéb rendszeradatbázisokat is titkosítja. Ekkor az ügyfeleknek hozzáférést kell kérniük ehhez a képességhez. Ha érdekli ezt a funkciót, forduljon a következőhöz: AzureSQLDoubleEncryptionAtRest@service.microsoft.com .

## <a name="benefits-of-the-customer-managed-tde"></a>Az ügyfél által felügyelt TDE előnyei

Az ügyfél által felügyelt TDE a következő előnyöket biztosítja az ügyfél számára:

- A TDE-védő használatának és felügyeletének teljes és részletes szabályozása;

- A TDE-védő használatának átláthatósága;

- Lehetőség a feladatok elkülönítésének megvalósítására a szervezeten belüli kulcsok és adatok kezelésében;

- Key Vault rendszergazda visszavonhatja a kulcs-hozzáférési engedélyeket, hogy a titkosított adatbázis elérhetetlenné váljon;

- Kulcsok központi kezelése a AKV-ben;

- Nagyobb megbízhatóság a végfelhasználók számára, mivel a AKV úgy lett kialakítva, hogy a Microsoft nem láthatja és nem tudja kinyerni a titkosítási kulcsokat;

## <a name="how-customer-managed-tde-works"></a>Az ügyfél által felügyelt TDE működése

![Az ügyfél által felügyelt TDE beállítása és működése](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

Ahhoz, hogy a kiszolgáló használhassa a AKV-ben tárolt TDE-kezelőt a ADATTITKOSÍTÁSI kulcsot titkosításához, a Key Vault rendszergazdájának a következő hozzáférési jogosultságokat kell biztosítania a kiszolgálóhoz az egyedi Azure Active Directory (Azure AD) identitásával:

- **Get** – a nyilvános rész és a kulcs tulajdonságainak lekérése a Key Vault

- **wrapKey** – a adattitkosítási kulcsot védelme (titkosítása)

- **unwrapKey** – a adattitkosítási kulcsot feloldása (visszafejtés)

A Key Vault rendszergazdája [engedélyezheti a Key Vault naplózási eseményeinek naplózását](../../azure-monitor/insights/key-vault-insights-overview.md)is, így később is naplózhatja őket.

Ha a kiszolgáló úgy van konfigurálva, hogy a AKV TDE-védőt használ, a kiszolgáló az egyes TDE-kompatibilis adatbázisok ADATTITKOSÍTÁSI kulcsot küldi a kulcstárolóba a titkosításhoz. A Key Vault a titkosított ADATTITKOSÍTÁSI kulcsot adja vissza, amelyet a rendszer a felhasználói adatbázisban tárol.

Szükség esetén a kiszolgáló a védett ADATTITKOSÍTÁSI kulcsot a kulcstárolóba küldi a visszafejtéshez.

A rendszernaplók a Azure Monitor használatával ellenőrizhetik a Key Vault AuditEvent naplóit, ha a naplózás engedélyezve van.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Az ügyfél által felügyelt TDE konfigurálásának követelményei

### <a name="requirements-for-configuring-akv"></a>A AKV konfigurálásának követelményei

- A Key vaultnak és a SQL Database/felügyelt példánynak ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia. A több-bérlős kulcstartó és a kiszolgáló közötti interakciók nem támogatottak. Ha ezt követően szeretné áthelyezni az erőforrásokat, újra kell konfigurálni a TDE a AKV. További információ az [erőforrások áthelyezéséről](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

- A Key vaulton engedélyezni kell a [Soft delete](../../key-vault/general/soft-delete-overview.md) funkciót, hogy az adatvesztést okozó véletlen kulcs (vagy Key Vault) törlésével védve legyen. A Soft-Deleted erőforrásokat 90 napig őrzi meg a rendszer, kivéve, ha az ügyfél nem állítja helyre vagy nem törli őket addig. A *helyreállítás* és *Törlés* műveletekhez saját engedélyek tartoznak a Key Vault hozzáférési házirendjében. A Soft-delete funkció alapértelmezés szerint ki van kapcsolva, és a [PowerShell](../../key-vault/general/key-vault-recovery.md?tabs=azure-powershell) vagy [a CLI](../../key-vault/general/key-vault-recovery.md?tabs=azure-cli)használatával engedélyezhető. Nem engedélyezhető a Azure Portalon keresztül.  

- Adja meg a kiszolgáló vagy a felügyelt példány hozzáférését a Key vaulthoz (get, wrapKey, unwrapKey) a saját Azure Active Directory identitásának használatával. A Azure Portal használatakor az Azure AD-identitás automatikusan létrejön. A PowerShell vagy a parancssori felület használatakor az Azure AD-identitást explicit módon létre kell hozni, és ellenőrizni kell a befejezést. Lásd: a TDE és a [BYOK](transparent-data-encryption-byok-configure.md) konfigurálása, valamint a TDE és a [BYOK konfigurálása az SQL felügyelt példányához](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md) , részletes útmutatást nyújt a PowerShell használatakor.

- Ha a AKV használatával tűzfalat használ, engedélyeznie kell *a megbízható Microsoft-szolgáltatások engedélyezése beállítást a tűzfal megkerüléséhez*.

### <a name="requirements-for-configuring-tde-protector"></a>A TDE-védő konfigurálásának követelményei

- A TDE-védő csak aszimmetrikus, RSA vagy RSA HSM-kulcs lehet. A támogatott kulcsok hossza 2048 és 3072 bájt.

- A kulcs aktiválási dátumát (ha be van állítva) a múltban dátumnak és időpontnak kell lennie. A lejárati dátumnak (ha be van állítva) jövőbeli dátumnak és időpontnak kell lennie.

- A kulcsnak *engedélyezett* állapotban kell lennie.

- Ha meglévő kulcsot importál a kulcstartóba, győződjön meg arról, hogy a támogatott fájlformátumokat (. pfx,. byok vagy. backup) adja meg.

> [!NOTE]
> Az Azure SQL mostantól támogatja a felügyelt HSM-ben tárolt RSA-kulcs használatát TDE-védőként. Ez a funkció **nyilvános előzetes** verzióban érhető el. Azure Key Vault felügyelt HSM egy teljes körűen felügyelt, magas rendelkezésre állású, egybérlős, szabványoknak megfelelő felhőalapú szolgáltatás, amely lehetővé teszi a felhőalapú alkalmazások titkosítási kulcsainak védelmét az FIPS 140-2 3. szintű hitelesített HSM használatával. További információ a [felügyelt HSM](../../key-vault/managed-hsm/index.yml).


## <a name="recommendations-when-configuring-customer-managed-tde"></a>Javaslatok az ügyfél által felügyelt TDE konfigurálásához

### <a name="recommendations-when-configuring-akv"></a>Javaslatok a AKV konfigurálásakor

- A magas rendelkezésre állás biztosítása érdekében a kiszolgáló a legfontosabb 500 általános célú vagy 200 üzletileg kritikus adatbázisokat egyetlen előfizetéshez tartozó kulcstartóval társítja, így biztosítva, hogy a kiszolgáló a kulcstartóban hozzáfér a TDE-védőhöz. Ezek az adatok a [Key Vault szolgáltatási korlátain](../../key-vault/general/service-limits.md)alapuló tapasztalatok alapján jelennek meg. A cél az, hogy megakadályozza a hibák elhárítását a kiszolgáló feladatátvétele után, mivel a kiszolgáló számos kulcsfontosságú műveletét indítja el a tárolóban, mivel vannak adatbázisok az adott kiszolgálón.

- Állítsa be a Key Vault erőforrás-zárolását annak szabályozására, hogy ki törölheti a kritikus erőforrást, és megelőzheti a véletlen vagy jogosulatlan törlést. További információ az [erőforrás-zárolásokról](../../azure-resource-manager/management/lock-resources.md).

- Naplózás és jelentéskészítés engedélyezése az összes titkosítási kulcson: a Key Vault olyan naplókat biztosít, amelyek könnyen beilleszthető más biztonsági információkba és az eseménykezelő eszközeibe. Az Operations Management Suite [log Analytics](../../azure-monitor/insights/key-vault-insights-overview.md) egy olyan szolgáltatásra mutat példát, amely már integrálva van.

- A titkosított adatbázisok magas rendelkezésre állásának biztosítása érdekében az egyes kiszolgálókat két, különböző régiókban található kulcstartóval kapcsolja össze, és ugyanazokat a kulcsfontosságú anyagokat kell tárolnia. Csak a Key vaultban lévő kulcs megjelölése a TDE-védővel megegyező régióban. A rendszer automatikusan átvált a kulcstartóra a távoli régióban, ha az adott régióban található kulcstartót érintő leállás van.

### <a name="recommendations-when-configuring-tde-protector"></a>Javaslatok a TDE-védő konfigurálásakor

- Őrizze meg a TDE Protector egy másolatát egy biztonságos helyen, vagy küldje el a letéti szolgáltatásnak.

- Ha a kulcsot a Key vaultban hozza létre, hozzon létre egy kulcsos biztonsági mentést, mielőtt első alkalommal használja a kulcsot a AKV-ben. A biztonsági másolat csak Azure Key Vault lehet visszaállítható. További információ a [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey) parancsról.

- Hozzon létre egy új biztonsági mentést, ha bármilyen változás történik a kulcsban (például a legfontosabb attribútumok, címkék, ACL-ek).

- A Key vaultban **megtartja a kulcs előző verzióit** a kulcsok elforgatásakor, így a régebbi adatbázisok biztonsági mentése visszaállítható. Ha a TDE-védőt módosítják egy adatbázisra vonatkozóan, az adatbázis régi biztonsági mentései **nem frissülnek** a legújabb TDE-oltalmazó használatára. A visszaállítás ideje alatt minden biztonsági mentéshez szükség van a TDE-védőre, amelyet a létrehozáskor titkosítottak. A kulcsok elforgatása elvégezhető a [transzparens adattitkosítás Protector a PowerShell használatával történő elforgatására](transparent-data-encryption-byok-key-rotation.md)vonatkozó utasítások követésével.

- A korábban használt kulcsok megtartása a AKV még a szolgáltatás által felügyelt kulcsokra váltás után is. Gondoskodik arról, hogy az adatbázis biztonsági másolatai visszaállíthatók legyenek a AKV-ben tárolt TDE-védelemmel.  A Azure Key Vaulttel létrehozott TDE-védőket fenn kell tartani, amíg az összes többi tárolt biztonsági másolat nem lett létrehozva a szolgáltatás által felügyelt kulcsokkal. A kulcsok helyreállítható biztonsági másolatait a [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey)használatával végezheti el.

- Ha egy biztonsági incidens során el szeretné távolítani egy potenciálisan feltört kulcsot az adatvesztés kockázata nélkül, kövesse a [potenciálisan feltört kulcs eltávolítása](transparent-data-encryption-byok-remove-tde-protector.md)című szakasz lépéseit.

## <a name="inaccessible-tde-protector"></a>Elérhetetlen TDE-védő

Ha az transzparens adattitkosítás ügyfél által felügyelt kulcs használatára van konfigurálva, a TDE-védő folyamatos hozzáférésre van szükség ahhoz, hogy az adatbázis online maradjon. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt TDE-védőhöz a AKV-ben, egy adatbázis akár 10 percen belül megtagadja az összes kapcsolatot a megfelelő hibaüzenettel, és nem *érhető* el az állapota. Az egyetlen olyan művelet, amely nem elérhető állapotban van, nem érhető el.

> [!NOTE]
> Ha az adatbázis egy időszakos hálózati leállás miatt nem érhető el, nincs szükség beavatkozásra, és az adatbázisok automatikusan újra online állapotba kerülnek.

A kulcshoz való hozzáférés visszaállítását követően az adatbázis visszahívása további időt és lépéseket igényel, amelyek a kulcshoz való hozzáférés és az adatbázisbeli adatmennyiség függvényében eltelt idő alapján változhatnak:

- Ha a kulcs-hozzáférés 8 órán belül helyreáll, az adatbázis a következő órában automatikusan meggyógyítható.

- Ha a rendszer 8 óránál hosszabb idő után állítja vissza a kulcsot, az automatikus javítás nem lehetséges, az adatbázis visszaállításához további lépésekre van szükség a portálon, és a folyamat az adatbázis méretétől függően jelentős időmennyiséget vehet igénybe. Miután az adatbázis ismét online állapotba került, korábban konfigurálta a kiszolgálói szintű beállításokat, például a [feladatátvételi csoport](auto-failover-group-overview.md) konfigurációját, az időponthoz tartozó visszaállítási előzményeket, a címkék pedig **elvesznek**. Ezért javasoljuk olyan értesítési rendszer megvalósítását, amely lehetővé teszi, hogy 8 órán belül azonosítsa és kezelje az alapul szolgáló kulcsfontosságú hozzáférési problémákat.

Az alábbiakban megtekintheti a portálon megjelenő további lépéseket, amelyekkel elérhetetlenné válik az adatbázisok online állapotba helyezése.

![TDE BYOK nem elérhető adatbázis](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>Véletlen TDE-védő hozzáférés visszavonása

Előfordulhat, hogy a Key vaulthoz megfelelő hozzáférési jogokkal rendelkező személy véletlenül letiltja a kiszolgáló hozzáférését a kulcshoz:

- a Key Vault *Get*, *wrapKey*, *unwrapKey* engedélyeinek visszavonása a kiszolgálóról

- a kulcs törlése

- a Key Vault törlése

- a Key Vault tűzfalszabály-szabályainak módosítása

- a kiszolgáló felügyelt identitásának törlése Azure Active Directory

További információ [az adatbázisok elérhetetlenné válásának gyakori okairól](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current&preserve-view=true#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Az ügyfél által felügyelt TDE figyelése

Az adatbázis állapotának figyeléséhez és a TDE-védő hozzáférésének elvesztése miatti riasztások engedélyezéséhez konfigurálja a következő Azure-szolgáltatásokat:

- [Azure Resource Health](../../service-health/resource-health-overview.md). Egy nem elérhető adatbázis, amely elvesztette a TDE-védőt, "nem érhető el" jelenik meg, miután megtagadták az adatbázishoz való első kapcsolódást.
- A [tevékenység naplója](../../service-health/alerts-activity-log-service-notifications-portal.md) , ha az ügyfél által felügyelt kulcstartóban lévő TDE-védőhöz való hozzáférés meghiúsul, a rendszer hozzáadja a bejegyzéseket a tevékenység naplójához.  Az eseményekhez tartozó riasztások létrehozása lehetővé teszi, hogy a lehető leghamarabb visszaállítsa a hozzáférést.
- A [csoportok](../../azure-monitor/alerts/action-groups.md) meghatározhatják, hogy az értesítések és a riasztások a beállításoknak megfelelően legyenek elküldve, például e-mail-/SMS-/leküldéses/hang-, Logic app-, ITSM-vagy Automation-Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Adatbázis biztonsági mentése és visszaállítása az ügyfél által felügyelt TDE

Ha egy adatbázis a Key Vault használatával titkosítva van a TDE, az újonnan létrehozott biztonsági másolatok is ugyanazzal a TDE-védővel lesznek titkosítva. A TDE-védő módosításakor az adatbázis régi biztonsági mentései **nem frissülnek** a legújabb TDE-oltalmazó használatára.

A Key Vault TDE-védővel titkosított biztonsági másolat visszaállításához ellenőrizze, hogy a fő anyag elérhető-e a célkiszolgálón. Ezért javasoljuk, hogy tartsa meg a TDE-védő összes korábbi verzióját a Key Vaultban, hogy az adatbázis biztonsági mentései visszaállíthatók legyenek.

> [!IMPORTANT]
> Egy adott kiszolgálón nem lehet egynél több TDE-védőt beállítani. Ez az a kulcs, amelynek a kulcsa az alapértelmezett TDE-védő legyen a Azure Portal panelen. Azonban több további kulcs is összekapcsolható egy kiszolgálóval anélkül, hogy TDE-védőként kellene megjelölni őket. Ezek a kulcsok nem használhatók a ADATTITKOSÍTÁSI kulcsot védelmére, de a biztonsági másolatból történő visszaállítás során használhatók, ha a biztonságimásolat-fájl a megfelelő ujjlenyomattal rendelkező kulccsal van titkosítva.

Ha a biztonsági mentés visszaállításához szükséges kulcs már nem érhető el a célkiszolgálón, a visszaállítási próbálkozáskor a következő hibaüzenet jelenik meg: "a célkiszolgáló nem fér hozzá a `<Servername>` és a között létrehozott összes AKV URI azonosítóhoz \<Timestamp #1> \<Timestamp #2> . Próbálkozzon újra a művelettel az összes AKV URI visszaállítása után. "

Ennek enyhítéséhez futtassa a [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) parancsmagot a célkiszolgálón vagy a [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) a célként felügyelt példányhoz, hogy visszaadja a rendelkezésre álló kulcsok listáját, és azonosítsa a hiányzókat. Annak érdekében, hogy az összes biztonsági másolat visszaállítható, győződjön meg arról, hogy a visszaállításhoz használt célkiszolgáló hozzáfér az összes szükséges kulcshoz. Ezeket a kulcsokat nem kell TDE-védőként megjelölni.

Ha többet szeretne megtudni a SQL Database biztonsági mentési helyreállításáról, tekintse meg az [adatbázis helyreállítása SQL Databaseban](recovery-using-backups.md)című témakört. Ha többet szeretne megtudni a dedikált SQL-készlet biztonsági mentési helyreállításáról az Azure szinapszis Analyticsben, tekintse meg [a DEDIKÁLT SQL-készlet helyreállítása](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md)című témakört. SQL Server natív biztonsági mentése/visszaállítása SQL felügyelt példánnyal: rövid útmutató [: adatbázis visszaállítása SQL felügyelt példányra](../managed-instance/restore-sample-database-quickstart.md)

További szempontok a naplófájlok számára: a biztonsági másolatba mentett naplófájlok továbbra is titkosítva maradnak az eredeti TDE-védővel, még akkor is, ha azt elforgatták, és az adatbázis mostantól új TDE-védőt használ.  A visszaállítás ideje alatt mindkét kulcsra szükség lesz az adatbázis visszaállításához.  Ha a naplófájl Azure Key Vaultban tárolt TDE-védőt használ, a kulcs visszaállításkor is szükséges lesz, még akkor is, ha az adatbázist úgy módosították, hogy a szolgáltatás által felügyelt TDE időközben is használhassa.

## <a name="high-availability-with-customer-managed-tde"></a>Magas rendelkezésre állás az ügyfél által felügyelt TDE

Olyan esetekben, amikor nincs konfigurálva geo-redundancia a kiszolgáló számára, javasoljuk, hogy konfigurálja úgy a kiszolgálót, hogy két különböző Key vaultot használjon két különböző régióban ugyanazzal a kulcsfontosságú anyaggal. A másik régió másodlagos kulcstartójában lévő kulcs nem lehet TDE-védőként megjelölve, és nem is engedélyezett. Ha van olyan leállás, amely hatással van az elsődleges Key vaultra, és csak ezután, a rendszer automatikusan átvált a másik csatolt kulcsra ugyanazzal az ujjlenyomattal a másodlagos kulcstartóban, ha létezik. Vegye figyelembe, hogy ha a TDE-védelem visszavont hozzáférési jogosultságok miatt nem érhető el, vagy mert a kulcs vagy kulcstartó törölve van, mivel előfordulhat, hogy az ügyfél szándékosan szeretné korlátozni a kiszolgáló hozzáférését a kulcshoz. Ha ugyanazokat a kulcsfontosságú anyagokat a különböző régiókban található két kulcstartóhoz szeretné biztosítani, a kulcs a kulcstartón kívüli létrehozásával, majd a Key vaultba való importálásával is elvégezhető. 

Azt is megteheti, hogy a kulcsot a-kiszolgálóval azonos régióban található elsődleges Key Vault használatával hozza létre, és a kulcsot egy másik Azure-régióban lévő kulcstartóba klónozással végzi. Használja a [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/Backup-AzKeyVaultKey) parancsmagot a kulcs titkosított formátumban való lekéréséhez az elsődleges Key vaultból, majd használja a [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) parancsmagot, és adja meg a második régióban található kulcstartót a kulcs klónozásához. Azt is megteheti, hogy a Azure Portal a kulcs biztonsági mentésére és visszaállítására használja. A kulcs biztonsági mentési/visszaállítási művelete csak az azonos Azure-előfizetésben és az [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/)található kulcstartók között engedélyezett.  

![Single-Server HA](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR és ügyfél által felügyelt TDE

Az [aktív geo-replikálási](active-geo-replication-overview.md) és [feladatátvételi csoportok](auto-failover-group-overview.md) esetében az összes érintett kiszolgálónak külön kulcstartóra van szüksége, amelynek a kiszolgálóval azonos Azure-régióban kell lennie. Az ügyfél feladata, hogy megőrizze a kulcsfontosságú anyagokat a kulcstartók között, így a Geo-másodlagos szinkronban van, és átveheti ugyanazt a kulcsot a helyi kulcstartóból, ha az elsődleges elérhetetlenné válik a régió meghibásodása miatt, és a feladatátvétel aktiválva van. Akár négy formátumú másodlagos zónák is konfigurálható, és a láncolás (formátumú másodlagos zónák of formátumú másodlagos zónák) nem támogatott.

Ha el szeretné kerülni, hogy a kulcsfontosságú anyagok hiányában a Geo-replikáció létrehozásakor vagy során problémák kerüljenek, fontos, hogy az ügyfél által felügyelt TDE konfigurálásakor kövesse ezeket a szabályokat:

- Az összes érintett kulcstárolónak azonos tulajdonságokkal kell rendelkeznie, és ugyanazokat a hozzáférési jogokat kell megadnia a megfelelő kiszolgálókhoz.

- Az összes érintett kulcstartónak azonos kulcsfontosságú anyagot kell tartalmaznia. Ez nem csak a jelenlegi TDE-védőre vonatkozik, hanem a biztonságimásolat-fájlokban esetlegesen használt összes korábbi TDE-védelemmel.

- A TDE-védő kezdeti beállítását és rotációját a másodlagos elsőn, majd az elsődlegesen kell elvégezni.

![Feladatátvételi csoportok és geo-Dr](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

A feladatátvétel teszteléséhez kövesse az [aktív geo-replikáció áttekintése](active-geo-replication-overview.md)című témakör lépéseit. A feladatátvételi tesztet rendszeresen kell elvégezni annak ellenőrzéséhez, hogy a SQL Database megőrizte-e a Key vaultok hozzáférési engedélyét.

## <a name="next-steps"></a>Következő lépések

A következő PowerShell-parancsfájlokat is érdemes megtekinteni az ügyfél által felügyelt TDE tartozó általános műveletekhez:

- [A SQL Database transzparens adattitkosítás Protector elforgatása a PowerShell használatával](transparent-data-encryption-byok-key-rotation.md)

- [Transzparens adattitkosítás (TDE) Protector eltávolítása a SQL Database PowerShell használatával](transparent-data-encryption-byok-remove-tde-protector.md)

- [A felügyelt SQL-példányok transzparens adattitkosítás kezelése a saját kulccsal a PowerShell használatával](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)