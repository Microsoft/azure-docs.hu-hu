---
title: Oracle-forrás- és beállításvizsgálatok regisztrálása (előzetes verzió) az Azure Purview-ban
description: Ez a cikk bemutatja, hogyan regisztrálhat Oracle-forrást az Azure Purview-ban, és hogyan állíthat be vizsgálatot.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 40c5e0ff2c2301607f5a548ff05c742c5c5a948d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517062"
---
# <a name="register-and-scan-oracle-source-preview"></a>Oracle-forrás regisztrálása és beolvasása (előzetes verzió)

Ez a cikk bemutatja, hogyan regisztrálhat Oracle-adatbázist a Purview szolgáltatásban, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Oracle-forrás támogatja a **teljes vizsgálatot** a metaadatok Oracle-adatbázisból való kinyerése és az adateszközök **közötti** életből való lekéréséhez.

## <a name="prerequisites"></a>Előfeltételek

1.  Állítsa be a legújabb [saját üzemeltetett integrációskörnyezetet.](https://www.microsoft.com/download/details.aspx?id=39717)
    További információ: Saját üzemeltetett integrációs környezet [létrehozása és konfigurálása.](../data-factory/create-self-hosted-integration-runtime.md)

2.  Győződjön meg arról, hogy a [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) telepítve van arra a virtuális gépre, ahol a saját üzemeltetett integrációs környezet telepítve van.

3.  Győződjön meg arról, hogy a \" Visual C++ redistributable 2012 4. frissítés telepítve van a saját \" üzemeltetett integrációskörnyezeti gépen. Ha még nincs \' telepítve, töltse [](https://www.microsoft.com/download/details.aspx?id=30679)le innen.

4.  Az Oracle JDBC-illesztőt manuálisan kell letöltenie innen a saját által üzemeltetett integrációs futtatókörnyezetet futtató virtuális gépről. [](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html)

    > [!Note] 
    > Az illesztőnek elérhetőnek kell lennie a virtuális gép összes fiókja számára. Ne telepítse felhasználói fiókba.

5.  A támogatott Oracle-adatbázisverziók a 6i és 19c között vannak.

6.  Felhasználói engedély: A rendszertáblákhoz csak olvasási hozzáférés szükséges. A felhasználónak engedéllyel kell rendelkeznie egy munkamenet létrehozásához, valamint szerepkörhöz rendelt SELECT \_ CATALOG \_ ROLE szerepkör. Azt is választhatja, hogy a felhasználó select engedéllyel rendelkezik minden olyan rendszertáblához, amelyről ez az összekötő lekérdezi a metaadatokat:
       > létrehozási munkamenet megadása a \[ \] felhasználónak;\
        grant select on all \_ users to \[ user \] ;\
        select megadása \_ dba-objektumokon \[ a \] felhasználónak;\
        grant select on dba \_ tab comments to user \_ \[ \] ;\
        select megadása dba \_ külső \_ helyeken \[ a \] felhasználónak;\
        select megadása dba \_ könyvtárakban a \[ \] felhasználónak;\
        grant select on dba \_ mviews to \[ user \] ;\
        select megadása dba \_ \_ clu-oszlopokon a \[ \] felhasználónak;\
        select megadása a dba \_ lapon \_ található oszlopokban a \[ \] felhasználónak;\
        grant select on dba \_ col comments to user \_ \[ \] ;\
        select megadása \_ dba-megkötések esetén \[ a felhasználó \] számára;\
        select megadása a dba \_ cons \_ oszlopokban \[ a felhasználó \] számára;\
        select megadása \_ dba-indexek alapján \[ a \] felhasználónak;\
        select megadása dba \_ ind \_ oszlopokon a \[ \] felhasználónak;\
        select megadása dba \_ eljárásokon \[ a \] felhasználónak;\
        select megadása dba \_ szinonimákon \[ a \] felhasználónak;\
        select megadása \_ dba-nézetekben \[ a \] felhasználónak;\
        select megadása a dba \_ forrásnál \[ a \] felhasználónak;\
        select megadása dba \_ eseményindítóknál \[ a felhasználó \] számára;\
        select megadása dba \_ argumentumok alapján \[ a \] felhasználónak;\
        select megadása dba \_ szekvenciákban \[ a \] felhasználónak;\
        select megadása dba \_ függőségek alapján \[ a \] felhasználónak;\
        select megadása v \_ \$ PÉLDÁNYon \[ a \] felhasználónak;\
        select megadása a v \_ \$ adatbázison \[ a \] felhasználónak;
    
## <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálatokhoz

Oracle-forrás esetén az egyetlen támogatott hitelesítés az **alapszintű hitelesítés.**

## <a name="register-an-oracle-source"></a>Oracle-forrás regisztrálása

Új Oracle-forrás adatkatalógusban való regisztráláshoz tegye a következőket:

1.  Nyissa meg a Purview-fiókját.
2.  A **bal oldali navigációs** sávon válassza a Források lehetőséget.
3.  Válassza a **Regisztráció lehetőséget**
4.  A Források regisztrálása oldalon válassza az **Oracle lehetőséget.** Válassza a **Folytatás** lehetőséget.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="források regisztrálása" border="true":::

A **Források regisztrálása (Oracle) képernyőn** tegye a következőket:

1.  Adjon meg **egy nevet,** amely alapján az adatforrás megjelenik a katalógusban.

2.  Adja meg **az Állomásnevet** az Oracle-forráshoz való csatlakozáshoz. Ez a következő lehet:
    - A JDBC által az adatbázis-kiszolgálóhoz való csatlakozáshoz használt állomásnév. Például: MyDatabaseServer.com vagy
    - IP-cím. Például: 192.169.1.2 vagy
    - A teljes JDBC kapcsolati sztring. Például: \
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD \_ BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT \_ DATA=(SERVICE \_ NAME=orcl)))

