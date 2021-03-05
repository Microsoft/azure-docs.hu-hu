---
title: Teradata-forrás és telepítési vizsgálatok (előzetes verzió) regisztrálása az Azure hatáskörébe
description: Ez a cikk azt ismerteti, hogyan regisztrálhat egy Teradata-forrást az Azure hatáskörébe, és hogyan állíthat be vizsgálatot.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 2008e014e9f160b643ed5f591fff81c0b215e24a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175082"
---
# <a name="register-and-scan-teradata-source-preview"></a>Teradata-forrás regisztrálása és bevizsgálása (előzetes verzió)

Ez a cikk azt ismerteti, hogyan regisztrálhat egy Teradata-forrást a hatáskörébe, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

A Teradata forrás támogatja a **teljes vizsgálatot** a metaadatok Teradata-adatbázisból való kinyeréséhez és az adategységek közötti **vonal** beolvasásához.

## <a name="prerequisites"></a>Előfeltételek

1.  Állítsa be a legújabb [saját üzemeltetésű integrációs](https://www.microsoft.com/download/details.aspx?id=39717)modult.
    További információ: saját üzemeltetésű [integrációs modul létrehozása és konfigurálása](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

2.  Győződjön meg arról, hogy a [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) telepítve van a virtuális gépen, ahol a saját üzemeltetésű Integration Runtime telepítve van.

3.  Győződjön \" meg arról, hogy a Visual C++ redistributable 2012 Update 4 \" telepítve van a saját üzemeltetésű Integration Runtime gépen. Ha még nem \' telepítette a t, töltse le innen. [](https://www.microsoft.com/download/details.aspx?id=30679)

4.  Manuálisan le kell töltenie a Teradata JDBC-illesztőprogramját a virtuális gépen, ahol a saját üzemeltetésű integrációs modul fut.
    A végrehajtható JAR-fájl letölthető a Teradata [webhelyéről](https://downloads.teradata.com/).

    > [!Note]
    > Az illesztőprogramnak elérhetőnek kell lennie a virtuális gép összes fiókja számára. Ne telepítsen felhasználói fiókba.

5.  A támogatott Teradata-adatbázis-verziók 12. x és 16. x közöttiek. Győződjön meg arról, hogy olvasási hozzáférése van a beolvasott Teradata-forráshoz.

## <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

A Teradata-források egyetlen támogatott hitelesítése **Egyszerű hitelesítés**.

## <a name="register-a-teradata-source"></a>Teradata-forrás regisztrálása

Ha új Teradata-forrást szeretne regisztrálni a adatkatalógusban, tegye a következőket:

1.  Navigáljon a hatáskörébe-fiókjába.
2.  A bal oldali navigációs sávon válassza a **források** lehetőséget.
3.  **Regisztráció** kiválasztása
4.  A források regisztrálása lapon válassza a **Teradata** lehetőséget. Válassza a **Folytatás** elemet

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Teradata-beállítások regisztrálása" border="true":::

A **források regisztrálása (Teradata)** képernyőn tegye a következőket:

1.  Adja meg azt a **nevet** , amelyben az adatforrás szerepelni fog a katalógusban.

2.  Adja meg az Teradata-forráshoz való kapcsolódáshoz használandó **állomásnevet** . A kiszolgáló IP-címe vagy teljesen minősített kapcsolódási karakterlánc is lehet.

3.  Válasszon ki egy gyűjteményt, vagy hozzon létre egy újat (nem kötelező)

4.  Fejezze be az adatforrás regisztrálását.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="Teradata regisztrálása" border="true":::

## <a name="creating-and-running-a-scan"></a>Vizsgálat létrehozása és futtatása

Új vizsgálat létrehozásához és futtatásához tegye a következőket:

1.  A felügyeleti központban kattintson az **Integration Runtimes** elemre. Győződjön meg arról, hogy a saját üzemeltetésű integrációs modul van beállítva. Ha nincs beállítva, kövesse az [itt](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) leírt lépéseket a saját üzemeltetésű integrációs modul beállításához.

2.  Navigáljon a **forrásokhoz**

3.  Válassza ki a regisztrált Teradata forrását.

4.  Válassza az **+ új vizsgálat** lehetőséget

5.  Adja meg az alábbi adatokat:

    a.  **Name (név**): a vizsgálat neve

    b.  **Csatlakozás Integration Runtime használatával**: válassza ki a konfigurált saját üzemeltetésű integrációs modult.

    c.  **Hitelesítő** adat: válassza ki az adatforráshoz való kapcsolódáshoz szükséges hitelesítő adatokat. Ügyeljen rá, hogy:

    -   Hitelesítő adatok létrehozásakor válassza az egyszerű hitelesítés lehetőséget.
    -   Adja meg az adatbázis-kiszolgálóhoz való kapcsolódáshoz használandó felhasználónevet a Felhasználónév beviteli mezőben.
    -   Tárolja az adatbázis-kiszolgáló jelszavát a titkos kulcsban.

        Ha többet szeretne megtudni a hitelesítő adatokról, tekintse meg az [itt](https://docs.microsoft.com/azure/purview/manage-credentials) található hivatkozást

6.  **Séma**: az importálandó sémák részhalmazának listázása pontosvesszővel tagolt listaként. például: Schema1; schema2. A rendszer minden felhasználói sémát importál, ha a lista üres. Alapértelmezés szerint a rendszer minden rendszersémát (például SysAdmin) és objektumot figyelmen kívül hagy. Ha a lista üres, a rendszer az összes elérhető sémát importálja.

    Az SQL LIKE Expressions szintaxist használó elfogadható sémák neve például A következőt használja:%, pl. A%; B % C%; D
    - Kezdés a vagy    
    - vége B vagy    
    - C vagy    
    - egyenlő D

    A nem és a speciális karakterek használata nem fogadható el

7.  **Illesztőprogram helye**: adja meg a JDBC-illesztő azon elérési útját a virtuális gépen, amelyen az önkiszolgáló Integration Runtime fut. Ennek a JAR-mappa érvényes helyének kell lennie.

8.  **Maximális rendelkezésre álló memória:** Az ügyfél virtuális gépén elérhető maximális memória (GB-ban), amelyet a folyamatok vizsgálatával lehet használni. Ez a vizsgálandó Teradata-forrás méretétől függ.

    > [!Note] 
    > Hüvelykujj szabályként minden 1000-táblázathoz 2 GB memóriát adjon meg

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="telepítési vizsgálat" border="true":::

6.  Kattintson a **Continue (folytatás**) gombra.

7.  Válassza ki a **vizsgálati triggert**. Beállíthat egy ütemtervet, vagy futtathatja a vizsgálatot egyszer.

8.  Tekintse át a vizsgálatot, és kattintson a **Mentés és Futtatás** gombra.

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