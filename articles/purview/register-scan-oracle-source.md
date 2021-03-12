---
title: Az Oracle Source és a Setup Scans (előzetes verzió) regisztrálása az Azure hatáskörébe
description: Ez a cikk azt ismerteti, hogyan regisztrálhat Oracle-forrásokat az Azure-ban, és hogyan állíthat be vizsgálatot.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 37f6a779e7dd83a6aa61de9850ad3b49b57393f9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010446"
---
# <a name="register-and-scan-oracle-source-preview"></a>Oracle-forrás regisztrálása és bevizsgálása (előzetes verzió)

Ez a cikk azt ismerteti, hogyan regisztrálhat egy Oracle-alapú adatbázist a hatáskörébe, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Oracle-forrás támogatja a **teljes vizsgálatot** , hogy kinyerje a metaadatokat  egy Oracle-adatbázisból, és lehívja az adategységeket

## <a name="prerequisites"></a>Előfeltételek

1.  Állítsa be a legújabb [saját üzemeltetésű integrációs](https://www.microsoft.com/download/details.aspx?id=39717)modult.
    További információ: saját üzemeltetésű [integrációs modul létrehozása és konfigurálása](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

2.  Győződjön meg arról, hogy a [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) telepítve van a virtuális gépen, ahol a saját üzemeltetésű Integration Runtime telepítve van.

3.  Győződjön \" meg arról, hogy a Visual C++ redistributable 2012 Update 4 \" telepítve van a saját üzemeltetésű Integration Runtime gépen. Ha még nem \' telepítette a t, töltse le innen. [](https://www.microsoft.com/download/details.aspx?id=30679)

4.  A saját üzemeltetésű integrációs modult futtató virtuális gépen manuálisan le kell töltenie egy Oracle [JDBC-illesztőprogramot](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) .

    > [!Note] 
    > Az illesztőprogramnak elérhetőnek kell lennie a virtuális gép összes fiókja számára. Ne telepítse azt egy felhasználói fiókban.

5.  A támogatott Oracle Database-verziók a 19c 6I.

6.  Felhasználói engedély: az első sikeres vizsgálathoz teljes sys rendszergazdai típusra van szükség.

    További vizsgálatok esetén a rendszertáblákhoz csak olvasási hozzáférés szükséges. A felhasználónak engedéllyel kell rendelkeznie a munkamenet létrehozásához, valamint a szerepkör kiválasztásához \_ \_ . AZT is megteheti, hogy a felhasználó az összes olyan rendszertáblára vonatkozóan engedélyezte a megadott engedélyt, amelyhez az összekötő a következő metaadatokat kérdezi le:
       > a felhasználó létrehozási munkamenetének megadása \[ \] ; \
        adja meg a Select elemet minden felhasználónál \_ \[ \] ; \
        Válassza a Select on DBA \_ -objektumok lehetőséget a \[ felhasználónak \] ; \
        adja meg a Select on DBA \_ lap \_ megjegyzéseit a \[ felhasználónak \] ; \
        Válassza a kiválasztás a DBA \_ külső \_ helyekről a \[ felhasználónak \] ; \
        Válassza a Select on DBA \_ -címtárak lehetőséget a \[ felhasználónak \] ; \
        adja meg a Select elemet a következőhöz: DBA \_ mviews a \[ User \] ; \
        adja meg a Select for DBA \_ CLU \_ oszlopokat a \[ felhasználó számára \] ; \
        adja meg a Select for DBA \_ Tab \_ -oszlopokat a \[ felhasználó számára \] ; \
        adja meg a Select on DBA \_ Col \_ megjegyzéseit a \[ felhasználónak \] ; \
        adja meg a következőt: DBA \_ megkötések a \[ felhasználó számára \] ; \
        adja meg a Select for DBA \_ ellenérvek \_ oszlopokat a \[ felhasználó számára \] ; \
        adja meg a Select elemet a következőhöz: DBA \_ indexek a \[ felhasználó számára \] ; \
        adja meg a Select elemet a következőhöz: DBA \_ ind \_ -oszlopok a \[ felhasználónak \] ; \
        adja meg a következőt: DBA \_ -eljárások a \[ felhasználó számára \] ; \
        adja meg a Select elemet a következőhöz: DBA \_ szinonimák a \[ felhasználónak \] ; \
        Válassza a kiválasztás a következőhöz: DBA \_ -nézetek a \[ felhasználónak \] ; \
        adja meg a Select on DBA \_ -forrást a \[ felhasználó számára \] ; \
        Válassza a Select on DBA \_ -Eseményindítók lehetőséget a \[ felhasználónak \] ; \
        adja meg a következőt: DBA \_ -argumentumok a \[ felhasználó számára \] ; \
        adja meg a Select for DBA \_ -sorozatot a \[ felhasználónak \] ; \
        adja meg a kiválasztás a következőhöz: DBA \_ -függőségek a \[ felhasználó számára \] ; \
        adja meg a Select on V \_ \$ példány a \[ User \] ; \
        adja meg a Select on v Database elemet a \_ \$ \[ felhasználó számára \] ;
    
## <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

Az Oracle-források egyetlen támogatott hitelesítése **Egyszerű hitelesítés**.

## <a name="register-an-oracle-source"></a>Oracle-forrás regisztrálása

Ha új Oracle-forrást szeretne regisztrálni az adatkatalógusban, tegye a következőket:

1.  Navigáljon a hatáskörébe-fiókjába.
2.  A bal oldali navigációs sávon válassza a **források** lehetőséget.
3.  **Regisztráció** kiválasztása
4.  A források regisztrálása lapon válassza az **Oracle** lehetőséget. Válassza a **Folytatás** lehetőséget.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="források regisztrálása" border="true":::

A **források regisztrálása (Oracle)** képernyőn tegye a következőket:

1.  Adja meg azt a **nevet** , amelyet az adatforrás a katalógusban fog szerepelni.

2.  Az Oracle-forráshoz való kapcsolódáshoz adja meg az **állomásnév** nevét. Ez a következők egyike lehet:
    - A JDBC által az adatbázis-kiszolgálóhoz való kapcsolódáshoz használt állomásnév. Például MyDatabaseServer.com vagy
    - IP-cím. Például 192.169.1.2 vagy
    - A teljes JDBC kapcsolati sztringje. Például: \
        JDBC: Oracle: vékony: @ (Leírás = (terheléselosztás \_ = bekapcsolva) (címe = (protokoll = TCP) (gazdagép = oracleserver1) (port = 1521)) (a következő: protokoll = TCP) (gazdagép = oracleserver2) (port = 1521)) (címe = (protokoll = TCP) (gazdagép = oracleserver3) (port = 1521)) ( \_ az adatkapcsolat = (szolgáltatásnév \_ = ORCL)))

3.  Adja meg a JDBC által az adatbázis-kiszolgálóhoz való csatlakozáshoz használt **portszámot** (1521 alapértelmezés szerint az Oracle számára).

4.  Adja meg a JDBC által az adatbázis-kiszolgálóhoz való kapcsolódáshoz használt **Oracle-szolgáltatás nevét** .

5.  Válasszon ki egy gyűjteményt, vagy hozzon létre egy újat (nem kötelező)

6.  Fejezze be az adatforrás regisztrálását.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="források regisztrálása – beállítások" border="true":::

## <a name="creating-and-running-a-scan"></a>Vizsgálat létrehozása és futtatása

Új vizsgálat létrehozásához és futtatásához tegye a következőket:

1.  A felügyeleti központban kattintson az Integration Runtimes elemre. Győződjön meg arról, hogy a saját üzemeltetésű integrációs modul van beállítva. Ha nincs beállítva, kövesse az [itt](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) leírt lépéseket a saját üzemeltetésű integrációs modul létrehozásához.

2.  Navigáljon a **forrásokhoz**.

3.  Válassza ki a regisztrált Oracle-forrást.

4.  Válassza az **+ új vizsgálat** lehetőséget.

5.  Adja meg az alábbi adatokat:

    a.  **Name (név**): a vizsgálat neve

    b.  **Csatlakozás Integration Runtime használatával**: válassza ki a konfigurált saját üzemeltetésű integrációs modult

    c.  **Hitelesítő** adat: válassza ki az adatforráshoz való kapcsolódáshoz szükséges hitelesítő adatokat. Ügyeljen rá, hogy:
    - Hitelesítő adatok létrehozásakor válassza az egyszerű hitelesítés lehetőséget.        
    - Adja meg a JDBC által az adatbázis-kiszolgálóhoz való kapcsolódáshoz használt felhasználónevet a Felhasználónév beviteli mezőjében        
    - Tárolja a JDBC által a titkos kulcsban található adatbázis-kiszolgálóhoz való kapcsolódáshoz használt felhasználói jelszót.

    d.  **Séma**: az importálandó sémák részhalmazának listázása pontosvesszővel tagolt listaként. Például: Schema1; schema2. A rendszer minden felhasználói sémát importál, ha a lista üres. Alapértelmezés szerint a rendszer minden rendszersémát (például SysAdmin) és objektumot figyelmen kívül hagy. Ha a lista üres, a rendszer az összes elérhető sémát importálja.
        Az SQL LIKE Expressions szintaxist használó elfogadható séma-nevek például a következőt használják:%. A (z)%; B % C%; D
       -   Kezdés a vagy        
       -   vége B vagy        
       -   C vagy        
       -   egyenlő D

    A nem és a speciális karakterek használata nem fogadható el.

6.  **Illesztőprogram helye**: adja meg a JDBC-illesztő azon elérési útját a virtuális gépen, amelyen az önkiszolgáló Integration Runtime fut. Ennek a JAR-mappa érvényes helyének kell lennie.

7.  **Maximális rendelkezésre álló memória**: az ügyfél virtuális gépén elérhető maximális memória (GB-ban) a folyamatok vizsgálatával használható. Ez a vizsgálandó SAP S/4HANA-forrás méretétől függ.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="Oracle vizsgálata" border="true":::

8.  Kattintson a **Continue (folytatás**) gombra.

9.  Válassza ki a **vizsgálati triggert**. Beállíthat egy ütemtervet, vagy futtathatja a vizsgálatot egyszer.

10.  Tekintse át a vizsgálatot, és kattintson a **Mentés és Futtatás** gombra.

## <a name="viewing-your-scans-and-scan-runs"></a>A vizsgálatok és a vizsgálat futtatásának megtekintése

1. Navigáljon a felügyeleti központhoz. Válassza ki az **adatforrásokat** a **források és vizsgálat** szakaszban.

2. Válassza ki a kívánt adatforrást. Ekkor megjelenik az adatforrás meglévő vizsgálatának listája.

3. Válassza ki azt a vizsgálatot, amelynek eredményeit szeretné megtekinteni.

4. Ezen az oldalon megtekintheti az összes korábbi vizsgálatot, valamint az egyes vizsgálati futtatások metrikáit és állapotát. Emellett azt is megjeleníti, hogy a vizsgálat ütemezett vagy kézi volt-e, hány eszközön alkalmaztak besorolást, hány teljes eszközt észlelt a rendszer, a vizsgálat kezdési és befejezési időpontja, valamint a vizsgálat teljes időtartama.

## <a name="manage-your-scans"></a>A vizsgálatok kezelése

A vizsgálat kezeléséhez vagy törléséhez tegye a következőket:

1. Navigáljon a felügyeleti központhoz. Válassza ki az **adatforrások** lehetőséget a **források és vizsgálat szakaszban,** majd válassza ki a kívánt adatforrást.

2. Válassza ki a kezelni kívánt vizsgálatot. A vizsgálat szerkesztéséhez kattintson a **Szerkesztés** lehetőségre.

3. A vizsgálatot a **delete (Törlés**) lehetőség kiválasztásával törölheti.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)