---
title: Az Azure Storage Explorer használatával a Data Lake Storage Gen2 engedélyeinek beállítása
description: A jelen, akkor meg, hogyan állítsa be az Azure Storage Explorer használatával a fájlok és könyvtárak belül az Azure Data Lake Storage Gen2 képes a storage-fiókot.
services: storage
author: roygara
ms.custom: mvc
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: f2569b29ab6124f1cfa22fa745d45082c213a6be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627883"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Azure Storage Explorer használata az Azure Data Lake Storage Gen2 fájl- és oszlopszintű engedélyek beállítása

Az Azure Data Lake Storage Gen2 tárolt fájlok részletes engedélyeket támogatja, és hozzáférés-vezérlési lista (ACL) kezelés. Együtt finom részletes engedélyek és ACL-kezelés lehetővé teszik a rendkívül részletes adatokhoz való hozzáférés kezelése.

Ez a cikk bemutatja az Azure Storage Explorer használata:

> [!div class="checklist"]
> * Fájl szintű engedélyek beállítása
> * Directory szintű engedélyek beállítása
> * A hozzáférés-vezérlési lista felhasználók vagy csoportok hozzáadása

## <a name="prerequisites"></a>Előfeltételek

Annak érdekében, hogy a legjobb tüntetik fel a folyamatot, kérjük, hajtsa végre a [Azure Storage Explorer rövid](data-lake-storage-Explorer.md). Ez biztosítja, hogy a tárfiók a leginkább megfelelő állapotban (létrehozott fájlrendszer és azt feltöltött adatok) lesz.

## <a name="managing-access"></a>Hozzáférés-kezelés

Beállíthatja, hogy a engedélyeit a fájlrendszer gyökérmappájában. Ehhez az Azure Storage Explorer jogosultságokkal (azaz nem egy olyan kapcsolati karakterlánccal) ehhez az egyéni fiókjával kell bejelentkeznie. Kattintson a jobb gombbal a fájlrendszert, és válassza ki **engedélyek kezelése**állapotba helyezése fel, a **kezelése engedély** párbeszédpanel bezárásához.

![A Microsoft Azure Storage Explorer – könyvtár-hozzáférés kezelése](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

A **kezelése engedély** párbeszédpanel segítségével kezelheti a tulajdonos és a tulajdonosok csoport engedélyeit. Lehetővé teszi az új felhasználók és csoportok hozzáadása a hozzáférés-vezérlési lista, amely ezután kezelheti az engedélyeket.

A hozzáférés-vezérlési lista az új felhasználó vagy csoport hozzáadásához válassza a **felhasználó vagy csoport hozzáadása** mező.

Adja meg a megfelelő Azure Active Directory (AAD) bejegyzés hozzáadása a listához, és válassza ki szeretné **Hozzáadás**.

A felhasználó vagy csoport megjelenik a **felhasználók és csoportok:** mezőt, így azok engedélyek kezelése.

> [!NOTE]
> Ajánlott eljárás, és ajánlott, hozzon létre egy biztonsági csoportot az aad-ben és az egyéni felhasználók helyett a csoport engedélyeit karbantartása. Ezt a javaslatot, valamint más ajánlott eljárásokról a részletekért lásd: [gyakorlati tanácsok a Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Hozzárendelhet engedélyek két kategóriába sorolhatók: hozzáférési ACL-EK és alapértelmezett ACL-ek.

* **Hozzáférés**: Hozzáférési ACL-objektumhoz való hozzáférés szabályozása. Fájlok és mappák egyaránt rendelkeznek hozzáférési ACL-ek.

* **Alapértelmezett**: Egy címtár, amely meghatározza, hogy a hozzáférési ACL-ek minden könyvtár alatt létrehozott gyermekelemek társított ACL-ek sablonját. Fájlok nem rendelkeznek alapértelmezett ACL-ek.

E kategóriák közül mindkettőt belül van három engedélyeket rendelhet a fájlok vagy könyvtárak: **Olvasási**, **írási**, és **végrehajtása**.

>[!NOTE]
> Itt van kijelölve nem állítja be engedélyeket a könyvtárán belül található bármelyik jelenleg meglévő elemre. Nyissa meg az egyes elemekre kell, és állítsa be az engedélyeket manuálisan, ha a fájl már létezik.

Kezelheti az engedélyeket az egyes könyvtárak, valamint az egyes fájlokat, amelyek részletes hozzáférés-vezérlés teszi lehetővé. A fájlok és könyvtárak engedélyeinek kezelésének folyamata megegyezik a fent leírt módon. Kattintson a jobb gombbal a fájl vagy könyvtár engedélyeinek kezelése a, és hajtsa végre az eljárást.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban útmutatóból megtudhatta, hogyan engedélyek beállítása a fájlok és könyvtárak használatával **Azure Storage Explorer**. Tudjon meg többet a hozzáférés-vezérlési listák, beleértve az alapértelmezett ACL-EK, hozzáférési ACL-EK, azok működését és a megfelelő engedélyekkel, továbbra is a tárgyban fogalmi című cikkben.

> [!div class="nextstepaction"]
> [Hozzáférés-vezérlés a 2. generációs Azure Data Lake Storage-ben](data-lake-storage-access-control.md)
