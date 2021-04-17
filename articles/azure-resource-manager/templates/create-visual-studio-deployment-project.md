---
title: Visual Studio Resource Group-projektek létrehozása & üzembe helyezése
description: A Visual Studióval hozzon létre egy Azure erőforráscsoport-projektet, és telepítse az erőforrásokat az Azure-ba.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 4c0e19042506e0cf1772398b0a02b81f0245b1c7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306976"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Azure erőforráscsoport-sablonok létrehozása és telepítése a Visual Studio alkalmazással

A Visual Studio alkalmazással olyan projekteket hozhat létre, amelyekkel telepíthető az infrastruktúra és kód az Azure rendszerébe. Telepítheti például a webhelyhez tartozó webállomást, webhelyet és kódot. A Visual Studio számos különböző kezdősablont kínál a gyakori forgatókönyvek telepítéséhez. Ebben a cikkben egy webalkalmazást telepítünk.

Ez a cikk bemutatja, hogyan használható [a Visual Studio 2019-es vagy újabb verziója a telepített Azure-beli fejlesztési és ASP.net számítási feladatokkal](/visualstudio/install/install-visual-studio). Ha a Visual Studio 2017-et használja, a felhasználói élmény nagyjából azonos.

## <a name="create-azure-resource-group-project"></a>Azure erőforráscsoport-projekt létrehozása

Ebben a szakaszban egy Azure erőforráscsoport-projektet hoz létre egy **webalkalmazás** -sablonnal.

1. A Visual Studióban válassza a **fájl** > **új** > **projekt** lehetőséget.
1. Válassza ki az **Azure erőforráscsoport** -projekt sablonját, és kattintson a **Tovább gombra**.

    ![Képernyőfelvétel: az új projekt létrehozása ablak az Azure-erőforráscsoport és a következő gomb kiemelése.](./media/create-visual-studio-deployment-project/create-project.png)

1. Adjon nevet a projektnek. A többi alapértelmezett beállítás valószínűleg megfelelő, de tekintse át őket, hogy működjenek a környezetében. Ha elkészült, válassza a **Létrehozás** lehetőséget.

    ![Projekt létrehozása](./media/create-visual-studio-deployment-project/name-project.png)

1. Válassza ki az Azure Resource Managerbe telepíteni kívánt sablont. Figyelje meg, hogy a telepíteni kívánt projekt típusától függően számos különböző lehetőség áll rendelkezésre. Ehhez a cikkhez válassza a **webalkalmazás** -sablont, és **az OK gombra**.

    ![Sablon kiválasztása](./media/create-visual-studio-deployment-project/select-project.png)

    A kiválasztott sablon csak egy kiindulási pont. A forgatókönyvnek való megfelelés érdekében hozzáadhat és eltávolíthat erőforrásokat.

1. A Visual Studio létrehoz egy erőforráscsoport-telepítési projektet a webalkalmazáshoz. A projekt fájljainak megtekintéséhez tekintse meg a telepítési projektben található csomópontot.

    ![Csomópontok megjelenítése](./media/create-visual-studio-deployment-project/show-items.png)

    A webalkalmazás-sablon választása óta a következő fájlok láthatók:

   | Fájlnév | Description |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |PowerShell-parancsfájl, amely PowerShell-parancsokat futtat az Azure Resource Manager üzembe helyezéséhez. A Visual Studio ezt a PowerShell-parancsfájlt használja a sablon üzembe helyezéséhez. |
   | WebSite.jsbekapcsolva |Az Azure szolgáltatásban telepíteni kívánt infrastruktúrát, valamint a telepítés során megadható paramétereket meghatározó Resource Manager-sablon. A telepített erőforrások közti függőségeket is meghatározza, így a Resource Manager megfelelő sorrendben telepíti azokat. |
   | WebSite.parameters.jsbekapcsolva |Paraméterfájl, amely a sablonhoz szükséges értékeket tartalmazza. Megadhat paraméterértékeket, amelyekkel testre szabhatóak az egyes telepítések. Figyelje meg, hogy a **Build művelet** **tartalomra** van beállítva. Ha további paramétereket ad hozzá, győződjön meg arról, hogy a Build művelet **tartalomra** van beállítva. |

    Mindegyik erőforráscsoport-telepítési projekt tartalmazza ezeket az alapvető fájlokat. Más projektek több fájllal is rendelkezhetnek más funkciók támogatásához.

