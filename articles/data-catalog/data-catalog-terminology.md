---
title: Azure Data Catalog terminológia
description: Ez a cikk a Azure Data Catalog dokumentációjában használt fogalmakat és kifejezéseket ismerteti.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 008d0f106b07d61f0989a479457b12398000b5d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674571"
---
# <a name="azure-data-catalog-terminology"></a>Azure Data Catalog terminológia

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Ez a cikk a Azure Data Catalog dokumentációjában használt fogalmakat és kifejezéseket ismerteti.

## <a name="catalog"></a>Katalógus

A Azure Data Catalog egy felhőalapú metaadat-tárház, amelyben az adatforrások és az adategységek regisztrálhatók. A katalógus központi tárolóhelyként szolgál az adatforrásokból kinyert szerkezeti metaadatokhoz, valamint a felhasználók által hozzáadott leíró metaadatokhoz.

## <a name="data-source"></a>Adatforrás

Az adatforrások olyan rendszerek vagy tárolók, amelyek az adategységeket kezelik. Ilyenek például SQL Server adatbázisok, Oracle-adatbázisok, SQL Server Analysis Services-adatbázisok (táblázatos vagy többdimenziós) és SQL Server Reporting Services-kiszolgálók.

## <a name="data-asset"></a>Adategység

Az adategységek olyan objektumok, amelyek a katalógusban regisztrálható adatforrásokon belül találhatók. Ilyenek például SQL Server táblák és nézetek, Oracle-táblák és nézetek, SQL Server Analysis Services mértékek, méretek és KPI-k, valamint SQL Server Reporting Services jelentések.

## <a name="data-asset-location"></a>Adategység helye

A katalógus egy adatforrás vagy adategység helyét tárolja, amely a forráshoz való kapcsolódáshoz használható ügyfélalkalmazás használatával. A hely formátuma és részletei az adatforrás típusától függően változnak. Például egy SQL Server tábla azonosítható a négy részének nevével, a kiszolgáló nevét, az adatbázis nevét, a séma nevét, az objektum nevét, míg a SQL Server Reporting Services jelentés az URL-címmel azonosítható.

## <a name="structural-metadata"></a>Szerkezeti metaadatok

A strukturális metaadatok az adategység szerkezetét leíró adatforrásból kinyert metaadatok. Ebbe beletartozik az eszközök helye, az objektum neve és típusa, valamint a további Type-specifikus jellemzők. A táblák és nézetek szerkezeti metaadatai például tartalmazzák az objektum oszlopainak nevét és adattípusait.

## <a name="descriptive-metadata"></a>Leíró metaadatok

A leíró metaadatok olyan metaadatok, amelyek egy adategység célját vagy szándékát írják le. Általában a Azure Data Catalog portál használatával adja hozzá a katalógus-felhasználók a leíró metaadatokat, de az adatforrásból is kinyerhető a regisztráció során. A Azure Data Catalog regisztrációs eszköz például Kinyeri a leírásokat a SQL Server Analysis Services és a SQL Server Reporting Services Description tulajdonságában, illetve a SQL Server-adatbázisok [ms_description kiterjesztett tulajdonságában](/previous-versions/sql/sql-server-2008-r2/ms190243(v=sql.105)) , ha ezek a tulajdonságok értékkel vannak feltöltve.

## <a name="request-access"></a>Hozzáférés kérése

Az adategység leíró metaadatai tartalmazhatják az adategységhez vagy az adatforráshoz való hozzáférés kérésének módját. Ezek az adatok az adategység helyeként jelennek meg, és az alábbi lehetőségek közül egyet vagy többet tartalmazhatnak:

* Az adatforráshoz való hozzáférés biztosításához felelős felhasználó vagy csoport e-mail-címe.
* A dokumentált folyamat URL-címe, amelyet a felhasználóknak követniük kell, hogy hozzáférjenek az adatforráshoz.
* Az adatforráshoz való hozzáféréshez használható identitás-és hozzáférés-kezelési eszköz (például Microsoft Identity Manager) URL-címe.
* Egy ingyenes szöveges bejegyzés, amely leírja, hogy a felhasználók hogyan férhetnek hozzá az adatforráshoz.

