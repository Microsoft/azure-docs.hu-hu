---
title: A metaadat-kinyerési ABAP Function modul üzembe helyezése az Azure-beli hidakon futó SAP R3 termékcsaládban
description: Ez a cikk a ABAP függvények moduljának SAP-kiszolgálón történő üzembe helyezésének lépéseit ismerteti.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 1d9f1c5beafb7b54c5fd0189dd738ff8e346a3e8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695949"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>A metaadat-kinyerő ABAP Function modul üzembe helyezése az SAP R3 híd-termékcsaládhoz 
Ez a cikk a ABAP függvények moduljának SAP-kiszolgálón történő üzembe helyezésének lépéseit ismerteti.
## <a name="overview"></a>Áttekintés 

Az SAP Business Suite 4 HANA (S/4HANA), ECC és R/3 ERP-híd használatával kinyerheti a metaadatokat az SAP-kiszolgálóról. Ezt úgy érheti el, ha ABAP Function-modult helyez el az SAP-kiszolgálón. Ezt a függvényt a híd távolról is elérhetővé teszi az SAP-kiszolgálón található metaadatok lekérdezéséhez és letöltéséhez (szövegfájlként).
A művelet végrehajtásakor a híd a következők egyike lehet:

1.  Metaadatok importálása egy korábbi híd-végrehajtásból már helyileg letöltött meglévő fájlból.

2.  Meghívja a ABAP modul API-ját, várjon a letöltésre, majd importálja a metaadatokat a fájlból.

Ez a dokumentum részletesen ismerteti a modul üzembe helyezéséhez szükséges lépéseket.

> [!Note] 
>Az alábbi utasításokat az SAP GUI v. 7.2 alapján állították össze

## <a name="deployment-of-the-module"></a>A modul üzembe helyezése 

### <a name="create-a-package"></a>Csomag létrehozása 

Ez a lépés nem kötelező, és egy meglévő csomag is használható.

1.  Jelentkezzen be az SAP S/4HANA vagy az SAP ECC-kiszolgálóra, és nyissa meg az \" Object Navigatort \" (SE80-tranzakció).

2.  Válassza ki \" a csomag lehetőséget \" a listából, és adja meg az új csomag nevét (például: Z kiválasztó), majd kattintson a \_ Display (megjelenítés) gombra.

3.  Nyomja meg az "igen" gombot a "csomag létrehozása" ablakban. Ebből következően egy Windows \" Package Builder: Create Package (létrehozási csomag \" ) lesz megnyitva. Adja meg az értéket a rövid Leírás mezőbe, és nyomja meg a \" Continue (folytatás \" ) ikont.

4.  Kattintson a "saját kérések" gombra a "helyi Workbench-kérelem kérése" ablakban. Válassza a fejlesztési kérelem lehetőséget.

### <a name="create-a-function-group"></a>Function Group létrehozása 

Az Object Navigatorban válassza ki \" a Function csoportot \" a listából, és írja be a nevét az alábbi beviteli mezőbe (például: Z \_ \_ FGROUP). Nyomja meg a nézet ikont.

1.  Az \" objektum létrehozása \" ablakban nyomja meg az Igen gombot egy új csoport létrehozásához.

2.  A rövid szövegmezőben válassza ki a megfelelő leírást \" \" , és kattintson a \" Mentés gombra \" .

3.  Válasszon egy csomagot, amelyet az előző lépésben készített, \" hozzon létre egy csomagot \" , és kattintson a \" Mentés ikonra \" .

4.  A folytatáshoz nyomja le az ikont a kérelem megerősítéséhez \" \" .

5.  A függvény csoport aktiválása.

### <a name="create-the-abap-function-module"></a>A ABAP függvény moduljának létrehozása 

Miután létrehozta és kiválasztotta a Function csoportot, kattintson a jobb gombbal a nevére a tárház böngészőben, és válassza a \" Létrehozás = \> függvény modul lehetőséget \" .

Adja meg a \" Z. \_ \_ \" \" .. Letöltés a Function modul \" mezőben, és töltse \" \" fel a rövid szöveges bevitelt a megfelelő leírással.

