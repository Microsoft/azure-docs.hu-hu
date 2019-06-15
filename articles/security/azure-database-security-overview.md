---
title: Azure database biztonsági áttekintése |} A Microsoft Docs
description: Ez a cikk áttekintést az Azure adatbázis biztonsági funkciók.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: 7e0e93c82279ec1a4fbecbbf27c7a1866286b2f8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587601"
---
# <a name="azure-database-security-overview"></a>Azure database biztonságának áttekintése

Biztonsági adatbázis elsődleges szempont, és azt mindig az, hogy az Azure SQL Database prioritást. Az Azure SQL Database tűzfalszabályok és internetkapcsolat titkosítással kapcsolatbiztonság támogatja. Támogatja a felhasználónévvel és jelszóval való hitelesítés és Azure Active Directory (Azure AD-) hitelesítést, amely az Azure Active Directory által felügyelt identitásokat használ. Engedélyezés szerepköralapú hozzáférés-vezérlés használ.

Az Azure SQL Database az alkalmazás módosítása nélkül az adatbázisok, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra inaktív valós idejű titkosítását és visszafejtését elvégzésével támogatja a titkosítást.

További lehetőségek a vállalati adatok titkosítása a Microsoft biztosítja:

-   Cellaszintű titkosítás titkosításához egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal érhető el.
-   Ha egy hardveres biztonsági modulra vagy a titkosítási kulcshierarchia központi kezelését van szüksége, fontolja meg az Azure Key Vault használata az SQL Server Azure virtuális gépen (VM).
-   Mindig titkosított (jelenleg előzetes verzióban érhető el) egyszerűvé teszi titkosítás átlátható alkalmazásokat. Azt is lehetővé teszi az ügyfelek bizalmas adatok ügyfélalkalmazásokon belüli titkosítását az SQL Database a titkosítási kulcsok megosztása nélkül.

Az Azure SQL Database naplózási lehetővé teszi a vállalatok számára, hogy a rekord a naplózási események naplózása az Azure Storage-ban. Az SQL Database naplózási szolgáltatása a Microsoft Power BI-ba is integrálható, hogy elősegítse a részletes jelentéskészítést és elemzést.