## <a name="customize-resource-manager-template"></a>Resource Manager-sablon testreszabása

A központi telepítési projektet testreszabhatja úgy, hogy módosítja a telepíteni kívánt erőforrásokat leíró Resource Manager-sablont. A Resource Manager-sablon elemeivel kapcsolatos információkért lásd: [Azure Resource Manager-sablonok készítése](template-syntax.md).

1. A sablonon való működéshez nyissa meg **aWebSite.jst a** következőn:.

1. A Visual Studio szerkesztő eszközöket biztosít a Resource Manager-sablon szerkesztéséhez. A **JSON-vázlat** ablak segítségével könnyen áttekinthetőek a sablonban meghatározott elemek.

   ![JSON-körvonal megjelenítése](./media/create-visual-studio-deployment-project/show-json-outline.png)

1. Válasszon ki egy elemet a vázlatban a sablon adott részének megadásához.

   ![Navigáljon a JSON-hoz](./media/create-visual-studio-deployment-project/navigate-json.png)

1. Hozzáadhat egy új erőforrást a JSON-vázlat ablak tetején található **Erőforrás hozzáadása** gomb kiválasztásával, vagy kattintson a jobb gombbal az **erőforrások** elemre, és válassza az **Új erőforrás hozzáadása** lehetőséget.

   ![A képernyőképen a JSON-vázlat ablak látható az új erőforrás hozzáadása lehetőség kiemelve.](./media/create-visual-studio-deployment-project/add-resource.png)

1. Válassza ki a **Storage-fiókot** , és adjon meg egy nevet. Olyan nevet adjon meg, amely nem hosszabb 11 karakternél, és csak számokat és kisbetűket tartalmaz.

   ![Tároló hozzáadása](./media/create-visual-studio-deployment-project/add-storage.png)

1. Figyelje meg, hogy nem csupán az erőforrás lett hozzáadva, hanem a tárfiók típusának paramétere is, valamint egy változó a tárfiók nevével.

   ![Körvonal megjelenítése](./media/create-visual-studio-deployment-project/show-new-items.png)

1. A Storage-fiók típusának paraméterét a rendszer előre definiálja az engedélyezett típusokkal és az alapértelmezett típussal. Megtarthatja ezeket az értékeket, vagy módosíthatja őket az adott forgatókönyvnek megfelelően. Ha nem szeretné, hogy bárki **Premium_LRS** tárfiókot helyezzen üzembe a sablonon keresztül, egyszerűen törölje azt az engedélyezett típusok közül.

   ```json
   "demoaccountType": {
     "type": "string",
     "defaultValue": "Standard_LRS",
     "allowedValues": [
       "Standard_LRS",
       "Standard_ZRS",
       "Standard_GRS",
       "Standard_RAGRS"
     ]
   }
   ```

