---
title: Az Azure SQL Edge védelme
description: Tudnivalók az Azure SQL Edge biztonságáról
keywords: SQL Edge, biztonság
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56d5eb981aa02d9da83973d49e8df79fcd9c7e9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95021014"
---
# <a name="securing-azure-sql-edge"></a>Az Azure SQL Edge védelme

A IoT és az Edge számítástechnika számos iparágban való bevezetésének növekedésével növekszik az eszközök száma és az ezekből az eszközökből generált adatok mennyisége. A megnövekedett adatmennyiség és az eszköz-végpontok száma jelentős kihívást jelent az adatbiztonság és az eszközök tekintetében. 

Az Azure SQL Edge több funkciót és képességet kínál, amelyek viszonylag könnyebben biztonságossá teszik a IoT-adatSQL Server-adatbázisokon belüli adatvédelmet. Az Azure SQL Edge a Microsoft SQL Server és az Azure SQL-t is felhasználó SQL-motoron alapul, és ugyanazokat a biztonsági képességeket használja, így könnyebben kiterjesztheti a felhőből az Edge-re vonatkozó biztonsági házirendeket és gyakorlatokat.

Csakúgy, mint a Microsoft SQL Server és az Azure SQL, az Azure SQL Edge-környezetek biztonságossá tétele a következő négy területet érintő lépések sorozata lehet: a platform, a hitelesítés, az objektumok (beleértve az adatmennyiséget) és a rendszerhez hozzáférő alkalmazások. 

## <a name="platform-and-system-security"></a>Platform és rendszer biztonsága

Az Azure SQL Edge platformja magában foglalja a fizikai Docker-gazdagépet, a gazdagép operációs rendszerét, valamint a fizikai eszközt az alkalmazásokhoz és az ügyfelekhez összekötő hálózati rendszerekhez. 

A platform biztonságának megvalósítása a jogosulatlan felhasználók a hálózatról való megtartásával kezdődik. Az ajánlott eljárások némelyike többek között a következőkre korlátozódik:
- Tűzfalszabályok megvalósítása a szervezeti biztonsági házirend biztosításához. 
- Győződjön meg arról, hogy a fizikai eszközön lévő operációs rendszer rendelkezik a legújabb biztonsági frissítésekkel. 
- Az Azure SQL Edge-hez használt gazdagép-portok meghatározása és korlátozása
- Annak biztosítása, hogy a megfelelő hozzáférés-vezérlés minden olyan adatköteten érvényesüljenek, amely az Azure SQL Edge-alapú adatmennyiséget tárolja. 

Az Azure SQL Edge hálózati protokollokkal és TDS-végpontokkal kapcsolatos további információkért lásd: [hálózati protokollok és TDS-végpontok](/previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105)).

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés 

### <a name="authentication"></a>Hitelesítés  
A hitelesítés az a folyamat, amellyel a felhasználó igazolni kívánja. Az Azure SQL Edge jelenleg csak a `SQL Authentication` mechanizmust támogatja.

- *SQL-hitelesítés*:

    Az SQL-hitelesítés a felhasználó hitelesítését jelenti, amikor az Azure SQL Edge-hez csatlakozik felhasználónévvel és jelszóval. Az SQL- **rendszergazdai** bejelentkezési jelszót meg kell adni az SQL Edge üzembe helyezése során. Ezt követően további SQL-bejelentkezéseket és felhasználókat hozhat létre a kiszolgáló rendszergazdája, amely lehetővé teszi a felhasználók számára a Felhasználónév és a jelszó használatával történő kapcsolódást.

    A bejelentkezések és a felhasználók SQL Edge-beli létrehozásával és kezelésével kapcsolatos további információkért tekintse meg [a bejelentkezési](/sql/relational-databases/security/authentication-access/create-a-login) adatok létrehozása és az [adatbázis-felhasználó létrehozása](/sql/relational-databases/security/authentication-access/create-a-database-user)című témakört.

### <a name="authorization"></a>Engedélyezés   

Az engedélyezés az Azure SQL Edge-adatbázisban lévő felhasználóhoz rendelt engedélyekre utal, és meghatározza, hogy a felhasználó mit tehet. Az engedélyek szabályozása úgy történik, hogy felhasználói fiókokat ad hozzá az [adatbázis szerepköreihez](/sql/relational-databases/security/authentication-access/database-level-roles) , és adatbázis-szintű engedélyeket rendel hozzájuk ezekhez a szerepkörökhöz, vagy ha a felhasználó számára bizonyos [objektum-szintű engedélyeket](/sql/relational-databases/security/permissions-database-engine)ad meg. További információ: [bejelentkezések és felhasználók](../azure-sql/database/logins-create-manage.md).

Ajánlott eljárásként hozzon létre egyéni szerepköröket, ha szükséges. Vegyen fel felhasználókat a szerepkörbe a feladat funkciójának végrehajtásához szükséges legalacsonyabb jogosultságokkal. Ne rendeljen engedélyeket közvetlenül a felhasználóknak. A kiszolgálói rendszergazdai fiók tagja a beépített db_owner szerepkörnek, amely kiterjedt engedélyekkel rendelkezik, és csak néhány rendszergazdai feladattal rendelkező felhasználónak kell megadnia. Alkalmazások esetén a [végrehajtás másként](/sql/t-sql/statements/execute-as-clause-transact-sql) beállítással adhatja meg a hívott modul végrehajtási környezetét, vagy használjon korlátozott engedélyekkel rendelkező [alkalmazás-szerepköröket](/sql/relational-databases/security/authentication-access/application-roles) . Ez a gyakorlat biztosítja, hogy az adatbázishoz csatlakozó alkalmazáshoz az alkalmazás által igényelt legalacsonyabb jogosultságok szükségesek. Az alábbi ajánlott eljárások a feladatok elkülönítését is támogatják.