## <a name="preview"></a>Előnézet

A Azure Data Catalog egy előzetes verziója legfeljebb 20 rekordot tartalmaz, amelyeket a rendszer a regisztráció során kinyerheti az adatforrásból, és a katalógusban tárolhatja az adategység metaadatainak használatával. Az előzetes verzió segíthet az adategységeket felderítő felhasználók számára, hogy jobban megértsék a funkcióját és célját. Más szóval a mintaadatok meglátása értékesebb lehet, mint az oszlopnevek és az adattípusok.
Az előzetes verziók csak táblák és nézetek esetén támogatottak, és a felhasználónak explicit módon ki kell választania a regisztráció során.

## <a name="data-profile"></a>Adatprofil

A Azure Data Catalog adatprofilja egy olyan regisztrált adategységre vonatkozó táblázat szintű és oszlopos metaadatokat tartalmazó pillanatkép, amely az adatforrásból kinyerhető a regisztráció során, és a katalógusban az adategység metaadatainak használatával tárolható. Az adatprofil segíthet az adategységeket felderítő felhasználók számára, hogy jobban megértsék a funkcióját és célját. Az előzetes verziókhoz hasonlóan az adatprofilokat a felhasználónak explicit módon kell kiválasztania a regisztráció során.

> [!NOTE]
> Az adatprofil kinyerése költséges művelet lehet a nagyméretű táblák és nézetek esetében, és jelentősen növelheti az adatforrások regisztrálásához szükséges időt.


## <a name="user-perspective"></a>Felhasználói perspektíva

A Azure Data Catalogban bármely felhasználó leíró metaadatokat adhat meg a regisztrált adategységekhez. Minden felhasználónak külön perspektívája van az adatra és annak használatára. A kiszolgálókért felelős rendszergazda például megadhatja a szolgáltatói szerződés (SLA) vagy a biztonsági mentési ablakok részleteit; az adatkezelők az által támogatott üzleti folyamatokra mutató hivatkozásokat is tartalmazhatnak. az elemzők pedig leírást adhatnak a más elemzőknek leginkább megfelelő feltételekről, amelyek pedig az adatokat felderíteni és értelmezni igénylő felhasználók számára is hasznosak lehetnek.

Ezek a perspektívák természetüknél fogva értékesek, és Azure Data Catalog minden felhasználó megadhatja azokat az adatokat, amelyek hasznosak lehetnek számukra, míg az összes felhasználó ezeket az információkat felhasználhatja az adatok és annak céljának megismerésére.

## <a name="expert"></a>Szakértő

A szakértő olyan felhasználó, aki egy adategységre vonatkozó tájékozott "szakértői" perspektívával rendelkezik. Bármely felhasználó hozzáadhat önmagához vagy egy másik felhasználóhoz szakértőként egy adott eszközhöz. A szakértőként való Listázás nem nyújt további jogosultságokat a Azure Data Catalog; lehetővé teszi a felhasználók számára, hogy könnyen megtalálják azokat a perspektívákat, amelyek a legvalószínűbben hasznosak az eszköz leíró metaadatainak áttekintéséhez.

## <a name="owner"></a>Tulajdonos

A tulajdonos olyan felhasználó, aki további jogosultságokkal rendelkezik a Azure Data Catalog adategységének kezeléséhez. A felhasználók tulajdonba vehetik a regisztrált adategységeket, és a tulajdonosok más felhasználókat is hozzáadhatnak tulajdonosként. További információ: az [adategységek kezelése](data-catalog-how-to-manage.md)  

> [!NOTE]
> A tulajdonosi és a felügyeleti szolgáltatás csak a Azure Data Catalog standard kiadásában érhető el.

## <a name="registration"></a>Regisztráció

A regisztráció az adategység metaadatainak adatforrásból való kinyerésének és a Azure Data Catalog szolgáltatásba való másolásának a feladata. A regisztrált adategységeket ezután megjegyzéseket és felderíteni lehet.

## <a name="next-steps"></a>Következő lépések

[Gyors útmutató: Azure Data Catalog létrehozása](data-catalog-get-started.md)