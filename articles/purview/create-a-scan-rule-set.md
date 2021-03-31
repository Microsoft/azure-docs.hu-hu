---
title: Vizsgálati szabálykészlet létrehozása
description: Hozzon létre egy ellenőrzési szabályt az Azure-beli hatáskörébe a szervezet adatforrásainak gyors vizsgálatához.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553047"
---
# <a name="create-a-scan-rule-set"></a>Vizsgálati szabálykészlet létrehozása

Az Azure-beli hatáskörébe tartozó katalógusban létrehozhat vizsgálati szabályok készleteket, amelyek lehetővé teszik a szervezeten belüli adatforrások gyors vizsgálatát.

A betekintő szabálykészlet egy olyan tároló, amely a vizsgálati szabályok csoportját csoportosítja, így egyszerűen társíthatja őket egy vizsgálathoz. Létrehozhat például egy alapértelmezett ellenőrzési szabályt az egyes adatforrások típusaihoz, majd a vállalaton belüli összes vizsgálathoz alapértelmezés szerint használhatja ezeket a beolvasási szabályokat. Azt is megteheti, hogy a felhasználók a megfelelő engedélyekkel rendelkeznek az üzleti igények alapján különböző konfigurációkkal rendelkező más vizsgálati szabályok létrehozásához.

## <a name="steps-to-create-a-scan-rule-set"></a>Vizsgálati szabálykészlet létrehozásának lépései

Vizsgálati szabálykészlet létrehozása:

1. Az Azure-beli hatáskörébe katalógusból válassza a **felügyeleti központ** elemet.

1. Válassza a **vizsgálati szabályok készleteket** a bal oldali ablaktáblán, majd válassza az **új** lehetőséget.

1. Az **új vizsgálati szabály beállítása** lapon válassza ki azokat az adatforrásokat, amelyeket a katalógus képolvasó a **forrás típusa** legördülő listából támogat. Létrehozhat egy vizsgálati szabályt minden olyan típusú adatforrás esetében, amelyet ellenőrizni szeretne.

1. Adjon meg egy **nevet** a vizsgálati szabálynak. A maximális hossz 63 karakter, és szóközök nélkül nem megengedett. Igény szerint megadhat egy **leírást**. A maximális hossz 256 karakter.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="Képernyőkép a bevizsgálati szabály készletéről." border="true":::

1. Válassza a **Folytatás** lehetőséget.

   Megjelenik a **fájltípusok kiválasztása** lap. Figyelje meg, hogy az ezen az oldalon található fájltípus-beállítások az előző oldalon kiválasztott adatforrás típusától függően változnak. Alapértelmezés szerint az összes fájltípus engedélyezve van.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="Képernyőfelvétel a fájltípusok kiválasztása oldalról.":::

   Az ezen a lapon található **dokumentum** -fájltípusok lehetővé teszik a következő Office-fájltípusok felvételét vagy kizárását:. doc,. docm,. docx,. dot,. ODP,. ODS,. odt,. pdf,. pot,. pps,. ppsx,. ppt,. pptm,. pptx,. xlc,. xls,. xlsb,. xlsm,. xlsx és. xlt.

1. Engedélyezheti vagy letilthatja a fájltípusok csempéjét a jelölőnégyzet bejelölésével vagy törlésével. Ha Data Lake típusú adatforrást (például Azure Data Lake Storage Gen2 vagy Azure Blob) választ, engedélyezze azokat a fájltípusokat, amelyekhez a sémát ki szeretné csomagolni és osztályozni kívánja.

