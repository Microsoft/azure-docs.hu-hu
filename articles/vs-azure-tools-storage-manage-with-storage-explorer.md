---
title: Ismerkedés a Storage Explorerrel | Microsoft Docs
description: Az Azure Storage-erőforrások kezelésének megkezdése Storage Explorerokkal. Töltse le és telepítse a Azure Storage Explorert, kapcsolódjon egy Storage-fiókhoz vagy szolgáltatáshoz, és így tovább.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 3a8fe3ded6608059cc6ad50901ffe6df5dcf1b08
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441582"
---
# <a name="get-started-with-storage-explorer"></a>A Storage Explorer használatának első lépései

## <a name="overview"></a>Áttekintés

A Microsoft Azure Storage Explorer egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszeren.

Ebből a cikkből megismerheti az Azure Storage-fiókok csatlakoztatásának és kezelésének számos módját.

:::image type="content" alt-text="Microsoft Azure Storage Explorer" source="./vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>Előfeltételek

# <a name="windows"></a>[Windows](#tab/windows)

A Windows támogatási Storage Explorer következő verziói:

* Windows 10 (ajánlott)
* Windows 8
* Windows 7

A Windows összes verziójában Storage Explorer legalább a .NET-keretrendszer 4.7.2 szükséges.

# <a name="macos"></a>[macOS](#tab/macos)

A macOS-támogatás következő verziói Storage Explorer:

* macOS 10,12 Sierra és újabb verziók

# <a name="linux"></a>[Linux](#tab/linux)