3.  Adja meg **a JDBC** által az adatbázis-kiszolgálóhoz való csatlakozáshoz használt portszámot (Oracle esetén alapértelmezés szerint az 1521-es).

4.  Adja meg **a** JDBC által az adatbázis-kiszolgálóhoz való csatlakozáshoz használt Oracle-szolgáltatásnevet.

5.  Válasszon ki egy gyűjteményt, vagy hozzon létre egy újat (nem kötelező)

6.  Fejezze be az adatforrás regisztrálását.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="források regisztrálása lehetőségek" border="true":::

## <a name="creating-and-running-a-scan"></a>Vizsgálat létrehozása és futtatása

Új vizsgálat létrehozásához és futtatásához tegye a következőket:

1.  A Felügyeleti központban kattintson az Integrációs rendszerek elemre. Győződjön meg arról, hogy be van állítva egy saját üzemelő integrációs környezet. Ha nincs beállítva, az itt [](./manage-integration-runtimes.md) említett lépésekkel hozzon létre egy saját üzemelő integrációskörnyezetet.

2.  Lépjen a **Források lapra.**

3.  Válassza ki a regisztrált Oracle-forrást.

4.  Válassza **az + Új vizsgálat lehetőséget.**

5.  Adja meg az alábbi adatokat:

    a.  **Név:** A vizsgálat neve

    b.  **Kapcsolódás integrációs környezeten keresztül:** Válassza ki a konfigurált saját üzemeltetett integrációskörnyezetet

    c.  **Hitelesítő** adat: Válassza ki az adatforráshoz való csatlakozáshoz szükséges hitelesítő adatokat. Győződjön meg arról, hogy:
    - Hitelesítő adatok létrehozásakor válassza az Alapszintű hitelesítés lehetőséget.        
    - A Felhasználónév bevitele mezőben adja meg a JDBC által az adatbázis-kiszolgálóhoz való csatlakozáshoz használt felhasználónevet        
    - Tárolja a JDBC által az adatbázis-kiszolgálóhoz való csatlakozáshoz használt felhasználói jelszót a titkos kulcsban.

    d.  **Séma:** Listába sorolja a sémák azon részkészletét, amelyek importálása pontosvesszővel elválasztott listában van kifejezve. Például: schema1; schema2. Ha a lista üres, a rendszer minden felhasználói sémát importál. Alapértelmezés szerint a rendszer minden rendszersémát (például SysAdmin) és objektumot figyelmen kívül hagy. Ha a lista üres, a rendszer az összes elérhető sémát importálja.
        Az ELFOGADHATÓ sémanév-minták AZ SQL LIKE-kifejezések szintaxisának használatával például a % használata. A%; %B; %C%; D
       -   A vagy kezdet        
       -   B vagy végződik        
       -   tartalmazza a C-t vagy a        
       -   egyenlő D

    A NOT és speciális karakterek használata nem elfogadható.

6.  **Illesztőprogram helye:** Adja meg a JDBC-illesztő azon helyének elérési útját a virtuális gépen, ahol az önkiszolgáló integrációs futtatókörnyezet fut. Ez a JAR-mappa érvényes helyének elérési útja.

7.  **Maximálisan elérhető memória:** A beolvasási folyamatok által az ügyfél virtuális gépére rendelkezésre álló maximális memória (GB-ban). Ez a vizsgálandó SAP S/4HANA-forrás méretétől függ.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="oracle beolvasása" border="true":::

8.  Kattintson a **Folytatás gombra.**

9.  Válassza ki a **beolvasási eseményindítót.** Beállíthatja az ütemezést, vagy futtathatja egyszer a vizsgálatot.

10.  Tekintse át a vizsgálatot, és kattintson **a Save and Run (Mentés és futtatás) gombra.**

## <a name="viewing-your-scans-and-scan-runs"></a>A vizsgálatok és a vizsgálatok futtatásai megtekintése

1. Lépjen a felügyeleti központba. A **Források és** vizsgálat szakaszban válassza az **Adatforrások** lehetőséget.

2. Válassza ki a kívánt adatforrást. Megjelenik az adatforráson meglévő vizsgálatok listája.

3. Válassza ki azt a vizsgálatot, amelynek az eredményeit meg szeretne tekinteni.

4. Ez az oldal az összes korábbi vizsgálatfuttatást, valamint az egyes vizsgálatfuttatások metrikákat és állapotokat mutatja. Azt is megjeleníti, hogy a vizsgálat ütemezve vagy manuálisan történt-e, hány adateszközt alkalmaztak besorolásra, hány eszközt fedeztek fel, a vizsgálat kezdési és záró idejét, valamint a vizsgálat teljes időtartamát.

## <a name="manage-your-scans"></a>A vizsgálatok kezelése

A vizsgálatok kezeléséhez vagy törléséhez tegye a következőket:

1. Lépjen a felügyeleti központba. A **Források és** vizsgálat szakaszban válassza az **Adatforrások** lehetőséget, majd válassza ki a kívánt adatforrást.

2. Válassza ki a kezelni kívánt vizsgálatot. A vizsgálat szerkesztéséhez válassza a **Szerkesztés gombra.**

3. A vizsgálatot a Törlés lehetőség kiválasztásával **törölheti.**

## <a name="next-steps"></a>Következő lépések

- [Az Azure Purview Data Catalog tallózása](how-to-browse-catalog.md)
- [Keresés az Azure Purview Data Catalog](how-to-search-catalog.md)