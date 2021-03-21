---
title: Alkalmazás futtatása ZIP-csomagból
description: Az alkalmazás ZIP-csomagjának üzembe helyezése az atomenergia-szolgáltatással. Javíthatja az alkalmazás működésének kiszámíthatóságát és megbízhatóságát a ZIP-telepítési folyamat során.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 036dd8c86251af2ed5553939e74a0d85fc303ecb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577546"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Az alkalmazás futtatása Azure App Service közvetlenül egy ZIP-csomagból

[Azure app Service](overview.md)az alkalmazásokat közvetlenül egy üzembe HELYEZÉSi zip-csomagfájl használatával futtathatja. Ez a cikk bemutatja, hogyan engedélyezheti ezt a funkciót az alkalmazásban.

A App Service összes többi üzembe helyezési módszere közös: a fájlok üzembe helyezése az alkalmazás *D:\home\site\wwwroot* (vagy Linux-alkalmazások */Home/site/wwwroot* ) történik. Mivel az alkalmazás futása közben ugyanazt a könyvtárat használja, lehetséges, hogy az üzembe helyezés sikertelen a zárolási ütközések miatt, és az alkalmazás nem kiszámíthatatlanul viselkedik, mert néhány fájl még nincs frissítve.

Ezzel szemben, ha közvetlenül egy csomagból futtatja, a csomagban lévő fájlok nem másolódnak át a *wwwroot* könyvtárba. Ehelyett maga a ZIP-csomag közvetlenül a csak olvasható *wwwroot* -címtárral lesz csatlakoztatva. A csomagok közvetlen futtatásának számos előnye van:

- Kiküszöböli a zárolási ütközéseket az üzembe helyezés és a futtatókörnyezet között.
- Gondoskodik arról, hogy csak a teljes körűen telepített alkalmazások futnak.
- Üzembe helyezhető egy éles alkalmazásban (újraindítással).
- Javítja Azure Resource Manager üzemelő példányok teljesítményét.
- Csökkentheti a hideg kezdési időpontokat, különösen a JavaScript-függvények esetében nagyméretű NPM-csomagokkal.

> [!NOTE]
> Jelenleg csak a ZIP-csomagok fájljai támogatottak.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Futtatás engedélyezése a csomagból

Az `WEBSITE_RUN_FROM_PACKAGE` Alkalmazásbeállítás lehetővé teszi a futtatást a csomagból. A beállításhoz futtassa a következő parancsot az Azure CLI-vel.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` lehetővé teszi az alkalmazás futtatását egy helyi csomagból az alkalmazásba. [Távoli csomagból is futtatható](#run-from-external-url-instead).

## <a name="run-the-package"></a>A csomag futtatása

A App Service-csomag futtatásának legegyszerűbb módja az Azure CLI az [WebApp Deployment Source config-zip](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) parancs. Például:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Mivel az Alkalmazásbeállítások `WEBSITE_RUN_FROM_PACKAGE` be van állítva, ez a parancs nem bontja ki a csomag tartalmát az alkalmazás *D:\home\site\wwwroot* könyvtárába. Ehelyett feltölti a ZIP-fájlt a *D:\home\data\SitePackages*, és létrehoz egy *packagename.txt* ugyanabban a címtárban, amely tartalmazza a futásidőben betölteni kívánt zip-csomag nevét. Ha a ZIP-csomagot más módon (például [FTP](deploy-ftp.md)) tölti fel, a *D:\home\data\SitePackages* könyvtárat és a *packagename.txt* fájlt manuálisan kell létrehoznia.

A parancs az alkalmazást is újraindítja. Mivel a `WEBSITE_RUN_FROM_PACKAGE` be van állítva, app Service a feltöltött csomagot írásvédett *wwwroot* -címtárként csatlakoztatja, és közvetlenül az adott csatlakoztatott könyvtárból futtatja az alkalmazást.

## <a name="run-from-external-url-instead"></a>Futtatás külső URL-címről

A csomagokat külső URL-címről, például az Azure Blob Storage is futtathatja. A [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) használatával tölthet fel csomagokat a blob Storage-fiókjába. Egy [megosztott hozzáférési aláírással (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) rendelkező privát tárolót kell használnia, amely lehetővé teszi, hogy a app Service futtatókörnyezet biztonságosan hozzáférjen a csomaghoz. 

Miután feltöltötte a fájlt a blob Storage-ba, és egy SAS URL-címmel rendelkezik a fájlhoz, állítsa az `WEBSITE_RUN_FROM_PACKAGE` alkalmazás beállítást az URL-címre. Az alábbi példa az Azure CLI-t használja:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Ha a blob Storage-hoz azonos nevű frissített csomagot tesz közzé, újra kell indítania az alkalmazást, hogy a frissített csomag betöltődik a App Serviceba.

## <a name="troubleshooting"></a>Hibaelhárítás

- A közvetlenül a csomagból való futtatása `wwwroot` csak olvasható. Az alkalmazás hibaüzenetet küld, ha fájlokat próbál írni ebbe a könyvtárba.
- A TAR és a GZIP formátum nem támogatott.
- A ZIP-fájl legfeljebb 1 GB-os lehet
- Ez a funkció nem kompatibilis a [helyi gyorsítótárral](overview-local-cache.md).
- A jobb hidegindító teljesítmény érdekében használja a helyi zip-beállítást ( `WEBSITE_RUN_FROM_PACKAGE` = 1).

## <a name="more-resources"></a>További erőforrások

- [Azure App Service folyamatos üzembe helyezése](deploy-continuous-deployment.md)
- [Kód üzembe helyezése ZIP-vagy WAR-fájllal](deploy-zip.md)