Az Storage Explorer a Linux leggyakoribb disztribúcióinak [beépülő moduljában](https://snapcraft.io/storage-explorer) érhető el. Javasoljuk, hogy ehhez a telepítéshez a Snap Store-t. A Storage Explorer beépülő modul telepíti az összes függőségét és frissítését, amikor a rendszer közzéteszi az új verziókat az illesztési tárolóban.

A támogatott disztribúciók esetében tekintse meg a [ `snapd` telepítési lapot](https://snapcraft.io/docs/installing-snapd).

Storage Explorer a Password Manager használatát igényli. Előfordulhat, hogy manuálisan kell csatlakoznia a jelszó-kezelőhöz. A következő parancs futtatásával csatlakozhat Storage Explorer a rendszer jelszavas kezelőjéhez:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

A Storage Explorer *. tar. gz* letöltésként is elérhető. Ha a *. tar. gz*-t használja, manuálisan kell telepítenie a függőségeket. A Linux-támogatás következő disztribúciói *. tar. gz* telepítés:

* Ubuntu 20,04 x64
* Ubuntu 18,04 x64
* Ubuntu 16,04 x64

A *. tar. gz* telepítése más disztribúciókban is működhet, de csak ezek a felsoroltak támogatottak.

A Storage Explorer Linux rendszeren való telepítésével kapcsolatos további segítségért tekintse meg a [Linux-függőségek](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) című részt a Azure Storage Explorer hibaelhárítási útmutatójában.

---

## <a name="download-and-install"></a>Letöltés és telepítés

Storage Explorer letöltéséhez és telepítéséhez lásd: [Azure Storage Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Csatlakozás egy tárfiókhoz vagy -szolgáltatáshoz

A Storage Explorer számos lehetőséget kínál az Azure-erőforrásokhoz való kapcsolódásra:

* [Jelentkezzen be az Azure-ba az előfizetések és erőforrásaik eléréséhez](#sign-in-to-azure)
* [Csatolás egyéni Azure Storage-erőforráshoz](#attach-to-an-individual-resource)
* [Csatolás CosmosDB-erőforráshoz](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

> [!NOTE]
> Ahhoz, hogy a bejelentkezés után teljesen hozzáférjen az erőforrásokhoz, Storage Explorer a felügyeleti (Azure Resource Manager) és az adatrétegbeli engedélyeket is. Ez azt jelenti, hogy Azure Active Directory (Azure AD) engedélyek szükségesek a Storage-fiók, a fiókban lévő tárolók és a tárolók adatai eléréséhez. Ha csak az adatréteghez rendelkezik engedélyekkel, érdemes lehet a **bejelentkezést Azure Active Directory (Azure ad)** beállítással használni egy erőforráshoz való csatoláskor. További információ a Storage Explorer szükséges konkrét engedélyekről: [Azure Storage Explorer hibaelhárítási útmutató](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. A Storage Explorer lapon válassza   >  a **Fiókkezelés** megtekintése lehetőséget, vagy kattintson a **fiókok kezelése** gombra.

    :::image type="content" alt-text="Fiókok kezelése" source ="./vs-storage-explorer-manage-accounts.png":::

1. A **Fiókkezelés** mostantól megjeleníti az összes olyan Azure-fiókot, amelybe be van jelentkezve. Egy másik fiókhoz való kapcsolódáshoz válassza a **fiók hozzáadása..**. lehetőséget.

1. Megnyílik a **Kapcsolódás az Azure Storage-hoz** párbeszédpanel. Az **erőforrás kiválasztása** panelen válassza az **előfizetés** lehetőséget.

    :::image type="content" alt-text="Csatlakozási párbeszédpanel" source="./vs-storage-explorer-connect-dialog.png":::

1. Az **Azure-környezet kiválasztása** panelen válassza ki a bejelentkezni kívánt Azure-környezetet. Bejelentkezhet a globális Azure-ba, egy nemzeti felhőbe vagy egy Azure Stack-példányba. Ezután kattintson a **Tovább** gombra.

    :::image type="content" alt-text="Bejelentkezés lehetősége" source="./vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > További információ a Azure Stackről: [Storage Explorer összekötése egy Azure stack előfizetés-vagy Storage-fiókkal](/azure-stack/user/azure-stack-storage-connect-se).

1. Storage Explorer megnyílik egy weblap, amelyen bejelentkezhet.

1. Miután sikeresen bejelentkezett egy Azure-fiókkal, a fiók és az ahhoz hozzárendelt Azure-előfizetések megjelennek **a fiók felügyelete alatt.** Válassza ki azt az Azure-előfizetést, amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget.

    :::image type="content" alt-text="Azure-előfizetések kiválasztása" source="./vs-storage-explorer-account-panel.png":::

1. Az **Explorer** megjeleníti a kiválasztott Azure-előfizetésekhez társított Storage-fiókokat.

    :::image type="content" alt-text="Kiválasztott Azure-előfizetések" source="./vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>Csatolás egy adott erőforráshoz

Storage Explorer lehetővé teszi, hogy különböző hitelesítési módszerekkel kapcsolódjon az egyes erőforrásokhoz, például egy Azure Data Lake Storage Gen2 tárolóhoz. Egyes hitelesítési módszerek csak bizonyos erőforrástípusok esetén támogatottak.

| Erőforrás típusa    | Azure AD | Fiók neve és kulcsa | Közös hozzáférésű jogosultságkód (SAS)  | Nyilvános (Névtelen) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Tárfiókok | Igen      | Yes                  | Igen (a kapcsolatok karakterlánca vagy URL-címe) | No                 |
| Blobtárolók  | Igen      | Nem                   | Igen (URL)                      | Yes                |
| Gen2-tárolók  | Igen      | Nem                   | Igen (URL)                      | Yes                |
| Gen2-címtárak | Igen      | Nem                   | Igen (URL)                      | Yes                |
| Fájlmegosztások      | Nem       | Nem                   | Igen (URL)                      | No                 |
| Üzenetsorok           | Igen      | Nem                   | Igen (URL)                      | No                 |
| Táblák           | Nem       | Nem                   | Igen (URL)                      | No                 |
 
A Storage Explorer az emulátor konfigurált portjaival is csatlakozhat a [helyi tároló-emulátorhoz](#local-storage-emulator) .

Egy adott erőforráshoz való kapcsolódáshoz kattintson a bal oldali eszköztáron a **Kapcsolódás** gombra. Ezután kövesse azon erőforrástípus utasításait, amelyhez csatlakozni szeretne.

:::image type="content" alt-text="Csatlakozás Azure Storage-hoz lehetőség" source="./vs-storage-explorer-connect-button.png":::

Ha a Storage-fiókhoz való kapcsolódás sikeres volt, egy új facsomópont fog megjelenni a **helyi & csatolt**  >  **Storage-fiókokban**.

Más erőforrástípusok esetében új csomópont kerül hozzáadásra a **helyi & csatlakoztatott**  >  **Storage**  >  **-fiókok (csatolt tárolók)** területen. A csomópont a típusának megfelelő csoport csomópont alatt fog megjelenni. Egy Azure Data Lake Storage Gen2 tárolóhoz való új kapcsolódás például a **blob-tárolók** területen jelenik meg.

Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a [Azure Storage Explorer hibaelhárítási útmutatót](./storage/common/storage-explorer-troubleshooting.md).

A következő szakaszok ismertetik az egyes erőforrásokhoz való kapcsolódáshoz használható különböző hitelesítési módszereket.

#### <a name="azure-ad"></a>Azure AD

A Storage Explorer az Azure-fiókkal csatlakozhat a következő típusú erőforrásokhoz:
* Blobtárolók
* Azure Data Lake Storage Gen2 tárolók
* Könyvtárak Azure Data Lake Storage Gen2
* Üzenetsorok
 
Az Azure AD az előnyben részesített lehetőség, ha adatrétege hozzáfér az erőforráshoz, de nincs hozzáférése a felügyeleti réteghez.

1. Jelentkezzen be legalább egy Azure-fiókba a [fent ismertetett lépések](#sign-in-to-azure)segítségével.
1. A **Kapcsolódás az Azure Storage-hoz** párbeszédpanel **erőforrás kiválasztása** paneljén válassza a **blob Container**, **ADLS Gen2 tároló** vagy a **várólista** lehetőséget.
1. Válassza **a bejelentkezés Azure Active Directory (Azure ad) használatával** lehetőséget, majd kattintson a **Tovább gombra**.
1. Válasszon ki egy Azure-fiókot és-bérlőt. A fióknak és a bérlőnek hozzáféréssel kell rendelkeznie a csatolni kívánt tárolási erőforráshoz. Kattintson a **Tovább** gombra.
1. Adja meg a kapcsolatok megjelenítendő nevét és az erőforrás URL-címét. Kattintson a **Tovább** gombra.
1. Tekintse át a kapcsolatok adatait az **Összefoglalás** panelen. Ha a kapcsolati adatok helyesek, **válassza a kapcsolat lehetőséget**.

#### <a name="account-name-and-key"></a>Fiók neve és kulcsa

A Storage Explorer a Storage-fiók nevét és kulcsát használva tud csatlakozni a Storage-fiókhoz.

A fiók kulcsai a [Azure Portalban](https://portal.azure.com)találhatók. Nyissa meg a Storage-fiók lapot, és válassza a **Beállítások**  >  **hozzáférési kulcsok** elemet.

1. A **Kapcsolódás az Azure Storage-hoz** párbeszédpanel **erőforrás kiválasztása** paneljén válassza a **Storage-fiók** lehetőséget.
1. Válassza a **fiók neve és kulcsa** lehetőséget, majd kattintson a **Tovább gombra**.
1. Adja meg a kapcsolatok megjelenítendő nevét, a fiók nevét és az egyik fiók kulcsát. Válassza ki a megfelelő Azure-környezetet. Kattintson a **Tovább** gombra.
1. Tekintse át a kapcsolatok adatait az **Összefoglalás** panelen. Ha a kapcsolati adatok helyesek, **válassza a kapcsolat lehetőséget**.

#### <a name="shared-access-signature-sas-connection-string"></a>Közös hozzáférésű aláírás (SAS) kapcsolati sztringje

Storage Explorer csatlakozhat egy Storage-fiókhoz egy megosztott hozzáférési aláírással (SAS) rendelkező kapcsolati karakterlánc használatával. A SAS-kapcsolatok karakterlánca így néz ki:

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. A **Kapcsolódás az Azure Storage-hoz** párbeszédpanel **erőforrás kiválasztása** paneljén válassza a **Storage-fiók** lehetőséget.
1. Válassza a **közös hozzáférési aláírás (SAS)** lehetőséget, és kattintson a **Tovább gombra**.
1. Adja meg a kapcsolatok megjelenítendő nevét és a Storage-fiók SAS-kapcsolatok sztringjét. Kattintson a **Tovább** gombra.
1. Tekintse át a kapcsolatok adatait az **Összefoglalás** panelen. Ha a kapcsolati adatok helyesek, **válassza a kapcsolat lehetőséget**.

#### <a name="shared-access-signature-sas-url"></a>Közös hozzáférésű aláírás (SAS) URL-címe

A Storage Explorer SAS URI használatával tud csatlakozni a következő erőforrás-típusokhoz:
* Blobtároló
* Azure Data Lake Storage Gen2 tároló vagy könyvtár
* Fájlmegosztás
* Üzenetsor
* Tábla

A SAS URI a következőképpen néz ki:

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. A **Kapcsolódás az Azure Storage-hoz** párbeszédpanel **erőforrás kiválasztása** paneljén válassza ki azt az erőforrást, amelyhez csatlakozni szeretne.
1. Válassza a **közös hozzáférési aláírás (SAS)** lehetőséget, és kattintson a **Tovább gombra**.
1. Adja meg a kapcsolatok megjelenítendő nevét, valamint az erőforrás SAS URI azonosítóját. Kattintson a **Tovább** gombra.
1. Tekintse át a kapcsolatok adatait az **Összefoglalás** panelen. Ha a kapcsolati adatok helyesek, **válassza a kapcsolat lehetőséget**.

#### <a name="local-storage-emulator"></a>Helyi tároló emulátora

Storage Explorer csatlakozhat egy Azure Storage-emulátorhoz. Jelenleg két támogatott emulátor létezik:

* [Azure Storage-emulátor](storage/common/storage-use-emulator.md) (csak Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS vagy Linux)

Ha az emulátor figyeli az alapértelmezett portokat, használhatja a **helyi & csatolt**  >  **Storage**  >  **-fiókok emulátora – az alapértelmezett portok** csomópontot az emulátor eléréséhez.

Ha más nevet szeretne használni a csatlakozáshoz, vagy ha az emulátor nem fut az alapértelmezett portokon:

1. Indítsa el az emulátort.

   > [!IMPORTANT]
   > A Storage Explorer nem indítja el automatikusan az emulátort. Manuálisan kell elindítania.

1. A **Kapcsolódás az Azure Storage-hoz** párbeszédpanel **erőforrás kiválasztása** paneljén válassza a **helyi tároló emulátor** lehetőséget.
1. Adja meg a kapcsolatok megjelenítendő nevét, valamint a használni kívánt emulált szolgáltatások portszámát. Ha nem szeretné használni a szolgáltatást, hagyja üresen a megfelelő portot. Kattintson a **Tovább** gombra.
1. Tekintse át a kapcsolatok adatait az **Összefoglalás** panelen. Ha a kapcsolati adatok helyesek, **válassza a kapcsolat lehetőséget**.

### <a name="connect-to-azure-cosmos-db"></a>Csatlakozás az Azure Cosmos DB-hez

A Storage Explorer a Azure Cosmos DB erőforrásokhoz való csatlakozást is támogatja.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Kapcsolódás Azure Cosmos DB fiókhoz kapcsolati sztring használatával

Azure Cosmos DB fiókok Azure-előfizetésen keresztüli kezelése helyett kapcsolati karakterlánc használatával kapcsolódhat Azure Cosmos DBhoz. A csatlakozáshoz kövesse az alábbi lépéseket:

1. Az **Explorer** alatt bontsa ki a **helyi & csatolva** elemet, kattintson a jobb gombbal **Cosmos db fiókok** elemre, majd válassza **a Kapcsolódás a** következőhöz: Azure Cosmos db

    ![Csatlakozás az Azure Cosmos DB-hez kapcsolati sztringgel][21]

1. Válassza ki a Azure Cosmos DB API-t, adja meg a **kapcsolati karakterlánc** adatait, majd kattintson az **OK** gombra a Azure Cosmos db fiók csatlakoztatásához. További információ a kapcsolódási karakterlánc beolvasásáról: [Azure Cosmos-fiók kezelése](./cosmos-db/how-to-manage-database-account.md).

    ![Kapcsolati sztring][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Kapcsolódás a Azure Data Lake Storehoz URI használatával

Elérheti az előfizetésében nem szereplő erőforrásokat. Szüksége van egy olyan személyre, aki hozzáfér az adott erőforráshoz, hogy megadja az erőforrás URI-JÁT. A bejelentkezést követően az URI használatával csatlakozhat a Data Lake Storehoz. A csatlakozáshoz kövesse az alábbi lépéseket:

1. Az **Explorer** alatt bontsa ki a **helyi & csatolva** elemet.

1. Kattintson a jobb gombbal a **Data Lake Storage Gen1** elemre, majd válassza **a kapcsolódás Data Lake Storage Gen1** lehetőséget.

    ![Kapcsolódás Data Lake Store helyi menühöz](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Adja meg az URI-t, majd kattintson **az OK gombra**. A Data Lake Store a **Data Lake Storage** alatt jelenik meg.

    ![Kapcsolódás Data Lake Store eredményhez](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

Ez a példa Data Lake Storage Gen1 használ. A Azure Data Lake Storage Gen2 mostantól elérhető. További információ: [What is Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Közös hozzáférési aláírás létrehozása Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Fiók szintű közös hozzáférés aláírása

1. Kattintson a jobb gombbal a megosztani kívánt Storage-fiókra, majd válassza a **közös hozzáférési aláírás beolvasása** elemet.

    ![Közös hozzáférésű aláírás helyi menüjének beolvasása][14]

1. A **megosztott hozzáférés aláírása** lapon adja meg a fiókhoz használni kívánt időkeretet és engedélyeket, majd válassza a **Létrehozás** lehetőséget.

    ![Közös hozzáférési aláírás beszerzése][15]

1. Másolja a **kapcsolódási karakterláncot** vagy a nyers **lekérdezési karakterláncot** a vágólapra.

### <a name="service-level-shared-access-signature"></a>A szolgáltatási szint megosztott hozzáférésének aláírása

A szolgáltatás szintjén közös hozzáférési aláírást kaphat. További információ: [sas beszerzése blob-tárolóhoz](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Tárfiókok keresése

A tárolási erőforrások kereséséhez kereshet az **Explorer** ablaktáblán.

Amikor szöveget ír be a keresőmezőbe, Storage Explorer megjeleníti az összes olyan erőforrást, amely megfelel az adott ponthoz megadott keresési értéknek. Ez a példa a **végpontok** keresését mutatja be:

![Tárfiók keresése][23]

> [!NOTE]
> A keresés felgyorsításához a **Fiókkezelés** használatával törölje azokat az előfizetéseket, amelyek nem tartalmazzák a keresett elemet. Kattintson a jobb gombbal a csomópontra, és válassza a **Keresés itt** lehetőséget egy adott csomópont keresésének megkezdéséhez.

## <a name="next-steps"></a>Következő lépések

* [Azure Blob Storage-erőforrások kezelése Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Adatok kezelése az Azure Storage Explorerrel](./cosmos-db/storage-explorer.md)
* [Azure Data Lake Store erőforrások kezelése a Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
