---
title: Az alkalmazás futtatása ZIP-csomagból
description: Az alkalmazás ZIP-csomagját atomitással helyezheti üzembe. Javíthatja az alkalmazás viselkedésének kiszámíthatóságát és megbízhatóságát a ZIP üzembe helyezési folyamat során.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: d3315370342f54091598aa3f77f70f03bda4ad33
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772738"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Az alkalmazás futtatása Azure App Service ZIP-csomagból

A [Azure App Service](overview.md)az alkalmazásokat közvetlenül egy központi telepítési ZIP-csomagfájlból futtathatja. Ez a cikk bemutatja, hogyan engedélyezheti ezt a funkciót az alkalmazásban.

A App Service összes többi üzembe helyezési metódusa tartalmaz valami közöset: a fájlok a *D:\home\site\wwwroot* mappában vannak telepítve az alkalmazásban (vagy */home/site/wwwroot* Linux-alkalmazások esetén). Mivel az alkalmazás futtatáskor ugyanazt a könyvtárat használja, előfordulhat, hogy az üzembe helyezés fájlzárolási ütközések miatt meghiúsul, és az alkalmazás kiszámíthatatlan módon viselkedik, mivel egyes fájlok még nincsenek frissítve.

Ezzel szemben, ha közvetlenül egy csomagból futtatja a fájlt, a rendszer nem másolja a csomagban lévő fájlokat a *wwwroot könyvtárba.* Ehelyett maga a ZIP-csomag közvetlenül a csak olvasható *wwwroot* könyvtárként lesz csatlakoztatva. A közvetlenül egy csomagból való futtatásnak számos előnye van:

- Kiküszöböli az üzembe helyezés és a futásidő közötti fájlzárolási ütközéseket.
- Gondoskodik arról, hogy mindig csak a teljes telepítésű alkalmazások futnak.
- Üzembe helyezhető éles alkalmazásban (újraindítással).
- Javítja az üzemelő példányok Azure Resource Manager teljesítményét.
- Csökkentheti a hideg indítási időket, különösen a nagy npm-csomagfákat használó JavaScript-függvények esetében.

> [!NOTE]
> Jelenleg csak a ZIP-csomagfájlok támogatottak.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Csomagból való futtatás engedélyezése

Az `WEBSITE_RUN_FROM_PACKAGE` alkalmazásbeállítás lehetővé teszi a csomagból való futtatást. A beállításhoz futtassa a következő parancsot az Azure CLI-val.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` lehetővé teszi az alkalmazás futtatását egy helyi csomagból az alkalmazásba. Távoli [csomagból is futtathatja a-t.](#run-from-external-url-instead)

## <a name="run-the-package"></a>A csomag futtatása

A legegyszerűbben az Azure CLI [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip) paranccsal futtathat csomagokat a App Service a paranccsal. Például:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Mivel az alkalmazásbeállítás be van állítva, ez a parancs nem csomagolja ki a csomag tartalmát az alkalmazás `WEBSITE_RUN_FROM_PACKAGE` *D:\home\site\wwwroot* könyvtárába. Ehelyett feltölti a ZIP-fájlt a *D:\home\data\SitePackages* mappába, és létrehoz egy *packagename.txt-t* ugyanabban a könyvtárban, amely tartalmazza a futásidőben betöltni kívánt ZIP-csomag nevét. Ha más módon tölti fel a ZIP-csomagot (például [FTP),](deploy-ftp.md)manuálisan létre kell hoznia a *D:\home\data\SitePackages* könyvtárat és a *packagename.txt* fájlt.

A parancs az alkalmazást is újraindítja. Mivel a be van állítva, App Service a feltöltött csomagot csak olvasható wwwroot könyvtárként csatlakoztatja, és közvetlenül a csatlakoztatott könyvtárból `WEBSITE_RUN_FROM_PACKAGE` futtatja az  alkalmazást.

## <a name="run-from-external-url-instead"></a>Futtatás külső URL-címről

A csomagokat külső URL-címről is futtathatja, például a Azure Blob Storage. A blobtároló [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) feltöltheti a csomagfájlokat a Blob Storage-fiókba. Közös hozzáférésű jogosultsággal [(SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) használt privát tárolót kell használnia, hogy a App Service-App Service biztonságosan hozzáfér a csomaghoz. 

Miután feltöltötte a fájlt a Blob Storage-be, és már van SAS URL-címe a fájlhoz, állítsa az alkalmazásbeállítást az `WEBSITE_RUN_FROM_PACKAGE` URL-címre. Az alábbi példa ezt az Azure CLI használatával teszi meg:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Ha egy azonos nevű frissített csomagot tesz közzé a Blob Storage-ba, újra kell indítania az alkalmazást, hogy a frissített csomag betöltve App Service.

## <a name="troubleshooting"></a>Hibaelhárítás

- A közvetlenül egy csomagból való futtatás csak `wwwroot` olvasható. Az alkalmazás hibaüzenetet kap, ha ebbe a könyvtárba próbál fájlokat írni.
- A TAR- és GZIP-formátumok nem támogatottak.
- A ZIP-fájl 1 GB-ig lehet
- Ez a funkció nem kompatibilis a helyi [gyorsítótárral.](overview-local-cache.md)
- A jobb hideg indítási teljesítmény érdekében használja a helyi Zip lehetőséget ( `WEBSITE_RUN_FROM_PACKAGE` =1).

## <a name="more-resources"></a>További erőforrások

- [Folyamatos üzembe helyezés Azure App Service](deploy-continuous-deployment.md)
- [Kód üzembe helyezése ZIP- vagy WAR-fájllal](deploy-zip.md)