1. A Visual Studio olyan IntelliSense-t is biztosít, amely segít megérteni a sablon szerkesztésekor elérhető tulajdonságokat. Például az App Service-csomag tulajdonságainak szerkesztéséhez lépjen a **HostingPlan** erőforrásra, és adjon meg egy értéket a **properties** elemnél. Figyelje meg, hogy az intellisense megjeleníti az elérhető értékeket, valamint az adott értékek leírását.

   ![IntelliSense megjelenítése](./media/create-visual-studio-deployment-project/show-intellisense.png)

   Beállíthatja a **numberOfWorkers** 1 értékre, és mentheti a fájlt.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Nyissa meg a **WebSite.parameters.js** fájlt. A parameters (paraméterek) fájl segítségével adja át az értékeket a központi telepítés során, amely testreszabja az üzembe helyezett erőforrást. Adja meg az üzemeltetési csomag nevét, és mentse a fájlt.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       }
     }
   }
   ```

## <a name="deploy-project-to-azure"></a>Projekt üzembe helyezése az Azure-ban

Most már készen áll a projekt egy erőforráscsoporthoz való üzembe helyezésére.

Alapértelmezés szerint a projektben található PowerShell-szkript (Deploy-AzureResourceGroup.ps1) a AzureRM modult használja. Ha még mindig telepítette a AzureRM modult, és szeretné tovább használni, használhatja ezt az alapértelmezett parancsfájlt. Ezzel a parancsfájllal a Visual Studio felületén üzembe helyezheti a megoldást.

Ha azonban áttelepítette az új az [modult](/powershell/azure/new-azureps-module-az), új parancsfájlt kell hozzáadnia a projekthez. Az az modult használó parancsfájl hozzáadásához másolja a [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) -parancsfájlt, és adja hozzá a projekthez. Ha ezt a parancsfájlt a központi telepítéshez szeretné használni, a Visual Studio telepítési felületének használata helyett egy PowerShell-konzolból kell futtatnia.

Ebben a cikkben mindkét megközelítés látható. Ez a cikk az alapértelmezett parancsfájlra hivatkozik, amely az AzureRM modul parancsfájlja, az új parancsfájl pedig az az Module script.

### <a name="az-module-script"></a>Az Module script

Az az Module script esetében nyisson meg egy PowerShell-konzolt, és futtassa a következő parancsot:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>AzureRM modul parancsfájlja

A AzureRM modul parancsfájlhoz használja a Visual studiót:

1. A központi telepítési projekt csomópontjának helyi menüjében válassza az   >  **új** telepítése lehetőséget.

    ![Új központi telepítési menüelem](./media/create-visual-studio-deployment-project/deploy.png)

1. Megjelenik a **Telepítés erőforráscsoportra** párbeszédpanel. Az **Erőforráscsoport** legördülő listából válasszon ki egy létező erőforráscsoportot, vagy hozzon létre egy újat. Válassza az **Üzembe helyezés** lehetőséget.

    ![Üzembe helyezés erőforráscsoport-párbeszédpanelen](./media/create-visual-studio-deployment-project/show-deployment.png)

1. A **Kimenet** ablakban követhető az üzembe helyezés állapota. Az üzembe helyezés befejeztével az utolsó üzenet jelzi az üzembe helyezés sikerességét, a következőhöz hasonló módon:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Üzembe helyezett erőforrások megtekintése

Vizsgáljuk meg az eredményeket.

1. Egy böngészőben nyissa meg az [Azure Portalt](https://portal.azure.com/), és jelentkezzen be a fiókjával. Az erőforráscsoport megtekintéséhez válassza az **Erőforráscsoportok** lehetőséget, valamint az erőforráscsoportot, amelyiken a telepítést végezte.

1. Az összes telepített erőforrás megjelenik. Figyeljen arra, hogy a tárfiók neve nem pontosan az, amit az erőforrás hozzáadásakor megadott. A tárfiók nevének egyedinek kell lennie. A sablon automatikusan egy karakterláncot ad hozzá a megadott névhez, amely egyedi nevet hoz létre.

    ![Erőforrások megjelenítése](./media/create-visual-studio-deployment-project/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Kód hozzáadása a projekthez

Ezen a ponton az alkalmazás infrastruktúrája már telepítve van, tényleges kód azonban még nincs telepítve a projekttel.

1. Adjon hozzá egy projektet a Visual Studio megoldásához. Kattintson a jobb gombbal a megoldásra, majd válassza az  >  **új projekt** hozzáadása lehetőséget.

    ![Projekt hozzáadása](./media/create-visual-studio-deployment-project/add-project.png)

1. **ASP.net Core webalkalmazás** hozzáadása.

    ![Webalkalmazás hozzáadása](./media/create-visual-studio-deployment-project/add-app.png)

1. Adjon nevet a webalkalmazásnak, és válassza a **Létrehozás** lehetőséget.

    ![Webhely neve webalkalmazás](./media/create-visual-studio-deployment-project/name-web-app.png)

1. Válassza a **webalkalmazás** lehetőséget, és **hozzon létre**.

    ![Webalkalmazás kiválasztása](./media/create-visual-studio-deployment-project/select-project-type.png)

1. Miután a Visual Studio létrehozta a webalkalmazást, a megoldásban mindkét projekt megjelenik.

    ![Projektek megjelenítése](./media/create-visual-studio-deployment-project/show-projects.png)

1. Most győződjön meg arról, hogy az erőforráscsoport észleli az új projektet. Térjen vissza az erőforráscsoport-projekthez (ExampleAppDeploy). Kattintson jobb gombbal a **References** (Hivatkozások) elemre és válassza az **Add Reference** (Hivatkozás hozzáadása) lehetőséget.

    ![Képernyőfelvétel: a ExampleAppDeploy menü és a hivatkozás hozzáadása lehetőség látható.](./media/create-visual-studio-deployment-project/add-new-reference.png)

1. Válassza ki a webalkalmazás-projektet, amelyet létrehozott.

   ![Hivatkozás hozzáadása](./media/create-visual-studio-deployment-project/add-reference.png)

   Egy hivatkozás hozzáadásával összekapcsolja a webalkalmazás-projektet az erőforráscsoport-projekttel, és automatikusan beállítja néhány tulajdonságot. Ezek a tulajdonságok a hivatkozáshoz tartozó **Properties** (Tulajdonságok) ablakban láthatók. A **Fájl elérési útjának belefoglalása** azt az útvonalat tartalmazza, ahol a csomag létrejött. Jegyezze meg a mappa (ExampleApp) és a fájl (package.zip) nevét. Ismernie kell ezeket az értékeket, mert az alkalmazás üzembe helyezésekor meg kell adni őket paraméterekként.

   ![Lásd: hivatkozás](./media/create-visual-studio-deployment-project/see-reference.png)

1. Térjen vissza a sablonhoz (WebSite.json), és adjon hozzá egy erőforrást a sablonhoz.

    ![Erőforrás hozzáadása](./media/create-visual-studio-deployment-project/add-resource-2.png)

1. Ez alkalommal válassza a **Web Deploy for Web Apps** (Webalkalmazások webes üzembe helyezése) lehetőséget.

    ![Web Deploy hozzáadása](./media/create-visual-studio-deployment-project/add-web-deploy.png)

   Mentse a sablont.

1. A sablonban néhány új paraméter szerepel. Ezek az előző lépésben lettek hozzáadva. Nem kell megadnia **_artifactsLocation** vagy **_artifactsLocationSasToken** értékét, mivel ezek az értékek automatikusan létrejönnek. A mappát és a fájlnevet azonban a központi telepítési csomagot tartalmazó elérési útra kell beállítania. Ezeknek a paramétereknek a nevei a **PackageFolder** és a **PackageFileName** végződéssel rendelkeznek. A név első része a hozzáadott webes üzembe helyezési erőforrás neve. Ebben a cikkben a **ExampleAppPackageFolder** és a **ExampleAppPackageFileName** nevet kapta.

   Nyissa megWebsite.parameters.jsbe, majd állítsa be ezeket a paramétereket a hivatkozás tulajdonságai között **megjelenő** értékekre. Állítsa a **ExampleAppPackageFolder** a mappa nevére. Állítsa a **ExampleAppPackageFileName** nevet a zip-fájl nevére.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       },
       "ExampleAppPackageFolder": {
         "value": "ExampleApp"
       },
       "ExampleAppPackageFileName": {
         "value": "package.zip"
       }
     }
   }
   ```

