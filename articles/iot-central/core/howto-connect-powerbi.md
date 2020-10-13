---
title: Az Azure IoT Central-adatPower BI-irányítópulton tárolt adatai megjelenítése | Microsoft Docs
description: Az Azure IoT Central Power BI megoldásával jelenítheti meg és elemezheti a IoT Central adatait.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: f996bb2d5126ef038ca872aee1f1893979a5229b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080998"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Azure IoT Central-beli adatPower BI-irányítópulton tárolt adatai megjelenítése és elemzése

*Ez a témakör a rendszergazdákra és a megoldás-fejlesztőre vonatkozik.*

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Power BI megoldási folyamat":::

Az Azure IoT Central v3 Power BI megoldásával hatékony Power BI irányítópultot hozhat létre a IoT-eszközök teljesítményének figyeléséhez. A Power BI-irányítópulton a következőket teheti:

- Az eszközök által az idő múlásával küldött adatok számának nyomon követése
- Adatkötetek összehasonlítása különböző telemetria-adatfolyamok között
- Szűrés adott eszközök által elküldve
- Egy táblázat legutóbbi telemetria-adatnézetének megtekintése

Ez a megoldás olyan folyamatot állít be, amely beolvassa az adatokat a [folyamatos adatexportálási](howto-export-data-blob-storage.md) Azure Blob Storage-fiókból. A folyamat Azure Functions, Azure Data Factory és Azure SQL Database használatával dolgozza fel és alakítja át az adatfeldolgozást. megjelenítheti és elemezheti az PBIX-fájlként letöltött Power BI-jelentésekben lévő összes adathalmazt. Az összes erőforrás az Azure-előfizetésében jön létre, így az egyes összetevők igényei szerint testreszabhatók.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek végrehajtásához aktív Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

A megoldás beállításához a következő erőforrások szükségesek:

- 3. verziójú IoT Central alkalmazás. Az alkalmazás verziószámának megismeréséhez tekintse meg [az alkalmazásról szóló](./howto-get-app-info.md)témakört. IoT Central alkalmazások létrehozásával kapcsolatos további információkért lásd: [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md).
- Folyamatos adatexportálás a telemetria, eszközök és eszközök Azure Blob Storage-ba való exportálására konfigurálva. További információ: [IoT-információk exportálása az Azure-ba célhelyekre](howto-export-data.md).
  - Győződjön meg arról, hogy csak a IoT Central alkalmazás exportálja az adatait a blob-tárolóba.
  - Az [eszközöknek JSON-kódolású üzeneteket kell küldeniük](../../iot-hub/iot-hub-devguide-messages-d2c.md). Az eszközöknek meg kell adniuk `contentType:application/JSON` és vagy `contentEncoding:utf-8` `contentEncoding:utf-16` vagy vagy `contentEncoding:utf-32` az üzenetrendszer tulajdonságait.
- Power BI Desktop (legújabb verzió). Lásd: [Power bi letöltések](https://powerbi.microsoft.com/downloads/).
- Power BI Pro (ha meg szeretné osztani másokkal az irányítópultot).

> [!NOTE]
> Ha 2. verziójú IoT Central alkalmazást használ, tekintse meg az Azure-beli [IoT Central-információk megjelenítése és elemzése egy Power bi irányítópulton](https://docs.microsoft.com/previous-versions/azure/iot-central/core/howto-connect-powerbi) az előző verziók dokumentációs webhelyén.

## <a name="install"></a>Telepítés

A folyamat beállításához navigáljon az Power BI- [megoldáshoz az Azure IoT Central v3](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) oldalához a **Microsoft AppSource** helyen. Válassza a **Letöltés most**lehetőséget, és kövesse az utasításokat.

A PBIX-fájl megnyitásakor ügyeljen rá, hogy olvassa el és kövesse a fedél oldalon található utasításokat. Ezek az utasítások azt írják le, hogyan csatlakoztatható a jelentés az SQL-adatbázishoz.

## <a name="report"></a>Jelentés

A PBIX-fájl tartalmazza az **eszközök és a telemetria** jelentést, amely az eszközök által elindított telemetria korábbi nézetét jeleníti meg. Lebontja a különböző típusú telemetria, és megjeleníti az eszközök által eljuttatott legutóbbi telemetria is.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI megoldási folyamat":::

## <a name="pipeline-resources"></a>Folyamat erőforrásai

A Azure Portal összes olyan Azure-erőforrását elérheti, amely a folyamatot a folyamaton belül hozza létre. Az összes erőforrás abban a csoportban található, amelyet a folyamat beállításakor hozott létre.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="Power BI megoldási folyamat":::

A következő lista ismerteti a folyamat egyes erőforrásainak a szerepét:

### <a name="azure-functions"></a>Azure Functions

Az Azure Function alkalmazás minden alkalommal aktiválódik, IoT Central egy új fájlt ír a blob Storage-ba. A függvények kinyerik az adatokat a telemetria, az eszközökből és az eszközök sablonjaiból, hogy feltöltsék a Azure Data Factory által használt köztes SQL-táblákat.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory csatlakozik a SQL Databasehoz társított szolgáltatásként. Tárolt eljárásokat futtat, hogy feldolgozza az adatfeldolgozást, és tárolja azt az elemzési táblákban.

Azure Data Factory 15 percenként fut, hogy átalakítsa a legutóbbi köteget az SQL-táblákba való betöltéshez (ez a jelenlegi minimális szám a **kieséses ablak-triggernél**).

### <a name="azure-sql-database"></a>Azure SQL Database

A Azure Data Factory elemzési táblázatok készletét hozza létre a Power BIhoz. Ezeket a sémákat megtekintheti Power BIban, és felhasználhatja saját vizualizációk létrehozásához.

## <a name="estimated-costs"></a>Becsült költségek

Az [Azure IoT Central V3 Power bi-megoldás](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) az Microsoft AppSource webhelyén található, a központilag telepített erőforrásokra mutató hivatkozást tartalmaz.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan jelenítheti meg az adatait Power BIban, a javasolt következő lépés az [eszközök felügyeletének](howto-manage-devices.md)megismerése.
