---
title: Azure Functions futtatása csomagból
description: A Azure Functions futtatókörnyezettel futtassa a függvényeket a Function app-projektfájlok fájljait tartalmazó központi telepítési csomagfájl csatlakoztatásával.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: aad6991d0ddd5c439d03e41adec63837a21db87b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581592"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Azure Functions futtatása csomagfájl

Az Azure-ban a függvényeket közvetlenül egy központi telepítési csomagból is futtathatja a Function alkalmazásban. A másik lehetőség, hogy a fájlokat a `d:\home\site\wwwroot` Function alkalmazás könyvtárába telepíti.

Ez a cikk a függvények csomagból való futtatásának előnyeit ismerteti. Azt is bemutatja, hogyan engedélyezheti ezt a funkciót a Function alkalmazásban.

## <a name="benefits-of-running-from-a-package-file"></a>A csomagfájl futtatásának előnyei
  
A csomagfájl számos előnnyel jár:

+ Csökkenti a fájlmásolás-zárolási problémák kockázatát.
+ Üzembe helyezhető egy éles alkalmazásban (újraindítással).
+ Biztos lehet benne, hogy az alkalmazásban futó fájlok vannak.
+ Javítja Azure Resource Manager üzemelő [példányok](functions-infrastructure-as-code.md)teljesítményét.
+ Csökkentheti a hideg kezdési időpontokat, különösen a JavaScript-függvények esetében nagyméretű NPM-csomagokkal.

További információkért tekintse meg [ezt a bejelentést](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>A függvények csomagból való futtatásának engedélyezése

Ha engedélyezni szeretné, hogy a Function alkalmazás egy csomagból fusson, egyszerűen adjon hozzá egy `WEBSITE_RUN_FROM_PACKAGE` beállítást a Function app-beállításokhoz. A `WEBSITE_RUN_FROM_PACKAGE` beállítás a következő értékek egyike lehet:

| Érték  | Leírás  |
|---------|---------|
| **`1`**  | A Windows rendszeren futó Function apps esetében ajánlott. Futtasson egy Package fájlból a `d:\home\data\SitePackages` Function alkalmazás mappájából. Ha nem [telepíti a zip-telepítést](#integration-with-zip-deployment), ez a beállítás azt igényli, hogy a mappában is legyen egy nevű fájl `packagename.txt` . Ez a fájl csak a mappában lévő csomagfájl nevét tartalmazza szóköz nélkül. |
|**`<URL>`**  | A futtatni kívánt csomag adott fájljának helye. A blob Storage használatakor egy [megosztott hozzáférési aláírással (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) rendelkező privát tárolót kell használnia, hogy a functions futtatókörnyezet hozzáférhessen a csomaghoz. A [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) használatával tölthet fel csomagokat a blob Storage-fiókjába. URL-cím megadásakor az [eseményindítókat is szinkronizálnia](functions-deployment-technologies.md#trigger-syncing) kell, miután közzétett egy frissített csomagot. |

> [!CAUTION]
> Ha Windows rendszeren futtat egy Function alkalmazást, a külső URL-cím beállításával rosszabb az indítási teljesítmény. Ha a Function alkalmazást a Windows rendszerre telepíti, be kell `WEBSITE_RUN_FROM_PACKAGE` állítania `1` és közzé kell tennie a zip-telepítéssel.

Az alábbi példa egy, az Azure Blob Storage-ban üzemeltetett. zip-fájlból való futtatásra konfigurált Function-alkalmazást mutat be:

![Alkalmazás-beállítás WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Jelenleg csak a. zip csomagfájl támogatottak.

## <a name="integration-with-zip-deployment"></a>Integráció a zip-telepítéssel

A [zip-telepítés][Zip deployment for Azure Functions] a Azure app Service szolgáltatása, amely lehetővé teszi a Function app-projekt üzembe helyezését a `wwwroot` címtárban. A projekt. zip telepítési fájlként van csomagolva. Ugyanazokat az API-kat használhatja a csomag telepítéséhez a `d:\home\data\SitePackages` mappába. Az Alkalmazásbeállítások `WEBSITE_RUN_FROM_PACKAGE` értékeként `1` a zip-telepítési API-k a fájlok mappába másolják a csomagot `d:\home\data\SitePackages` `d:\home\site\wwwroot` . Létrehozza a fájlt is `packagename.txt` . Újraindítás után a csomag `wwwroot` írásvédett fájlrendszerhez van csatlakoztatva. További információ a zip-telepítésről: [a Azure functions zip központi telepítése](deployment-zip-push.md).

> [!NOTE]
> Központi telepítés esetén a rendszer elindítja a Function alkalmazás újraindítását. Újraindítás előtt az összes meglévő függvény végrehajtásának befejezése vagy időtúllépése engedélyezett. További információ: a [központi telepítés viselkedése](functions-deployment-technologies.md#deployment-behaviors).

## <a name="adding-the-website_run_from_package-setting"></a>Az WEBSITE_RUN_FROM_PACKAGE-beállítás hozzáadása

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Hibaelhárítás

- A Futtatás a csomagból `wwwroot` csak olvasható, így hibaüzenetet kap, amikor fájlokat ír a könyvtárba.
- A tar és a gzip formátum nem támogatott.
- A ZIP-fájl legfeljebb 1 GB-nyi lehet.
- Ez a funkció nem a helyi gyorsítótárral együtt működik.
- A jobb hidegindító teljesítmény érdekében használja a helyi zip-beállítást ( `WEBSITE_RUN_FROM_PACKAGE` = 1).
- A Futtatás a csomagból nem kompatibilis a telepítés testreszabási beállításával ( `SCM_DO_BUILD_DURING_DEPLOYMENT=true` ), a rendszer figyelmen kívül hagyja a Build lépést az üzembe helyezés során.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
