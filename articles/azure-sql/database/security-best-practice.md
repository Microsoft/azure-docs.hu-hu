---
title: Forgatókönyv a közös biztonsági követelmények kezeléséhez
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Ez a cikk a Azure SQL Database és az Azure SQL felügyelt példányának általános biztonsági követelményeit és ajánlott eljárásait ismerteti.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 09/21/2020
ms.reviewer: ''
ms.openlocfilehash: 5d83f6585500316515139f937a56889dfc1f8fac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642711"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database-and-azure-sql-managed-instance"></a>A közös biztonsági követelmények kezelése a Azure SQL Database és az Azure SQL felügyelt példányával
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ez a cikk az általános biztonsági követelmények megoldásának ajánlott eljárásait ismerteti. Nem minden követelmény alkalmazható minden környezetre, és érdemes megtekinteni az adatbázissal és a biztonsági csapattal, hogy mely szolgáltatásokat kell megvalósítani.

## <a name="solving-common-security-requirements"></a>Gyakori biztonsági követelmények megoldása

Ez a dokumentum útmutatást nyújt a Azure SQL Database és az Azure SQL felügyelt példányát használó új vagy meglévő alkalmazások általános biztonsági követelményeinek megoldásához. Ez magas szintű biztonsági területek szerint van rendszerezve. Az adott veszélyforrások kezeléséhez tekintse meg a [gyakori biztonsági fenyegetéseket és a lehetséges kockázatcsökkentő](#common-security-threats-and-potential-mitigations) szakaszokat. Bár a bemutatott javaslatok némelyike az alkalmazások helyszíni rendszerből az Azure-ba történő áttelepítésekor alkalmazható, az áttelepítési forgatókönyvek nem a dokumentum középpontjában állnak.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Azure SQL Database az útmutatóban foglalt központi telepítési ajánlatok

- [Azure SQL Database](./index.yml): [önálló adatbázisok](single-database-overview.md) és [rugalmas készletek](elastic-pool-overview.md) a [kiszolgálókon](logical-servers.md)
- [Felügyelt Azure SQL-példány](../managed-instance/sql-managed-instance-paas-overview.md)

### <a name="deployment-offers-not-covered-in-this-guide"></a>Az útmutatóban nem szereplő központi telepítési ajánlatok

- Azure Synapse Analytics
- Azure SQL virtuális gépek (IaaS)
- SQL Server

### <a name="audience"></a>Célközönség

Az útmutató célközönségei a Azure SQL Database biztonságossá tételével kapcsolatos kérdések. Az ebben az ajánlott eljárásban érintett szerepkörök a következők:

- Biztonsági építészek
- Biztonsági vezetők
- Megfelelőségi tisztviselők
- Adatvédelmi tisztviselők
- Biztonsági mérnökök

### <a name="using-this-guide"></a><a id="using"></a> Az útmutató használata

A dokumentum célja, hogy a meglévő [Azure SQL Database biztonsági](security-overview.md) dokumentációhoz tartozó társa legyen.

Ha másként nincs megadva, javasoljuk, hogy kövesse az egyes szakaszokban felsorolt ajánlott eljárásokat a megfelelő cél vagy követelmény eléréséhez. Az egyes biztonsági megfelelőségi szabványoknak vagy ajánlott eljárásoknak megfelelően a követelmények vagy célok szakaszban felsorolt fontos szabályozási megfelelőségi szabályozások, ahol alkalmazhatók. Ezek a dokumentumban hivatkozott biztonsági szabványok és rendeletek:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SoC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): cm-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Access Control, kriptográfia
- [Microsoft Operational Security Assurance (OSA) eljárások](https://www.microsoft.com/securityengineering/osa/practices): gyakorlat #1 – 6 és #9
- [NIST speciális kiadvány 800-53 biztonsági vezérlők](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

Azt tervezzük, hogy továbbra is frissítjük az itt felsorolt ajánlásokat és ajánlott eljárásokat. Adja meg a dokumentum bemenetét vagy helyesbítéseit a cikk alján található **visszajelzési** hivatkozás használatával.

## <a name="authentication"></a>Hitelesítés

A hitelesítés az a folyamat, amellyel a felhasználó igazolni kívánja. A Azure SQL Database és az SQL felügyelt példánya két hitelesítési típust támogat:

- SQL-hitelesítés
- Hitelesítés Azure Active Directory-fiókkal

> [!NOTE]
> Azure Active Directory hitelesítés nem támogatott minden eszközön és külső féltől származó alkalmazásban.

### <a name="central-management-for-identities"></a>Identitások központi kezelése

A központi Identitáskezelés a következő előnyöket kínálja:

- Csoportfiókok kezelése és felhasználói engedélyek vezérlése a kiszolgálók, adatbázisok és felügyelt példányok közötti bejelentkezések megkettőzése nélkül.
- Egyszerűsített és rugalmas engedélyek kezelése.
- Alkalmazások felügyelete nagy léptékben.

**Megvalósítás**:

- Használja a Azure Active Directory (Azure AD) hitelesítést a központosított Identitáskezelés kezeléséhez.

**Ajánlott eljárások**:

- Hozzon létre egy Azure AD-bérlőt, és [hozzon létre felhasználókat](../../active-directory/fundamentals/add-users-azure-active-directory.md) az emberi felhasználók képviseletére, és hozzon létre [egyszerű szolgáltatásokat](../../active-directory/develop/app-objects-and-service-principals.md) az alkalmazások, szolgáltatások és automatizálási eszközök megjelenítéséhez. Az egyszerű szolgáltatások a Windows és a Linux szolgáltatás fiókjainak felelnek meg.

- Hozzáférési jogosultságok hozzárendelése az erőforrásokhoz az Azure AD résztvevői számára csoportos hozzárendelés használatával: Azure AD-csoportok létrehozása, hozzáférés biztosítása a csoportokhoz, és az egyes tagok hozzáadása a csoportokhoz. Az adatbázisban hozzon létre olyan tárolt adatbázis-felhasználókat, akik leképezik az Azure AD-csoportokat. Az adatbázison belüli engedélyek kiosztásához helyezze azokat a felhasználókat, akik az Azure AD-csoportokhoz vannak társítva az adatbázis-szerepkörökben a megfelelő engedélyekkel.
  - Tekintse meg a cikkeket, [konfigurálja és kezelje Azure Active Directory hitelesítést az SQL](authentication-aad-configure.md) -sel, és [használja az Azure ad-t az SQL-hitelesítéshez](authentication-aad-overview.md).
  > [!NOTE]
  > A felügyelt SQL-példányokban olyan bejelentkezéseket is létrehozhat, amelyek az Azure AD-rendszerbiztonsági tag számára a Master adatbázisban képezhetők le. Lásd: [create login (Transact-SQL)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

- Az Azure AD-csoportok használata leegyszerűsíti az engedélyek kezelését és a csoport tulajdonosát, és az erőforrás tulajdonosa hozzáadhat/eltávolíthat tagokat a csoporthoz/csoportba.

- Hozzon létre külön csoportot az Azure AD-rendszergazdák számára az egyes kiszolgálók vagy felügyelt példányok számára.

  - Tekintse meg a következő cikket: [Azure Active Directory-rendszergazda kiépítése a kiszolgálóhoz](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

- Az Azure AD-csoporttagság változásainak figyelése az Azure AD naplózási tevékenységeinek jelentéseivel.

- Felügyelt példány esetén külön lépésre van szükség az Azure AD-rendszergazda létrehozásához.
  - Tekintse meg a [felügyelt példányok Azure Active Directory rendszergazdájának kiépítése](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)című cikket.

> [!NOTE]
>
> - Az Azure AD-hitelesítés az Azure SQL-naplókban van rögzítve, de nem az Azure AD bejelentkezési naplóiban.
> - Az Azure-ban megadott Azure RBAC engedélyek nem vonatkoznak Azure SQL Database vagy SQL felügyelt példány engedélyeire. Ezeket az engedélyeket manuálisan kell létrehozni/leképezni a meglévő SQL-engedélyek használatával.
> - Az Azure AD-hitelesítésnek szüksége van az internethez vagy a felhasználó által megadott útvonalon (UDR) keresztül egy virtuális hálózatra.
> - Az Azure AD hozzáférési jogkivonat gyorsítótárazva van az ügyféloldali oldalon, és az élettartama a jogkivonat konfigurációjától függ. Tekintse meg a következő cikket: [konfigurálható jogkivonat élettartama Azure Active Directory](../../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Az Azure AD-hitelesítési problémák elhárításával kapcsolatos útmutatásért tekintse meg a következő blogot: az [Azure ad hibaelhárítása](https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991).

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD többtényezős hitelesítés

> Megemlítve: OSA Practice #2, ISO Access Control (AC)

Az Azure AD Multi-Factor Authentication további biztonságot nyújt azáltal, hogy egynél több hitelesítési módszert igényel.

**Megvalósítás**:

- Az Azure AD-ben való [multi-Factor Authentication engedélyezése](../../active-directory/authentication/concept-mfa-howitworks.md) feltételes hozzáférés használatával és interaktív hitelesítés használata.

- A másik lehetőség a teljes Azure AD-vagy AD-tartomány Multi-Factor Authenticationának engedélyezése.

**Ajánlott eljárások**:

- Aktiválja a feltételes hozzáférést az Azure AD-ben (prémium szintű előfizetés szükséges).
  - Tekintse meg a [feltételes hozzáférés az Azure ad-ben](../../active-directory/conditional-access/overview.md)című cikket.  

- Azure AD-csoport (ok) létrehozása és Multi-Factor Authentication szabályzat engedélyezése a kiválasztott csoportokhoz az Azure AD feltételes hozzáférés használatával.
  - Tekintse meg a [feltételes hozzáférés üzembe helyezésének megtervezése](../../active-directory/conditional-access/plan-conditional-access.md)című cikket.

- Multi-Factor Authentication engedélyezhető a teljes Azure AD-hez vagy az Azure AD-vel összevont teljes Active Directory.

- Az Azure AD interaktív hitelesítési mód használata a Azure SQL Database és az Azure SQL felügyelt példányai esetében, ha a jelszót interaktívan kérik, majd Multi-Factor Authentication:
  - Használjon univerzális hitelesítést a SSMS-ben. Tekintse meg a [többtényezős Azure ad-hitelesítés Azure SQL Database, SQL felügyelt példánnyal, az Azure szinapszis (SSMS-támogatás multi-Factor Authentication)](authentication-mfa-ssms-overview.md)című cikket.
  - SQL Server Data Tools (SSDT) által támogatott interaktív hitelesítés használata. Tekintse meg a [SQL Server Data Tools (SSDT) Azure Active Directory támogatását](/sql/ssdt/azure-active-directory?view=azuresqldb-current&preserve-view=true)ismertető cikket.
  - Használjon más, Multi-Factor Authentication támogató SQL-eszközöket.
    - A SSMS varázsló támogatja az adatbázis exportálását/kinyerését és üzembe helyezését  
    - [sqlpackage.exe](/sql/tools/sqlpackage): "/ua" kapcsoló
    - [Sqlcmd segédprogram](/sql/tools/sqlcmd-utility): Option-G (interaktív)
    - [BCP-segédprogram](/sql/tools/bcp-utility): Option-G (interaktív)

- Az alkalmazások implementálása Azure SQL Database vagy Azure SQL felügyelt példányhoz való kapcsolódáshoz Multi-Factor Authentication támogatással történő interaktív hitelesítéssel.
  - Tekintse meg a következő cikket: [Kapcsolódás a Azure SQL Databasehoz az Azure ad-multi-Factor Authentication](active-directory-interactive-connect-azure-sql-db.md).
  > [!NOTE]
  > Ez a hitelesítési mód felhasználói identitásokat igényel. Olyan esetekben, amikor olyan megbízható identitású modellt használ, amely az egyes Azure AD-felhasználók hitelesítésének megkerülését végzi (például az Azure-erőforrások felügyelt identitásának használatával), Multi-Factor Authentication nem alkalmazható.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>A jelszó-alapú hitelesítés használatának csökkentése a felhasználók számára

> Megemlítve: OSA Practice #4, ISO Access Control (AC)

A jelszó-alapú hitelesítési módszerek a hitelesítés gyengébb formája. A hitelesítő adatok biztonsága vagy tévedése nem lehetséges.

**Megvalósítás**:

- Használjon olyan integrált Azure AD-hitelesítést, amely kiküszöböli a jelszavak használatát.

**Ajánlott eljárások**:

- Egyszeri bejelentkezéses hitelesítés használata Windows rendszerbeli hitelesítő adatokkal. Összevonása a helyszíni AD-tartományt az Azure AD-vel, és integrált Windows-hitelesítést használ (tartományhoz csatlakoztatott gépekhez az Azure AD-vel).
  - Tekintse meg az [Azure ad integrált hitelesítésének SSMS-támogatását](authentication-aad-configure.md#active-directory-integrated-authentication)ismertető cikket.

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>A jelszó-alapú hitelesítés használatának csökkentése az alkalmazásoknál

> Megemlítve: OSA Practice #4, ISO Access Control (AC)

**Megvalósítás**:

- Az Azure által felügyelt identitás engedélyezése. Használhat integrált vagy tanúsítványalapú hitelesítést is.

**Ajánlott eljárások**:

- [Felügyelt identitások használata az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md).
  - [Rendszer által hozzárendelt felügyelt identitás](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)
  - [Felhasználó által hozzárendelt felügyelt identitás](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Felügyelt identitással rendelkező Azure App Service Azure SQL Database használata (kód módosítása nélkül)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Tanúsítvány alapú hitelesítés használata alkalmazásokhoz.
  - Tekintse meg ezt a [mintakód-mintát](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token).

- Az Azure AD-hitelesítést az integrált összevont tartományhoz és a tartományhoz csatlakoztatott számítógépekhez használhatja (lásd a fenti szakaszt).
  - Tekintse [meg az integrált hitelesítéshez használható minta alkalmazást](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Jelszavak és titkos kódok elleni védelem

Olyan esetekben, amikor a jelszavak nem elkerülhetők, győződjön meg róla, hogy biztonságosak.

**Megvalósítás**:

- A jelszavak és a titkos kódok tárolására Azure Key Vault használhatja. Ha szükséges, használja a Multi-Factor Authenticationt az Azure AD-felhasználók Azure SQL Databaseához.

**Ajánlott eljárások**:

- Ha nem sikerül elkerülni a jelszavakat vagy a titkos kulcsokat, a felhasználói jelszavakat és az alkalmazás titkos kulcsait a Azure Key Vault és Key Vault hozzáférési házirendeken keresztül kezelheti.

- A különböző alkalmazásfejlesztés-keretrendszerek olyan keretrendszer-specifikus mechanizmusokat is kínálnak, amelyek védik a titkokat az alkalmazásban. Például: [ASP.net Core alkalmazás](/aspnet/core/security/app-secrets?tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>SQL-hitelesítés használata örökölt alkalmazásokhoz

Az SQL-hitelesítés a felhasználó hitelesítésére utal Azure SQL Database vagy SQL felügyelt példányhoz való csatlakozáskor Felhasználónév és jelszó használatával. Az egyes kiszolgálókon vagy felügyelt példányokon, valamint az egyes adatbázisokban létrehozott felhasználókat is létre kell hozni.

**Megvalósítás**:

- SQL-hitelesítés használata.

**Ajánlott eljárások**:

- Kiszolgáló vagy példány rendszergazdájaként hozzon létre bejelentkezéseket és felhasználókat. Hacsak nem használ a jelszóval rendelkező tárolt adatbázis-felhasználókat, a rendszer az összes jelszót a Master adatbázisban tárolja.
  - Tekintse meg a következő cikket: az [adatbázis-hozzáférés szabályozása és megadása SQL Database, SQL felügyelt példányhoz és az Azure szinapszis Analytics szolgáltatáshoz](logins-create-manage.md).

## <a name="access-management"></a>Hozzáférés-kezelés

A hozzáférés-kezelés (más néven engedélyezés) a jogosult felhasználók hozzáférésének és jogosultságának vezérlése és kezelése Azure SQL Database vagy SQL felügyelt példányhoz.

### <a name="implement-principle-of-least-privilege"></a>A legalacsonyabb jogosultsági szint megvalósítási elve

> Megemlítve: FedRamp Controls AC-06, NIST: AC-6, OSA Practice #3

A minimális jogosultság elve szerint a felhasználóknak a feladataik elvégzéséhez a szükségesnél több jogosultsággal kell rendelkezniük. További információ: [elég adminisztráció](/powershell/scripting/learn/remoting/jea/overview).

**Megvalósítás**:

Csak a szükséges [engedélyek](/sql/relational-databases/security/permissions-database-engine) kiosztása a szükséges feladatok elvégzéséhez:

- SQL-adatbázisokban:
  - Használjon részletes engedélyeket és felhasználó által definiált adatbázis-szerepköröket (vagy kiszolgálói szerepköröket a felügyelt példányban):
    1. A szükséges szerepkörök létrehozása
       - [SZEREPKÖR LÉTREHOZÁSA](/sql/t-sql/statements/create-role-transact-sql)
       - [KISZOLGÁLÓI SZEREPKÖR LÉTREHOZÁSA](/sql/t-sql/statements/create-server-role-transact-sql)
    1. Szükséges felhasználók létrehozása
       - [FELHASZNÁLÓ LÉTREHOZÁSA](/sql/t-sql/statements/create-user-transact-sql)
    1. Felhasználók hozzáadása tagként a szerepkörökhöz
       - [SZEREPKÖR MÓDOSÍTÁSA](/sql/t-sql/statements/alter-role-transact-sql)
       - [A KISZOLGÁLÓI SZEREPKÖR MÓDOSÍTÁSA](/sql/t-sql/statements/alter-server-role-transact-sql)
    1. Ezután rendeljen engedélyeket a szerepkörökhöz.
       - [GRANT](/sql/t-sql/statements/grant-transact-sql)
  - Ügyeljen arra, hogy ne rendeljen hozzá felhasználókat felesleges szerepkörökhöz.

- Azure Resource Manager:
  - Ha elérhető vagy egyéni Azure-szerepköröket használ, és hozzárendeli a szükséges engedélyeket, használja a beépített szerepköröket.
    - [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md)
    - [Egyéni Azure-szerepkörök](../../role-based-access-control/custom-roles.md)

**Ajánlott eljárások**:

A következő ajánlott eljárások nem kötelezőek, de a biztonsági stratégia jobb kezelhetőségét és támogatását eredményezik:

- Ha lehetséges, kezdje a lehető legkevesebb engedély megadásával, és az engedélyek hozzáadásának megkezdése egyenként, ha valós szükség van rá (és indoklással) – az ellenkező megközelítés helyett: az engedélyek elvégzése lépésről lépésre.

- Ne rendeljen engedélyeket az egyes felhasználókhoz. A szerepköröket (adatbázis vagy kiszolgálói szerepkörök) következetesen használja helyette. A szerepkörök nagy mértékben segítik a jelentéskészítési és hibaelhárítási engedélyeket. (Az Azure RBAC csak szerepkörökön keresztül támogatja az engedélyek hozzárendelését.)

- Egyéni szerepköröket hozhat létre és használhat pontosan a szükséges engedélyekkel. A gyakorlatban használt tipikus szerepkörök:
  - Biztonsági telepítés
  - Rendszergazda
  - Fejlesztő
  - Támogatási munkatársak
  - Auditor
  - Automatizált folyamatok
  - Végfelhasználó
  
- A beépített szerepkörök csak akkor használhatók, ha a szerepkörök engedélyei pontosan egyeznek a felhasználóhoz szükséges engedélyekkel. A felhasználókat több szerepkörhöz is hozzárendelheti.

- Ne feledje, hogy az adatbázismotor engedélyei a következő hatókörökön belül is alkalmazhatók (minél kisebb a hatókör, annál kisebb a megadott engedélyek hatása):
  - Kiszolgáló (speciális szerepkörök a Master adatbázisban) az Azure-ban
  - Adatbázis
  - Séma
    - Az ajánlott eljárás a sémák használata az engedélyek adatbázison belüli megadásához. (lásd még: [séma-design: javaslatok a séma kialakításához a biztonság szem előtt](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)tartásával)
  - Objektum (tábla, nézet, eljárás stb.)

  > [!NOTE]
  > Az objektum szintjén nem ajánlott engedélyeket alkalmazni, mert ez a szint szükségtelen bonyolultságot okoz a teljes megvalósításban. Ha úgy dönt, hogy az objektum szintű engedélyeket használja, ezeket egyértelműen dokumentálni kell. Ugyanez vonatkozik az oszlop szintű engedélyekre is, amelyek ugyanezen okokból még kevésbé ajánlottak. Azt is vegye figyelembe, hogy alapértelmezés szerint a tábla szintű [Megtagadás](/sql/t-sql/statements/deny-object-permissions-transact-sql) nem bírálja felül az oszlop szintű engedélyeket. Ehhez aktiválni kell az [általános feltételek megfelelőségi kiszolgálójának konfigurációját](/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) .

- Végezzen rendszeres ellenőrzéseket a [sebezhetőségi felmérés (va)](/sql/relational-databases/security/sql-vulnerability-assessment) használatával a túl sok engedély teszteléséhez.

### <a name="implement-separation-of-duties"></a>A feladatok elkülönítésének megvalósítása

> Megemlítve: FedRamp: AC-04, NIST: AC-5, ISO: A. 6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

A feladatok elkülönítése – más néven a vámok elkülönítése – azt a követelményt írja le, hogy a bizalmas feladatokat több, különböző felhasználókhoz rendelt feladatba ossza fel. A feladatok elkülönítése segít megelőzni az adatok megsértését.

**Megvalósítás**:

- Azonosítsa a feladatok elkülönítésének szükséges szintjét. Angol nyelvű Példák:
  - Fejlesztési, tesztelési és éles környezetek között
  - Biztonság – a bizalmas feladatok és az adatbázis-adminisztrátor (DBA) felügyeleti szintű feladatai és fejlesztői feladatok.
    - Példák: auditor, biztonsági szabályzat létrehozása szerepköralapú biztonsághoz (RLS), SQL Database objektumok implementálása DDL-engedélyekkel.

- Azonosítsa a rendszerhez hozzáférő felhasználók (és automatizált folyamatok) átfogó hierarchiáját.

- Hozzon létre szerepköröket a szükséges felhasználói csoportoknak megfelelően, és rendeljen engedélyeket a szerepkörökhöz.
  - A Azure Portal vagy PowerShell-Automation használatával történő felügyeleti szintű feladatokhoz Azure-szerepköröket használjon. Keressen egy beépített szerepkört, amely megfelel a követelménynek, vagy hozzon létre egy egyéni Azure-szerepkört az elérhető engedélyek használatával.
  - Kiszolgálói szerepköröket hozhat létre a felügyelt példányok kiszolgáló szintű feladatai számára (új bejelentkezések és adatbázisok létrehozása).
  - Adatbázis-szintű feladatokhoz tartozó adatbázis-szerepkörök létrehozása.

- Bizonyos bizalmas feladatok esetében érdemes lehet olyan speciális tárolt eljárásokat létrehozni, amelyeket egy tanúsítvány írt alá a feladatok a felhasználók nevében történő végrehajtásához. A digitálisan aláírt tárolt eljárások egyik fontos előnye, hogy ha az eljárást megváltoztatják, a rendszer azonnal eltávolítja az eljárás korábbi verziójához megadott engedélyeket.
  - Példa: [oktatóanyag: tárolt eljárások aláírása tanúsítvánnyal](/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate)

- Transzparens adattitkosítás (TDE) implementálása az ügyfél által felügyelt kulcsokkal Azure Key Vault, hogy lehetővé tegye a feladatok elkülönítését az adatok tulajdonosa és a biztonsági tulajdonos között.
  - Tekintse meg az [Azure Storage-titkosításhoz az ügyfél által felügyelt kulcsok konfigurálása a Azure Portalről](../../storage/common/customer-managed-keys-configure-key-vault.md)című cikket.

- Annak biztosítása érdekében, hogy a DBA ne lásson olyan adatokat, amelyek nagyon kényesek, és továbbra is a DBA-feladatokat tudják használni, a szerepkörök elkülönítésével Always Encrypted.
  - Tekintse át a [Always encrypted kulcskezelő szolgáltatással](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), a [szerepkör-elkülönítéssel való központi](/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)telepítéssel, valamint az [oszlop főkulcsának rotációs szerepkör-elkülönítéssel](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation)kapcsolatos cikkeit.

- Azokban az esetekben, amikor a Always Encrypted használata nem valósítható meg, vagy legalábbis nem olyan jelentős költségek és erőfeszítések nélkül, amelyek akár a rendszer használhatatlanná tételét is lehetővé tehetik, a kompromisszumokat a kompenzáló vezérlők használatával, például:
  - Emberi beavatkozás folyamatokban.
  - Naplózási nyomvonalak – további információ a naplózásról: [kritikus fontosságú biztonsági események naplózása](#audit-critical-security-events).

**Ajánlott eljárások**:

- Győződjön meg arról, hogy a fejlesztési, tesztelési és éles környezetekben különböző fiókok vannak használatban. A különböző fiókok segítenek a tesztelési és éles rendszerek elkülönítésének betartásában.

- Ne rendeljen engedélyeket az egyes felhasználókhoz. A szerepköröket (adatbázis vagy kiszolgálói szerepkörök) következetesen használja helyette. A szerepkörök nagy mértékben segítik a jelentéskészítési és hibaelhárítási engedélyeket.

- Beépített szerepkörök használata, ha az engedélyek pontosan egyeznek a szükséges engedélyekkel – ha a több beépített szerepkörből álló engedélyek Uniója egy 100%-os egyeztetéshez vezet, több szerepkört is hozzárendelhet egyidejűleg.

- Felhasználó által definiált szerepkörök létrehozása és használata, ha a beépített szerepkörök túl sok engedélyt vagy nem megfelelő engedélyeket biztosítanak.

- A szerepkör-hozzárendelések ideiglenesen is megvalósíthatók, más néven a feladatok dinamikus elkülönítése (DSD), vagy a T-SQL SQL Agent-feladatok lépésein belül, vagy az Azure PIM Azure-szerepkörök használatával.

- Győződjön meg arról, hogy a Adattervezők nincs hozzáférése a titkosítási kulcsokhoz vagy a kulcstárolóhoz, és hogy a kulcsokhoz hozzáféréssel rendelkező biztonsági rendszergazdák nem férnek hozzá az adatbázishoz. Az [Extensible Key Management (EKM)](/sql/relational-databases/security/encryption/extensible-key-management-ekm) használata megkönnyíti ezt az elkülönítést. A [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használható a EKM megvalósításához.

- Mindig ellenőrizze, hogy van-e naplózási nyomvonal a biztonsággal kapcsolatos műveletekhez.

- Az Azure beépített szerepköreinek definícióját lekérdezve megtekintheti a használt engedélyeket, és létrehozhat egy egyéni szerepkört a részletek és a PowerShell használatával történő összesítések alapján.

- Mivel a db_owner adatbázis-szerepkör bármelyik tagja módosíthatja a biztonsági beállításokat, például a transzparens adattitkosítás (TDE), vagy módosíthatja az SLO-t, ezt a tagságot körültekintően kell megadnia. Azonban számos olyan feladatra van szükség, amely db_owner jogosultságokat igényel. Olyan feladat, mint például az adatbázis-beállítások módosítása. A naplózás kulcsfontosságú szerepet játszik bármely megoldásban.

- Nem lehet korlátozni egy db_owner engedélyeit, így megakadályozhatja, hogy egy rendszergazdai fiók megtekintse a felhasználói adatait. Ha egy adatbázisban nagyon bizalmas adatok találhatók, Always Encrypted segítségével biztonságosan megakadályozhatja db_owners vagy bármely más DBA megtekintését.

> [!NOTE]
> A feladatok elkülönítésének megvalósítása (SoD) a biztonsággal kapcsolatos vagy hibaelhárítási feladatokra is kihívást jelent. Más területek, például a fejlesztés és a végfelhasználói szerepkörök könnyebben elkülöníthető. A többségi megfelelőséggel kapcsolatos vezérlők lehetővé teszik alternatív vezérlési függvények használatát, például a naplózást, ha más megoldások nem praktikusak.

A SoD-ban mélyebbre szánt olvasóknak a következő erőforrásokat ajánlott használni:

- Azure SQL Database és SQL felügyelt példány esetén:  
  - [Adatbázis-hozzáférés szabályozása és biztosítása](logins-create-manage.md)
  - [Az alkalmazás fejlesztője feladatai elkülönítése](/previous-versions/sql/sql-server-2008/cc974525(v=sql.100))
  - [A feladatok elkülönítése](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Tárolt eljárások aláírása](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Azure Resource Management esetén:
  - [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md)
  - [Egyéni Azure-szerepkörök](../../role-based-access-control/custom-roles.md)
  - [A Azure AD Privileged Identity Management használata emelt szintű hozzáféréshez](https://www.microsoft.com/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Szabványos kód-felülvizsgálatok végrehajtása

> Megemlítve: PCI: 6.3.2, SOC: SDL-3

A feladatok elkülönítése nem korlátozódik az adatbázisban lévő adatokra, de tartalmazza az alkalmazás kódját. A kártékony kódok megkerülhetik a biztonsági vezérlőket. Az egyéni kód éles környezetben való üzembe helyezése előtt elengedhetetlen, hogy áttekintse az üzembe helyezett környezeteket.

**Megvalósítás**:

- Használjon olyan adatbázis-eszközt, mint a verziókövetés használatát támogató Azure Data Studio.

- Hozzon létre egy elkülönített programkód-telepítési folyamatot.

- A fő ág véglegesítése előtt egy személynek (a kód szerzőjének kivételével) meg kell vizsgálnia a jogosultsági szint lehetséges kiterjesztését, valamint a rosszindulatú adatmódosításokat a csalások és a tisztességtelen hozzáférés elleni védelem érdekében. Ez a forrás-ellenőrzési mechanizmusok használatával végezhető el.

**Ajánlott eljárások**:

- Szabványosítás: segítséget nyújt egy olyan szabványos eljárás megvalósításához, amelyet a kód frissítéseihez kell követni.

- A sebezhetőségi felmérés olyan szabályokat tartalmaz, amelyek megkeresik a túlzott engedélyeket, a régi titkosítási algoritmusok használatát, valamint az adatbázis-sémán belüli egyéb biztonsági problémákat.

- A további ellenőrzéseket a fejlett veszélyforrások elleni védelem segítségével végezheti el egy MINŐSÉGBIZTOSÍTÁSi vagy tesztelési környezetben, amely az SQL-injektálás miatt sebezhető kódot keres.

- Példák a következőkre:
  - A felhasználó létrehozása vagy a biztonsági beállítások módosítása egy automatikus SQL-kód – frissítés telepítésen belülről.
  - Egy tárolt eljárás, amely a megadott paraméterektől függően a cellában lévő pénzügyi értéket nem megfelelő módon frissíti.

- Győződjön meg arról, hogy a felülvizsgálatot végző személy nem a kezdeményező kód szerzője, és a Code-Reviews és a Secure kódolással jól tájékozott.

- Győződjön meg arról, hogy a Code-Changes minden forrását ismeri. A kód lehet T-SQL-szkriptekben. Az ad-hoc parancsok hajthatók végre, illetve a nézetek, függvények, eseményindítók és tárolt eljárások formájában is üzembe helyezhetők. Ez az SQL Agent-feladatdefiníciók (lépések) része lehet. A SSIS-csomagokon, Azure Data Factoryeken és más szolgáltatásokon belül is végrehajtható.

## <a name="data-protection"></a>Adatvédelem

Az adatvédelem olyan képességek összessége, amelyekkel a titkosítás vagy a elhomályosítás révén biztosítható a fontos információk védelme.

> [!NOTE]
> A Microsoft tanúsítja Azure SQL Database és az SQL felügyelt példányát, mint az 1. szintű FIPS 140-2 szabványnak megfelelőt. Ez a FIPS 140-2 1. szintű elfogadható algoritmusok és a FIPS 140-2 1. szintű hitelesített példányának ellenőrzése után történik, beleértve a szükséges kulcs hosszával, a kulcs-kezeléssel, a kulcs generálásával és a kulcsos tárolással való konzisztencia mértékét. Ez az igazolás arra szolgál, hogy ügyfeleink válaszolják meg a FIPS 140-2 1. szintű, ellenőrzött példányok használatát az adatfeldolgozásban, illetve a rendszerek vagy alkalmazások továbbítása során. A fenti nyilatkozatban a "FIPS 140-2 1. szintnek megfelelő" és "FIPS 140-2 1. szintű megfelelőség" kifejezést adjuk meg, amely igazolja, hogy az Amerikai Egyesült Államok és a kanadai kormány a különböző "FIPS 140-2 1. szintű" minősítéssel való használatra való alkalmazhatóságát mutatja be.

### <a name="encrypt-data-in-transit"></a>Adatforgalom titkosítása

> Megemlítve: OSA Practice #6, ISO Control Family: titkosítás

Védi az adatait, miközben az ügyfél és a kiszolgáló közötti adatátvitelt. Tekintse meg a [hálózati biztonság](#network-security)című témakört.

### <a name="encrypt-data-at-rest"></a>Inaktív adatok titkosítása

> Megemlítve: OSA Practice #6, ISO Control Family: titkosítás

A inaktív adatok titkosítása az adatok titkosítási védelme, ha az adatbázis-, napló-és biztonságimásolat-fájlokban is megmarad.

**Megvalósítás**:

- A szolgáltatás által felügyelt kulcsokkal rendelkező [transzparens adatbázis-titkosítás (TDE)](transparent-data-encryption-tde-overview.md) alapértelmezés szerint engedélyezve van a Azure SQL Database és az SQL felügyelt példányának 2017 után létrehozott adatbázisok esetében.
- Felügyelt példány esetén, ha az adatbázis egy helyszíni kiszolgáló használatával történő visszaállítási műveletből jön létre, a rendszer az eredeti adatbázis TDE-beállítását fogja tiszteletben venni. Ha az eredeti adatbázis nem rendelkezik TDE-engedélyezéssel, javasoljuk, hogy a felügyelt példányhoz manuálisan be kell kapcsolni a TDE.

**Ajánlott eljárások**:

- Ne tárolja a főadatbázisban titkosítást igénylő adatok tárolását. A Master adatbázis nem titkosítható a TDE.

- Az ügyfél által felügyelt kulcsok használata Azure Key Vault, ha nagyobb átláthatóságra és részletes szabályozásra van szükség a TDE-védelem felett. Azure Key Vault lehetővé teszi az engedélyek visszavonását, hogy az adatbázis nem érhető el. Központilag kezelheti a TDE-védőket más kulcsokkal együtt, vagy elforgathatja a TDE-védőt a saját ütemtervében Azure Key Vault használatával.

- Ha Azure Key Vault használja az ügyfél által felügyelt kulcsokat, kövesse a TDE és a [Azure Key Vault konfigurálására vonatkozó útmutatót](transparent-data-encryption-byok-overview.md#recommendations-when-configuring-akv) , valamint a [geo-DR és a Azure Key Vault konfigurálásának módját](transparent-data-encryption-byok-overview.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Magas jogosultsági szintű, jogosulatlan felhasználók által használt bizalmas adatok védelme

A használatban lévő adatmennyiség az adatbázis-rendszer memóriájában tárolt, az SQL-lekérdezések végrehajtása során tárolt adatmennyiség. Ha az adatbázis bizalmas adatokat tárol, előfordulhat, hogy a szervezetnek meg kell győződnie arról, hogy a magas jogosultságú felhasználók nem tudják megtekinteni a bizalmas adatokat az adatbázisban. A magas szintű jogosultsággal rendelkező felhasználóknak, például a Microsoft-kezelőknek vagy a szervezeten belüli Adattervezők képesnek kell lenniük az adatbázis kezelésére, de nem tekinthetik meg és nem hogy kiszivárogtassák bizalmas adatokat az SQL-folyamat memóriájában vagy az adatbázis lekérdezésével.

Azok a házirendek, amelyek meghatározzák, hogy mely adatok érzékenyek, és hogy a bizalmas adatokat titkosítva kell-e a memóriában, és nem hozzáférhetők a rendszergazdák számára az egyszerű szöveges szolgáltatásban, a vállalatra és a megfelelőségi előírásokra vonatkozó szabályokra van szükség. Tekintse meg a kapcsolódó követelményt: [bizalmas adatok azonosítása és címkézése](#identify-and-tag-sensitive-data).

**Megvalósítás**:

- A [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) használatával biztosíthatja, hogy a bizalmas adatok ne legyenek elérhetők Azure SQL Database vagy SQL felügyelt példányban, még a memóriában/használatban. Always Encrypted védi az adatbázis-rendszergazdák (Adattervezők-EK) és a Felhőbeli rendszergazdák (vagy a magas jogosultságú, de jogosulatlan felhasználók megszemélyesítését végző rossz szereplők) adatait, és így jobban szabályozhatja, hogy ki férhet hozzá az adataihoz.

**Ajánlott eljárások**:

- A Always Encrypted nem helyettesíti az inaktív adatok titkosítását (TDE) vagy átvitelét (SSL/TLS). A teljesítmény és a funkciók hatásának csökkentése érdekében a Always Encrypted nem használhatók a nem bizalmas adatokhoz. A Always Encrypted a TDE és a Transport Layer Security (TLS) együttes használata ajánlott a REST-alapú, a tranzit-és a használatban lévő adatok átfogó védelméhez.

- A Always Encrypted éles adatbázisban való telepítése előtt mérje fel, hogy milyen hatással van az azonosított bizalmas adatok oszlopainak titkosítására. A Always Encrypted általában csökkenti a titkosított oszlopokra vonatkozó lekérdezések funkcióit, és más korlátozásokkal rendelkezik, amelyek a [Always encrypted funkció részletei](/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details)között szerepelnek. Ezért előfordulhat, hogy az alkalmazás újratervezéséhez újra kell telepítenie a funkciót, a lekérdezés nem támogatott, az ügyfél oldalán vagy az adatbázis-séma újrabontásakor, beleértve a tárolt eljárások, függvények, nézetek és triggerek definícióit. Előfordulhat, hogy a meglévő alkalmazások nem működnek titkosított oszlopokkal, ha nem tartják be a Always Encrypted korlátozásait és korlátozásait. Noha a Microsoft eszközeinek, termékeinek és szolgáltatásainak Always Encryptedt támogató ökoszisztémája egyre növekszik, többek között nem működnek titkosított oszlopokkal. Az oszlopok titkosítása hatással lehet a lekérdezés teljesítményére is, a számítási feladatok jellemzőitől függően.

- Always Encrypted kulcsok kezelése szerepkör-elkülönítéssel, ha Always Encryptedt használ a rosszindulatú Adattervezők származó adatok védelme érdekében. A szerepkörök elkülönítésével a biztonsági rendszergazda létrehozza a fizikai kulcsokat. A DBA létrehozza az oszlop főkulcsát és az oszlop titkosítási kulcsának metaadat-objektumait, amelyek az adatbázis fizikai kulcsait ismertetik. A folyamat során a biztonsági rendszergazdának nem kell hozzáférése az adatbázishoz, és a DBA-nek nem kell hozzáférése a fizikai kulcsokhoz az egyszerű szöveges verzióban.
  - A részletekért tekintse meg a [kulcsok kezelése a szerepkörök elkülönítésével](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) című cikket.

- Az egyszerű felügyelet érdekében az oszlopok főkulcsait Azure Key Vault tárolja. Kerülje a Windows-tanúsítványtároló használatát (és az elosztott kulcstároló-megoldásokat, amelyek nem rendelkeznek a kulcsfontosságú felügyeleti megoldásokkal).

- Alaposan gondolja át a több kulcs (oszlop főkulcsa vagy az oszlop titkosítási kulcsa) használatának kompromisszumait. Csökkentse a kulcsok számát a legfontosabb felügyeleti díjak csökkentése érdekében. Az adatbázisokban az egyik főkulcs és egy oszlop titkosítási kulcsa általában elegendő az állandó állapotú környezetekben (nem a kulcs forgásának közepéhez). További kulcsokra lehet szükség, ha különböző felhasználói csoportokkal rendelkezik, amelyek mindegyike különböző kulcsokat használ, és különböző adatokhoz fér hozzá.  

- A megfelelőségi követelmények alapján forgassa el az oszlop főkulcsait. Ha az oszlop titkosítási kulcsait is el kell forgatni, érdemes lehet online titkosítást használni az alkalmazások leállásának minimalizálásához.
  - Tekintse meg a cikk, a [teljesítmény és a rendelkezésre állás szempontjait](/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations).

- Használjon determinisztikus titkosítást, ha az adatok számításait (egyenlőségét) támogatni kell. Egyéb esetben véletlenszerű titkosítást használjon. Kerülje a determinisztikus titkosítás használatát az alacsony entrópia-adatkészletekhez, vagy nyilvánosan ismert eloszlású adatkészletekhez.

- Ha azt szeretné, hogy az Ön beleegyezike nélkül a harmadik féltől származó adatokhoz hozzáférjen, akkor győződjön meg arról, hogy minden olyan alkalmazás és eszköz, amely nem fér hozzá a kulcsokhoz és az adatokhoz a Microsoft Azure felhőn kívülről futtatva. A kulcsokhoz való hozzáférés nélkül a harmadik fél semmilyen módon nem tudja visszafejteni az adattitkosítást, kivéve, ha megkerüli a titkosítást.

- Always Encrypted nem támogatja a kulcsok (és a védett adat) ideiglenes hozzáférésének megadását. Ha például a kulcsokat egy DBA-vel kell megosztania, hogy a DBA bizonyos tisztítási műveleteket végezzen a bizalmas és titkosított adatokon. Az egyetlen módja annak, hogy a megbízhatóság visszavonja a DBA-ből származó adatokhoz való hozzáférést, az oszlop titkosítási kulcsainak és az adatok védelmét szolgáló oszlop főkulcsának elforgatásával, ami költséges művelet.

- A titkosított oszlopokban lévő egyszerű szöveges értékek eléréséhez a felhasználónak hozzáféréssel kell rendelkeznie az oszlopokat védő főkulcshoz (CMK), amely a CMK tároló kulcstárolóban van konfigurálva. A felhasználónak Emellett meg kell adnia **bármely oszlop FŐkulcsának definícióját** , és **meg kell TEKINTENIE az összes oszlop titkosítási kulcsának definícióját** tartalmazó adatbázis engedélyeit.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Az alkalmazás felhasználói bizalmas adatokhoz való hozzáférésének szabályozása titkosítás útján

A titkosítás használatával biztosítható, hogy csak a titkosítási kulcsokhoz hozzáférő alkalmazás-felhasználók tekinthetik meg vagy frissíthetik az adatfájlokat.

**Megvalósítás**:

- Használjon cella szintű titkosítást (CLE). A részletekért tekintse meg a következő cikket: [adatok adatoszlopának titkosítása](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) .
- Használja Always Encrypted, de vegye figyelembe a korlátozását. A korlátozások alább láthatók.

**Ajánlott eljárások**

A CLE használatakor:

- A kulcsokhoz való hozzáférés szabályozása SQL-engedélyekkel és-szerepkörökkel.

- Használjon AES-t (AES 256 ajánlott) az adattitkosításhoz. Az algoritmusok, például az RC4, a DES és a TripleDES elavultak, és az ismert biztonsági rések miatt nem használhatók fel.

- A 3DES használatának elkerüléséhez használjon aszimmetrikus kulcsokkal vagy tanúsítványokkal (nem jelszavakkal) rendelkező szimmetrikus kulcsokat.

- Ha Exportálás/Importálás (bacpac-fájlok) használatával Cell-Level titkosítást használ az adatbázis áttelepítéséhez.
  - Tekintse meg a következő cikket: [javaslatok a cella szintű titkosítás használatára a Azure SQL Databaseban](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database) , hogy miként lehet megakadályozni a kulcsok elindítását az adatáttelepítés során, valamint az ajánlott eljárásokkal kapcsolatos további útmutatást.

Ne feledje, hogy a Always Encrypted elsődlegesen a Azure SQL Database (Cloud Operators, Adattervezők) magas jogosultságú felhasználóitól származó bizalmas adatok védelme érdekében van kialakítva – lásd: [bizalmas adatok védelme magas jogosultságú, jogosulatlan felhasználóktól](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Az alkalmazás felhasználóitól származó adatok védelme Always Encrypted használatakor vegye figyelembe az alábbi kihívásokat:

- Alapértelmezés szerint az összes Always Encrypted támogató Microsoft-ügyfél-illesztőprogram az oszlop titkosítási kulcsainak globális (egy-egy/alkalmazás-) gyorsítótárát tartja karban. Ha az ügyfél-illesztőprogram egy egyszerű szöveges oszlop titkosítási kulcsát szerzi be, lépjen kapcsolatba egy, az oszlop főkulcsát tároló kulcstárolóval, a rendszer gyorsítótárazza az egyszerű szöveges oszlop titkosítási kulcsát. Így a többfelhasználós alkalmazások felhasználóitól származó adatok elkülönítését teszik lehetővé. Ha az alkalmazás megszemélyesíti a végfelhasználókat a kulcstárolókkal való interakció során (például Azure Key Vault), miután a felhasználó lekérdezése egy oszlopos titkosítási kulccsal tölti fel a gyorsítótárat, egy későbbi, ugyanazt a kulcsot igénylő lekérdezést, amelyet egy másik felhasználó aktivál, a gyorsítótárazott kulcsot fogja használni. Az illesztőprogram nem hívja meg a kulcstárolót, és nem fogja tudni, hogy a második felhasználónak van-e engedélye az oszlop titkosítási kulcsának elérésére. Ennek eredményeképpen a felhasználó akkor is láthatja a titkosított információt, ha a felhasználó nem rendelkezik hozzáféréssel a kulcsokhoz. A felhasználók egy többfelhasználós alkalmazáson belüli elkülönítésének eléréséhez letilthatja az oszlop titkosítási kulcsának gyorsítótárazását. A gyorsítótárazás letiltása további teljesítménybeli terhelést eredményez, mivel az illesztőprogramnak kapcsolatba kell lépnie a kulcstárolóval az egyes adattitkosítási vagy visszafejtési műveletekhez.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Az alkalmazások felhasználói általi jogosulatlan megtekintésének védelme az adatformátum megőrzése mellett

Egy másik módszer, amellyel megakadályozható, hogy a jogosulatlan felhasználók megtekintsék az adatmegjelenítést, és az adattípusok és a formátumok megtartásával megőrizze az adattípusokat és a formátumokat, hogy a felhasználói alkalmazások továbbra is képesek legyenek kezelni

**Megvalósítás**:

- A [dinamikus Adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking) használatával elhomályosíthatja a táblák oszlopait.

> [!NOTE]
> A Always Encrypted dinamikus adatmaszkolással nem működik. Egyazon oszlopot nem lehet titkosítani és eltakarni, ami azt jelenti, hogy a használatban lévő adatok védelméhez és a dinamikus adatmaszkoláson keresztül az alkalmazás felhasználóinak az adatok maszkolására van szükség.

**Ajánlott eljárások**:

> [!NOTE]
> A dinamikus adatmaszkolás nem használható a magas jogosultságú felhasználóktól származó adatok védelme érdekében. A maszkolási házirendek nem vonatkoznak olyan felhasználókra, akik rendszergazdai hozzáféréssel rendelkeznek, például db_owner.

- Ne engedélyezze az alkalmazások felhasználóinak alkalmi lekérdezések futtatását (mivel lehetséges, hogy a dinamikus adatmaszkolás megkerülheti őket).  
  - A részletekért tekintse meg a következő cikket: a [maszkolás kihagyása a következtetések és a találgatásos technikák használatával](/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) .  

- A megfelelő hozzáférés-vezérlési házirend (SQL-engedélyek, szerepkörök, RLS) használatával korlátozza a felhasználók engedélyeit a maszkolt oszlopokban lévő frissítések biztosításához. Egy oszlophoz tartozó maszk létrehozása nem akadályozza meg az adott oszlop frissítését. Azok a felhasználók, akik maszkolt adattárolást kapnak a maszkolt oszlop lekérdezése során, frissíthetik az adatkérést, ha írási engedéllyel rendelkeznek.

- A dinamikus adatmaszkolás nem őrzi meg a maszkolt értékek statisztikai tulajdonságait. Ez befolyásolhatja a lekérdezési eredményeket (például a szűrési predikátumokat vagy a maszkolt adategyesítéseket tartalmazó lekérdezéseket).

## <a name="network-security"></a>Hálózati biztonság

A hálózati biztonság a hozzáférés-vezérlésre és az ajánlott eljárásokra hivatkozik, hogy az adatátvitelt az Azure SQL Databaseba.

### <a name="configure-my-client-to-connect-securely-to-sql-databasesql-managed-instance"></a>Az ügyfél konfigurálása a biztonságos SQL Database/SQL felügyelt példányhoz való kapcsolódáshoz

Ajánlott eljárások az ügyfélszámítógépek és alkalmazások ismert biztonsági rések (például a régebbi TLS-protokollok és a titkosítási csomagok használatával) a Azure SQL Database és az SQL felügyelt példányhoz való csatlakozásának megakadályozására.

**Megvalósítás**:

- Győződjön meg arról, hogy az Azure SQL Database és az SQL felügyelt példányához csatlakozó ügyfélszámítógépek  [Transport Layer Security (TLS) protokollt](security-overview.md#transport-layer-security-encryption-in-transit)használnak.

**Ajánlott eljárások**:

- Az összes alkalmazás és eszköz konfigurálása a SQL Databasehoz való kapcsolódáshoz titkosítás engedélyezve
  - Encrypt = on, TrustServerCertificate = off (vagy nem Microsoft-illesztőprogramokkal egyenértékű).

- Ha az alkalmazás olyan illesztőprogramot használ, amely nem támogatja a TLS protokollt, vagy a TLS egy régebbi verzióját támogatja, cserélje le az illesztőprogramot, ha lehetséges. Ha nem lehetséges, gondosan értékelje ki a biztonsági kockázatokat.

- Csökkentse a támadási vektorokat az SSL 2,0, az SSL 3,0, a TLS 1,0 és a TLS 1,1 biztonsági rések révén, ha letiltja azokat a Azure SQL Database [Transport Layer Security (TLS) beállításjegyzék-beállításokhoz](/windows-server/security/tls/tls-registry-settings#tls-10)csatlakozó ügyfélszámítógépeken.

- Győződjön meg arról, hogy az ügyfélen elérhető titkosítási csomagok találhatók a [TLS/SSL (Schannel SSP) titkosítási csomagjaiban](/windows/desktop/SecAuthN/cipher-suites-in-schannel). Pontosabban tiltsa le a 3DES-t a [TLS titkosítási csomag megrendelésének konfigurálásával](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order).

- Azure SQL Database és SQL felügyelt példány esetén a titkosítást a proxy és az átirányítás típusú kapcsolatok esetében is érvényesíti a rendszer. Az Azure SQL felügyelt példányai esetében használja a **proxy** kapcsolattípus (alapértelmezett) értéket, mivel ez kikényszeríti a titkosítást a kiszolgáló oldaláról. Az **átirányítási** kapcsolat típusa jelenleg nem támogatja a titkosítás kényszerítését, és csak magánhálózati IP-kapcsolatokon érhető el.

- További információ: [Azure SQL Database kapcsolati architektúra – kapcsolati házirend](connectivity-architecture.md#connection-policy).

### <a name="minimize-attack-surface"></a>A támadási felület csökkentése

A rosszindulatú felhasználók által megtámadható szolgáltatások számának csökkentése. A Azure SQL Database hálózati hozzáférés-vezérlésének implementálása.

> Megemlítve: OSA Practice #5

**Megvalósítás**:

SQL Database:

- Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése a kiszolgáló szintjén
- VNet-szolgáltatási végpontok és VNet-tűzfalszabályok használata.
- Használjon privát hivatkozást (előzetes verzió).

A felügyelt SQL-példányban:

- Kövesse a [hálózati követelmények](../managed-instance/connectivity-architecture-overview.md#network-requirements)című témakör útmutatását.

**Ajánlott eljárások**:

- A Azure SQL Database és az SQL felügyelt példány hozzáférésének korlátozása egy privát végponthoz való csatlakozással (például privát adatútvonal használatával):
  - A külső hozzáférés megakadályozása érdekében a felügyelt példányok elkülöníthetők egy virtuális hálózaton belül. Az ugyanabban a régióban található azonos vagy egyenrangú virtuális hálózatban lévő alkalmazások és eszközök közvetlenül hozzáférhetnek. A különböző régiókban található alkalmazások és eszközök virtuális hálózat – virtuális hálózati kapcsolat vagy ExpressRoute áramköri kapcsolatok használatával hozhatnak létre kapcsolatot. Az ügyfélnek hálózati biztonsági csoportokat (NSG) kell használnia az 1433-as porton keresztüli hozzáférés korlátozásához a felügyelt példányokhoz hozzáférést igénylő erőforrásokhoz.
  - SQL Database esetén használja a [privát hivatkozás](../../private-link/private-endpoint-overview.md) funkciót, amely dedikált magánhálózati IP-címet biztosít a kiszolgáló számára a virtuális hálózaton belül. Virtuális hálózati szolgáltatás- [végpontokat is használhat virtuális hálózati tűzfalszabályok](vnet-service-endpoint-rule-overview.md) használatával a kiszolgálókhoz való hozzáférés korlátozására.
  - A mobil felhasználóknak pont – hely típusú VPN-kapcsolatokat kell használniuk az adatútvonalon való csatlakozáshoz.
  - A helyszíni hálózathoz csatlakozó felhasználóknak helyek közötti VPN-kapcsolatot vagy ExpressRoute kell használniuk az adatútvonalon való csatlakozáshoz.

- Az Azure SQL Database és az SQL felügyelt példányát nyilvános végponthoz való csatlakozással érheti el (például egy nyilvános adatútvonal használatával). A következő ajánlott eljárásokat kell figyelembe venni:
  - SQL Database-kiszolgáló esetén az [IP-tűzfalszabályok](firewall-configure.md) használatával korlátozza a hozzáférést csak a jogosult IP-címekhez.
  - SQL felügyelt példány esetén használjon hálózati biztonsági csoportokat (NSG) a 3342-as porton keresztüli hozzáférés korlátozásához a szükséges erőforrásokhoz. További információ: [felügyelt példány biztonságos használata nyilvános végpontokkal](../managed-instance/public-endpoint-overview.md).

> [!NOTE]
> Az SQL felügyelt példány nyilvános végpontja alapértelmezés szerint nincs engedélyezve, és explicit módon engedélyezve kell lennie. Ha a vállalati házirend nem engedélyezi a nyilvános végpontok használatát, a [Azure Policy](../../governance/policy/overview.md) használatával megakadályozhatja, hogy az első helyen engedélyezze a nyilvános végpontokat.

- Azure hálózati összetevők beállítása:
  - [A hálózati biztonságra vonatkozó Azure ajánlott eljárások](../../security/fundamentals/network-best-practices.md)követése.
  - Tervezze meg Virtual Network konfigurációt az [Azure Virtual Network gyakori kérdések (GYIK)](../../virtual-network/virtual-networks-faq.md) és a terv című szakaszban ismertetett ajánlott eljárások alapján.
  - A virtuális hálózatokat több alhálózatra is kioszthatja, és erőforrásokat rendelhet ugyanahhoz az alhálózathoz (például előtér-és háttér-erőforrásokhoz) hasonló szerepkörhöz.
  - Használjon [hálózati biztonsági csoportokat (NSG)](../../virtual-network/network-security-groups-overview.md) az alhálózatok közötti adatforgalom vezérléséhez az Azure-beli virtuális hálózat határain belül.
  - Az előfizetéshez tartozó [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) engedélyezése a bejövő és kimenő hálózati forgalom figyelésére.

### <a name="configure-power-bi-for-secure-connections-to-sql-databasesql-managed-instance"></a>Power BI konfigurálása a SQL Database/SQL felügyelt példány biztonságos kapcsolataihoz

**Ajánlott eljárások**:

- Power BI Desktop esetén, amikor csak lehet, használja a személyes adatelérési utat.

- Győződjön meg arról, hogy a Power BI Desktop a TLS 1.2-es verzióval csatlakozik, ha a beállításkulcsot [Transport Layer Security (TLS) beállításjegyzék-](/windows-server/security/tls/tls-registry-settings) beállítások szerint állítja be az ügyfélszámítógépen.

- Az adott felhasználók adathozzáférésének korlátozása [a soros szintű biztonság (RLS) és a Power bi](/power-bi/service-admin-rls)használatával.

- Power BI szolgáltatás esetén használja a helyszíni [adatátjárót](/power-bi/service-gateway-onprem), figyelembe véve a [korlátozásokat és a szempontokat](/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-sql-databasesql-managed-instance"></a>App Service konfigurálása a SQL Database/SQL felügyelt példány biztonságos kapcsolataihoz

**Ajánlott eljárások**:

- Egy egyszerű webalkalmazáshoz a nyilvános végponthoz való csatlakozáshoz az **Azure-szolgáltatások engedélyezése** beállítást kell beállítani.

- Az [alkalmazás integrálása Azure-Virtual Network](../../app-service/web-sites-integrate-with-vnet.md) a felügyelt példányokhoz való magánhálózati adatelérési utakhoz való kapcsolódáshoz. Igény szerint egy webalkalmazást is üzembe helyezhet [app Service környezetekkel](../../app-service/environment/intro.md).

- A [virtuális hálózati szolgáltatás-végpontok és](vnet-service-endpoint-rule-overview.md) a virtuális hálózati tűzfalszabályok használatával egy adott virtuális hálózat és alhálózat elérésének korlátozására használhat olyan webalkalmazást, amely vagy a virtuális hálózat integrált webalkalmazása SQL Database-adatbázishoz csatlakozik. Ezután állítsa be az Azure-szolgáltatások kikapcsolásának **engedélyezése lehetőséget** . Az SQL felügyelt példányában található felügyelt példányhoz is csatlakozhat egy privát adatútvonalon keresztül.  

- Győződjön meg arról, hogy a webalkalmazás konfigurálva van a cikkben, [ajánlott eljárások a platform szolgáltatásként (Péter) webes és mobil alkalmazások biztonságossá tételéhez Azure app Service használatával](../../security/fundamentals/paas-applications-using-app-services.md).

- Telepítse a [webalkalmazási tűzfalat (WAF)](../../web-application-firewall/ag/ag-overview.md) , hogy megvédje a webalkalmazást a gyakori biztonsági rések és sebezhetőségek ellen.

### <a name="configure-azure-virtual-machine-hosting-for-secure-connections-to-sql-databasesql-managed-instance"></a>Azure-beli virtuális gépek üzemeltetésének konfigurálása biztonságos kapcsolatokhoz SQL Database/SQL felügyelt példányhoz

**Ajánlott eljárások**:

- Az engedélyezési és megtagadási szabályok kombinációját használhatja az Azure-beli virtuális gépek NSG annak szabályozására, hogy mely régiók érhetők el a virtuális gépről.

- Győződjön meg arról, hogy a virtuális gép az Azure-ban a IaaS számítási feladatokhoz [szükséges biztonsági eljárások](../../security/fundamentals/iaas.md)alapján van konfigurálva.

- Győződjön meg arról, hogy az összes virtuális gép társítva van egy adott virtuális hálózathoz és alhálózathoz.

- Ellenőrizze, hogy szükség van-e az alapértelmezett 0.0.0.0/Internet útvonalra a [kényszerített bújtatással kapcsolatos](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)útmutatás alapján.
  - Ha igen – például az előtér-alhálózat –, akkor tartsa meg az alapértelmezett útvonalat.
  - Ha nem – például a középső réteg vagy a háttérbeli alhálózat – lehetővé teszi a kényszerített bújtatást, hogy az interneten keresztüli forgalom ne haladjon át a helyszínen (a. k. egy telephelyen).

- Nem [kötelező alapértelmezett útvonalakat](../../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) implementálhat, ha a társ-vagy a helyszíni hálózathoz csatlakozik.

- A [felhasználó által megadott útvonalak](../../virtual-network/virtual-networks-udr-overview.md#user-defined) implementálása, ha a virtuális hálózatban lévő összes forgalmat egy hálózati virtuális készülékre kell elküldeni a csomagok vizsgálatához.

- [Virtuális hálózati szolgáltatás-végpontok](vnet-service-endpoint-rule-overview.md) használatával biztonságos hozzáférést biztosíthat a Pásti-szolgáltatásokhoz, például az Azure Storage-hoz az Azure gerinc hálózaton keresztül.

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelem

Az elosztott szolgáltatásmegtagadási (DDoS) támadások arra irányulnak, hogy a rosszindulatú felhasználók elküldjék a hálózati forgalom Azure SQL Databaseét az Azure-infrastruktúra túlnyomó részeként, és így az érvényes bejelentkezések és számítási feladatok elutasítását.

> Megemlítve: OSA Practice #9

**Megvalósítás**:

A DDoS elleni védelem automatikusan engedélyezve van az Azure platform részeként. Magában foglalja a forgalom folyamatos figyelését és a nyilvános végpontok hálózati szintű támadásának valós idejű enyhítését.

- A [Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md) használatával figyelheti a virtuális hálózatokban üzembe helyezett erőforrásokhoz társított nyilvános IP-címeket.

- A [Azure SQL Database komplex veszélyforrások elleni védelem](threat-detection-overview.md) használatával észlelheti a szolgáltatásmegtagadási (DOS) támadásokat az adatbázisokon.

**Ajánlott eljárások**:

- A [támadási felület minimálisra csökkentése](#minimize-attack-surface) című cikkben ismertetett eljárások követésével csökkentheti a DDOS-támadások elleni fenyegetéseket.

- A komplex veszélyforrások elleni védelem – az **SQL hitelesítő adataival** kapcsolatos riasztások segítségével észlelheti a találgatásos támadásokat. Bizonyos esetekben a riasztás a penetráció tesztelési feladatait is megkülönböztetheti.

- SQL Databasehoz csatlakozó Azure-beli virtuális gépekhez:
  - A Azure Security Center internetes végpontokon keresztüli hozzáférésének korlátozásához kövesse az ajánlást.
  - A virtuálisgép-méretezési csoportok segítségével az alkalmazás több példányát futtathatja Azure-beli virtuális gépeken.
  - Tiltsa le az internetről az RDP-t és az SSH-t a találgatásos támadás megelőzése érdekében.

## <a name="monitoring-logging-and-auditing"></a>Figyelés, naplózás és naplózás

Ez a szakasz olyan képességeket mutat be, amelyek segítségével szokatlan és potenciálisan ártalmas kísérletek észlelhetők az adatbázisok eléréséhez vagy kiaknázásához. Emellett ismerteti az adatbázis-naplózás konfigurálásának ajánlott eljárásait az adatbázis-események nyomon követéséhez és rögzítéséhez.

### <a name="protect-databases-against-attacks"></a>Adatbázisok elleni védelem támadásokkal szemben

A komplex veszélyforrások elleni védelem lehetővé teszi, hogy a rendellenes tevékenységekre vonatkozó biztonsági riasztások révén észlelje és reagáljon a lehetséges fenyegetésekre.

**Megvalósítás**:

- A komplex [veszélyforrások elleni védelem](threat-detection-overview.md#alerts) használatával azonosíthatja a szokatlan és potenciálisan ártalmas támadási kísérleteket az adatbázisok eléréséhez vagy kiaknázásához, többek között:
  - SQL-injektálási támadás.
  - Hitelesítő adatok ellopása/szivárgása.
  - Jogosultságokkal való visszaélés.
  - Az adatkiszűrése.

**Ajánlott eljárások**:

- Az [Azure Defender for SQL](azure-defender-for-sql.md)konfigurálása   egy adott kiszolgálóhoz vagy felügyelt példányhoz. Az Azure Defender for SQL-t az előfizetéshez tartozó összes kiszolgáló és felügyelt példány esetében úgy is beállíthatja, hogy [Azure Security Center standard szintű](../../security-center/security-center-pricing.md)csomagra váltson.

- A teljes körű vizsgálathoz javasolt a [SQL Database naplózás](../../azure-sql/database/auditing-overview.md)engedélyezése. A naplózással nyomon követheti az adatbázis eseményeit, és egy Azure Storage-fiókban vagy Azure Log Analytics-munkaterületen is megírhatja őket egy naplóba.

### <a name="audit-critical-security-events"></a>Kritikus biztonsági események naplózása

Az adatbázis-események nyomon követése segít megérteni az adatbázis tevékenységeit. Olyan eltéréseket és rendellenességeket szerezhet be, amelyek üzleti vagy feltételezett biztonsági szabálysértéseket jelezhetnek. Emellett a megfelelőségi szabványok betartását is lehetővé teszi és megkönnyíti.

**Megvalósítás**:

- Engedélyezze [SQL Database naplózást](../../azure-sql/database/auditing-overview.md) vagy a [felügyelt példányok naplózását](../managed-instance/auditing-configure.md) az adatbázis-események nyomon követéséhez és az Azure Storage-fiókban, log Analytics munkaterület (előzetes verzió) vagy Event Hubs (előzetes verzió) naplóba való íráshoz.

- A naplók beírhatók egy Azure Storage-fiókba, egy Log Analytics munkaterületre Azure Monitor naplók általi felhasználáshoz, illetve az Event hub használatával történő felhasználáshoz. Ezen beállítások bármely kombinációját konfigurálhatja, és a rendszer a naplókat is megírja a naplókba.

**Ajánlott eljárások**:

- Ha [SQL Database naplózást](../../azure-sql/database/auditing-overview.md) konfigurál a kiszolgálón vagy a [felügyelt példányok naplózásakor](../managed-instance/auditing-configure.md) az eseményeket naplózza, az adott kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázis naplózva lesz.
- Alapértelmezés szerint a naplózási házirend minden műveletet (lekérdezéseket, tárolt eljárásokat és sikeres és sikertelen bejelentkezést) tartalmaz az adatbázisokon, ami nagy mennyiségű naplót eredményezhet. Javasoljuk, hogy az ügyfelek a [PowerShell használatával konfigurálják a különböző típusú műveletek és műveleti csoportok naplózását](./auditing-overview.md#manage-auditing). A konfigurálásával szabályozhatja a naplózott műveletek számát, és csökkentheti az események elvesztésének kockázatát. Az egyéni naplózási konfigurációk lehetővé teszik, hogy az ügyfelek csak a szükséges naplózási adatmennyiséget rögzítsen.
- A naplók közvetlenül a [Azure Portal](https://portal.azure.com/)vagy a konfigurált tárolási helyről is felhasználhatók.

> [!NOTE]
> A Log Analytics naplózásának engedélyezése a betöltési díjak alapján jár. Vegye figyelembe a kapcsolódó költségeket ezzel a [lehetőséggel](https://azure.microsoft.com/pricing/details/monitor/), vagy a naplókat egy Azure Storage-fiókban kell tárolnia.

**További források**:

- [SQL Database naplózás](../../azure-sql/database/auditing-overview.md)
- [SQL Server naplózás](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="secure-audit-logs"></a>Biztonságos naplózási naplók

Korlátozza a hozzáférést a Storage-fiókhoz a feladatok elkülönítésének támogatásához és a DBA-ket a könyvvizsgálók elkülönítéséhez.

**Megvalósítás**:

- A naplók Azure Storage-ba mentésekor győződjön meg arról, hogy a Storage-fiókhoz való hozzáférés a minimális biztonsági alapelvekre korlátozódik. Szabályozhatja, hogy ki férhet hozzá a Storage-fiókhoz.
- További információ: az [Azure Storage hozzáférésének engedélyezése](../../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Ajánlott eljárások**:

- A naplózási cél elérésének szabályozása kulcsfontosságú fogalom a DBA a könyvvizsgálók általi elkülönítéséhez.

- A bizalmas adatokhoz való hozzáférés naplózásakor érdemes lehet az adatokat adattitkosítással biztosítani, hogy elkerülje az adatszivárgást a könyvvizsgálónak. További információkért lásd a [bizalmas adatok védelme magas jogosultságú, jogosulatlan felhasználóktól](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)című szakaszt.

## <a name="security-management"></a>Biztonság kezelése

Ez a szakasz az adatbázisok biztonsági helyzetének kezelésére szolgáló különböző szempontokat és ajánlott eljárásokat ismerteti. Ajánlott eljárásokat tartalmaz, amelyekkel biztosítható, hogy az adatbázisok megfeleljenek a biztonsági normáknak, a felfedezéshez, valamint az adatbázisokban potenciálisan bizalmas adatokhoz való hozzáférés besorolásához és nyomon követéséhez.

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Győződjön meg arról, hogy az adatbázisok úgy vannak konfigurálva, hogy megfeleljenek az ajánlott biztonsági eljárásoknak

Proaktív módon fejlesztheti adatbázisa biztonságát a lehetséges adatbázis-sebezhetőségek felderítésével és szervizelését.

**Megvalósítás**:

- Az [SQL sebezhetőségi felmérés](/sql/relational-databases/security/sql-vulnerability-assessment) (va) lehetővé teszi az adatbázis biztonsági problémákra való vizsgálatát, és rendszeres időközönként automatikusan futtatni az adatbázisokat.

**Ajánlott eljárások**:

- Először futtassa a VA-t az adatbázisain, és ismételje meg a szervizelését, ha nem végez ellenőrzéseket az ajánlott biztonsági eljárásokkal szemben. Állítsa be az alapkonfigurációkat az elfogadható konfigurációkhoz, amíg a vizsgálat nem kerül _tiszta_ állapotba, vagy az összes ellenőrzés el lett mentve.  

- Konfigurálja a rendszeres ismétlődő vizsgálatokat hetente egyszer, és konfigurálja az érintett személyt az összegző e-mailek fogadására.

- Tekintse át az összes heti vizsgálatot követő VA összefoglalót. A talált biztonsági rések esetében értékelje ki az előző vizsgálat eredményének eltolódását, és állapítsa meg, hogy az ellenőrzést meg kell-e oldani. Ellenőrizze, hogy van-e jogos oka a konfiguráció változásának.

- Az ellenőrzések és a frissítési alaptervek feloldása, ahol szükséges. Hozzon létre jegyeket a műveletek feloldásához, és kövesse ezeket, amíg meg nem oldódik.

**További források**:

- [SQL-sebezhetőségi felmérés](/sql/relational-databases/security/sql-vulnerability-assessment)
- [Az SQL sebezhetőség-felmérési szolgáltatás segítséget nyújt az adatbázis-sebezhetőségek azonosításában](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Bizalmas adatok azonosítása és címkézése

Olyan oszlopok felderítése, amelyek potenciálisan bizalmas adatokat tartalmaznak. A bizalmas adatokat figyelembe vevő adatok nagy mértékben függenek az ügyféltől, a megfelelőségi szabályozástól stb., és az adatokért felelős felhasználóknak ki kell értékelniük azokat. Az oszlopok osztályozása speciális, érzékenységen alapuló naplózási és védelmi forgatókönyvek használatához.

**Megvalósítás**:

- Az [SQL-adatfelderítés és-besorolás](data-discovery-and-classification-overview.md) használatával felderítheti, osztályozhatja, címkézheti és megóvja a bizalmas adatokat az adatbázisaiban.
  - Megtekintheti az automatikus észlelés által létrehozott besorolási javaslatokat az SQL-adatfelderítés és besorolás irányítópulton. Fogadja el a releváns besorolásokat, így a bizalmas adatok állandó címkével vannak ellátva a besorolási címkékkel.
  - Adja meg manuálisan a besorolásokat az automatikus mechanizmus által nem felderített további bizalmas adatmezőkhöz.
- További információ: [SQL-adatok felderítése és besorolása](/sql/relational-databases/security/sql-data-discovery-and-classification).

**Ajánlott eljárások**:

- Az adatbázis besorolási állapotának pontos értékeléséhez rendszeresen figyelje a besorolási irányítópultot. Az adatbázis besorolási állapotáról szóló jelentés exportálható vagy kinyomtatható a megfelelőségi és naplózási célokra való megosztáshoz.

- Az ajánlott bizalmas adatok állapotának folyamatos figyelése az SQL sebezhetőségi felmérésben. Nyomon követheti a bizalmas adatfelderítési szabályt, és azonosíthatja a besorolást az ajánlott oszlopokban.  

- A besorolást a szervezet konkrét igényeihez igazított módon használhatja. Szabja testre a Information Protection szabályzatot (érzékenységi címkék, adattípusok, felderítési logika) a Azure Security Center [SQL Information Protection](../../security-center/security-center-info-protection-policy.md) házirendjében.

### <a name="track-access-to-sensitive-data"></a>Bizalmas adatokhoz való hozzáférés nyomon követése

Figyelje meg, hogy ki fér hozzá a bizalmas adatokhoz, és hogyan rögzíthet lekérdezéseket bizalmas adatokon a naplókban.

**Megvalósítás**:

- Használja együtt az SQL-naplózást és az adatbesorolást.
  - A [SQL Database naplójában](../../azure-sql/database/auditing-overview.md) a hozzáférést a bizalmas adatokhoz is nyomon követheti. Megtekintheti az adatok, például az elért adatokat, valamint az érzékenységi címkét is. További információ: [adatfelderítés és besorolás](data-discovery-and-classification-overview.md) és [a bizalmas adatokhoz való hozzáférés naplózása](data-discovery-and-classification-overview.md#audit-sensitive-data).

**Ajánlott eljárások**:

- Tekintse meg a naplózási és adatbesorolási szakaszban ajánlott eljárásokat:
  - [Kritikus biztonsági események naplózása](#audit-critical-security-events)
  - [Bizalmas adatok azonosítása és címkézése](#identify-and-tag-sensitive-data)

### <a name="visualize-security-and-compliance-status"></a>Biztonsági és megfelelőségi állapot megjelenítése

Olyan egységes infrastruktúra-alapú biztonsági felügyeleti rendszer használata, amely megerősíti az adatközpontok biztonsági állapotát (beleértve a SQL Database adatbázisait is). Az adatbázisok és a megfelelőségi állapot biztonságával kapcsolatos javaslatok listájának megtekintése.

**Megvalósítás**:

- Az SQL-hez kapcsolódó biztonsági javaslatok és az aktív fenyegetések figyelése [Azure Security Centerban](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Gyakori biztonsági fenyegetések és lehetséges enyhítések

Ez a szakasz segítséget nyújt az egyes támadási vektorokkal szembeni védelemhez szükséges biztonsági intézkedések megkeresésében. A fentiekben ismertetett biztonsági irányelvek közül egyet vagy többet a lehető legtöbb megoldással lehet megvalósítani.

### <a name="security-threat-data-exfiltration"></a>Biztonsági fenyegetés: az adatkiszűrése

Az adatok kiszűrése az adatok jogosulatlan másolása, továbbítása vagy lekérése egy számítógépről vagy kiszolgálóról. Tekintse meg a wikipedia [adatkiszűrése](https://en.wikipedia.org/wiki/Data_exfiltration) definícióját.

Ha nyilvános végponton keresztül csatlakozik a kiszolgálóhoz, a kiszűrése kockázatot jelent, mivel az ügyfelek számára meg kell nyitni a tűzfalat a nyilvános IP-címekre.  

**1. forgatókönyv**: az Azure-beli virtuális gépeken futó alkalmazások Azure SQL Database-adatbázishoz kapcsolódnak. A szélhámos színész hozzáfér a virtuális géphez, és megsérül. Ebben az esetben az kiszűrése azt jelenti, hogy a szélhámos virtuális gépet használó külső entitás csatlakozik az adatbázishoz, átmásolja a személyes adataikat, és egy blob Storage-ban tárolja, vagy egy másik SQL Database egy másik előfizetésben.

**2. forgatókönyv**: egy Rouge dBA. Ezt a forgatókönyvet gyakran a szabályozott iparágak biztonsági szempontból érzékeny ügyfelei okozják. Ebben a forgatókönyvben egy magas szintű jogosultságú felhasználó másolhatja az adatok Azure SQL Databaseról egy másik előfizetésre, amelyet nem az adatok tulajdonosa ellenőriz.

**Lehetséges enyhítések**:

Napjainkban a Azure SQL Database és az SQL felügyelt példánya a következő módszereket kínálja az adatkiszűrése fenyegetések enyhítésére:

- Az engedélyezési és megtagadási szabályok kombinációját használhatja az Azure-beli virtuális gépek NSG, hogy megtudja, mely régiók érhetők el a virtuális gépről.
- Ha SQL Database kiszolgálót használ, állítsa be az alábbi beállításokat:
  - Az Azure-szolgáltatások kikapcsolásának engedélyezése.
  - Csak az Azure-beli virtuális gépet tartalmazó alhálózatról érkező forgalom engedélyezése VNet tűzfalszabály beállításával.
  - [Privát hivatkozás](../../private-link/private-endpoint-overview.md) használata
- A felügyelt SQL-példányok esetében a magánhálózati IP-hozzáférés alapértelmezés szerint az első olyan kiszűrése, amely a szélhámos virtuális gépekre vonatkozik. Kapcsolja be az alhálózat-delegálás funkciót egy alhálózaton, hogy automatikusan beállítja a legszigorúbb házirendet egy SQL felügyelt példány alhálózatán.
- A szélhámos DBA-mel kapcsolatos aggályok nagyobb mértékben jelennek meg az SQL felügyelt példányaival, mivel nagyobb felületi területtel rendelkezik, és a hálózati követelmények láthatók az ügyfelek számára. A legjobb megoldás az ebben a biztonsági útmutatóban található összes eljárás alkalmazásával megakadályozza a szélhámos DBA-forgatókönyvet az első helyen (nem csak az adatkiszűrése esetében). Always Encrypted egy módszer a bizalmas adatok védelmére azáltal, hogy titkosítja, és a kulcs nem érhető el a DBA számára.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Az üzletmenet folytonosságának és rendelkezésre állásának biztonsági szempontjai

A legtöbb biztonsági szabvány a működés folytonossága szempontjából az adatrendelkezésre állást kezeli, és a redundancia és a feladatátvételi funkciók megvalósításával kerülhető el, hogy elkerülje az egyes meghibásodási pontokat. Katasztrófa esetén az adatok és a naplófájlok biztonsági mentéseit gyakran érdemes megtartani.A következő szakasz átfogó áttekintést nyújt az Azure-ba beépített képességekről. Emellett további beállításokat is biztosít, amelyek az adott igények kielégítésére konfigurálhatók:

- Az Azure beépített magas rendelkezésre állást kínál: [magas rendelkezésre állás SQL Database és SQL felügyelt példánnyal](high-availability-sla.md)

- A üzletileg kritikus szinten a feladatátvételi csoportok, a teljes és a különbözeti naplók biztonsági mentései, valamint az alapértelmezés szerint engedélyezett időponthoz tartozó biztonsági másolatok találhatók:  
  - [Automatikus biztonsági mentések](automated-backups-overview.md)
  - [Adatbázis helyreállítása automatikus adatbázis-biztonsági mentéssel – időponthoz való visszaállítás](recovery-using-backups.md#point-in-time-restore)

- További üzletmenet-folytonossági funkciók, mint például a zóna redundáns konfigurációja és az automatikus feladatátvételi csoportok különböző Azure-térségek konfigurálható: 
    - [Magas rendelkezésre állású zóna redundáns konfigurációja prémium & üzletileg kritikus szolgáltatási szintekhez](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)
    - [Magas rendelkezésre állás – zóna redundáns konfigurációja általános célú szolgáltatási réteghez](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
    - [Az üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a Azure SQL Database biztonsági funkcióinak áttekintését](security-overview.md)