Az Azure SQL Database is szorosan biztosítani, hogy megfelel a legtöbb szabályozási és biztonsági követelmények, többek között a HIPAA, az ISO 27001/27002 és a PCI DSS Level 1. Biztonsági megfelelőségi tanúsítványok aktuális listáját érhető el: a [Microsoft Azure Trust Center webhely](https://azure.microsoft.com/support/trust-center/services/).

Ez a cikk végigvezeti a strukturált, a táblázatos és a relációs adatok a Microsoft Azure SQL-adatbázisok védelme alapjait. A cikk ismerteti az adatok védelméhez, a hozzáférés szabályozásához és a proaktív figyeléshez szükséges erőforrások az első lépéseit is.

## <a name="protection-of-data"></a>Adatok védelme

Az SQL Database segíti az adatok védelme titkosítás megadásával:

- Keresztül mozgásban lévő adatok [Transport Layer Security (TLS)](https://support.microsoft.com/kb/3135244).
- Keresztül inaktív adatok [transzparens adattitkosítás](https://go.microsoft.com/fwlink/?LinkId=526242).
- Adatok használatra [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Az adatok titkosításának egyéb módjaira vonatkozóan fontolja meg az alábbiakat:

-   A [cellaszintű titkosítás](https://msdn.microsoft.com/library/ms179331.aspx) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
-   [Az SQL Server egy Azure-beli virtuális gépen az Azure Key Vault](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), ha egy hardveres biztonsági modul vagy a titkosítási kulcshierarchia központi kezelését.

### <a name="encryption-in-motion"></a>Mozgásban lévő titkosítás

Minden ügyfél-kiszolgáló alkalmazás gyakran okoz problémát adatvédelmi szükség megegyezik adatokat helyez át a nyilvános és privát hálózaton keresztül. Az adatok hálózaton keresztüli áthelyezése nincs titkosítva, van-e arra, hogy azt rögzíthetik és a jogosulatlan felhasználók ellopták. Ha az adatbázis-szolgáltatásokkal foglalkozó, győződjön meg arról, hogy az adatok titkosítva van-e az adatbázis-ügyfél és kiszolgáló közötti. Győződjön meg arról, hogy az adatok titkosítva van-e adatbázis-kiszolgálói közötti, amelyek kommunikálnak egymással és a középső rétegbeli alkalmazásokhoz is.

Az egyik probléma egy hálózat adminisztrálásakor alkalmazások között nem megbízható hálózaton keresztül küldött adatok védelme. Használhat [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) kiszolgálók és ügyfelek hitelesítéséhez, és aztán a segítségükkel titkosíthatja az üzeneteket a hitelesített felek között.

A hitelesítési folyamatban egy TLS/SSL-ügyfél egy üzenetet küld egy TLS/SSL-kiszolgálónak. A kiszolgáló válaszol, amelyet a kiszolgáló hitelesítse magát a adatokkal. Az ügyfél és kiszolgáló emellett végrehajtja a munkamenetkulcsok, és a hitelesítési párbeszéd vége. Hitelesítés elvégzése, ha SSL-lel védett kommunikáció megkezdheti a kiszolgáló és az ügyfél között a hitelesítési folyamat során létesített szimmetrikus titkosítási kulcsok használatával.

Az Azure SQL Database összes kapcsolat titkosításának megkövetelése (TLS/SSL) minden alkalommal adatokat, amíg "átvitel", és az adatbázisból. SQL-adatbázis a TLS/SSL segítségével hitelesítheti a kiszolgálókat és ügyfeleket, és aztán a segítségükkel titkosíthatja az üzeneteket a hitelesített felek között. 

Az alkalmazás kapcsolati karakterláncában meg kell adnia a kapcsolat titkosítását, és nem az, hogy bízzon meg a tanúsítvány paraméterek. (Ez történik, ha másolja a kapcsolati karakterláncot az Azure Portalon.) Ellenkező esetben a kapcsolat nem tudja ellenőrizni a kiszolgáló identitását, és ki van téve a támadások "man-in-the-middle" lesz. Az ADO.NET-illesztő számára például kapcsolati karakterlánc paraméterei vannak `Encrypt=True` és `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az adatbázis biztonságossá tételéhez számos óvintézkedéseket is igénybe vehet. Például egy biztonságos rendszer tervezése, bizalmas eszközök titkosítását, és az adatbázis-kiszolgálók körül tűzfal létrehozása. Azonban egy forgatókönyvben, ahol a fizikai adathordozó (például meghajtókat vagy a biztonsági mentési szalagot) ellopják, a rosszindulatú fél csak visszaállíthatja vagy csatolni az adatbázist, és keresse meg az adatokat.

Egy megoldás, ha titkosíthatja a bizalmas adatokat az adatbázisban, és a egy tanúsítványt az adatok titkosításához használt kulcsok védelme. Ez a megoldás megakadályozza, hogy a kulcsok nélkül bárki az adatokat, de az ilyen típusú védelmet kell készülni.

Megoldani a problémát, SQL Server és SQL Database-támogatás [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Transzparens adattitkosítás titkosítja az SQL Server és SQL Database az adatfájlokat, más néven az inaktív adatok titkosítása.

Transzparens adattitkosítás segít a rosszindulatú tevékenység elleni. Valós idejű titkosítási és visszafejtési az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra inaktív azt végez anélkül, hogy a módosításokat.  

Transzparens adattitkosítás a tárolót a teljes adatbázisra az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával titkosítja. Az SQL Database az adatbázis-titkosítási kulcs védi egy beépített kiszolgálói tanúsítványt. A beépített kiszolgálói tanúsítvány egy egyedülálló megoldás minden egyes SQL Database-kiszolgáló.

Ha egy adatbázis Geo-DR kapcsolatban, védi egy másik kulcsot minden egyes kiszolgálón. Ha a két adatbázis ugyanazon a kiszolgálón csatlakozik, a beépített tanúsítvány osztoznak. A Microsoft legalább 90 naponként automatikusan elforgatja ezeket a tanúsítványokat. 

További információkért lásd: [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Titkosítás az használata (ügyfél)

A legtöbb adatszivárgásokat például hitelkártyaszámokat tartalmazó vagy személyes azonosításra alkalmas adatokat kritikus fontosságú adatok ellopása járnak. Adatbázisok kincsem troves bizalmas adatok is lehetnek. Ügyfelek személyes adatokat (például a különféle nemzeti azonosítószámokat), a versenyképes bizalmas információkat és a szellemi tulajdont is tartalmazhatnak. Elveszett vagy ellopott adatokat, különösen a vásárlói adatokat, a márka kárt versenyhelyzetbe és súlyos bírságok – még akkor is, szemben eredményezhet.

![Az Always Encrypted funkció szemléltetett zárolási és kulcs](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) egy szolgáltatás, Azure SQL Database vagy SQL Server-adatbázisokban tárolt bizalmas adatok védelme érdekében. Always Encrypted lehetővé teszi az ügyfelek számára a bizalmas adatok ügyfélalkalmazásokon belüli titkosítását, és soha ne adja meg a titkosítási kulcsokat az adatbázismotorhoz (SQL Database vagy SQL Server).

Mindig titkosított biztosít egy elválasztó azokat, akik a saját adatok (és annak megtekintéséhez) és azokat, akik kezelése az adatok (de nem rendelkezhetnek) között. Ez segít, győződjön meg arról, hogy a helyszíni adatbázis-rendszergazdák, a felhőalapú adatbázis-kezelők vagy más kiemelt jogosultságú, de a jogosulatlan felhasználók nem férhetnek hozzá a titkosított adatokat.

Emellett Always Encrypted teszi titkosítás átlátható alkalmazásokhoz. Az Always Encrypted-kompatibilis illesztőprogram telepítve van az ügyfélszámítógép automatikusan titkosítja és visszafejteni bizalmas adatokat az ügyfélalkalmazásban. Az illesztőprogram bizalmas oszlopok adatait titkosítja az adatokat az adatbázismotorhoz való továbbítása előtt. Az illesztőprogram automatikusan lekérdezés újraírja, úgy, hogy a rendszer megőrzi a szemantikáját, hogy az alkalmazás. Ehhez hasonlóan a az illesztőprogram transzparens módon visszafejti a titkosított oszlopokat, a lekérdezési eredményekben található tárolt adatokat.

## <a name="access-control"></a>Hozzáférés-vezérlés

A biztonság érdekében az SQL Database használatával irányítja a hozzáférést:

- Az tűzfalszabályt, amely korlátozza a kapcsolódási IP-cím alapján.
- Felhasználók kell igazolnia az identitását igénylő hitelesítési mechanizmusok.
- Engedélyezési mechanizmus, amely korlátozza a felhasználók számára bizonyos műveletek és adatok.

### <a name="database-access"></a>Adatbázis-hozzáférés

Az adatvédelem kezdődik az adatokhoz való hozzáférés szabályozása. Az adatokat tároló adatközpont fizikai hozzáférés kezeli. Beállíthatja, hogy a tűzfal a hálózati rétegen biztonság kezeléséhez. Hozzáférés a hitelesítési bejelentkezéseket, és engedélyek a kiszolgáló és az adatbázis-szerepkörök definiálása is szabályozhatja.

#### <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok

[Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/) Azure és egyéb internetalapú alkalmazások relációsadatbázis-szolgáltatás. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján. További információkért lásd: [Az Azure SQL Database-tűzfalszabályok áttekintése](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Az Azure SQL Database szolgáltatás kizárólag a 1433-as TCP-porton keresztül érhető el. SQL-adatbázis eléréséhez a számítógépről, győződjön meg arról, hogy az ügyfél tűzfal engedélyezi-e a kimenő TCP-kommunikációt az 1433-as TCP-porton. Bejövő kapcsolatok esetén más alkalmazásokat nem szükséges, ha letiltja az 1433-as TCP-porton.

#### <a name="authentication"></a>Hitelesítés

A hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való kapcsolódáskor. Az SQL Database két hitelesítési típust támogat:

-   **SQL Server-hitelesítés**: Egyetlen bejelentkezési fiók jön létre a logikai SQL-példány létrehozásakor, az úgynevezett SQL Database-előfizetői fiók. Ennek a fióknak a használatával kapcsolódik [SQL Server-hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (felhasználónév és jelszó). Ez a fiók rendszergazdának számít a logikai kiszolgáló példányán és a példányhoz csatolt összes felhasználói adatbázisban. Az előfizetői fiók engedélyei nem korlátozhatók. Ilyen fiókból csak egy létezhet.
-   **Az Azure Active Directory-hitelesítés**: [Az Azure AD-hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) olyan mechanizmus, az Azure SQL Database és az Azure SQL Data Warehouse csatlakozik az Azure AD-identitások használatával. Használhatja az adatbázis-felhasználók identitásainak központi kezelését.

![Az SQL Database az Azure AD-hitelesítés](./media/azure-databse-security-overview/azure-database-fig2.png)

 Az Azure AD-hitelesítés előnyei a következők:
  - SQL Server-hitelesítés helyett biztosít.
  - Ez segít a felhasználói identitások elterjedése adatbázis-kiszolgáló között, és lehetővé teszi, hogy a jelszóváltoztatás egyetlen helyen történő.
  - Adatbázis-engedélyek külső (az Azure AD-) csoportok használata kezelheti.
  - Integrált Windows-hitelesítés és egyéb, amely támogatja az Azure AD-hitelesítés engedélyezésével, nyilvánvaló jelszavak tárolását.

#### <a name="authorization"></a>Engedélyezés

[Engedélyezési](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) felhasználók mire képes az Azure SQL Database hivatkozik. A felhasználói fiókjának adatbázis szabályozza azt [szerepkörtagságai](https://msdn.microsoft.com/library/ms189121) és [objektumszintű engedélyei](https://msdn.microsoft.com/library/ms191291.aspx). Engedélyezési az a folyamat meghatározásának adott erőforrásokhoz tartozó rendszerbiztonsági tag férhetnek biztonságos erőforrásokat, és mely műveletek engedélyezettek.

### <a name="application-access"></a>Alkalmazás-hozzáférés

#### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

Egy telefonos szolgáltatás képviselővel előfordulhat, hogy a hívók társadalombiztosítási szám vagy hitelkártya száma több számjegy azonosításához. Azonban ezeket az elemeket nem teljes mértékben láthatók a munkatársának.

Megadhatja, hogy maszkolja a társadalombiztosítási szám vagy az eredményhalmaz minden lekérdezés hitelkártya száma az utolsó négy számjegy kivételével az összes maszkolási szabály.

![Egy SQL database és az üzleti alkalmazások között küldött több maszkolási](./media/azure-databse-security-overview/azure-database-fig3.png)

Másik példaként a megfelelő adatok maszk személyes azonosításra alkalmas információk védelme érdekében lehet definiálni. A fejlesztő ezután lekérdezheti az éles környezetekre vonatkozó hibaelhárítási célból megfelelőségi szabályzat megsértése nélkül.

[Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) úgy korlátozza a bizalmas adatok közzétételét, hogy maszkolja azokat a nem kiemelt jogosultságú felhasználók számára. Dinamikus adatmaszkolás az Azure SQL Database V12-es verzió támogatott.

[Dinamikus adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) által meghatározhatóvá a bizalmas adatok felfedéséhez az alkalmazásrétegre gyakorolt minimális hatás mellett segít megakadályozni az bizalmas adatokhoz való illetéktelen hozzáférést. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.

> [!Note]
> Dinamikus adatmaszkolás az Azure adatbázis-rendszergazda, a kiszolgáló-rendszergazdai vagy biztonsági tisztviselő szerepkörök is konfigurálhatók.

#### <a name="row-level-security"></a>Sorszintű biztonság

Egy másik gyakori biztonsági követelmény, több-bérlős adatbázisok [sorszintű biztonság](https://msdn.microsoft.com/library/dn765131.aspx). Ez a funkció segítségével szabályozzák egy adatbázistábla soraihoz, a rendszer a lekérdezést végrehajtó felhasználó jellemzői alapján való hozzáférést. (A példában mutatókat csoport tagságát, és végrehajtási környezetben.)

![Sorszintű biztonság, amely a felhasználó hozzáférési sorok egy tábla egy ügyfél alkalmazáson keresztül](./media/azure-databse-security-overview/azure-database-fig4.png)

A hozzáférés korlátozási logika található az adatbázisszinten, hanem helyezkedik el az adatokat egy másik alkalmazás szinten. Az adatbázis-rendszer a hozzáférési korlátozásokat alkalmazza, minden alkalommal, amikor az egyik csomagunkban kísérel meg, hogy adatelérési. Így a biztonsági rendszer megbízhatóbb és robusztus a biztonsági rendszer támadási csökkentésével.

Sorszintű biztonság predikátum-alapú hozzáférés-vezérlés mutatja be. A rugalmas, központosított próbaverzióra, amelyeket végrehajthat szempont metaadatait, vagy a többi feltételt, a rendszergazda meghatározza, hogy megfelelő módon biztosítja. A predikátum feltételeként meghatározására szolgál-e a felhasználó rendelkezik-e a megfelelő adatokhoz való hozzáférést a felhasználói attribútumok alapján. Címke-alapú hozzáférés-vezérlés is alkalmazható predikátum-alapú hozzáférés-vezérlés használatával.

## <a name="proactive-monitoring"></a>Proaktív figyelés

Az SQL Database segíti az adatok biztonságos azáltal, hogy *naplózás* és *fenyegetésészlelés* képességeket.

### <a name="auditing"></a>Naplózás

[Az Azure SQL Database naplózási](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) növeli az események és az adatbázison belül fellépő változások képességét. Példák az adatokat érintő frissítéseket és lekérdezéseket.

Az SQL Database naplózási szolgáltatása nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, az Azure storage-fiókban. Naplózás segíthet a jogszabályoknak való megfelelőség, adatbázis-tevékenység megértésében, valamint betekintést nyerhet az eltéréseket és rendellenességeket, amelyek esetleg üzleti veszélyeket vagy biztonsági problémákat. Naplózás lehetővé teszi, hogy és megfelelőségi szabvány betartása elősegíti, de nem garantálja a megfelelőség.

Az SQL Database naplózását is használhatja:

- **Megőrzése** auditnaplót kiválasztott események. Megadhatja, hogy az adatbázis-műveleteket naplózni kívánt kategóriáit.
- **A jelentés** az adatbázis-tevékenységeket. Előre konfigurált jelentéseket és a egy irányítópult segítségével gyorsan használatba tevékenység és az események naplózásához.
- **Elemezheti** jelentéseket. Megtalálhatja a gyanús eseményeket, a szokatlan tevékenységeket és a trendeket.

Két naplózási módszer van:

-   **A blobnaplózás**: A naplókat a rendszer az Azure Blob Storage-ba írja. Ez a egy újabb naplózási mód. Ez nagyobb teljesítményt nyújt, támogatja a nagyobb részletességgel objektumszintű naplózást, és költséghatékonyabb.
-   **Táblanaplózás**: Naplók az Azure Table storage készültek.

### <a name="threat-detection"></a>Fenyegetések észlelése

[Komplex veszélyforrások elleni védelem az Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-advanced-threat-protection) észleli a gyanús, potenciálisan biztonsági fenyegetést jelentő tevékenységeket. Fenyegetésészlelés segítségével válaszolni az adatbázisban, például az SQL-injektálásokat, a gyanús eseményeket azok bekövetkezésekor. Riasztásokat biztosít, és engedélyezi az Azure SQL Database naplózását a gyanús események feltárását.

![A fenyegetésészlelés az SQL Database és a egy webalkalmazás, egy külső támadó és a egy rosszindulatú bennfentes](./media/azure-databse-security-overview/azure-database-fig5.jpg)

SQL a speciális veszélyforrások elleni védelem (ATP) egy speciális SQL biztonsági funkciókat, többek között az Adatfelderítés és a besorolás, a sebezhetőségi felmérés és a Fenyegetésészlelés kínál. 

- [Adatfelderítés és besorolás](../sql-database/sql-database-data-discovery-and-classification.md)
- [Sebezhetőségi felmérés](../sql-database/sql-vulnerability-assessment.md)  
- [Fenyegetések észlelése](../sql-database/sql-database-threat-detection.md)

[Azure Database for PostgreSQL komplex veszélyforrások elleni védelem](../postgresql/concepts-data-access-and-security-threat-protection.md) egy új réteget jelent, amely lehetővé teszi, és reagálhassanak a lehetséges veszélyforrásokra bekövetkezésük pillanatában észlelhessék a biztonsági riasztások rendellenes tevékenységekre adott biztosít. Felhasználók a gyanús adatbázis-tevékenységekről, és a lehetséges biztonsági résekről, valamint a rendellenes adatbázis-hozzáférés és a lekérdezések minták esetén riasztást küld. Komplex veszélyforrások elleni védelem az Azure Database for postgresql-hez az Azure Security Center integrálható a riasztásokat. A riasztások típusa a következők:

- Hozzáférés szokatlan helyről
- Hozzáférés szokatlan Azure-beli adat központból 
- Hozzáférés résztvevő részéről 
- Hozzáférés potenciálisan káros-alkalmazás 
- Azure database for PostgreSQL hitelesítő adatok találgatásos támadás 

[Azure Database for MySQL komplex veszélyforrások elleni védelem](../mysql/concepts-data-access-and-security-threat-protection.md) hasonló PostgreSQL speciális Protection védelmet biztosít.  

## <a name="centralized-security-management"></a>Központi biztonsági kezelése

[Az Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. Integrált biztonsági monitorozást és felügyeletet biztosít az Azure-előfizetések között. Ez segít egyébként észrevétlenül maradó fenyegetések észlelésére, és a biztonsági megoldások átfogó ökoszisztémájának működik.

[A Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) segítségével adatokat SQL Database-ben azáltal, hogy a kiszolgálók és adatbázisok biztonsági védelme érdekében. A Security Center segítségével:

- SQL Database-titkosítás és naplózás házirendeket határozhat meg.
- SQL-adatbázis-erőforrások figyelése az összes előfizetésére.
- Gyorsan azonosíthatja és biztonsági problémák elhárítására.
- A riasztásainak integrálása [Azure SQL Database fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

A Security Center támogatja a szerepköralapú hozzáférés.

## <a name="sql-information-protection"></a>SQL-információvédelem

[SQL-információvédelem](../sql-database/sql-database-data-discovery-and-classification.md) automatikusan felderíti és osztályozza a potenciálisan bizalmas adatokat, lehetővé teszi a címkézési tartósan címkézése a bizalmas adatok besorolása attribútumokkal és biztosít egy részletes irányítópult-megjelenítő a besorolás az adatbázis állapotát.  

Emellett számítja ki az eredmény az SQL-lekérdezések érzékenységi állíthat be, így a lekérdezéseket, amelyek bizalmas adatokat nyerhet ki az explicit módon naplózhatóvá válik, és az adatok védelme biztosítható. SQL-információvédelem további információkért tekintse meg az Azure SQL Database az Adatfelderítés és besorolás.

Konfigurálható [SQL Information Protection-szabályzatok](../security-center/security-center-info-protection-policy.md) az Azure Security Centerben.

## <a name="azure-marketplace"></a>Azure Piactér

Az Azure Piactér olyan online alkalmazás- és szolgáltatáspiactér, amelyen az induló vállalkozások és a független szoftverszállítók kínálhatják megoldásaikat az Azure-szolgáltatásokat világszerte használó ügyfeleknek.
Az Azure piactér jobb szolgálnak ügyfelek és partnerek számára egységes platformban ötvözi a Microsoft Azure partneri rendszereit. Is [keresést futtat](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) az Azure piactéren elérhető biztonsági termékek adatbázis megtekintéséhez.

## <a name="next-steps"></a>További lépések

- [Az Azure SQL-adatbázis védelme](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Az Azure Security Center és Azure SQL Database szolgáltatás](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [Az SQL Database fenyegetések észlelése](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Az SQL database teljesítményének növelése](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
