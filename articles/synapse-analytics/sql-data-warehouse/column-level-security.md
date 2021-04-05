---
title: Oszlop szintű biztonság dedikált SQL-készlethez
description: A Column-Level biztonság lehetővé teszi az ügyfelek számára, hogy az adatbázis-táblázat oszlopaihoz való hozzáférést a felhasználó végrehajtási környezete vagy csoporttagság alapján szabályozzák, leegyszerűsítve az alkalmazás biztonságának megtervezését és kódolását, és lehetővé teszi az oszlopok hozzáférésére vonatkozó korlátozások megvalósítását.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: fb34051f7d4b24190806dde939c8cc6d9c2a4896
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679947"
---
# <a name="column-level-security"></a>Oszlopszintű biztonság

A Column-Level biztonság lehetővé teszi, hogy az ügyfelek a felhasználó végrehajtási környezete vagy csoporttagság alapján szabályozzák a táblákhoz való hozzáférést.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Mivel ez a videó közzé lett téve, a [soros szintű biztonság](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) elérhetővé vált a dedikált SQL-készlet számára az Azure szinapszisban.

Az oszlop szintű biztonság leegyszerűsíti az alkalmazás biztonságának megtervezését és kódolását, ami lehetővé teszi az oszlopokhoz való hozzáférés korlátozását a bizalmas adatok védelme érdekében. Tegyük fel például, hogy egy adott felhasználó csak a saját részlegéhez tartozó táblázat bizonyos oszlopaihoz fér hozzá. A hozzáférés-korlátozási logika az adatbázis-szinten található, nem pedig egy másik alkalmazási szinten lévő adatoktól. Az adatbázis minden alkalommal alkalmazza a hozzáférési korlátozásokat, amikor az adathozzáférés bármely szintjéről megkísérelhető. Ez a korlátozás megbízhatóbb és robusztus biztonságot tesz lehetővé azáltal, hogy csökkenti az általános biztonsági rendszerek felületi területét. Emellett az oszlop szintű biztonság is szükségtelenné teszi a nézetek bevezetését, hogy kiszűrje a hozzáférési korlátozásokat a felhasználók számára.

Az oszlop szintű biztonság a [Grant](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL-utasítással valósítható meg. Ezzel a mechanizmussal az SQL és a Azure Active Directory (Azure AD) hitelesítés is támogatott.

![A diagramon egy sematikus táblázat látható, amelyben az első oszlop egy zárt lakattal és a hozzá tartozó cellákkal narancssárga színű, míg a többi oszlop fehér cella.](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntax

```syntaxsql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Példa

A következő példa azt mutatja be, hogyan lehet korlátozni a `TestUser` `SSN` tábla oszlopának elérését `Membership` :

A `Membership` közösségi biztonsági számok tárolására szolgáló SSN oszlop létrehozásával létrehozott tábla:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Az `TestUser` összes oszlop elérésének engedélyezése a bizalmas adatokkal rendelkező SSN oszlop kivételével:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

A másként végrehajtott lekérdezések `TestUser` sikertelenek lesznek, ha tartalmazzák a SSN oszlopot:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Használati esetek

Néhány példa arra, hogyan használják az oszlop szintű biztonságot a mai napon:

- Egy pénzügyi szolgáltatói cég lehetővé teszi, hogy csak a fiókok kezelői férhessenek hozzá az ügyfél társadalombiztosítási számaihoz (SSN), a telefonszámokhoz és egyéb személyes adatokhoz.
- Az egészségügyi szolgáltató lehetővé teszi, hogy csak az orvosok és ápolók férhessenek hozzá bizalmas egészségügyi adatokhoz, miközben a számlázási részleg tagjai nem tekintik meg ezeket az adatokat.
