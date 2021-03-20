---
title: Kezelés az áttelepítés után
titleSuffix: Azure SQL Database
description: Ismerje meg, hogyan kezelheti az önálló és a készletezett adatbázisokat a Azure SQL Database való áttelepítést követően.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: b34ac24cb26bf5db4a49a5ad5b531deb252f4695
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96446117"
---
# <a name="new-dba-in-the-cloud--managing-azure-sql-database-after-migration"></a>Új DBA a felhőben – Azure SQL Database kezelése az áttelepítés után
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A hagyományos, önfelügyelt, önálló vezérlésű környezetből a Pásti-környezetbe való áttérés első lépésként tűnhet. Alkalmazás-fejlesztőként vagy DBA-ként érdemes megismernie a platform alapvető képességeit, amely segítséget nyújt az alkalmazás elérhetőségének, teljesítményének, biztonságossá tételének és rugalmasságának fenntartásában. A cikk célja, hogy pontosan ezt tegye. A cikk röviden rendszerezi az erőforrásokat, és útmutatást nyújt arról, hogyan használhatók fel a Azure SQL Database főbb képességei az önálló és a készletezett adatbázisokkal, így hatékonyan kezelhetik és megtarthatják az alkalmazásait, és a felhőben optimális eredményeket érhet el. A cikk általános célközönsége a következő:

- Az alkalmazás (ok) áttelepítésének kiértékelése Azure SQL Database – az alkalmazás (ok) modernizálása.
- Folyamatban van az alkalmazás (ok) áttelepítésének folyamata – folyamatban lévő áttelepítési forgatókönyv.
- A közelmúltban befejezte az áttelepítést Azure SQL Database – új DBA-re a felhőben.

Ez a cikk a Azure SQL Database alapvető jellemzőit tárgyalja olyan platformként, amely az önálló adatbázisok és a rugalmas készletekben található készletezett adatbázisok használata esetén könnyen kihasználható. Ezek a következők:

- Adatbázisok figyelése Azure Portal használatával
- Üzletmenet-folytonosság és vészhelyreállítás (BCDR)
- Biztonság és megfelelőség
- Intelligens adatbázis figyelése és karbantartása
- Adatáthelyezés

## <a name="monitor-databases-using-the-azure-portal"></a>Adatbázisok figyelése Azure Portal használatával

