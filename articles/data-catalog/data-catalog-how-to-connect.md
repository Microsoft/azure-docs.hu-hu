---
title: Kapcsolódás adatforrásokhoz a Azure Data Catalogban
description: 'Útmutató: a Azure Data Catalog használatával felderített adatforrásokhoz való kapcsolódási útmutató.'
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: da3302ac25aaac6202f42cee400b307fc701fe50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674971"
---
# <a name="how-to-connect-to-data-sources"></a>Kapcsolódás az adatforrásokhoz

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Bevezetés
A **Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőalapú szolgáltatás, amely a vállalati adatforrások regisztrálási és felderítési rendszereként szolgál. Ez azt jelenti, hogy **Azure Data Catalog** az a célja, hogy segítsen az embereknek felderíteni, megérteni és használni az adatforrásokat, és hogy segítse a szervezeteket abban, hogy a meglévő adatokból több értéket kapjanak. Ennek a forgatókönyvnek a fő aspektusa az adatforrások használata – Ha a felhasználó felvesz egy adatforrást, és megértette a célját, a következő lépés az adatforráshoz való kapcsolódás, hogy az adatai használhatók legyenek.

## <a name="data-source-locations"></a>Adatforrás helyei
Az adatforrás-regisztráció során **Azure Data Catalog** fogadja az adatforrásra vonatkozó metaadatokat. Ez a metaadatok az adatforrás helyének részleteit tartalmazzák. A hely részletei az adatforrás és az adatforrás között változnak, de mindig a kapcsolódáshoz szükséges adatokat tartalmazzák. Például egy SQL Server tábla helye tartalmazza a kiszolgáló nevét, az adatbázis nevét, a séma nevét és a tábla nevét, míg a SQL Server Reporting Services jelentés helye tartalmazza a kiszolgáló nevét és a jelentés elérési útját. Más adatforrás-típusok olyan helyekkel rendelkeznek, amelyek tükrözik a forrásrendszer szerkezetét és képességeit.

## <a name="integrated-client-tools"></a>Integrált ügyféleszközök
Az adatforrásokhoz való kapcsolódás legegyszerűbb módja a "Megnyitás a következőben:..." menü a **Azure Data Catalog** portálon. Ez a menü a kiválasztott adategységhez való csatlakozás lehetőségeinek listáját jeleníti meg.
A csempe alapértelmezett nézetének használatakor ez a menü az egyes csempén érhető el.

 ![SQL Server-táblázat megnyitása az Excelben az adategység csempén](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

A listanézet használatakor a menü a portál ablakának tetején található keresési sávban érhető el.

 ![SQL Server Reporting Services jelentés megnyitása Jelentéskezelő](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Támogatott ügyfélalkalmazások
A Megnyitás a következőben:... a Azure Data Catalog-portálon lévő adatforrások menüjében a megfelelő ügyfélalkalmazás telepítése szükséges az ügyfélszámítógépen.

| Megnyitás az alkalmazásban | Fájlkiterjesztés/protokoll | Támogatott alkalmazások verziói |
| --- | --- | --- |
| Excel |. odc |Excel 2010 vagy újabb verzió |
| Excel (Top 1000) |. odc |Excel 2010 vagy újabb verzió |
| Power Query |.xlsx |Excel 2016 vagy Excel 2010 vagy Excel 2013 az Excel-bővítményhez telepített Power Query |
| Power BI Desktop |. pbix |Power BI Desktop július 2016 vagy újabb |
| SQL Server Data Tools |vsweb:// |A Visual Studio 2013 4. vagy újabb frissítése a telepített SQL Server eszközzel |
| Jelentéskezelő |http:// |Lásd [a SQL Server Reporting Services böngészőre vonatkozó követelményeit](/sql/reporting-services/browser-support-for-reporting-services-and-power-view) |

## <a name="your-data-your-tools"></a>Az Ön adatai, eszközei
A menüben elérhető lehetőségek a jelenleg kiválasztott adategység típusától függenek. Természetesen nem minden lehetséges eszköz fog szerepelni a "Megnyitás a következőben:..." menü, de még mindig egyszerűen csatlakozhat az adatforráshoz bármely ügyfél eszköz használatával. Ha egy adategység van kiválasztva a **Azure Data Catalog** -portálon, a Tulajdonságok ablaktáblán megjelenik a teljes hely.

 ![SQL Server tábla csatlakoztatási adatai](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

A kapcsolati adatok részletei eltérnek az adatforrás típusától és az adatforrás típusától, de a portálon található információk minden ügyfél-eszközben elérhetővé teszik az adatforráshoz való kapcsolódáshoz szükséges adatokat. A felhasználók átmásolhatják a **Azure Data Catalog** használatával felderített adatforrások kapcsolati adatait, lehetővé téve számukra, hogy a választott eszközön lévő adatokkal működjenek.

## <a name="connecting-and-data-source-permissions"></a>Kapcsolódási és adatforrás-engedélyek
Habár a **Azure Data Catalog** az adatforrások felderíthetővé tételével, maga az adathoz való hozzáférés az adatforrás tulajdonosának vagy rendszergazdájának a felügyelete alatt marad. **Azure Data Catalog** adatforrásának felfedése nem biztosít semmilyen engedélyt a felhasználónak az adatforrás eléréséhez.

Annak érdekében, hogy könnyebb legyen az adatforrást felderítő felhasználók számára, de nincs engedélye az adatok elérésére, a felhasználók adatokat adhatnak meg a kérés hozzáférése tulajdonságban az adatforrás megjegyzése során. Az itt található információk – beleértve az adatforráshoz való hozzáférés megszerzésére irányuló folyamatra vagy kapcsolattartási pontra mutató hivatkozásokat is – a portálon az adatforrás helye információi mellett jelennek meg.

 ![Kapcsolódási információk a kérelemhez megadott hozzáférési utasításokkal](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Összefoglalás
Az adatforrások **Azure Data Catalogsal** való regisztrálása lehetővé teszi, hogy az adatforrásból a katalógus szolgáltatásba másolja a strukturális és leíró metaadatokat az adatokból. Miután regisztrálta az adatforrást, és felderített, a felhasználók csatlakozhatnak az adatforráshoz a **Azure Data Catalog** -portál Megnyitás a következőben:... Válassza a menü vagy a saját adateszközeinek használatát.

## <a name="see-also"></a>Lásd még
* [Ismerkedjen meg Azure Data Catalog](data-catalog-get-started.md) oktatóanyaggal, amely részletesen ismerteti az adatforrásokhoz való kapcsolódás lépéseit.