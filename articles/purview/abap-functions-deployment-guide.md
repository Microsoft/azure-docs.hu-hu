---
title: Metaadatok kinyerése ABAP Function modul az SAP R3-ban – Azure-beli hatáskörébe
description: Ez a cikk a ABAP függvények moduljának SAP-kiszolgálón történő üzembe helyezésének lépéseit ismerteti.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 9bd3c315fcc15317a9fa483289fdc326ca6aa47f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614360"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>A metaadat-kinyerő ABAP Function modul üzembe helyezése az SAP R3 híd-termékcsaládhoz

Ez a cikk a ABAP függvények moduljának SAP-kiszolgálón történő üzembe helyezésének lépéseit ismerteti.

## <a name="overview"></a>Áttekintés

Az SAP Business Suite 4 HANA (S/4HANA), ECC és R/3 ERP-híd használatával kinyerheti a metaadatokat az SAP-kiszolgálóról. Ezt úgy érheti el, ha ABAP Function-modult helyez el az SAP-kiszolgálón. Ezt a függvényt a híd távolról is elérhetővé teszi az SAP-kiszolgálón található metaadatok lekérdezéséhez és letöltéséhez (szövegfájlként).

A művelet végrehajtásakor a híd a következők egyike lehet:

1. Metaadatok importálása egy korábbi híd-végrehajtásból már helyileg letöltött meglévő fájlból.

2. Meghívja a ABAP modul API-ját, várjon a letöltésre, majd importálja a metaadatokat a fájlból.

Ez a dokumentum részletesen ismerteti a modul üzembe helyezéséhez szükséges lépéseket.

> [!Note]
> Az alábbi utasításokat az SAP GUI v. 7.2 alapján állították össze

## <a name="deployment-of-the-module"></a>A modul üzembe helyezése

### <a name="create-a-package"></a>Csomag létrehozása

Ez a lépés nem kötelező, és egy meglévő csomag is használható.

1. Jelentkezzen be az SAP S/4HANA vagy az SAP ECC-kiszolgálóra, és nyissa meg az **Object Navigatort** (SE80-tranzakció).

2. Válassza ki a **csomag** lehetőséget a listából, és adja meg az új csomag nevét (például: Z kiválasztó \_ ), majd kattintson a gomb **megjelenítése** gombra.

3. A **csomag létrehozása** ablakban válassza az **Igen** lehetőséget. Ebből következően egy Windows **Package Builder: Create Package** megnyitható. Adja meg az értéket **rövid leírás** mezőbe, és válassza a **Folytatás** ikont.

4. Válassza ki a **saját kérések** lehetőséget a **helyi Workbench-kérelem kérése** ablakban. Válassza a **fejlesztési** kérelem lehetőséget.

### <a name="create-a-function-group"></a>Function Group létrehozása

Az Object Navigatorban válassza ki a **Function csoportot** a listából, és írja be a nevét az alábbi beviteli mezőbe (például: Z \_ \_ FGROUP). Válassza a **nézet** ikont.

1. Az **objektum létrehozása** ablakban válassza az **Igen** lehetőséget egy új függvény csoport létrehozásához.

2. A **rövid szövegmezőben** válassza ki a megfelelő leírást, és kattintson a **Mentés** gombra.

3. Válasszon egy csomagot, amely az előző lépésben előkészített **csomagot hoz létre** , majd válassza a **Mentés** lehetőséget.

4. A **folytatáshoz** nyomja le az ikont a kérelem megerősítéséhez.

5. Aktiválja a függvény csoportot.

### <a name="create-the-abap-function-module"></a>A ABAP függvény moduljának létrehozása

1. A csoport létrehozása után válassza ki azt.

2. Kattintson a jobb gombbal a függvény csoport nevére az adattár böngészőben, majd válassza a **Létrehozás**, majd a **függvény modul** elemet.

3. A **függvény modul** mezőjébe írja be a értéket `Z_MITI_DOWNLOAD` . Töltse fel a **rövid szöveges** bevitelt a megfelelő leírással.

