---
title: biztonsági áttekintés
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Ismerje meg a biztonsági Azure SQL Database és Azure SQL Managed Instance, beleértve a különbségeket a SQL Server.
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
ms.openlocfilehash: 39012e1f5a0282da7dda6bab216719e31fdc5061
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752173"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>A biztonsági képességek Azure SQL Database SQL Managed Instance áttekintése
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ez a cikk ismerteti az alkalmazások adatrétegének biztonságossá tétele alapjait a [Azure SQL Database](sql-database-paas-overview.md) [,](../managed-instance/sql-managed-instance-paas-overview.md)Azure SQL Managed Instance és [Azure Synapse Analytics.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Az ismertetett biztonsági stratégia az alábbi képen látható, mélységi védelemre irányuló megközelítést követi, és kívülről a következőképpen halad:

![A mélységi védelem réteges ábrázolása. Az ügyféladatok a hálózati biztonság, a hozzáférés-kezelés, valamint a fenyegetés- és információvédelem rétegeibe vannak bevetve.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Hálózati biztonság

Microsoft Azure SQL Database, SQL Managed Instance és Azure Synapse Analytics relációsadatbázis-szolgáltatást biztosítanak a felhőbeli és vállalati alkalmazásokhoz. Az ügyféladatok védelme érdekében a tűzfalak megakadályozzák a kiszolgálóhoz való hálózati hozzáférést, amíg a hozzáférést kifejezetten meg nem adták az IP-cím vagy az Azure-beli virtuális hálózati forgalom eredete alapján.

### <a name="ip-firewall-rules"></a>IP-tűzfalszabályok

Az IP-tűzfalszabályok az egyes kérések eredeti IP-címe alapján biztosítják az adatbázisokhoz való hozzáférést. További információ: [A tűzfalszabályok Azure SQL Database Azure Synapse Analytics áttekintése.](firewall-configure.md)

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai

[A virtuális hálózati szolgáltatásvégpontokkal](../../virtual-network/virtual-network-service-endpoints-overview.md) kiterjesztheti a virtuális hálózati kapcsolatot az Azure gerinchálózatán keresztül, és lehetővé teheti Azure SQL Database számára, hogy azonosítsa a virtuális hálózat azon alhálózatát, amelyről a forgalom származik. Ahhoz, hogy a forgalom elér Azure SQL Database, [](../../virtual-network/network-security-groups-overview.md) az SQL-szolgáltatáscímkék használatával engedélyezze a hálózati biztonsági csoportokon keresztül kimenő forgalmat.

[A virtuális hálózati szabályok](vnet-service-endpoint-rule-overview.md) Azure SQL Database, hogy csak a virtuális hálózat kiválasztott alhálózatairól küldött kommunikációt fogadják.

> [!NOTE]
> A tűzfalszabályokkal való hozzáférés-vezérlés *nem vonatkozik* a **SQL Managed Instance.** További információ a szükséges hálózati konfigurációról: Csatlakozás felügyelt [példányhoz](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>Hozzáférés-kezelés

> [!IMPORTANT]
> Az adatbázisok és kiszolgálók Azure-ban való felügyeletét a portál felhasználói fiókjának szerepkör-hozzárendelései vezérlik. További információ erről a cikkről: [Azure szerepköralapú hozzáférés-vezérlés a Azure Portal.](../../role-based-access-control/overview.md)

### <a name="authentication"></a>Hitelesítés

A hitelesítés az a folyamat, amely igazolja, hogy a felhasználó az, akinek ki van mondani. Azure SQL Database és SQL Managed Instance kétféle hitelesítést támogatnak:

- **SQL-hitelesítés:**

    Az SQL-hitelesítés a felhasználó hitelesítését jelenti, amikor felhasználónévvel Azure SQL Database jelszóval Azure SQL Managed Instance felhasználóhoz vagy fiókhoz csatlakozik. A **kiszolgáló létrehozásakor** meg kell adni egy kiszolgáló-rendszergazdai bejelentkezést felhasználónévvel és jelszóval. Ezekkel a hitelesítő adatokkal a **kiszolgálói** rendszergazda az adott kiszolgáló vagy példány bármelyik adatbázisában hitelesítheti magát az adatbázis tulajdonosaként. Ezt követően a kiszolgálói rendszergazda további SQL-bejelentkezéseket és -felhasználókat hoz létre, amelyek lehetővé teszik, hogy a felhasználók felhasználónévvel és jelszóval csatlakozznak.

- **Azure Active Directory hitelesítés:**

    Azure Active Directory a hitelesítés egy olyan mechanizmus, Azure SQL Database [,](sql-database-paas-overview.md)Azure SQL Managed Instance és [](../managed-instance/sql-managed-instance-paas-overview.md) [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) identitások használatával az Azure Active Directory (Azure AD) szolgáltatásban. Az Azure AD-hitelesítéssel a rendszergazdák központilag kezelhetik az adatbázis-felhasználók identitását és engedélyét, valamint más Azure-szolgáltatásokat egy központi helyen. Ez magában foglalja a jelszótárolás minimalizálását, és lehetővé teszi a központi jelszórotációs szabályzatok beállítását.

     Létre kell Active Directory  nevű kiszolgálói rendszergazdát, hogy Azure AD-hitelesítést használ SQL Database. További információ: Csatlakozás SQL Database [hitelesítéssel Azure Active Directory.](authentication-aad-overview.md) Az Azure AD-hitelesítés a felügyelt és az összevont fiókokat is támogatja. Az összevont fiókok támogatják a Windows-felhasználókat és -csoportokat az Azure AD-val összevont ügyféltartományok esetében.

    Az Azure AD további hitelesítési lehetőségei Active Directory általános hitelesítést érhetők el SQL Server Management Studio [kapcsolatokhoz,](authentication-mfa-ssms-overview.md) beleértve a [többtényezős](../../active-directory/authentication/concept-mfa-howitworks.md) hitelesítést és a [feltételes hozzáférést.](conditional-access-configure.md)

> [!IMPORTANT]
> Az adatbázisok és kiszolgálók Azure-ban való felügyeletét a portál felhasználói fiókjának szerepkör-hozzárendelései vezérlik. További információ erről a cikkről: [Azure szerepköralapú hozzáférés-vezérlés a Azure Portal.](../../role-based-access-control/overview.md) A tűzfalszabályokkal való hozzáférés-vezérlés *nem vonatkozik* a **SQL Managed Instance.** A szükséges hálózati konfigurációval kapcsolatos további információkért tekintse meg [a](../managed-instance/connect-application-instance.md) felügyelt példányhoz való csatlakozásról a következő cikket.

## <a name="authorization"></a>Engedélyezés

Az engedélyezés a felhasználóhoz egy adatbázison belüli, a Azure SQL Database vagy Azure SQL Managed Instance belüli felhasználóhoz rendelt engedélyekre vonatkozik, és meghatározza, hogy a felhasználó mit engedélyezhet. Az engedélyeket úgy szabályozhatja, hogy felhasználói fiókokat ad hozzá az adatbázis-szerepkörökhöz, és adatbázisszintű engedélyeket rendel ezekhez a szerepkörökhöz, vagy bizonyos objektumszintű engedélyeket ad a [felhasználónak.](/sql/relational-databases/security/permissions-database-engine) [](/sql/relational-databases/security/authentication-access/database-level-roles) További információ: [Bejelentkezések és felhasználók](logins-create-manage.md)

Ajánlott eljárásként szükség esetén hozzon létre egyéni szerepköröket. Adjon hozzá felhasználókat a szerepkörhöz a feladatukhoz szükséges legkevesebb jogosultsággal. Ne rendeljen engedélyeket közvetlenül a felhasználókhoz. A kiszolgálói rendszergazdai fiók a beépített db_owner szerepkör tagja, amely széles körű engedélyekkel rendelkezik, és csak kevés rendszergazdai feladatokkal rendelkező felhasználó számára adható meg. Alkalmazások esetén az [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) használatával adja meg a [](/sql/relational-databases/security/authentication-access/application-roles) nevű modul végrehajtási környezetét, vagy használjon korlátozott engedélyekkel rendelkező alkalmazásszerepk szerepköröket. Ez a gyakorlat biztosítja, hogy az adatbázishoz csatlakozó alkalmazás a legkevesebb, az alkalmazás számára szükséges jogosultsággal rendelkezik. Ezeknek az ajánlott eljárásoknak a követnie kell a feladatok elkülönítését is.

### <a name="row-level-security"></a>Sorszintű biztonság

Row-Level Security lehetővé teszi az ügyfelek számára, hogy a lekérdezést végrehajtó felhasználó jellemzői (például csoporttagság vagy végrehajtási környezet) alapján szabályozni ellenőrizzék az adatbázistáblák soraihoz való hozzáférést. Row-Level Security egyéni címkealapú biztonsági fogalmak megvalósításához is használható. További információ: [Sorszintű biztonság.](/sql/relational-databases/security/row-level-security)

![A biztonsági Row-Level az SQL-adatbázis egyes sorait védi a felhasználók ügyfélalkalmazáson keresztüli hozzáférése ellen.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Fenyegetések elleni védelem

SQL Database és SQL Managed Instance az ügyféladatokat naplózási és fenyegetésészlelési képességek biztosításával.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-naplózás az Azure Monitor naplókban és Event Hubs

SQL Database és SQL Managed Instance naplózás nyomon követi az adatbázis-tevékenységeket, és segít a biztonsági szabványoknak való megfelelés fenntartásában azáltal, hogy rögzíti az adatbázis-eseményeket egy ügyfél tulajdonában lévő Azure-tárfiókban lévő auditnaplóba. A naplózás lehetővé teszi a felhasználók számára a folyamatban lévő adatbázis-tevékenységek monitorozását, valamint az előzménytevékenységek elemzését és kivizsgálását a potenciális fenyegetések, a gyanús visszaélések és a biztonsági szabálysértések azonosítása érdekében. További információ: Az SQL Database [első lépések.](../../azure-sql/database/auditing-overview.md)  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Az Advanced Threat Protection a naplók elemzésével észleli a szokatlan viselkedést és az adatbázisokhoz való hozzáférésre vagy az adatbázisok kihasználására tett potenciálisan rosszindulatú kísérleteket. A rendszer riasztásokat hoz létre olyan gyanús tevékenységekhez, mint az SQL-injektálás, a potenciális adatszivárgás és a találgatásos támadások, vagy a hozzáférési minták rendellenességei a jogosultságeszkalációk és a hitelesítő adatok használatának észlelése érdekében. A riasztások a következő Azure Security Center megtekinthetők:  [,](https://azure.microsoft.com/services/security-center/)ahol a rendszer a gyanús tevékenységek részleteit tartalmazza, valamint javaslatokat tesz a további vizsgálatra, valamint a fenyegetés mérséklésére irányuló műveletekre. Az Advanced Threat Protection kiszolgálónként további díj ellenében engedélyezhető. További információ: [Az Advanced Threat Protection SQL Database első lépések.](threat-detection-configure.md)

![Diagram, amely azt mutatja be, hogyan figyeli az SQL-fenyegetésészlelés egy webalkalmazás SQL-adatbázisához való hozzáférést egy külső támadótól és egy rosszindulatú belső támadótól.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Adatvédelem és titkosítás

### <a name="transport-layer-security-encryption-in-transit"></a>Transport Layer Security (átvitel közbeni titkosítás)

SQL Database , SQL Managed Instance és Azure Synapse Analytics ügyféladatokat a mozgásban Transport Layer Security [(TLS) titkosításával.](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

SQL Database, SQL Managed Instance és Azure Synapse Analytics titkosítás (SSL/TLS) kényszerítése minden kapcsolathoz. Ez biztosítja, hogy minden adat titkosítva legyen az ügyfél és a kiszolgáló között, függetlenül az **Encrypt** vagy **a TrustServerCertificate** beállításától a kapcsolati sztringben.

Ajánlott eljárásként javasoljuk, hogy az alkalmazás által használt kapcsolati sztringben titkosított kapcsolatot adjon meg, és ne bízzon meg a kiszolgálói tanúsítványban. __ Ez arra kényszeríti az alkalmazást, hogy ellenőrizze a kiszolgálói tanúsítványt, így megakadályozza, hogy az alkalmazás sebezhető legyen a középső típusú támadásokkal szemben.

Ha például a ADO.NET illesztőt használja, ezt az  **Encrypt=True** és **a TrustServerCertificate=False** használatával lehet elvégezni. Ha a kapcsolati sztringet a Azure Portal, az a megfelelő beállításokkal fog rendelkezik.

> [!IMPORTANT]
> Vegye figyelembe, hogy egyes nem Microsoft-illesztőprogramok alapértelmezés szerint nem használják a TLS-t, vagy a TLS (<1.2) egy régebbi verziójára támaszkodnak a működéshez. Ebben az esetben a kiszolgáló továbbra is lehetővé teszi az adatbázishoz való csatlakozást. Azt javasoljuk azonban, hogy értékelje ki, milyen biztonsági kockázatokkal jár, ha engedélyezi az ilyen illesztőprogramok és alkalmazások SQL Database, különösen akkor, ha bizalmas adatokat tárol.
>
> További információ a TLS-ről és a kapcsolatokról: [TLS-megfontolások](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>transzparens adattitkosítás (titkosítás az adattitkosítás során)

A transzparens adattitkosítás [(TDE) SQL Database, SQL Managed Instance](transparent-data-encryption-tde-overview.md) és Azure Synapse Analytics egy biztonsági réteget biztosít, amely segít megvédeni az aktív adatokat a nyers fájlokhoz vagy biztonsági másolatokhoz való jogosulatlan vagy offline hozzáféréstől. Gyakori forgatókönyvek például az adatközpontok ellopása vagy a hardverek vagy adathordozók, például lemezmeghajtók és biztonságimásolat-szalagok nem biztonságos megsemmisítése.A TDE egy AES titkosítási algoritmussal titkosítja a teljes adatbázist, amely nem követeli meg, hogy az alkalmazásfejlesztők módosítanák a meglévő alkalmazásokat.

Az Azure-ban alapértelmezés szerint minden újonnan létrehozott adatbázis titkosítva van, és az adatbázis-titkosítási kulcsot egy beépített kiszolgálói tanúsítvány védi.  A tanúsítványkarbantartást és a rotációt a szolgáltatás kezeli, és nem igényel felhasználói beavatkozást. Azok az ügyfelek, akik szeretnék át venni az irányítást a titkosítási kulcsok felett, a következő [Azure Key Vault.](../../key-vault/general/security-overview.md)

### <a name="key-management-with-azure-key-vault"></a>Kulcskezelés Azure Key Vault

[Bring Your Own Key](transparent-data-encryption-byok-overview.md) (BYOK) támogatása lehetővé teszi az [transzparens adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) számára, hogy az azure-beli felhőalapú külső kulcskezelő rendszer, a Azure Key Vault használatával saját tulajdonba vegye a kulcskezelést és [a](../../key-vault/general/security-overview.md)rotációt. Ha visszavonják az adatbázis kulcstartóhoz való hozzáférését, az adatbázis nem lesz visszafejthető és nem olvasható be a memóriába. Azure Key Vault központi kulcskezelési platformot biztosít, szorosan figyelt hardveres biztonsági modulokat (HSM-eket) biztosít, és lehetővé teszi a kulcsok és adatok kezelése közötti feladatok elkülönítését a biztonsági megfelelőségi követelmények teljesítéséhez.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (Használatban van titkosítás)

![A szolgáltatás alapjait Always Encrypted ábra. A zárolást tartalmazó SQL-adatbázishoz csak egy kulcsot tartalmazó alkalmazás fér hozzá.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkció arra lett tervezve, hogy bizonyos adatbázis-oszlopokban tárolt bizalmas adatokat védenének a hozzáféréstől (például hitelkártyaszámok, országos azonosítószámok vagy adatokat, amelyekről adatokat _kell okkal adatokat_ adatokat ismernünk). Ebbe beletartoznak az adatbázisgazdák vagy más kiemelt jogosultságú felhasználók is, akik jogosultak az adatbázis elérésére felügyeleti feladatok elvégzéséhez, de nincs üzleti szükségük arra, hogy hozzáférjenek a titkosított oszlopokban lévő adott adatokhoz. Az adatok mindig titkosítva vannak, ami azt jelenti, hogy a titkosított adatok visszafejtése csak a titkosítási kulcshoz hozzáférő ügyfélalkalmazások számára történik. A titkosítási kulcs soha nem lesz SQL Database vagy SQL Managed Instance, és tárolható a [Windows](always-encrypted-certificate-store-configure.md) tanúsítványtárolójában vagy a [Azure Key Vault.](always-encrypted-azure-key-vault-configure.md)

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

![Dinamikus adatmaszkolást bemutató ábra. Az üzleti alkalmazások adatokat küldenek egy SQL-adatbázisba, amely maszkolást hoz létre, mielőtt visszaküldi őket az üzleti alkalmazásnak.](./media/security-overview/azure-database-ddm.png)

A dinamikus adatmaszkolás csökkenti a bizalmas adatok kitettségét azzal, hogy nem jogosult felhasználókhoz maszkolja őket. A dinamikus adatmaszkolás automatikusan felderíti a potenciálisan bizalmas adatokat a Azure SQL Database-ban SQL Managed Instance, és lehetővé teszi a mezők maszkolását, minimális hatással az alkalmazásrétegre. Rejtjelezi a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja. További információ: A dinamikus [adatmaszkolás SQL Database SQL Managed Instance első lépések.](dynamic-data-masking-overview.md)

## <a name="security-management"></a>Biztonsági felügyelet

### <a name="vulnerability-assessment"></a>Sebezhetőségi felmérés

[A sebezhetőségi](sql-vulnerability-assessment.md) felmérés egy könnyen konfigurálható szolgáltatás, amely képes felderítni, nyomon követni és orvosolni az adatbázisok lehetséges biztonsági réseit azzal a céllal, hogy proaktív módon javítsa az adatbázisok általános biztonságát. A sebezhetőségi felmérés (VA) az SQL-Azure Defender részét képezi, amely a fejlett SQL biztonsági képességek egységes csomagja. A sebezhetőségi felmérés az SQL Portal központi Azure Defender érhető el és kezelhető.

### <a name="data-discovery-and-classification"></a>Adatfelderítés és besorolás

Az adatfelderítés és -besorolás (amely jelenleg előzetes verzióban érhető el) az Azure SQL Database és SQL Managed Instance beépített speciális képességeket biztosít az adatbázisokban lévő bizalmas adatok felderítéséhez, besorolásához, címkézéséhez és védelméhez. A legérzékenyebb bizalmas adatok (üzleti/pénzügyi, egészségügyi, személyes adatok stb.) felderítése és besorolása kulcsfontosságú szerepet játszhat a szervezeti Information Protection-környezetben. Infrastruktúraként alkalmas lehet az alábbiakra:

- Különböző biztonsági forgatókönyvek, például figyelés (naplózás) és riasztások a bizalmas adatokhoz való rendellenes hozzáférésről.
- Szigorúan bizalmas adatokat tartalmazó adatbázisokhoz való hozzáférés szabályozása és biztonságának ellenőrzése.
- Segíthet megfelelni az adatvédelmi szabványoknak és a szabályozási megfelelőség követelményeinek.

További információ: [Az adatfelderítés és -besorolás első lépések.](data-discovery-and-classification-overview.md)

### <a name="compliance"></a>Megfelelőség

A fenti funkciók és funkciók mellett, amelyek segítségével az alkalmazás megfelelhet a különböző biztonsági követelményeknek, a Azure SQL Database rendszeres naplózásban is részt vesz, és számos megfelelőségi szabványnak megfelelően lett hitelesítve. További információért tekintse meg a [Microsoft Azure Biztonsági](https://www.microsoft.com/trust-center/compliance/compliance-overview) és biztonsági központot, ahol megtalálja a megfelelőségi tanúsítványok SQL Database listáját.

## <a name="next-steps"></a>Következő lépések

- A bejelentkezések, felhasználói fiókok, adatbázis-szerepkörök és engedélyek használatával kapcsolatos további SQL Database és SQL Managed Instance lásd: Bejelentkezések és felhasználói fiókok [kezelése.](logins-create-manage.md)
- Az adatbázis-naplózással kapcsolatos további adatokért lásd: [naplózás.](../../azure-sql/database/auditing-overview.md)
- A fenyegetésészlelésről lásd: [fenyegetésészlelés.](threat-detection-configure.md)