---
title: Data Lake Storage Gen1 integrálása Azure Data Catalog
description: Megtudhatja, hogyan regisztrálhat adatok Azure Data Catalog Azure Data Lake Storage Gen1ból, hogy az adatok felderíthetők legyenek a szervezetben.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 02544489816f5711ca6e599c2bce03737c747934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106620"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Adatok regisztrálása Azure Data Lake Storage Gen1ról Azure Data Catalog
Ebből a cikkből megtudhatja, hogyan integrálhatja a Azure Data Lake Storage Gen1t a Azure Data Catalogekkel, hogy a szervezeten belül felfedjék az adatait a Data Catalog integrálásával. További információ az adatok katalogizálása: [Azure Data Catalog](../data-catalog/overview.md). A Data Catalog használatára szolgáló forgatókönyvek megismeréséhez tekintse meg a [Azure Data Catalog gyakori forgatókönyvek](../data-catalog/data-catalog-common-scenarios.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Engedélyezze Data Lake Storage Gen1 Azure-előfizetését** . Lásd: [utasítások](data-lake-store-get-started-portal.md).
* **Egy Data Lake Storage Gen1-fiók**. Kövesse a [Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](data-lake-store-get-started-portal.md)című témakör utasításait. Ebben az oktatóanyagban hozzon létre egy **datacatalogstore** nevű Data Lake Storage Gen1 fiókot.

    Miután létrehozta a fiókot, töltsön fel egy minta adathalmazt. Ebben az oktatóanyagban töltse fel a **AmbulanceData** mappában található összes. csv fájlt a [Azure Data Lake git-tárházban](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Az adatok blob-tárolóba való feltöltéséhez különböző ügyfeleket, például [Azure Storage Explorereket](https://storageexplorer.com/)használhat.
* **Azure Data Catalog**. A szervezetnek már létre kell hoznia egy Azure Data Catalog a szervezet számára. Minden szervezet esetében csak egy katalógus engedélyezett.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Data Lake Storage Gen1 regisztrálása Data Catalog forrásaként

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Nyissa meg a t `https://azure.microsoft.com/services/data-catalog` , és kattintson az első **lépések** elemre.
1. Jelentkezzen be az Azure Data Catalog-portálra, majd kattintson a **Publish data** (Adatok közzététele) lehetőségre.

    ![Adatforrás regisztrálása](./media/data-lake-store-with-data-catalog/register-data-source.png "Adatforrás regisztrálása")
1. A következő lapon kattintson az **alkalmazás indítása** lehetőségre. Ekkor a rendszer letölti az alkalmazás jegyzékfájlját a számítógépen. Az alkalmazás elindításához kattintson duplán a manifest-fájlra.
1. Az Üdvözöljük lapon kattintson a **Bejelentkezés** elemre, és adja meg a hitelesítő adatait.

    ![Üdvözlőképernyő](./media/data-lake-store-with-data-catalog/welcome.screen.png "Üdvözlőképernyő")
1. Az adatforrás kiválasztása lapon válassza a **Azure Data Lake Store** lehetőséget, majd kattintson a **tovább** gombra.

    ![Adatforrás kiválasztása](./media/data-lake-store-with-data-catalog/select-source.png "Adatforrás kiválasztása")
1. A következő lapon adja meg a Data Catalog regisztrálni kívánt Data Lake Storage Gen1 fiók nevét. Hagyja meg a többi beállítást alapértelmezettként, majd kattintson a **kapcsolat** gombra.

    ![Kapcsolódás adatforráshoz](./media/data-lake-store-with-data-catalog/connect-to-source.png "Kapcsolódás adatforráshoz")
1. A következő oldal a következő szegmensekre osztható.

    a. A **kiszolgálói hierarchia** mező a Data Lake Storage Gen1 fiók mappájának struktúráját jelöli. **$Root** a Data Lake Storage Gen1-fiók gyökerét jelöli, a **AmbulanceData** pedig az Data Lake Storage Gen1 fiók gyökerében létrehozott mappát jelöli.

    b. A **rendelkezésre álló objektumok** mező felsorolja a **AmbulanceData** mappában található fájlokat és mappákat.

    c. A **regisztrálni kívánt objektumok** lista felsorolja azokat a fájlokat és mappákat, amelyeket regisztrálni szeretne Azure Data Catalogban.

    ![Képernyőkép a Microsoft Azure Data Catalog – áruházbeli fiók párbeszédpanelről.](./media/data-lake-store-with-data-catalog/view-data-structure.png "Adatstruktúra megtekintése")
1. Ebben az oktatóanyagban regisztrálnia kell a címtárban található összes fájlt. Ehhez kattintson a (![objektumok áthelyezése](./media/data-lake-store-with-data-catalog/move-objects.png "Objektumok áthelyezése")) gombra az összes fájlnak a **regisztrálni kívánt objektumba** való áthelyezéséhez.

    Mivel az adatok egy szervezetre kiterjedő adatkatalógusban lesznek regisztrálva, ajánlott olyan metaadatokat hozzáadni, amelyeket később az adatok gyors megkereséséhez használhat fel. Hozzáadhat például egy e-mail-címet az adattulajdonoshoz (például egy, aki feltölti az adatfájlokat), vagy hozzáadhat egy címkét az adatazonosításhoz. Az alábbi képernyőfelvételen egy olyan címke látható, amelyet az adatbevitelhez adott hozzá.

    ![Képernyőkép a Microsoft Azure Data Catalog-Store-fiók párbeszédpanelről, amely az elnevezett adatmennyiséghez hozzáadott címkével rendelkezik.](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Adatstruktúra megtekintése")

    Kattintson a **Regisztrálás** parancsra.
1. Az alábbi képernyőfelvétel azt jelzi, hogy az adatData Catalog sikeresen regisztrálva lettek.

    ![A regisztráció befejeződött](./media/data-lake-store-with-data-catalog/registration-complete.png "Adatstruktúra megtekintése")
1. Kattintson a **portál megtekintése** gombra, és térjen vissza a Data Catalog portálra, és ellenőrizze, hogy most már elérhető-e a regisztrált adatok a portálon. Az adatkereséshez használhatja az adatregisztráció során használt címkét is.

     ![Keresés az adatkatalógusban](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Keresés az adatkatalógusban")
1. Mostantól olyan műveleteket is végrehajthat, mint például a jegyzetek és a dokumentáció hozzáadása az adatokhoz. További információkért tekintse meg a következő hivatkozásokat.

    * [Az adatforrások megjegyzései Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Adatforrások dokumentálása Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Lásd még
* [Az adatforrások megjegyzései Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Adatforrások dokumentálása Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Data Lake Storage Gen1 integrálása más Azure-szolgáltatásokkal](data-lake-store-integrate-with-other-services.md)