1. Bizonyos adatforrás-típusok esetében [Egyéni fájltípust is létrehozhat](#create-a-custom-file-type).

1. Válassza a **Folytatás** lehetőséget.

   Megjelenik a **besorolási szabályok kiválasztása** lap. Ezen a lapon láthatók a kijelölt **rendszerszabályok** és **Egyéni szabályok**, valamint a kiválasztott besorolási szabályok teljes száma. Alapértelmezés szerint az összes **rendszerszabály** jelölőnégyzet be van jelölve

1. A felvenni vagy kizárni kívánt szabályokhoz jelölje be a **rendszerszabályok** besorolása szabály jelölőnégyzetet, vagy törölje a jelölést a globálisan kategória szerint jelölőnégyzetből.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="A besorolási szabályok kiválasztása lapot ábrázoló képernyőfelvétel.":::

1. Kibonthatja a kategória csomópontot, és kijelölheti vagy törölheti az egyes jelölőnégyzeteket. Ha például az **Argentína. dni számú** szabálya magas hamis pozitív értékkel rendelkezik, akkor törölheti ezt a jelölőnégyzetet.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="A rendszerszabályok kijelölését bemutató képernyőkép.":::

1. Válassza a **Létrehozás** lehetőséget a vizsgálati szabálykészlet létrehozásának befejezéséhez.

### <a name="create-a-custom-file-type"></a>Egyéni fájltípus létrehozása

Az Azure hatáskörébe tartozik az egyéni bővítmény hozzáadása és az egyéni oszlop elválasztójának definiálása egy ellenőrzési szabályban.

Egyéni fájltípus létrehozásához írja be a következőt:

1. Kövesse az 1 – 5. lépést a [bevizsgálati szabály létrehozásához](#steps-to-create-a-scan-rule-set) vagy egy meglévő ellenőrzési szabálykészlet szerkesztéséhez szükséges lépések végrehajtásához.

1. A **fájltípusok kiválasztása** lapon válassza az **új fájltípus** lehetőséget az új egyéni fájltípus létrehozásához.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="Képernyőfelvétel: az új fájltípus kiválasztása a fájltípusok kiválasztása lapon.":::

1. Adjon meg egy **fájlkiterjesztést és egy** opcionális **leírást**.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="Képernyőfelvétel: az új egyéni fájl típusa lap." border="true":::

1. A fájl **tartalmának** a fájl tartalmának megadásához adja meg a következő lehetőségek egyikét a fájlban:

   - Válassza az **Egyéni** elválasztó lehetőséget, és adja meg a saját **Egyéni elválasztóját** (csak egyetlen karakter).

   - Válassza a rendszerfájl **típusa** lehetőséget, és válasszon egy rendszerfájl-típust (például XML) a **rendszerfájl típusa** legördülő listából.

1. Válassza a **Létrehozás** lehetőséget az egyéni fájl mentéséhez.

   A rendszer visszatér a **fájltípusok kiválasztása** lapra, és beszúrja az új egyéni fájltípust új csempévé.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="Képernyőfelvétel: az új egyéni fájltípus csempéje a fájltípusok kiválasztása lapon.":::

1. Ha módosítani vagy törölni szeretné, válassza a **Szerkesztés** lehetőséget az új fájltípus csempén.

1. Válassza a **Folytatás** lehetőséget a vizsgálati szabálykészlet konfigurálásának befejezéséhez.

## <a name="system-scan-rule-sets"></a>Rendszervizsgálati szabályok készletei

A rendszervizsgálati szabálykészlet a Microsoft által definiált vizsgálati szabálykészlet, amely minden Azure-beli hatáskörébe tartozó katalógushoz automatikusan létrejön. Minden rendszer-ellenőrzési szabálykészlet egy adott adatforrás-típushoz van társítva. A vizsgálat létrehozásakor társíthatja azt egy rendszervizsgálati szabálykészlet használatával. Minden alkalommal, amikor a Microsoft frissíti a rendszerszabály-készleteket, frissítheti őket a katalógusban, és alkalmazhatja a frissítést az összes kapcsolódó vizsgálatra.

1. A rendszervizsgálati szabályok listájának megtekintéséhez válassza a **felügyeleti központban** a **vizsgálati szabályok készlete** lehetőséget, és válassza a **System (számítógép** ) lapot.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="A rendszervizsgálati szabálykészlet listáját megjelenítő képernyőkép." lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. Minden rendszervizsgálati szabályhoz tartozik egy **név**, egy **Forrástípus** és egy **verzió**. Ha a **Version (verzió** ) oszlopban a vizsgálati szabálykészlet verziószámát választja, akkor az aktuális verzióhoz és az előző verzióhoz (ha van) társított szabályok láthatók.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="A rendszervizsgálati szabályok készletét bemutató képernyőkép." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. Ha egy rendszervizsgálati szabálykészlet számára elérhető frissítés, a **verzió** oszlopban válassza a **frissítés** lehetőséget. A rendszervizsgálati szabály lapon válasszon egy verziót az **új verzió kiválasztása a frissítéshez** legördülő listára. A lap az új verzióhoz és az aktuális verzióhoz társított rendszerbesorolási szabályok listáját tartalmazza.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="A rendszervizsgálati Szabálykészlet verziójának módosítását bemutató képernyőkép." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>Vizsgálat hozzárendelése rendszervizsgálati szabállyal

[A vizsgálat létrehozásakor](tutorial-scan-data.md#scan-data-into-the-catalog)azt is megteheti, hogy egy rendszervizsgálati szabállyal társítja azt a következő módon:

1. A **vizsgálati szabály kiválasztása** lapon válassza ki a rendszer-ellenőrzési szabályt.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="A vizsgálathoz beállított rendszervizsgálati szabálykészlet kiválasztását bemutató képernyőkép." lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. Válassza a **Folytatás**, majd a **Mentés és Futtatás** lehetőséget.