## <a name="deploy-code-with-infrastructure"></a>Kód üzembe helyezése infrastruktúrával

Mivel programkódot adott hozzá a projekthez, az üzemelő példány egy kicsit más. Az üzembe helyezés során a projekthez tartozó összetevők a Resource Manager által elérhető helyre kerülnek. Az összetevők egy Storage-fiókba vannak berendezve.

### <a name="az-module-script"></a>Az Module script

Ha az az Module parancsfájlt használja, egy kis módosítást kell végeznie a sablonban. Ez a szkript egy perjelet szúr be az összetevők helyére, de a sablon nem vár erre a perjelre. Nyissa meg a WebSite.jst, és keresse meg a MSDeploy-bővítmény tulajdonságait. Rendelkezik egy **packageUri** nevű tulajdonsággal. Távolítsa el az összetevők helye és a csomag mappája közötti perjelet.

Ennek így kell kinéznie:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Figyelje meg, hogy az előző példában nincs `'/',` **paraméter ("_artifactsLocation")** és **paraméter ("ExampleAppPackageFolder")**.

Hozza létre újra a projektet. A projekt létrehozása biztosítja, hogy a telepíteni kívánt fájlok hozzá legyenek adva az átmeneti mappához.

Most nyisson meg egy PowerShell-konzolt, és futtassa a következőket:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>AzureRM modul parancsfájlja