## <a name="database-object-security"></a>Adatbázis-objektum biztonsága

A rendszerbiztonsági tag az SQL Edge elérésére jogosult személyek, csoportok és folyamatok. A "biztonságos elemek migrálására" az adatbázis által tartalmazott kiszolgáló, adatbázis és objektumok. Mindegyik rendelkezik olyan engedélyekkel, amelyek a felszín területének csökkentése érdekében konfigurálhatók. Az alábbi táblázat a rendszerbiztonsági tag-és biztonságos elemek migrálására tartalmaz információkat.

|További információ|Lásd:|  
|---------------------------|---------|  
|Kiszolgáló-és adatbázis-felhasználók, szerepkörök és folyamatok|[Rendszerbiztonsági tag adatbázismotor](/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|Kiszolgáló-és adatbázis-objektumok biztonsága|[Biztonságos elemek migrálására](/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>Titkosítás és tanúsítványok  
 
A titkosítás nem oldja meg a hozzáférés-vezérlési problémákat. Ez azonban fokozza a biztonságot azáltal, hogy korlátozza az adatvesztést, még abban a ritka esetben is, ha a hozzáférés-vezérlés kikerül. Ha például az adatbázis-gazda számítógép helytelenül van konfigurálva, és egy rosszindulatú felhasználó bizalmas adatokat (például hitelkártyaszám) kap, akkor az ellopott információ használhatatlan lehet, ha titkosítva van. Az alábbi táblázat a titkosítással kapcsolatos további információkat tartalmazza az Azure SQL Edge-ben.  
  
|További információ|Lásd:|  
|---------------------------|---------|  
|Biztonságos kapcsolatok megvalósítása|[Kapcsolatok titkosítása](/sql/linux/sql-server-linux-encrypted-connections)|  
|Titkosítási függvények|[Titkosítási függvények &#40;Transact-SQL&#41;](/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|Inaktív adatok titkosítása|[Transzparens adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Always Encrypted|[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> A [SQL Server on Linux](/sql/linux/sql-server-linux-security-overview) ismertetett biztonsági korlátozások az Azure SQL Edge-re is érvényesek. 


> [!NOTE]
> Az Azure SQL Edge nem tartalmazza az MSSQL-conf segédprogramot. Az összes konfigurációt, beleértve a titkosítással kapcsolatos konfigurációt, az [MSSQL. conf fájl](configure.md#configure-by-using-an-mssqlconf-file) vagy [környezeti változók](configure.md#configure-by-using-environment-variables)használatával kell végrehajtani. 


Az Azure SQL és a Microsoft SQL Serverhöz hasonlóan az Azure SQL Edge ugyanazt a mechanizmust biztosítja a tanúsítványok létrehozásához és használatához az objektumok és a kapcsolatok biztonságának növelése érdekében. További információ: [create Certificate (Transact-SQL)](/sql/t-sql/statements/create-certificate-transact-sql).


## <a name="application-security"></a>Alkalmazások biztonsága

### <a name="client-programs"></a>Ügyfélalkalmazások

Az Azure SQL Edge biztonsági eljárásai a biztonságos ügyfélalkalmazások írására is használhatók. További információ az ügyfélalkalmazások biztonságossá tételéről a hálózati rétegben: [ügyfél hálózati konfigurációja](/sql/database-engine/configure-windows/client-network-configuration).

### <a name="security-catalog-views-and-functions"></a>A biztonsági katalógus nézetei és funkciói  
A biztonsági információk több, a teljesítményre és a segédprogramra optimalizált nézetben és funkcióval is elérhetők. A következő táblázat az Azure SQL Edge biztonsági nézeteit és funkcióit ismerteti.  
  
|Függvények és nézetek|Hivatkozások|  
|---------------------------|---------|  
|Biztonsági katalógus nézetei, amelyek az adatbázis-szintű és a kiszolgálói szintű engedélyekkel, a résztvevőkkel, a szerepkörökkel és egyebekkel kapcsolatos információkat adnak vissza. Emellett vannak olyan katalógus-nézetek, amelyek információkat biztosítanak a titkosítási kulcsokról, a tanúsítványokról és a hitelesítő adatokról.|[Biztonsági katalógus nézetei &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|Biztonsági függvények, amelyek az aktuális felhasználóval, engedélyekkel és sémákkal kapcsolatos információkat adnak vissza.|[Biztonsági függvények &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)|  
|Biztonsági dinamikus felügyeleti nézetek.|[Biztonsággal kapcsolatos dinamikus felügyeleti nézetek és függvények &#40;Transact-SQL&#41;](/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>Naplózás 

Az Azure SQL Edge ugyanazokat a naplózási mechanizmusokat biztosítja, mint a SQL Server. További információ: [SQL Server audit (adatbázismotor)](/sql/relational-databases/security/auditing/sql-server-audit-database-engine).


## <a name="next-steps"></a>Következő lépések

- [Első lépések biztonsági funkciókkal](/sql/linux/sql-server-linux-security-get-started)
- [Az Azure SQL Edge futtatása nem legfelső szintű felhasználóként](configure.md#run-azure-sql-edge-as-non-root-user)
- [IoT Azure Security Center](../defender-for-iot/overview.md)