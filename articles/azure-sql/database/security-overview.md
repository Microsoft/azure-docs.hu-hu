---
title: Biztonsági áttekintés
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Ismerkedjen meg a Azure SQL Database és az Azure SQL felügyelt példányának biztonságával, beleértve a SQL Servertól való eltérést is.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: 39119f62fa938f5f4f6529539d4ca9a84bdf8fd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94989190"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>A Azure SQL Database és az SQL felügyelt példányának biztonsági képességeinek áttekintése
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ez a cikk a [Azure SQL Database](sql-database-paas-overview.md), az [Azure SQL felügyelt példányával](../managed-instance/sql-managed-instance-paas-overview.md)és az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)szolgáltatással való alkalmazás adatszintjeinek biztonságossá tételének alapjait ismerteti. A leírt biztonsági stratégia az alábbi képen látható, többrétegű védelem részletes megközelítését követi, és a következőn kívülről lép:

![A réteges védelem részletes ábrája. A vásárlói adatok a hálózati biztonság, a hozzáférés-kezelés, a fenyegetések és az adatvédelem rétegeibe vannak befoglalva.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Hálózati biztonság

A Microsoft Azure SQL Database, az SQL felügyelt példánya és az Azure szinapszis Analytics egy kapcsolódó adatbázis-szolgáltatást biztosít a felhőalapú és vállalati alkalmazásokhoz. Az ügyféladatok védelme érdekében a tűzfalak megakadályozzák a kiszolgálóhoz való hálózati hozzáférést, amíg az IP-cím vagy az Azure-beli virtuális hálózati forgalom nem engedélyezi a hozzáférést explicit módon.

### <a name="ip-firewall-rules"></a>IP-tűzfalszabályok

Az IP-tűzfalszabályok az egyes kérések származó IP-címe alapján biztosítanak hozzáférést az adatbázisokhoz. További információk: [a Azure SQL Database és az Azure szinapszis Analytics-tűzfalszabályok áttekintése](firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai

A [virtuális hálózati szolgáltatás végpontok](../../virtual-network/virtual-network-service-endpoints-overview.md) kiterjesztik a virtuális hálózati kapcsolatot az Azure-gerinc fölé, és lehetővé teszik Azure SQL Database azon virtuális hálózati alhálózat azonosítását, amelyből a forgalom származik. Ha engedélyezni szeretné, hogy a forgalom elérje a Azure SQL Database, használja az SQL- [szolgáltatás címkéit](../../virtual-network/network-security-groups-overview.md) , hogy engedélyezze a kimenő forgalmat a hálózati biztonsági csoportokon keresztül.

A [virtuális hálózati szabályok](vnet-service-endpoint-rule-overview.md) lehetővé teszik, hogy a Azure SQL Database csak a virtuális hálózaton belüli kijelölt alhálózatokból továbbított kommunikációt fogadják.

> [!NOTE]
> A tűzfalszabályok hozzáférésének szabályozása *nem* vonatkozik az **SQL felügyelt példányára**. További információ a szükséges hálózati konfigurációról: [Csatlakozás felügyelt példányhoz](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>Hozzáférés-kezelés

> [!IMPORTANT]
> Az adatbázisok és kiszolgálók Azure-beli kezelését a portál felhasználói fiókjának szerepkör-hozzárendelései vezérlik. Erről a cikkről a [Azure Portal Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md)című részében olvashat bővebben.

### <a name="authentication"></a>Hitelesítés

A hitelesítés az a folyamat, amellyel a felhasználó igazolni kívánja. A Azure SQL Database és az SQL felügyelt példánya két hitelesítési típust támogat:

- **SQL-hitelesítés**:

    Az SQL-hitelesítés a felhasználó hitelesítésére utal Azure SQL Database vagy Azure SQL felügyelt példányhoz való csatlakozáskor Felhasználónév és jelszó használatával. A kiszolgáló létrehozásakor meg kell adni egy felhasználónévvel és jelszóval rendelkező **kiszolgáló-rendszergazdai** bejelentkezést. Ezeknek a hitelesítő adatoknak a használatával a **kiszolgáló rendszergazdája** a kiszolgálón vagy a példányon található adatbázis-tulajdonosként hitelesítheti magát. Ezt követően további SQL-bejelentkezéseket és felhasználókat hozhat létre a kiszolgáló rendszergazdája, amely lehetővé teszi a felhasználók számára a Felhasználónév és a jelszó használatával történő kapcsolódást.

- **Azure Active Directory hitelesítés**:

    Azure Active Directory hitelesítés a [Azure SQL Databasehoz](sql-database-paas-overview.md), az [Azure SQL felügyelt példányához](../managed-instance/sql-managed-instance-paas-overview.md) és az [Azure szinapszis analyticshez](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) való csatlakozás mechanizmusa az Azure Active Directory (Azure ad) identitások használatával. Az Azure AD-hitelesítés lehetővé teszi, hogy a rendszergazdák központilag kezeljék az adatbázis-felhasználók identitásait és engedélyeit, valamint egy központi helyen lévő többi Azure-szolgáltatást. Ez magában foglalja a jelszó-tárolás minimalizálása és a központosított jelszó-rotációs házirendek használatát.

     Az Azure AD-hitelesítés SQL Database használatával való használatához létre kell hozni egy **Active Directory rendszergazdának** nevezett kiszolgáló-rendszergazdát. További információ: [csatlakozás SQL Databasehoz Azure Active Directory hitelesítés használatával](authentication-aad-overview.md). Az Azure AD-hitelesítés mind a felügyelt, mind az összevont fiókokat támogatja. Az összevont fiókok támogatják a Windows-felhasználókat és-csoportokat az Azure AD-vel összevont ügyfél-tartományokhoz.

    Az elérhető további Azure AD-hitelesítési lehetőségek a SQL Server Management Studio-kapcsolatokhoz [Active Directory univerzális hitelesítés,](authentication-mfa-ssms-overview.md) például [multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) és [feltételes hozzáférés](conditional-access-configure.md).

> [!IMPORTANT]
> Az adatbázisok és kiszolgálók Azure-beli kezelését a portál felhasználói fiókjának szerepkör-hozzárendelései vezérlik. Erről a cikkről a [Azure Portal Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md)című témakörében olvashat bővebben. A tűzfalszabályok hozzáférésének szabályozása *nem* vonatkozik az **SQL felügyelt példányára**. Ha további információkra van szüksége a hálózati konfigurációval kapcsolatban, tekintse meg a következő cikket a [felügyelt példányhoz való csatlakozásról](../managed-instance/connect-application-instance.md) .

## <a name="authorization"></a>Engedélyezés

Az engedélyezés az Azure SQL Database vagy az Azure SQL felügyelt példányában lévő egyik felhasználóhoz rendelt engedélyekre vonatkozik, és meghatározza, hogy a felhasználó mit tehet. Az engedélyek szabályozása úgy történik, hogy felhasználói fiókokat ad hozzá az [adatbázis szerepköreihez](/sql/relational-databases/security/authentication-access/database-level-roles) , és adatbázis-szintű engedélyeket rendel hozzájuk ezekhez a szerepkörökhöz, vagy ha a felhasználó számára bizonyos [objektum-szintű engedélyeket](/sql/relational-databases/security/permissions-database-engine)ad meg. További információ: [bejelentkezések és felhasználók](logins-create-manage.md)

Ajánlott eljárásként hozzon létre egyéni szerepköröket, ha szükséges. Vegyen fel felhasználókat a szerepkörbe a feladat funkciójának végrehajtásához szükséges legalacsonyabb jogosultságokkal. Ne rendeljen engedélyeket közvetlenül a felhasználóknak. A kiszolgálói rendszergazdai fiók tagja a beépített db_owner szerepkörnek, amely kiterjedt engedélyekkel rendelkezik, és csak néhány rendszergazdai feladattal rendelkező felhasználónak kell megadnia. Alkalmazások esetén a [végrehajtás másként](/sql/t-sql/statements/execute-as-clause-transact-sql) beállítással adhatja meg a hívott modul végrehajtási környezetét, vagy használjon korlátozott engedélyekkel rendelkező [alkalmazás-szerepköröket](/sql/relational-databases/security/authentication-access/application-roles) . Ez a gyakorlat biztosítja, hogy az adatbázishoz csatlakozó alkalmazáshoz az alkalmazás által igényelt legalacsonyabb jogosultságok szükségesek. Az alábbi ajánlott eljárások a feladatok elkülönítését is támogatják.

### <a name="row-level-security"></a>Sorszintű biztonság

A Row-Level biztonság lehetővé teszi az ügyfelek számára, hogy a lekérdezést végrehajtó felhasználó jellemzői alapján szabályozzák az adatbázistábla soraihoz való hozzáférést (például csoporttagság vagy végrehajtási környezet). Row-Level biztonság az egyéni címkéken alapuló biztonsági fogalmak megvalósítására is használható. További információ: [sor szintű biztonság](/sql/relational-databases/security/row-level-security).

![A diagram azt mutatja, hogy Row-Level biztonsági védelem egy SQL-adatbázis egyes sorait a felhasználók által egy ügyfélalkalmazás segítségével érheti el.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Fenyegetések elleni védelem

Az SQL Database és az SQL felügyelt példánya biztonságos ügyféladatokat biztosít a naplózási és a fenyegetés-észlelési képességek biztosításával.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-naplózás Azure Monitor naplókban és Event Hubs

A SQL Database és az SQL felügyelt példányának naplózása nyomon követi az adatbázis-tevékenységeket, és a biztonsági szabványoknak való megfelelést az adatbázis-események az ügyfél által birtokolt Azure Storage-fiókba való rögzítésével segíti. A naplózás lehetővé teszi a felhasználók számára a folyamatos adatbázis-tevékenységek figyelését, valamint a múltbeli tevékenységek elemzését és kivizsgálását a potenciális fenyegetések vagy a gyanús visszaélések és biztonsági szabálysértések azonosítása érdekében. További információ: Ismerkedés a [SQL Database naplózással](../../azure-sql/database/auditing-overview.md).  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

A komplex veszélyforrások elleni védelem a naplók elemzésével azonosíthatja a szokatlan viselkedést és a potenciálisan ártalmas kísérleteket az adatbázisok eléréséhez vagy kiaknázásához. A riasztások olyan gyanús tevékenységekhez jönnek létre, mint például az SQL-injektálás, a lehetséges adatszivárgások és a találgatásos támadások, illetve a hozzáférési mintákban mutatkozó rendellenességek a jogosultság-eszkalációs és a megsértett hitelesítő adatok használata esetén. A riasztásokat a  [Azure Security Center](https://azure.microsoft.com/services/security-center/)tekintik meg, ahol a gyanús tevékenységek részletei megtalálhatók, valamint a fenyegetést enyhítő műveletekkel kapcsolatos javaslatok a további vizsgálathoz. A komplex veszélyforrások elleni védelem felár ellenében engedélyezhető kiszolgálónként. További információ: Ismerkedés [a SQL Database komplex veszélyforrások elleni védelemmel](threat-detection-configure.md).

![Ábra: az SQL-veszélyforrások észlelésének figyelése az SQL Database-hez egy külső támadótól és rosszindulatú bennfentestől származó webalkalmazáshoz való hozzáféréshez.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Adatvédelem és titkosítás

### <a name="transport-layer-security-encryption-in-transit"></a>Transport Layer Security (adatátviteli titkosítás)

A SQL Database, az SQL felügyelt példánya és az Azure szinapszis Analytics biztonságos ügyféladatokat biztosít a [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)használatával végzett adattitkosítással.

Az SQL Database, az SQL felügyelt példánya és az Azure szinapszis Analytics minden kapcsolat esetében minden alkalommal kikényszeríti a titkosítást (SSL/TLS). Ez biztosítja, hogy az összes adatforgalom titkosítva legyen az ügyfél és a kiszolgáló között, függetlenül attól, hogy a **titkosítás** vagy a **TrustServerCertificate** a kapcsolati sztringben van-e beállítva.

Ajánlott eljárásként Azt javasoljuk, hogy az alkalmazás által használt kapcsolati sztringben egy titkosított kapcsolatot határozzon meg, és _**ne**_ Bízzon meg a kiszolgálói tanúsítványban. Ez arra kényszeríti az alkalmazást, hogy ellenőrizze a kiszolgáló tanúsítványát, így megakadályozza, hogy az alkalmazás sebezhető legyen a középső típusú támadásokban lévő ember számára.

Például a ADO.NET-illesztőprogram használata esetén ez a  **titkosítás = True** és a **TrustServerCertificate = false** használatával valósítható meg. Ha a Azure Portalból szerzi be a kapcsolatok karakterláncát, akkor a megfelelő beállításokkal fog rendelkezni.

> [!IMPORTANT]
> Vegye figyelembe, hogy egyes nem Microsoft-illesztőprogramok nem használhatják a TLS-t alapértelmezés szerint, vagy a TLS (<1,2) régebbi verzióját használják a működéshez. Ebben az esetben a kiszolgáló továbbra is lehetővé teszi az adatbázishoz való kapcsolódást. Javasoljuk azonban, hogy értékelje a biztonsági kockázatokat, amelyek lehetővé teszik, hogy az ilyen illesztőprogramok és alkalmazások csatlakozzanak a SQL Databasehoz, különösen ha bizalmas adatokat tárol.
>
> A TLS-vel és a kapcsolattal kapcsolatos további információkért lásd: [TLS-megfontolások](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transzparens adattitkosítás (titkosítás – REST)

A [SQL Database, az SQL felügyelt példányának és az Azure szinapszis Analytics transzparens adattitkosítása (TDE)](transparent-data-encryption-tde-overview.md) egy biztonsági réteget biztosít, amely segít az inaktív adatoknak a nyers fájlokhoz vagy biztonsági másolatokhoz való jogosulatlan vagy offline hozzáférésének biztosításához. Gyakori forgatókönyvek például a következők: adatközpont-lopás vagy hardver vagy adathordozó nem biztonságos ártalmatlanítása, például lemezmeghajtók és biztonsági mentési szalagok.A TDE egy AES titkosítási algoritmussal titkosítja a teljes adatbázist, amely nem igényli, hogy az alkalmazás fejlesztői ne kelljen módosítaniuk a meglévő alkalmazásokat.

Az Azure-ban az összes újonnan létrehozott adatbázis alapértelmezés szerint titkosítva van, és az adatbázis-titkosítási kulcsot egy beépített kiszolgálótanúsítvány védi.  A tanúsítvány-karbantartási és-rotációs szolgáltatást a szolgáltatás felügyeli, és nem igényel bemenetet a felhasználótól. A titkosítási kulcsok felügyeletét előnyben részesített ügyfelek a [Azure Key Vaultban](../../key-vault/general/secure-your-key-vault.md)kezelhetik a kulcsokat.

### <a name="key-management-with-azure-key-vault"></a>Kulcskezelő Azure Key Vault

A [transzparens adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) [bring your own Key](transparent-data-encryption-byok-overview.md) (BYOK) támogatása lehetővé teszi, hogy az ügyfelek a [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md), az Azure felhőalapú külső kulcskezelő rendszerének használatával magukénak vállaljanak a kulcskezelő és a rotációs szolgáltatásban. Ha az adatbázis a kulcstartóhoz való hozzáférését visszavonja, az adatbázist nem lehet visszafejteni és a memóriába beolvasni. A Azure Key Vault egy központi kulcskezelő platformot biztosít, amely szorosan felügyelt hardveres biztonsági modulokat (HSM-ket) használ, és lehetővé teszi a kulcsok és adatok kezelése közötti feladatok elkülönítését a biztonsági megfelelőségi követelmények kielégítése érdekében.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (titkosítás használatban)

![A Always Encrypted funkció alapjait bemutató ábra. A zárolással rendelkező SQL Database-adatbázist csak egy kulcsot tartalmazó alkalmazás éri el.](./media/security-overview/azure-database-ae.png)

A [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) az adott adatbázis-oszlopokban tárolt bizalmas adatok hozzáférésének (például a hitelkártya-számok, a nemzeti azonosító számok vagy az adatok _ismerete_ alapján) való védelme. Ebbe beletartozik az adatbázis-rendszergazdák vagy más, jogosultsággal rendelkező felhasználók, akik jogosultak az adatbázis elérésére a felügyeleti feladatok elvégzéséhez, de nincs szükségük üzleti tevékenységre a titkosított oszlopokban lévő adatok eléréséhez. A rendszer mindig titkosítja az adattitkosítást, ami azt jelenti, hogy a titkosított adatforgalom csak olyan ügyfélalkalmazások általi feldolgozásra van visszafejtve, amelyek hozzáférnek a titkosítási kulcshoz. A titkosítási kulcs soha nem érhető el SQL Database vagy SQL felügyelt példány számára, és a [Windows tanúsítványtárolóban](always-encrypted-certificate-store-configure.md) vagy [Azure Key Vault](always-encrypted-azure-key-vault-configure.md)tárolható.

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

![A dinamikus adatmaszkolást ábrázoló diagram. Az üzleti alkalmazások adatokat küldenek egy SQL-adatbázisba, amely elfedi az adatokat, mielőtt visszaküldi azt az üzleti alkalmazásnak.](./media/security-overview/azure-database-ddm.png)

A dinamikus adatmaszkolás csökkenti a bizalmas adatok kitettségét azzal, hogy nem jogosult felhasználókhoz maszkolja őket. A dinamikus adatmaszkolás automatikusan felfedi a Azure SQL Database és az SQL felügyelt példányában előforduló potenciálisan bizalmas adatokat, és gyakorlati ajánlásokat biztosít ezen mezők maszkolásához, az alkalmazás rétegének minimális hatásával. Rejtjelezi a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja. További információ: Ismerkedés [a SQL Database és az SQL felügyelt példányának dinamikus adatmaszkolásával](dynamic-data-masking-overview.md).

## <a name="security-management"></a>Biztonsági felügyelet

### <a name="vulnerability-assessment"></a>Sebezhetőségi felmérés

A [sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket azzal a céllal, hogy proaktív módon javítsa a teljes adatbázis-biztonságot. A biztonsági rések felmérése (VA) az Azure Defender for SQL-ajánlat része, amely a fejlett SQL biztonsági funkciók egységes csomagja. A sebezhetőségi felmérés a központi Azure Defender for SQL Portalon érhető el és kezelhető.

### <a name="data-discovery-and-classification"></a>Adatfelderítés és besorolás

Az adatfelderítés és-besorolás (jelenleg előzetes verzióban érhető el) a Azure SQL Database és az SQL felügyelt példányaiba beépített fejlett képességeket nyújt a bizalmas adatok felderítéséhez, besorolásához, címkézéséhez és védelméhez az adatbázisaiban. A rendkívül bizalmas adatok (üzleti/pénzügyi, egészségügyi, személyes adatok stb.) felfedése és besorolása kulcsfontosságú szerepet játszik a szervezeti adatok védelmében. Infrastruktúraként alkalmas lehet az alábbiakra:

- Különböző biztonsági forgatókönyvek, például a figyelés (naplózás) és a bizalmas adatok rendellenes hozzáférésének riasztása.
- A szigorúan bizalmas adatokat tartalmazó adatbázisok hozzáférésének szabályozása, és a biztonság megerősítése.
- Segíthet megfelelni az adatvédelmi szabványoknak és a szabályozási megfelelőség követelményeinek.

További információ: Ismerkedés [az adatok felderítésével és besorolásával](data-discovery-and-classification-overview.md).

### <a name="compliance"></a>Megfelelőség

A fenti szolgáltatások és funkciók mellett, amelyek segíthetnek az alkalmazásnak a különböző biztonsági követelmények teljesítésében, Azure SQL Database is részt vesz a rendszeres auditálásokban, és számos megfelelőségi szabványnak megfelelő minősítéssel rendelkezik. További információkért tekintse meg a [Microsoft Azure adatvédelmi központot](https://www.microsoft.com/trust-center/compliance/compliance-overview) , ahol megtalálhatja a SQL Database megfelelőségi minősítések legfrissebb listáját.

## <a name="next-steps"></a>Következő lépések

- A bejelentkezések, felhasználói fiókok, adatbázis-szerepkörök és engedélyek a SQL Database és az SQL felügyelt példányban való használatának megvitatására a [bejelentkezések és a felhasználói fiókok kezelése](logins-create-manage.md)című témakörben olvashat.
- Az adatbázis-naplózással kapcsolatos megbeszélések: [naplózás](../../azure-sql/database/auditing-overview.md).
- A veszélyforrások észlelésével kapcsolatban lásd: [veszélyforrások észlelése](threat-detection-configure.md).