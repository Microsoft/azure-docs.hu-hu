---
title: Az SAP ECC-forrás és a telepítési vizsgálatok (előzetes verzió) regisztrálása az Azure hatáskörébe
description: Ez a cikk azt ismerteti, hogyan regisztrálhatók az SAP ECC-források az Azure-ban, és hogyan állítható be a vizsgálat.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: af3f54778882df9aaa06297f291c12a0f4b1577c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046045"
---
# <a name="register-and-scan-sap-ecc-source-preview"></a>SAP ECC-forrás regisztrálása és ellenőrzése (előzetes verzió)

Ez a cikk azt ismerteti, hogyan regisztrálhat egy SAP ECC-forrást a hatáskörébe, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

Az SAP ECC-forrás támogatja a **teljes vizsgálatot** , hogy kinyerje a metaadatokat egy SAP ECC-példányból, és beolvassa az adategységek közötti **vonal**

## <a name="prerequisites"></a>Előfeltételek

1.  Állítsa be a legújabb [saját üzemeltetésű integrációs](https://www.microsoft.com/download/details.aspx?id=39717)modult.
    További információ: saját üzemeltetésű [integrációs modul létrehozása és konfigurálása](../data-factory/create-self-hosted-integration-runtime.md).

2.  Győződjön meg arról, hogy a [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) telepítve van a virtuális gépen, ahol a saját üzemeltetésű Integration Runtime telepítve van.

3.  Győződjön \" meg arról, hogy a Visual C++ redistributable 2012 Update 4 \" telepítve van a saját üzemeltetésű Integration Runtime gépen. Ha még nem \' telepítette a t, töltse le innen. [](https://www.microsoft.com/download/details.aspx?id=30679)

4.  Töltse le a 64 bites [SAP-összekötőt Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) -re az SAP \' s webhelyről, és telepítse a saját üzemeltetésű integrációs modult futtató számítógépre. A telepítés során győződjön meg arról, hogy az **opcionális telepítési lépések** ablakban a **szerelvények telepítése a GAC** -ba lehetőségre van kiválasztva.

    :::image type="content" source="media/register-scan-sapecc-source/requirement.png" alt-text="Előfeltételek" border="true":::

5.  Az összekötő a Java Connector (JCo) 3,0 API használatával olvassa be a metaadatokat az SAP-ből. Ezért győződjön meg arról, hogy a Java-összekötő elérhető azon a virtuális gépen, ahol a saját üzemeltetésű Integration Runtime telepítve van.
    Győződjön meg arról, hogy a megfelelő JCo-eloszlást használja a környezetéhez. Egy Microsoft Windows rendszerű gépen például ellenőrizze, hogy a sapjco3. jar és a sapjco3.dll fájlok elérhetők-e.

    > [!Note] 
    > Az illesztőprogramnak elérhetőnek kell lennie a virtuális gép összes fiókja számára. Ne telepítse azt egy felhasználói fiókban.

6.  A [ABAP függvények telepítési útmutatójában](abap-functions-deployment-guide.md)ismertetett lépéseket követve telepítse az SAP-kiszolgálón a metaadatok kinyerésének ABAP funkciójának modulját. Egy ABAP fejlesztői fiókra lesz szüksége az RFC Function modul létrehozásához az SAP-kiszolgálón. A felhasználói fióknak megfelelő engedélyekkel kell rendelkeznie az SAP-kiszolgálóhoz való kapcsolódáshoz, és végre kell hajtania a következő RFC-függvények moduljait:
    -   STFC_CONNECTION (kapcsolat ellenőrzése)
    -   RFC_SYSTEM_INFO (a rendszerinformációk megkeresése)


## <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

Az SAP ECC-forrás egyetlen támogatott hitelesítése **Egyszerű hitelesítés**.

## <a name="register-sap-ecc-source"></a>SAP ECC-forrás regisztrálása

Ha új SAP ECC-forrást szeretne regisztrálni az adatkatalógusban, tegye a következőket:

1.  Navigáljon a hatáskörébe-fiókjába.
2.  A bal oldali navigációs sávon válassza a **források** lehetőséget.
3.  **Regisztráció** kiválasztása
4.  A források regisztrálása lapon válassza az **SAP ECC** lehetőséget. Válassza a **Folytatás lehetőséget.**

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="SAPECC-beállítások regisztrálása" border="true":::

A **források regisztrálása (SAP ECC)** képernyőn tegye a következőket:

1.  Adja meg azt a **nevet** , amelyet az adatforrás a katalógusban fog szerepelni.

2.  Adja meg az **alkalmazás-kiszolgáló** nevét az SAP ECC-forráshoz való kapcsolódáshoz.
    Az SAP Application Server-gazdagép IP-címe is lehet.

3.  Adja meg az SAP- **rendszerszámot**. Ez két számjegyből álló, 00 és 99 közötti egész szám.

4.  Válasszon ki egy gyűjteményt, vagy hozzon létre egy újat (nem kötelező)

5.  Fejezze be az adatforrás regisztrálását.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="SAPECC regisztrálása" border="true":::

## <a name="creating-and-running-a-scan"></a>Vizsgálat létrehozása és futtatása

Új vizsgálat létrehozásához és futtatásához tegye a következőket:

1.  A felügyeleti központban kattintson az Integration Runtimes elemre. Győződjön meg arról, hogy a saját üzemeltetésű integrációs modul van beállítva. Ha nincs beállítva, kövesse az [itt](./manage-integration-runtimes.md) leírt lépéseket a saját üzemeltetésű integrációs modul létrehozásához.

2.  Navigáljon a **forrásokhoz**

3.  Válassza ki a regisztrált SAP ECC-forrást.

4.  Válassza az **+ új vizsgálat** lehetőséget

5.  Adja meg az alábbi adatokat:

    a.  **Name (név**): a vizsgálat neve

    b.  **Csatlakozás Integration Runtime használatával**: válassza ki a konfigurált saját üzemeltetésű integrációs modult

    c.  **Hitelesítő** adat: válassza ki az adatforráshoz való kapcsolódáshoz szükséges hitelesítő adatokat. Ügyeljen rá, hogy:

    -   Hitelesítő adatok létrehozásakor válassza az egyszerű hitelesítés lehetőséget.
    -   Adja meg a felhasználói azonosítót az SAP-kiszolgálóhoz való kapcsolódáshoz a Felhasználónév beviteli mezőben.
    -   Tárolja az SAP-kiszolgálóhoz való kapcsolódáshoz használt felhasználói jelszót a titkos kulcsban.

    d.  **Ügyfél-azonosító**: adja meg az SAP ügyfél-azonosítót. Ez egy három számjegyből álló numerikus szám, amely 1000 és 999 között van.

    e.  **JCo könyvtár elérési útja**: az a könyvtár elérési útja, ahol a JCo-kódtárak találhatók

    f.  **Maximális rendelkezésre álló memória:** Az ügyfél virtuális gépén elérhető maximális memória (GB-ban), amelyet a folyamatok vizsgálatával lehet használni. Ez a vizsgálandó SAP ECC-forrás méretétől függ.

    :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="SAPECC vizsgálata" border="true":::

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