A AzureRM modul parancsfájlhoz használja a Visual studiót:

1. Az újbóli üzembe helyezéshez válassza a **telepítés** lehetőséget, majd a korábban üzembe helyezett erőforráscsoportot.

    ![Projekt újbóli üzembe helyezése](./media/create-visual-studio-deployment-project/redeploy.png)

1. Válassza ki azt a Storage-fiókot, amelyet az adott erőforráscsoporthoz telepített az összetevő- **tárolási fiókhoz**.

   ![A web Deploy újratelepítése](./media/create-visual-studio-deployment-project/redeploy-web-app.png)

## <a name="view-web-app"></a>Webalkalmazás megtekintése

1. Az üzembe helyezés befejeztével válassza ki a webalkalmazást a portálon. Válassza ki az URL-t a hely böngészéséhez.

   ![Webhely tallózása](./media/create-visual-studio-deployment-project/browse-site.png)

1. Láthatja, hogy az alapértelmezett ASP.NET-alkalmazást sikeresen üzembe helyezte.

   ![Telepített alkalmazás megjelenítése](./media/create-visual-studio-deployment-project/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Műveleti irányítópult hozzáadása

Nem csak a Visual Studio felületén keresztül elérhető erőforrásokat használhatja. Az üzemelő példány testreszabásához adjon egyéni erőforrást a sablonhoz. Az erőforrás hozzáadásának megjelenítéséhez műveleti irányítópultot kell hozzáadnia az üzembe helyezett erőforrás kezelése érdekében.

1. Nyissa meg a WebSite.jsfájlt, és adja hozzá a következő JSON-t a Storage-fiók erőforrása után, de az `]` erőforrások szakasz bezárása előtt.

   ```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "type": "Microsoft.Portal/dashboards",
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. A projekt újbóli üzembe helyezése.

1. Az üzembe helyezés befejezése után tekintse meg az irányítópultot a portálon. Válassza ki az **irányítópultot** , és válassza ki az üzembe helyezett elemet.

   ![A képernyőképen az irányítópult oldal jelenik meg, amely egy példaként szolgáló egyéni irányítópultot mutat be.](./media/create-visual-studio-deployment-project/view-custom-dashboards.png)

1. Megjelenik a testreszabott irányítópult.

   ![Egyéni irányítópult](./media/create-visual-studio-deployment-project/Ops-DemoSiteGroup-dashboard.png)

Az irányítópulthoz való hozzáférést az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével kezelheti. Az irányítópult kinézetét is testreszabhatja az üzembe helyezése után. Ha azonban újból üzembe helyezi az erőforráscsoportot, az irányítópult visszaáll a sablonban szereplő alapértelmezett állapotba. Az irányítópultok létrehozásával kapcsolatos további információkért lásd az [Azure-irányítópultok szoftveres létrehozásával](../../azure-portal/azure-portal-dashboards-create-programmatically.md) foglalkozó témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal a bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.

1. Válassza ki az erőforráscsoport nevét.

1. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre és helyezhet üzembe sablonokat a Visual Studióval. A sablonok fejlesztésével kapcsolatos további tudnivalókért tekintse meg az új kezdő oktatóanyag-sorozatot:

> [!div class="nextstepaction"]
> [Kezdő szintű oktatóanyagok](./template-tutorial-create-first-template.md)