A modul létrehozása után a következő információkat kell megadnia:

1.  Navigáljon az \" attribútumok \" lapra.

2.  Válassza a feldolgozás típus = Remote-Enabled függvény modul elemet.

    :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="források regisztrálása – beállítások" border="true":::

3.  Navigáljon a \" forráskód \" lapra. A függvényhez kétféleképpen telepíthet programkódot:

    a.  A főmenüből töltse fel a \_ Z \_DOWNLOAD.txt fájlt a Utilities = \> további segédprogramok = \> feltöltés/letöltés = feltöltés lehetőség kiválasztásával \> .

    b.  Másik lehetőségként nyissa meg a fájlt, másolja a tartalmát, és illessze be a \" forráskód \" területére.

4.  Navigáljon az \" Importálás \" lapra, és hozza létre a következő paramétereket:

    a.  P \_ -DD02L-TABNAME (opcionális = igaz)

    b.  *P \_ HELYI \_ elérésiút-típus karakterlánca* (nem kötelező = igaz)

    c.  *P \_ nyelv típusa L001TAB – alapértelmezett érték \' E\'*

    d.  *ROWSKIPS típusa, tehát \_ int alapértelmezett 0*

    e.  *SORSZÁM típusa, tehát \_ int alapértelmezett 0*

    > [!Note]
    > \"Adja meg a pass Value értéket \" mindegyikhez

    :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="források regisztrálása – beállítások" border="true":::

5.  Navigáljon a "táblák" lapra, és adja meg a következőket:

    *Táblázat exportálása, \_ például TAB512*

    :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="források regisztrálása – beállítások" border="true":::

6.  Navigáljon a \" kivételek \" lapra, és adja meg a következő kivételt:

    *E \_ exp \_ grafikus felhasználói felület \_ DOWNLOADFAILED*

    :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="források regisztrálása – beállítások" border="true":::

7.  Mentse a függvényt (nyomja le a CTRL + S billentyűkombinációt, vagy a főmenüben válassza a függvény modul = \> Mentés lehetőséget).

8.  \" \" Az eszköztáron kattintson az aktiválás ikonra (CTRL + F3), majd nyomja le a \" Continue (folytatás) gombot a \" párbeszédablakban. Ha a rendszer kéri, válassza ki a generált belefoglalt tartalmakat a fő Function modullal együtt.

### <a name="testing-the-function"></a>A függvény tesztelése 

Az összes korábbi lépés befejezése után kövesse az alábbi lépéseket a függvény teszteléséhez:

1.  Nyissa meg a Z \_ \_ kitöltendő. Letöltés funkció modult.

2.  \" \> A főmenüben válassza a függvény modul = test = \> teszt függvény modul \" elemet (vagy nyomja le az F8 billentyűt).

3.  Adja meg a helyi fájlrendszer mappájának elérési útját a P \_ helyi \_ elérési út paraméterrel, és nyomja le \" \" az eszköztár végrehajtás ikonját (vagy nyomja le az F8 billentyűt).

4.  \_Ha a metaadatokat tartalmazó fájlt le kell tölteni vagy frissíteni kell, helyezze a P terület mezőbe a érdeklődési terület nevét. Ha a függvény befejezi a munkát, a P \_ Local \_ Path paraméterben jelzett mappának több, metaadatokat tartalmazó fájlt kell tartalmaznia. A fájlok nevei olyan területeket utánoznak, amelyek a P terület mezőben adhatók meg \_ .

A függvény befejezi a végrehajtást, és a metaadatok sokkal gyorsabban letöltődnek a gépen, amely nagy sebességű hálózati kapcsolattal rendelkezik az SAP S/4HANA vagy ECC-kiszolgálóval.

## <a name="next-steps"></a>Következő lépések

- [SAP ECC-forrás regisztrálása és ellenőrzése](register-scan-sapecc-source.md)
- [SAP S/4HANA-forrás regisztrálása és ellenőrzése](register-scan-saps4hana-source.md)