A [Azure Portal](https://portal.azure.com/)az adatbázis kiválasztásával figyelheti az egyes adatbázisok kihasználtságát, és a **figyelési** diagramra kattinthat. Ekkor megjelenik a **Metrika** ablak, amelyet a **Diagram szerkesztése** gombra kattintva módosíthat. Adja hozzá a következő metrikákat:

- Processzorhasználat (%)
- DTU-kihasználtság (%)
- Adat IO kihasználtsága (%)
- Adatbázis méretének kihasználtsága

Miután hozzáadta ezeket a metrikákat, továbbra is megtekintheti őket a **figyelési** diagramon, és további információkat kaphat a **metrika** ablakáról. A négy metrika az átlagos kihasználtság százalékos arányát jeleníti meg az adatbázis **DTU-jához** viszonyítva. A szolgáltatási rétegekkel kapcsolatos további információkért tekintse meg a [DTU-alapú vásárlási modellt](service-tiers-dtu.md) és a [virtuális mag-alapú vásárlási modellt](service-tiers-vcore.md) ismertető cikket.  

![Adatbázis-teljesítményének szolgáltatásszint-figyelése.](./media/manage-data-after-migrating-to-database/sqldb_service_tier_monitoring.png)

A metrikákhoz riasztásokat is lehet konfigurálni. Kattintson a **Riasztás hozzáadása** gombra a **Metrika** ablakban. A riasztás konfigurálásához kövesse a Varázslót. Lehetőség van riasztást kérni, ha a metrikák túllépnek egy bizonyos küszöböt, vagy egy bizonyos küszöb alá esnek.

Például ha az adatbázisban munkaterhelés-növekedésére számít, beállíthatja, hogy riasztást kapjon elektronikus üzenet formájában abban az esetben, ha az adatbázisra vonatkozó bármelyik metrika eléri a 80 százalékot. Ezt korai figyelmeztetésként használhatja, ha lehetséges, hogy a következő legmagasabb számítási méretre kell váltania.

A teljesítménnyel kapcsolatos mérőszámok segítségével eldöntheti, hogy alacsonyabb számítási méretre tudja-e váltani. Tegyük fel, hogy Standard S2 adatbázist használ, és a metrikák azt mutatják, hogy az adatbázis átlagos kihasználtsága egy adott időpontban nem több, mint 10 százalék. Ebben az esetben valószínű, hogy az adatbázis Standard S1 teljesítményszinten is megfelelően fog működni. Azonban ügyeljen arra, hogy a döntés meghozatala előtt a nyárs vagy ingadozó munkaterhelések alacsonyabb számítási méretre lépjenek.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Üzletmenet-folytonosság és vészhelyreállítás (BCDR)

Az üzletmenet folytonossága és a vész-helyreállítási képességek lehetővé teszik, hogy a szokásos módon folytassa üzleti tevékenységét, ha vészhelyzet esetén. A katasztrófa lehet egy adatbázis-szintű esemény (például valaki véletlenül elveszít egy kritikus táblázatot), vagy egy adatközpont szintű esemény (regionális katasztrófa, például egy szökőár).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hogyan biztonsági másolatok létrehozása és kezelése SQL Database

Nem kell biztonsági másolatokat létrehoznia Azure SQL Databaseon, és ez azért van, mert nem kell. SQL Database automatikusan biztonsági mentést készít az adatbázisokról, így többé nem kell aggódnia a biztonsági mentések ütemezésével, bevezetésével és kezelésével kapcsolatban. A platform teljes biztonsági mentést készít minden héten, a különbözeti biztonsági mentést óránként, a napló biztonsági mentését pedig 5 percenként, így biztosítva a vész-helyreállítás hatékonyságát és az adatvesztést. Az első teljes biztonsági mentés az adatbázis létrehozása után azonnal megtörténik. Ezek a biztonsági másolatok a "megőrzési időszak" nevű meghatározott időszakra érhetők el, és a kiválasztott szolgáltatási rétegtől függően változnak. A SQL Database lehetővé teszi a megőrzési időszakon belüli bármely időpontra való visszaállítást az [időponthoz tartozó helyreállítás (PITR)](recovery-using-backups.md#point-in-time-restore)használatával.

|Szolgáltatási szint|Megőrzési időszak (nap)|
|---|:---:|
|Alapszintű|7|
|Standard|35|
|Prémium|35|
|||

Emellett a [hosszú távú adatmegőrzés (ltr)](long-term-retention-overview.md) funkciója lehetővé teszi, hogy a biztonsági másolat fájljait jóval hosszabb ideig, akár 10 évig is megtartsa, és a biztonsági másolatokból származó adatokat az adott időszakon belül bármikor helyreállítsa. Emellett az adatbázis biztonsági mentései a földrajzilag replikált tárolókban is megmaradnak a regionális katasztrófák rugalmasságának biztosítása érdekében. Ezeket a biztonsági másolatokat bármely Azure-régióban visszaállíthatja a megőrzési időtartamon belül bármikor. Lásd: [Üzletmenet-folytonosság áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Hogyan gondoskodik az üzletmenet folytonosságáról az adatközpont szintű katasztrófák vagy regionális katasztrófák esetén

Mivel az adatbázis biztonsági másolatait földrajzilag replikált tárolóban tárolják, hogy regionális katasztrófa esetén a biztonsági mentést egy másik Azure-régióba lehessen visszaállítani. Ezt nevezzük geo-visszaállításnak. A RPO (helyreállítási pont célkitűzése) általában < 1 óra és az ERT (becsült helyreállítási idő – néhány perc – óra).

A kritikus fontosságú adatbázisok esetében Azure SQL Database ajánlatokat, aktív földrajzi replikálást. Ez lényegében azt eredményezi, hogy az eredeti adatbázis földrajzilag replikált másodlagos példányát egy másik régióban hozza létre. Ha például az adatbázis eredetileg az USA nyugati régiójában található, és a regionális katasztrófa-rugalmasságot kívánja használni. Az USA nyugati régiójában, az USA keleti régiójában lévő adatbázis aktív földrajzi replikáját érdemes létrehozni. Ha a szerencsétlenség az USA nyugati régiójában éri el a nehézségeket, a feladatátvételt az USA keleti régiójába helyezheti át. Az automatikus feladatátvételi csoportban való konfigurálás még jobb, mivel ez biztosítja, hogy az adatbázis automatikusan átadja a másodlagosnak az USA keleti régiójában vészhelyzet esetén. A RPO < 5 másodperc, az ERT < 30 másodperc.

Ha nincs konfigurálva automatikus feladatátvételi csoport, az alkalmazásnak aktívan figyelnie kell a katasztrófát, és feladatátvételt kell kezdeményezni a másodlagosnak. Akár 4 ilyen aktív földrajzi replika is létrehozható különböző Azure-régiókban. Még jobbá válik. Ezeket a másodlagos aktív földrajzi replikákat a csak olvasási hozzáféréshez is elérheti. Ez nagyon hasznos lehet a földrajzilag elosztott alkalmazási forgatókönyvek késésének csökkentése érdekében.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Hogyan változik a vész-helyreállítási terv a helyszínen a SQL Database

Összefoglalva, SQL Server a telepítőnek aktívan kell kezelnie a rendelkezésre állást olyan funkciókkal, mint a feladatátvételi fürtszolgáltatás, az adatbázis-tükrözés, a tranzakciós replikáció vagy a napló szállítása, valamint a biztonsági másolatok karbantartása és kezelése az üzletmenet folytonosságának biztosítása érdekében. A SQL Database segítségével a platform kezeli ezeket az alkalmazásokat, így az adatbázis-alkalmazás fejlesztésére és optimalizálására koncentrálhat, és nem kell aggódnia a katasztrófák kezelésével kapcsolatban. A biztonsági mentési és vész-helyreállítási terveket konfigurálhatja, és csak néhány kattintással dolgozhat a Azure Portalon (vagy a PowerShell API-kat használó néhány paranccsal).

További információ a vész-helyreállítási szolgáltatásról: [Azure SQL Database vész-helyreállítási 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Biztonság és megfelelőség

A SQL Database nagyon komolyan veszi a biztonságot és az adatvédelmet. A SQL Databaseon belüli biztonság az adatbázis szintjén és a platform szintjén érhető el, és a legjobban értelmezhető, ha több rétegbe van kategorizálva. Minden rétegben vezérelheti az alkalmazás optimális biztonságát. A rétegek a következők:

- Identitás & hitelesítés ([SQL-hitelesítés és Azure Active Directory [Azure ad]-hitelesítés](logins-create-manage.md)).
- Figyelési tevékenység ([auditálás](../../azure-sql/database/auditing-overview.md) és [fenyegetések észlelése](threat-detection-configure.md)).
- A tényleges adatok védelme ([transzparens adattitkosítás [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) és [Always encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- A bizalmas és a privilegizált adatokhoz való hozzáférés szabályozása ([sor szintű biztonság](/sql/relational-databases/security/row-level-security) és [dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking)).

A [Azure Security Center](https://azure.microsoft.com/services/security-center/) központosított biztonsági felügyeletet biztosít az Azure-ban, a helyszínen és más felhőkben futó munkaterhelések között. Megtekintheti, hogy az alapvető SQL Database-védelem, például a [naplózás](../../azure-sql/database/auditing-overview.md) és a [transzparens adattitkosítás [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) minden erőforráson konfigurálva van-e, és szabályzatokat hozhat létre a saját igényei alapján.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Milyen felhasználói hitelesítési módszereket kínálnak a SQL Database

A SQL Databaseban két hitelesítési módszer érhető el:

- [Azure Active Directory-hitelesítés](authentication-aad-overview.md)
- [SQL-hitelesítés](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

A hagyományos Windows-hitelesítés nem támogatott. Azure Active Directory (Azure AD) egy központi identitás-és hozzáférés-kezelési szolgáltatás. Ezzel kihasználhatja az egyszeri bejelentkezéses hozzáférést (SSO) a szervezet minden munkatársa számára. Ez azt jelenti, hogy a hitelesítő adatok az összes Azure-szolgáltatásban meg vannak osztva az egyszerűbb hitelesítéshez. 

Az Azure AD támogatja az [Azure ad multi-Factor Authentication](authentication-mfa-ssms-overview.md) , és [néhány kattintással](../../active-directory/hybrid/how-to-connect-install-express.md) integrálható az Azure ad-vel a Windows Server Active Directory. Az SQL-hitelesítés pontosan úgy működik, ahogy korábban is használta. Adja meg a felhasználónevet és a jelszót, és a felhasználókat egy adott kiszolgálón található adatbázishoz hitelesítheti. Ez lehetővé teszi az SQL Database és az Azure szinapszis Analytics számára, hogy Multi-Factor Authentication és vendég felhasználói fiókokat nyújtson az Azure AD-tartományon belül. Ha már rendelkezik egy Active Directory helyszíni szolgáltatással, a könyvtárat összevonása a Azure Active Directory segítségével bővítheti a címtárat az Azure-ban.

|**Ha...**|**SQL Database/Azure szinapszis-elemzés**|
|---|---|
|Inkább nem a Azure Active Directory (Azure AD) használata az Azure-ban|[SQL-hitelesítés](security-overview.md) használata|
|Az AD-t a helyszínen SQL Server használni|[ÖSSZEVONÁSA ad az Azure ad-vel](../../active-directory/hybrid/whatis-hybrid-identity.md), és használja az Azure ad-hitelesítést. Ezzel az egyszeri bejelentkezést is használhatja.|
|Meg kell kényszeríteni Multi-Factor Authentication|A [Microsoft feltételes hozzáférésének](conditional-access-configure.md)biztosításához a multi-Factor Authentication szabályzatot kell használni, és [Az Azure ad univerzális hitelesítést](authentication-mfa-ssms-overview.md)kell használnia multi-Factor Authentication-támogatással.|
|Legyenek a Microsoft-fiókok (live.com, outlook.com) vagy más tartományok (gmail.com) vendég fiókjai|Az [Azure ad univerzális hitelesítés](authentication-mfa-ssms-overview.md) használata SQL Database/adattárházban, amely az [Azure ad B2B-együttműködés](../../active-directory/external-identities/what-is-b2b.md)használatát teszi lehetővé.|
|Bejelentkezve a Windowsba egy összevont tartomány Azure AD-beli hitelesítő adataival|Az [Azure ad integrált hitelesítésének](authentication-aad-configure.md)használata.|
|Az Azure-ba nem összevont tartomány hitelesítő adataival vannak bejelentkezve a Windowsba|Az [Azure ad integrált hitelesítésének](authentication-aad-configure.md)használata.|
|Olyan közepes szintű szolgáltatásokkal kell rendelkezniük, amelyeknek SQL Database vagy Azure szinapszis Analyticshez kell csatlakozniuk|Az [Azure ad integrált hitelesítésének](authentication-aad-configure.md)használata.|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Az adatbázishoz való kapcsolódás Hogyan korlátozása vagy vezérlése

Az Ön rendelkezésére áll több olyan módszer is, amelyekkel optimális kapcsolati szervezetet érhet el az alkalmazáshoz.

- Tűzfalszabályok
- VNet-szolgáltatásvégpontok
- Fenntartott IP-címek

#### <a name="firewall"></a>Firewall

A tűzfal nem engedélyezi a hozzáférést a kiszolgálóhoz egy külső entitásból azáltal, hogy csak bizonyos entitások férhetnek hozzá a kiszolgálóhoz. Alapértelmezés szerint a kiszolgálón belüli adatbázisokhoz való összes kapcsolat nem engedélyezett, a többi Azure-szolgáltatástól érkező kapcsolatok kivételével (optionally7). Tűzfalszabály esetén a számítógép IP-címének a tűzfalon keresztüli engedélyezésével megnyithatja a kiszolgálóhoz való hozzáférést csak olyan entitások (például egy fejlesztői számítógép) számára, amelyeknek jóvá kell hagynia. Azt is lehetővé teszi, hogy olyan IP-címtartományt határozzon meg, amelyet engedélyezni szeretne a kiszolgálóhoz való hozzáféréshez. A szervezet fejlesztői számítógépének IP-címei például a tűzfal beállításai lapon egy tartomány megadásával adhatók hozzá.

A tűzfalszabályok a kiszolgáló szintjén vagy az adatbázis szintjén hozhatók létre. A kiszolgálói szintű IP-tűzfalszabályok a Azure Portal vagy a SSMS használatával hozhatók létre. A kiszolgáló-és adatbázis-szintű tűzfalszabály beállításával kapcsolatos további információkért lásd: [IP-tűzfalszabályok létrehozása a SQL Databaseban](secure-database-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Szolgáltatásvégpontok

Alapértelmezés szerint az adatbázis az "Azure-szolgáltatások elérésének engedélyezése kiszolgáló számára" beállításra van konfigurálva – ami azt jelenti, hogy az Azure-beli virtuális gépek megpróbálnak csatlakozni az adatbázishoz. Ezeknek a kísérleteknek még mindig meg kell kapniuk a hitelesítést. Ha azonban nem szeretné, hogy az adatbázis bármely Azure-beli IP-címen elérhető legyen, akkor letilthatja az "Azure-szolgáltatások elérésének engedélyezése a kiszolgálón" lehetőséget. Emellett a [VNet szolgáltatás-végpontokat](vnet-service-endpoint-rule-overview.md)is konfigurálhatja.

A szolgáltatási végpontok (SE) lehetővé teszik, hogy a kritikus Azure-erőforrásokat csak az Azure saját privát virtuális hálózatára tegye elérhetővé. Ezzel lényegében megszünteti az erőforrásaihoz való nyilvános hozzáférést. Az Azure-beli virtuális hálózat közötti forgalom az Azure gerinc hálózatán marad. Az SE használata nélkül kényszerített bújtatású csomagok útválasztása. A virtuális hálózata kényszeríti az internetes forgalmat a szervezet és az Azure-szolgáltatás forgalmára, hogy ugyanarra az útvonalra lépjen át. A szolgáltatási végpontokkal optimalizálhatja ezt, mivel a csomagok közvetlenül a virtuális hálózatról az Azure gerinces hálózaton lévő szolgáltatásba áramlanak.

![VNet-szolgáltatásvégpontok](./media/manage-data-after-migrating-to-database/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Fenntartott IP-címek

Egy másik lehetőség, hogy kiépítse a virtuális gépek számára [fenntartott IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) -címeket, és hozzáadja az adott virtuális gép IP-címeit a kiszolgáló tűzfalának beállításaiban. A fenntartott IP-címek hozzárendelésével megtakaríthatja a problémát, hogy a tűzfalszabályok módosításával módosítsák az IP-címeket.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Milyen porton csatlakozik SQL Database

1433-es port. SQL Database a porton keresztül kommunikál. A vállalati hálózaton belülről való kapcsolódáshoz hozzá kell adnia egy kimenő szabályt a szervezet tűzfal-beállításaiban. A 1433-es port az Azure határán kívül kerül kitéve.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Hogyan figyelhető meg és szabályozható a kiszolgáló és az adatbázis tevékenysége SQL Database

#### <a name="sql-database-auditing"></a>SQL Database naplózás

A SQL Database bekapcsolhatja a naplózást az adatbázis eseményeinek nyomon követéséhez. [SQL Database naplózás](../../azure-sql/database/auditing-overview.md) rögzíti az adatbázis eseményeit, és egy naplófájlba írja azokat az Azure Storage-fiókjában. A naplózás különösen akkor hasznos, ha szeretne betekintést nyújtani a lehetséges biztonsági és szabályzatok megsértésére, a szabályozási megfelelőség fenntartására stb. Lehetővé teszi, hogy definiáljon és konfiguráljon bizonyos, a naplózásra szoruló eseményeket, amelyek alapján előre konfigurált jelentéseket és irányítópultokat kaphat, hogy áttekintést kapjon az adatbázisban előforduló eseményekről. Ezeket a naplózási házirendeket az adatbázis szintjén vagy a kiszolgáló szintjén is alkalmazhatja. Útmutató a kiszolgálók/adatbázisok naplózásának bekapcsolásához: [SQL Database naplózás engedélyezése](secure-database-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Fenyegetések észlelése

A [fenyegetések észlelése](threat-detection-configure.md)lehetővé teszi, hogy az auditálás által felderített biztonsági vagy szabályzatok megsértésével járjon el. Nem kell biztonsági szakértőnek lennie ahhoz, hogy foglalkozzon a lehetséges fenyegetésekkel vagy a rendszer megsértésével. A veszélyforrások észlelése olyan beépített képességekkel is rendelkezik, mint az SQL-injektálási észlelés. Az SQL-injektálás megkísérli az adatmódosítást vagy az adatsérülést, valamint az adatbázis-alkalmazások általános megtámadását. A veszélyforrások észlelése több olyan algoritmust futtat, amely észleli a potenciális biztonsági réseket és az SQL-injektálási támadásokat, valamint a rendellenes adatbázis-hozzáférési mintákat (például a hozzáférést szokatlan helyről vagy egy ismeretlen résztvevőtől). A biztonsági tisztviselők vagy más kijelölt rendszergazdák e-mailben értesítést kapnak, ha fenyegetést észlelnek az adatbázison. Minden értesítés részletesen ismerteti a gyanús tevékenységeket, és javaslatokat tesz a fenyegetés további kivizsgálására és enyhítésére. A veszélyforrások észlelésének bekapcsolásával kapcsolatos további információkért lásd: a [fenyegetések észlelésének engedélyezése](secure-database-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Az adataik védelme Hogyan általában SQL Database

A titkosítás erős mechanizmust biztosít a behatolókkal szembeni bizalmas adatok védelméhez és biztonságossá tételéhez. A titkosított adatai nem használhatók a behatolóhoz a visszafejtési kulcs nélkül. Így egy további védelmi réteget is felhasznál a SQL Database beépített biztonsági rétegének tetején. Az adatok védelmének két aspektusa van SQL Databaseban:

- Az adatok és naplófájlok adatait tartalmazó adatok
- A repülés közben tárolt adatai

A SQL Database alapértelmezés szerint az adatok és naplófájlok a tárolási alrendszerben tárolt adatai teljesen és mindig titkosítva vannak a [transzparens adattitkosítás [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használatával. A biztonsági másolatok is titkosítva vannak. A TDE esetében nem szükséges módosítani az alkalmazás oldalán az adatokhoz való hozzáférést. A titkosítás és a visszafejtés transzparens módon történik; Ezért a név.
A bizalmas adatok repülés közbeni és nyugalmi állapotban való védelme érdekében SQL Database a [Always encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine)nevű szolgáltatást nyújt. Az AE az ügyféloldali titkosítás egy formája, amely titkosítja a bizalmas oszlopokat az adatbázisban (így rejtjelezett az adatbázis-rendszergazdák és a jogosulatlan felhasználók számára). A kiszolgáló fogadja a titkosított adatvesztést. A Always Encrypted kulcsát az ügyfél oldalán is tárolja a rendszer, így csak a hitelesítő ügyfelek tudják visszafejteni a bizalmas oszlopokat. A kiszolgáló és az adatok rendszergazdái nem láthatják a bizalmas adatokat, mivel a titkosítási kulcsok tárolása az ügyfélen történik. Az AE a bizalmas oszlopokat a tábla végétől végéig titkosítja, a jogosulatlan ügyfelektől a fizikai lemezig. Az AE támogatja a mai egyenlőség-összehasonlításokat, így a Adattervezők továbbra is lekérdezheti a titkosított oszlopokat az SQL-parancsaik részeként. Always Encrypted számos kulcstároló-lehetőséggel használható, például a [Azure Key Vault](always-encrypted-azure-key-vault-configure.md), a Windows tanúsítványtároló és a helyi hardveres biztonsági modulok használatával.

|**Jellemzők**|**Always Encrypted**|**Transzparens adattitkosítás**|
|---|---|---|
|**Titkosítási tartomány**|Végpontok közötti|Rest-adatok|
|**A kiszolgáló bizalmas adatokat tud elérni**|No|Igen, mivel az inaktív adatok titkosítása|
|**Engedélyezett T-SQL-műveletek**|Egyenlőség összehasonlítása|Az összes T-SQL Surface terület elérhető|
|**A funkció használatához szükséges módosítások**|Minimális|Nagyon minimális|
|**Titkosítási részletesség**|Oszlop szintje|az adatbázis-szintű megadás helyett|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Hogyan lehet korlátozni a bizalmas adatokhoz való hozzáférést az adatbázisban

Minden alkalmazásnak van egy bizonyos kis bizalmas adata az adatbázisban, amelyet védeni kell mindenki számára. A szervezeten belül bizonyos munkatársaknak meg kell tekinteniük ezeket az adatfájlokat, azonban mások nem tekinthetik meg ezeket az adatfájlokat. Az egyik példa az alkalmazottak bérét. A feletteseknek azonban hozzá kell férniük a közvetlen jelentésekhez tartozó bér-információhoz, de az egyes csapattagok nem férhetnek hozzá a társaik bér-adataihoz. Egy másik forgatókönyv olyan adatfejlesztő, aki a fejlesztési fázisokban vagy tesztelésben, például az ügyfelek SSNs a bizalmas adatokkal is működhet. Ezt az információt nem kell a fejlesztőnek kitenni. Ilyen esetekben a bizalmas adatokat el kell takarni vagy egyáltalán nem kell kitenni. SQL Database két ilyen megközelítést biztosít annak megakadályozása érdekében, hogy jogosulatlan felhasználók ne tudják megtekinteni a bizalmas adatokat:

A [dinamikus adatmaszkolás](dynamic-data-masking-overview.md) egy adatmaszkolási funkció, amely lehetővé teszi, hogy az alkalmazás rétegében lévő nem Kiemelt felhasználók számára maszkolással korlátozza a bizalmas adatokra való adatvédelmet. Olyan maszkolási szabályt határozhat meg, amely létrehozhat egy maszkolási mintát (például hogy csak az utolsó négy számjegyet jelenítse meg az SSN: XXX-XX-0000 és a legtöbbt XS-ként megjelölve), és azonosítsa, hogy mely felhasználókat kívánja kizárni a maszkolási szabályból. A maszkolás menet közben történik, és különböző maszkolási funkciók érhetők el különböző adatkategóriákhoz. A dinamikus adatmaszkolás lehetővé teszi, hogy automatikusan azonosítsa a bizalmas adatokat az adatbázisban, és alkalmazza a maszkolást.

A [sorok szintjének biztonsága](/sql/relational-databases/security/row-level-security) lehetővé teszi a hozzáférés vezérlését a sor szintjén. Ez azt jelenti, hogy a lekérdezést végrehajtó felhasználó (csoporttagság vagy végrehajtási környezet) alapján bizonyos sorok rejtettek. A hozzáférési korlátozás az alkalmazási réteg helyett az adatbázis-rétegen történik, így egyszerűbbé válik az alkalmazás logikája. Első lépésként hozzon létre egy szűrő predikátumot, és szűrje ki azokat a sorokat, amelyek nem lesznek elérhetők, és a következő biztonsági házirend határozza meg, hogy ki férhet hozzá ezekhez a sorokhoz. Végül a végfelhasználó futtatja a lekérdezést, és a felhasználó jogosultsága alapján megtekinti ezeket a korlátozott sorokat, vagy egyáltalán nem láthatja őket.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Hogyan a titkosítási kulcsok kezelése a felhőben

A Always Encrypted (ügyféloldali titkosítás) és a transzparens adattitkosítás (inaktív titkosítás) kulcsfontosságú felügyeleti lehetőségei vannak. Javasoljuk, hogy rendszeresen forgassa el a titkosítási kulcsokat. A rotációs gyakoriságnak igazodnia kell a belső szervezeti szabályozáshoz és a megfelelőségi követelményekhez.

#### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

Létezik egy kétkulcsos hierarchia a TDE-ben – az egyes felhasználói adatbázisokban lévő adatok titkosítása szimmetrikus AES-256 adatbázis-egyedi adatbázis-titkosítási kulcs (ADATTITKOSÍTÁSI kulcsot) használatával történik, amely viszont egy kiszolgáló-egyedi aszimmetrikus RSA 2048-főkulcs titkosítása. A főkulcs kezelése a következőkkel végezhető el:

- A platform által automatikusan SQL Database.
- Vagy ha a [Azure Key Vault](always-encrypted-azure-key-vault-configure.md) használja a Key Store-ban.

Alapértelmezés szerint a transzparens adattitkosítás főkulcsát a SQL Database szolgáltatás kezeli a kényelem érdekében. Ha a szervezet a főkulcs feletti irányítást szeretné vezérelni, akkor lehetőség van Azure Key Vault] (mindig titkosított-Azure-Key-Vault-configure.md) kulcs-tárolóként való használatára. A Azure Key Vault használatával a szervezete a kulcs kiépítésének, a rotációs és az engedélyek vezérlésének szabályozását feltételezi. A [TDE főkulcs típusának elforgatása vagy átváltása](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) gyors, mivel csak a adattitkosítási kulcsot újra titkosítja. A biztonsági és adatkezelési szerepköröket elválasztó szervezetek számára a biztonsági rendszergazda kiépítheti a TDE főkulcsának kulcsát a Azure Key Vaultban, és megAzure Key Vault adhatja az adatbázis-rendszergazda számára, hogy a kiszolgálón ne használja a titkosítást. A Key Vault úgy van kialakítva, hogy a Microsoft ne tekintse meg vagy ne bontsa ki a titkosítási kulcsokat. A szervezethez tartozó kulcsok központi felügyeletét is elérheti.

#### <a name="always-encrypted"></a>Always Encrypted

A Always Encrypted egy [kétkulcsos hierarchia](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) is van – a bizalmas adatok egy oszlopa titkosítva van egy AES 256-oszlopos titkosítási kulccsal (CEK), amelyet egy oszlop főkulcsa (CMK) titkosít. A Always Encryptedhoz megadott ügyfél-illesztőprogramok nem korlátozzák a CMKs hosszát. A CEK titkosított értékét a rendszer az adatbázisban tárolja, és a CMK megbízható kulcs-tárolóban, például a Windows tanúsítványtárolóban, Azure Key Vault vagy hardveres biztonsági modulban tárolja.

- A [CEK és a CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) is elforgatható.
- A CEK forgása az adatműveletek mérete, és a titkosított oszlopokat tartalmazó táblák méretétől függően időigényes lehet. Ezért körültekintően kell megtervezni a CEK elforgatását.
- A CMK rotációja azonban nem zavarja az adatbázis teljesítményét, és külön szerepkörökkel végezhető el.

A következő ábrán az oszlopok főkulcsaihoz tartozó kulcstároló-beállítások láthatók Always Encrypted

![Mindig titkosított CMK-tároló szolgáltatók](./media/manage-data-after-migrating-to-database/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Hogyan optimalizálható és biztonságossá tehető a szervezet és a SQL Database közötti forgalom

A szervezet és a SQL Database közötti hálózati forgalom általában a nyilvános hálózaton keresztül lesz átirányítva. Ha azonban úgy dönt, hogy optimalizálja ezt az elérési utat, és biztonságosabbá teszi, megtekintheti az Azure-ExpressRoute. A ExpressRoute lényegében lehetővé teszi a vállalati hálózat kibővítését az Azure platformra privát kapcsolaton keresztül. Ezzel nem a nyilvános interneten halad át. Magasabb szintű biztonságot, megbízhatóságot és útválasztási optimalizációt is kap, amely alacsonyabb hálózati késéseket és gyorsabb sebességet tesz lehetővé, mint általában a nyilvános interneten. Ha azt tervezi, hogy a szervezet és az Azure közötti jelentős adatmennyiséget szeretne átvinni, a ExpressRoute használatával költségmegtakarítást eredményezhet. Három különböző csatlakozási modell közül választhat a szervezet és az Azure közötti kapcsolathoz:

- [Felhőalapú Exchange közös elhelyezés](../../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Bármely elemek között](../../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Pont – pont](../../expressroute/expressroute-connectivity-models.md#Ethernet)

A ExpressRoute azt is lehetővé teszi, hogy a megvásárolt sávszélesség legfeljebb 2x-re legyen feldolgozva, felár nélkül. Lehetőség van a régiók közötti kapcsolat konfigurálására is a ExpressRoute használatával. A ExpressRoute-kapcsolatok szolgáltatóinak listáját a következő témakörben tekintheti meg: [ExpressRoute Partners és peering Locations](../../expressroute/expressroute-locations.md). A következő cikkek részletesebben ismertetik az expressz útvonalat:

- [Az expressz útvonal bemutatása](../../expressroute/expressroute-introduction.md)
- [Előfeltételek](../../expressroute/expressroute-prerequisites.md)
- [Munkafolyamatok](../../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>A SQL Database megfelel a szabályozás követelményeinek, és hogyan segíti a saját szervezete megfelelőségét

SQL Database megfelel a szabályozási compliancies. A SQL Database által teljesített compliancies legújabb készletének megtekintéséhez látogasson el a [Microsoft adatvédelmi központba](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , és tekintse meg a szervezet számára fontos compliancies, és ellenőrizze, hogy az SQL Database szerepel-e a megfelelő Azure-szolgáltatásokban. Fontos megjegyezni, hogy bár a SQL Database megfelelőségi szolgáltatásként is megtekinthetők, az informatikai részleg a szervezet szolgáltatásának megfelelőségét segíti elő, de nem garantálja automatikusan.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Intelligens adatbázis figyelése és karbantartása az áttelepítés után

Miután áttelepítette az adatbázist SQL Databasere, figyelni fogja az adatbázist (például ellenőrizze, hogy az Erőforrás kihasználtsága hasonló-e vagy DBCC-e), és rendszeres karbantartást végez (például újraépíti vagy átrendezi az indexeket, statisztikákat stb.). Szerencsére SQL Database intelligens abban az értelemben, hogy a korábbi trendeket és a rögzített mérőszámokat és statisztikákat használja az adatbázis figyelésére és karbantartására, hogy az alkalmazás optimálisan fusson. Bizonyos esetekben a Azure SQL Database a konfiguráció beállításától függően automatikusan végezhet karbantartási feladatokat. Az adatbázis figyelésének három aspektusa van SQL Databaseban:

- Teljesítmény monitorozása és optimalizálása.
- Biztonsági optimalizálás.
- A Cost optimalizálása.

### <a name="performance-monitoring-and-optimization"></a>Teljesítményfigyelés és optimalizálás

A lekérdezési teljesítmény-ellenőrzésekkel személyre szabott javaslatokat kaphat az adatbázis-számítási feladatokhoz, így az alkalmazások optimális szinten futhatnak – mindig. Azt is beállíthatja, hogy a javaslatok automatikusan érvényesüljenek, és nem kell bajlódnia a karbantartási feladatok elvégzéséhez. A SQL Database Advisor segítségével automatikusan megvalósíthatja az indexelési javaslatokat a számítási feladatok alapján. Ez az úgynevezett automatikus hangolás. A javaslatok úgy alakulnak ki, ahogy az alkalmazás munkaterhelése megváltozik, hogy megadja a legfontosabb javaslatokat. Lehetősége van arra is, hogy manuálisan áttekintse ezeket az ajánlásokat, és alkalmazza őket saját belátása szerint.  

### <a name="security-optimization"></a>Biztonsági optimalizálás

A SQL Database végrehajtható biztonsági javaslatokat nyújt, amelyekkel biztonságossá teheti az adatait és a fenyegetések észlelését a gyanús adatbázis-tevékenységek azonosításához és kivizsgálásához, amelyek potenciális szálat jelenthetnek az adatbázis számára. A [sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy adatbázis-ellenőrzési és jelentéskészítési szolgáltatás, amely lehetővé teszi, hogy az adatbázisok biztonsági állapotát nagy léptékben figyelje, és azonosítsa a biztonsági kockázatokat és a drifteket az Ön által meghatározott biztonsági alaptervből. Minden vizsgálat után meg kell adni a végrehajtható lépések és szervizelési parancsfájlok testreszabott listáját, valamint egy értékelési jelentést, amely a megfelelőségi követelmények teljesítéséhez használható.

A Azure Security Center segítségével azonosíthatja a biztonsági javaslatokat a tábla minden részén, és egyetlen kattintással alkalmazhatja őket.

### <a name="cost-optimization"></a>Költségoptimalizálás

Az Azure SQL platform a kiszolgáló adatbázisaiban elemzi a kihasználtsági előzményeket, hogy kiértékelje és javasolja a költséghatékony optimalizálási lehetőségeket. Ez az elemzés általában hetente két hetet vesz igénybe, és felépíti a gyakorlatban alkalmazható ajánlásokat. A rugalmas készlet egy ilyen lehetőség. Az ajánlás a portálon szalagcímként jelenik meg:

![rugalmas készletre vonatkozó javaslatok](./media/manage-data-after-migrating-to-database/elastic-pool-recommendations.png)

Az elemzést az "Advisor" szakaszban is megtekintheti:

![rugalmas készletre vonatkozó javaslatok – Advisor](./media/manage-data-after-migrating-to-database/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Hogyan a teljesítmény és az erőforrás kihasználtságának figyelése a SQL Database

SQL Database a platform intelligens elemzéseit kihasználva figyelheti a teljesítményt, és ennek megfelelően hangolhatja. A teljesítmény-és erőforrás-kihasználtságot a következő módszerekkel figyelheti SQL Databaseban:

#### <a name="azure-portal"></a>Azure Portal

A Azure Portal egy adatbázis kihasználtságát jeleníti meg az adatbázis kiválasztásával, majd az Áttekintés ablaktáblán a diagramra kattintva. A diagramot úgy módosíthatja, hogy több metrikát jelenítsen meg, például a CPU-százalékot, a DTU százalékát, az adatok IO-százalékos arányát, a munkamenetek százalékos arányát és az adatbázis méretét

![Figyelési diagram](./media/manage-data-after-migrating-to-database/monitoring-chart.png)

![Chart2 figyelése](./media/manage-data-after-migrating-to-database/chart.png)

Ebből a diagramból erőforrás alapján is konfigurálhatja a riasztásokat. Ezek a riasztások lehetővé teszik, hogy válaszoljon az erőforrásokra vonatkozó feltételekre e-mailben, írjon egy HTTPS/HTTP-végpontra, vagy hajtson végre egy műveletet. További információt a [riasztások létrehozása](alerts-insights-configure-portal.md)című témakörben talál.

#### <a name="dynamic-management-views"></a>Dinamikus felügyeleti nézetek

Lekérdezheti az [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dinamikus felügyeleti nézetet, hogy az elmúlt órában visszaállítsa az erőforrás-felhasználás statisztikáinak előzményeit, valamint a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) Rendszerkatalógus nézetet az elmúlt 14 nap előzményeinek visszaküldéséhez.

#### <a name="query-performance-insight"></a>Lekérdezési terheléselemző

[Lekérdezési terheléselemző](query-performance-insight-use.md) lehetővé teszi, hogy megtekintse a leggyakoribb erőforrás-felhasználású lekérdezések előzményeit, valamint egy adott adatbázis hosszan futó lekérdezéseit. Gyorsan azonosíthatja a leggyakoribb lekérdezéseket az erőforrások kihasználtsága, az időtartam és a végrehajtás gyakorisága alapján. A lekérdezéseket nyomon követheti és visszaállíthatja a regressziót. Ehhez a szolgáltatáshoz a [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) engedélyezése szükséges, és az adatbázis aktív.

![Lekérdezési terheléselemző](./media/manage-data-after-migrating-to-database/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL Analytics (előzetes verzió) Azure Monitor naplókban

[Azure monitor naplók](../../azure-monitor/insights/azure-sql.md) lehetővé teszik a kulcsfontosságú Azure SQL Database teljesítmény-metrikák összegyűjtését és megjelenítését, a legfeljebb 150 000 adatbázis és a 5 000 SQL rugalmas készletek használatát munkaterületen. Az értesítések figyelésére és fogadására használható. Több Azure-előfizetéshez és rugalmas készlethez is figyelheti SQL Database és rugalmas készlet mérőszámait, és felhasználhatja az egyes alkalmazási veremben előforduló problémák azonosítására.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Észrevettem a teljesítménnyel kapcsolatos problémákat: hogyan különböznek a SQL Database hibaelhárítási módszerei a SQL Server

A lekérdezési és adatbázis-teljesítménnyel kapcsolatos problémák diagnosztizálásához használni kívánt hibaelhárítási technikák jelentős része változatlan marad. Azt követően, hogy az összes adatbázis-motor felhatalmazott a felhőre. A platform-Azure SQL Database azonban a "Intelligence"-ben készült. Megkönnyíti a teljesítménnyel kapcsolatos problémák megoldását és diagnosztizálását. Emellett elvégezheti ezeket a javítási műveleteket az Ön nevében, és bizonyos esetekben proaktív módon kijavítani azokat – automatikusan.

A teljesítménnyel kapcsolatos problémák megoldásának megközelítése jelentősen kihasználhatja az olyan intelligens funkciók használatát, mint például a [lekérdezési terheléselemző (QPI)](query-performance-insight-use.md) és a [Database Advisor](database-advisor-implement-performance-recommendations.md) , és így a módszertanban bekövetkező különbség különbözik ebben a tekintetben. a továbbiakban nem kell elvégezni a manuális munkát, amely segíthet a probléma megoldásában. A platform a nehezen használható. Egy példa erre a QPI. A QPI segítségével egészen a lekérdezés szintjéig lehatolhat, és megtekintheti a korábbi trendeket, és kiderítheti, hogy pontosan a lekérdezés romlott. A Database Advisor javaslatokat tesz olyan dolgokra, amelyek segíthetnek az általános teljesítmény növelésében, például a hiányzó indexek, az indexek eldobása, a lekérdezések parameterizing stb.

A teljesítménnyel kapcsolatos hibaelhárítás során fontos megállapítani, hogy csak az alkalmazás vagy az adatbázis biztonsági mentése van-e hatással az alkalmazás teljesítményére. A teljesítménnyel kapcsolatos probléma gyakran az alkalmazás rétegében rejlik. Ez lehet az architektúra vagy az adatelérési minta. Tegyük fel például, hogy egy olyan csevegési alkalmazás van, amely érzékeny a hálózati késésre. Ebben az esetben az alkalmazás az alkalmazás és a kiszolgáló, illetve egy zsúfolt hálózat között sok rövid kérelem ("beszédes") miatt válik elérhetővé, és a szolgáltatás gyorsan felveszi a kapcsolatot. Ebben az esetben a teljesítmény javítása érdekében [kötegelt lekérdezéseket](performance-guidance.md#batch-queries)használhat. A batchs használatával rendkívül nagy mértékben biztosíthatja, hogy a kérések feldolgozása egy kötegben történjen. így segítve az adatelérési idő csökkentését és az alkalmazások teljesítményének növelését.

Továbbá, ha az adatbázis általános teljesítményében romlást tapasztal, figyelheti a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) és [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dinamikus felügyeleti nézeteket a CPU, az IO és a memóriahasználat megismerése érdekében. Lehetséges, hogy az Ön teljesítménye hatással van az erőforrásokra. Előfordulhat, hogy módosítania kell a számítási méretet és/vagy a szolgáltatási szintet a növekvő és a csökkenő munkaterhelési igények alapján.

A teljesítménnyel kapcsolatos problémák hangolásával kapcsolatos javaslatok széles körét lásd: [az adatbázis finomhangolása](performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Hogyan arról, hogy a megfelelő szolgáltatási szintet és számítási méretet használom

A SQL Database különböző szolgáltatási szinteket kínál alapszintű, standard és prémium szintű szolgáltatásként. Minden szolgáltatási szinten a szolgáltatás szintjéhez kötött garantált kiszámítható teljesítmény fog megjelenni. A számítási feladattól függően előfordulhat, hogy az Erőforrás-kihasználtsága olyan tevékenységgel rendelkezik, amelynél az erőforrás-felhasználás a jelenlegi számítási méret felső határát elérheti. Ilyen esetekben érdemes először kipróbálni, hogy a hangolás segíthet-e (például egy index hozzáadásával vagy módosításával stb.). Ha továbbra is problémákba ütközik a korlátozások között, érdemes lehet magasabb szolgáltatási szintet vagy számítási méretet áthelyezni.

|**Szolgáltatási szint**|**Gyakori használati esetek**|
|---|---|
|**Basic**|Egy maroknyi felhasználóval és egy olyan adatbázissal, amely nem rendelkezik magas párhuzamosságtal, méretezéssel és teljesítménnyel kapcsolatos követelményekkel. |
|**Standard**|A jelentős párhuzamosságtal, méretezéssel és teljesítménnyel kapcsolatos követelményekkel rendelkező alkalmazások alacsony – közepes i/o-igényekkel párosulnak. |
|**Prémium**|Számos egyidejű felhasználóval, magas CPU/memóriával és magas i/o-igényű alkalmazásokkal. A magas Egyidejűség, a nagy átviteli sebesség és a késésre érzékeny alkalmazások kihasználhatják a prémium szintet. |
|||

A megfelelő számítási méret biztosításához a "Hogyan a SQL Database teljesítményének és erőforrás-kihasználtságának figyelése" című részében felsorolt módszerek egyikén keresztül figyelheti a lekérdezési és az adatbázis-erőforrás-felhasználást. Ha úgy találja, hogy a lekérdezések/adatbázisok folyamatosan futnak a PROCESSZORon vagy a memórián, stb., érdemes lehet nagyobb számítási méretre méretezni. Hasonlóképpen, ha úgy gondolja, hogy a csúcsidőben még nem használja az erőforrásokat, érdemes megfontolni a méretezést az aktuális számítási mérettől.

Ha SaaS-alkalmazási mintát vagy adatbázis-konszolidációs forgatókönyvet használ, érdemes rugalmas készletet használni a Cost Optimization számára. A rugalmas készlet nagyszerű lehetőséget biztosít az adatbázisok összevonására és a költséghatékonyság optimalizálására. Ha többet szeretne megtudni több adatbázis rugalmas készlettel történő kezeléséről, olvassa el a következő témakört: [készletek és adatbázisok kezelése](elastic-pool-manage.md#azure-portal).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Milyen gyakran van szükség az adatbázis-integritási ellenőrzések futtatására az adatbázison

A SQL Database olyan intelligens technikákat használ, amelyek lehetővé teszik bizonyos adatsérülések automatikus kezelését, adatvesztés nélkül. Ezek a technikák beépítettek a szolgáltatásba, és a szolgáltatás igénybe veszik a szolgáltatást. Az adatbázis biztonsági másolatait rendszeresen teszteli a szolgáltatáson keresztül, és a DBCC CHECKDB UTASÍTÁST futtatja. Ha problémák merülnek fel, SQL Database proaktív módon kezeli őket. Az automatikus oldal javítása kihasználható a sérült vagy adatintegritási problémákkal rendelkező lapok [kijavításához](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) . Az adatbázis-lapokat mindig az alapértelmezett ELLENŐRZŐÖSSZEG-beállítással ellenőrzik, amely ellenőrzi az oldal integritását. SQL Database proaktív módon figyeli és áttekinti az adatbázis adatintegritását, és ha problémák merülnek fel, a legmagasabb prioritással kezeli őket. Ezen kívül dönthet úgy is, hogy opcionálisan futtatja saját integritási ellenőrzéseit is.  További információ: az [adatintegritás SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Adatáthelyezés az áttelepítés után

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database-using-the-azure-portal"></a>Hogyan az adatok exportálása és importálása BACPAC-fájlként a SQL Database használatával a Azure Portal

- **Exportálás**: az adatbázis Azure SQL Database BACPAC fájlként is exportálható a Azure Portal

   ![Adatbázis-exportálás](./media/manage-data-after-migrating-to-database/database-export1.png)

- **Importálás**: az BACPAC-fájlként importálhatja az adatait Azure SQL Database adatbázisba a Azure Portal használatával.

   ![adatbázis importálása](./media/manage-data-after-migrating-to-database/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Hogyan SQL Database és SQL Server közötti adatszinkronizálás

Ezt többféleképpen is elérheti:

- **[Adatszinkronizálás](sql-data-sync-data-sql-server-sql-database.md)** – ez a funkció segítséget nyújt a kétirányú adatszinkronizáláshoz több SQL Server adatbázis és SQL Database között. SQL Server adatbázisokkal való szinkronizáláshoz telepítenie és konfigurálnia kell a szinkronizálási ügynököt egy helyi számítógépen vagy virtuális gépen, és meg kell nyitnia a 1433-es kimenő TCP-portot.
- **[Tranzakciós](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** replikáció – a tranzakciós replikációval szinkronizálhatja az adatokat egy SQL Server adatbázisból, hogy Azure SQL Database a közzétevő és az előfizető Azure SQL Database SQL Server példányával. Egyelőre csak ez a beállítás támogatott. Az adatok SQL Server adatbázisból az Azure SQL-be minimális állásidővel való áttelepítésével kapcsolatos további információkért lásd: a [tranzakciós replikáció használata](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Következő lépések

A [SQL Database](sql-database-paas-overview.md)megismerése.