A modul létrehozása után a következő információkat kell megadnia:

1. Navigáljon az **attribútumok** lapra.

2. Válassza a **feldolgozási típus** lehetőséget a **távoli engedélyezve függvény modulként**.

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Források regisztrálása lehetőség – Remote-Enabled függvény modul" border="true":::

3. Navigáljon a **forráskód** lapra. A függvényhez kétféleképpen telepíthet programkódot:

   a. A főmenüben töltse fel a Z. [. \_ . \_ ](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) fájlt tartalmazó szövegfájlt. Ehhez válassza a **segédprogramok**, **további segédeszközök**, majd a feltöltés **/Letöltés**, majd a **feltöltés** lehetőséget.

   b. Másik lehetőségként nyissa meg a fájlt, másolja a tartalmát, és illessze be a **forráskód** területére.

4. Navigáljon az **Importálás** lapra, és hozza létre a következő paramétereket:

   a.  P \_ -DD02L-TABNAME (opcionális = igaz)

   b.  *P \_ HELYI \_ elérésiút-típus karakterlánca* (nem kötelező = igaz)

   c.  *P \_ nyelv típusa L001TAB – alapértelmezett érték \' E\'*

   d.  *ROWSKIPS típusa, tehát \_ int alapértelmezett 0*

   e.  *SORSZÁM típusa, tehát \_ int alapértelmezett 0*

   > [!Note]
   > Adja meg a **pass Value értéket** mindegyikhez

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Források regisztrálása beállítás – paraméterek importálása" border="true":::

5. Navigáljon a "táblák" lapra, és adja meg a következőket:

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="A források beállításainak regisztrálása – táblák lap" border="true":::

6. Navigáljon a **kivételek** lapra, és adja meg a következő kivételt: `E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="A források beállításainak regisztrálása – kivételek lap" border="true":::

7. Mentse a függvényt (nyomja le a CTRL + S billentyűkombinációt, vagy válassza a **függvény modult**, majd **mentse** a főmenübe).

8. Az eszköztáron kattintson az **aktiválás** ikonra (CTRL + F3), majd válassza a  **Folytatás** gombot a párbeszédablakban. Ha a rendszer kéri, válassza ki a generált belefoglalt tartalmakat a fő Function modullal együtt.

### <a name="testing-the-function"></a>A függvény tesztelése

Az összes korábbi lépés befejezése után kövesse az alábbi lépéseket a függvény teszteléséhez:

1. Nyissa meg a Z \_ \_ kitöltendő. Letöltés funkció modult.

2. Válassza a **függvény modul** lehetőséget, majd a főmenüben kattintson a **teszt**, majd a **függvény tesztelése elemre** (vagy nyomja le az F8 billentyűt).

3. Adja meg a helyi fájlrendszer mappájának elérési útját a P \_ helyi \_ elérési út paraméterrel, és nyomja le az eszköztár **végrehajtás** ikonját (vagy nyomja le az F8 billentyűt).

4. \_Ha a metaadatokat tartalmazó fájlt le kell tölteni vagy frissíteni kell, helyezze a P terület mezőbe a érdeklődési terület nevét. Ha a függvény befejezi a munkát, a P \_ Local \_ Path paraméterben jelzett mappának több, metaadatokat tartalmazó fájlt kell tartalmaznia. A fájlok nevei olyan területeket utánoznak, amelyek a P terület mezőben adhatók meg \_ .

A függvény befejezi a végrehajtást, és a metaadatok sokkal gyorsabban letöltődnek a gépen, amely nagy sebességű hálózati kapcsolattal rendelkezik az SAP S/4HANA vagy ECC-kiszolgálóval.

## <a name="next-steps"></a>Következő lépések

- [SAP ECC-forrás regisztrálása és ellenőrzése](register-scan-sapecc-source.md)
- [SAP S/4HANA-forrás regisztrálása és ellenőrzése](register-scan-saps4hana-source.md)
