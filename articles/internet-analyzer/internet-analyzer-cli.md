---
title: Teszt létrehozása Internet Analyzer CLI-| Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhatja létre az első Internet Analyzer az Azure CLI használatával.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 46caae53ed81de335c2b9d5ddbd3fd7f89424fdd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780414"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Teszt létrehozása Internet Analyzer CLI (előzetes verzió) használatával

Kétféleképpen hozhat létre erőforrást Internet Analyzer a Azure Portal [cli](internet-analyzer-create-test-portal.md) használatával. Ez a szakasz segítséget nyújt egy új Azure Internet Analyzer létrehozásához a CLI-felület használatával. 


> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

A nyilvános előzetes verzió globálisan használható; az előzetes verzióban azonban az adattárolás az *USA 2.* nyugati régiója.

## <a name="object-model"></a>Objektummodell
A Internet Analyzer CLI a következő típusú erőforrásokat teszi elérhetővé:
* **Tesztek** – Egy teszt összehasonlítja két internetes végpont (A és B) végfelhasználói teljesítményét az idő során.
* **Profilok** – A tesztek egy profilban Internet Analyzer létre. A profilok lehetővé teszik a kapcsolódó tesztek csoportosítását; egy profil egy vagy több tesztet is tartalmazhat.
* **Előre konfigurált végpontok** – Számos különböző konfigurációval (régiókkal, gyorsítási technológiákkal stb.) beállítottunk végpontokat. A tesztekben használhatja ezen előre konfigurált végpontok bármelyikét.
* **Mutatószámok** – A mutatószámok a mérési eredmények gyors és jelentéssel bíró összegzését biztosítják. Tekintse meg [a mutatószámrendszer értelmezését.](internet-analyzer-scorecard.md)
* **Time Series** – Egy idősorozat azt mutatja be, hogyan változik egy metrika az idő alatt.

## <a name="profile-and-test-creation"></a>Profil és teszt létrehozása
1. Az Internet Analyzer elérésének megtekintéséhez kövesse az Hogyan az előzetes verzióban való részvételre vonatkozó **útmutatót?** című [Azure Internet Analyzer tartalmaz.](internet-analyzer-faq.md)
2. [Telepítse az Azure CLI-t.](/cli/azure/install-azure-cli)
3. Futtassa `login` a parancsot egy parancssori felületi munkamenet futtatásához:
    ```azurecli-interactive
    az login
    ```

    Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt fogja megtenni, és betölt egy Azure bejelentkezési oldalt.
    Ellenkező esetben nyisson meg egy böngészőoldalt a oldalon, és adja meg a terminálon https://aka.ms/devicelogin megjelenő engedélyezési kódot.

4. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

5. Válassza ki azt az előfizetés-azonosítót, amely hozzáféréssel rendelkezik Internet Analyzer nyilvános előzetes verzióhoz.

    Bejelentkezés után megjelenik az Azure-fiókjához társított előfizetések listája. A-val kapcsolatos `isDefault: true` előfizetési adatok a jelenleg aktivált előfizetés a bejelentkezés után. Másik előfizetés kiválasztásához használja [az az account set](/cli/azure/account#az_account_set) parancsot az előfizetés azonosítójával, hogy átváltson a következőre: . További információ az előfizetés kiválasztásáról: [Több Azure-előfizetés használata.](/cli/azure/manage-azure-subscriptions-azure-cli)

    Nem interaktív módok is rendelkezésre állnak a bejelentkezéshez, amelyekről részletesen [az Azure CLI-vel történő bejelentkezést](/cli/azure/authenticate-azure-cli) ismertető részben olvashat.

6. **[Nem kötelező]** Hozzon létre egy új Azure-erőforráscsoportot:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Telepítse az Azure CLI Internet Analyzer bővítményt:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Hozzon létre egy új Internet Analyzer profilt:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Sorolja fel az újonnan létrehozott profilhoz elérhető összes előre konfigurált végpontot:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Hozzon létre egy új tesztet az újonnan létrehozott InternetAnalyzer profil alatt:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    A fenti parancs feltételezi, hogy a és a is egy képpontos képet (trans.gif) az `www.contoso.com` egyéni elérési `www.microsoft.com` utakon.[ ](https://fpc.msedge.net/apc/trans.gif) Ha az objektum elérési útja nincs explicit módon megadva, a Internet Analyzer alapértelmezés szerint az objektum elérési útjaként fogja használni, ahol az előre konfigurált végpontok az egy képpontos képet `/apc/trans.gif` tetszetik. Azt is vegye figyelembe, hogy a sémát (https/http) nem kell megadni; Internet Analyzer csak a HTTPS-végpontokat támogatja, ezért a rendszer a HTTPS-t feltételezi.

11. Az új tesztnek a következő profil alatt Internet Analyzer meg:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Példa a kimenetre:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. A mérések generálásának megkezdéséhez a teszt **scriptFileUri** fájlja által mutatott JavaScript-fájlt be kell ágyazni a webalkalmazásba. A részletes utasításokat a Beágyazás és Internet Analyzer [oldalon talál.](internet-analyzer-embed-client.md)

13. A teszt előrehaladását az "állapot" értékének nyomon követésével figyelheti:
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. A teszt összegyűjtött eredményeit időzők vagy mutatószámok generálása alapján vizsgálhatja meg:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Következő lépések

* A támogatott [Internet Analyzer](/cli/azure/ext/internet-analyzer/internet-analyzer) és használati példák teljes listájáért tekintse meg a parancssori felület referenciáját.
* Olvassa el [a gyakori Internet Analyzer.](internet-analyzer-faq.md)
* További információ a Internet Analyzer [ügyfél beágyazásról](internet-analyzer-embed-client.md) és egyéni [végpont létrehozásáról.](internet-analyzer-custom-endpoint.md)