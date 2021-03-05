---
title: Internet Analyzer-teszt létrehozása a parancssori felület használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhatja létre az első Internet Analyzer-tesztet az Azure CLI használatával.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a9a9fe93ebe302a76d69249dc56933e1bcc924d1
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200087"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Internet Analyzer-teszt létrehozása a parancssori felület használatával (előzetes verzió)

Az Internet Analyzer erőforrásait kétféleképpen lehet létrehozni – a [Azure Portal](internet-analyzer-create-test-portal.md) vagy a parancssori felület használatával. Ez a szakasz segítséget nyújt egy új Azure Internet Analyzer-erőforrás létrehozásához a CLI-élmény használatával. 


> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

A nyilvános előzetes verzió globálisan használható; az adattárolás azonban az *USA 2. nyugati* régiójára korlátozódik az előzetes verzióban.

## <a name="object-model"></a>Objektummodell
Az Internet Analyzer CLI a következő típusú erőforrásokat teszi elérhetővé:
* **Tesztek** – a teszt összehasonlítja a két internetes végpont (A és B) végfelhasználói teljesítményét az idő múlásával.
* **Profilok** – a tesztek egy Internet Analyzer-profil alatt jönnek létre. A profilok lehetővé teszik a kapcsolódó tesztek csoportosítását; egy profil egy vagy több tesztet is tartalmazhat.
* Előre **konfigurált végpontok** – különböző konfigurációkkal (régiókkal, gyorsítási technológiákkal stb.) rendelkező végpontokat állítottunk be. Ezen előre konfigurált végpontok bármelyikét felhasználhatja a tesztekbe.
* **Scorecardok** – a scorecard gyors és hasznos összefoglalókat biztosít a mérési eredményekről. Tekintse át [a scorecard értelmezését](internet-analyzer-scorecard.md)ismertető témakört.
* **Idősorozat** – egy idősorozat azt mutatja, hogyan változik a metrika időbeli változása.

## <a name="profile-and-test-creation"></a>Profil és tesztelés létrehozása
1. Az Internet Analyzer előnézeti hozzáférésének beszerzése a **hogyan az előzetes** verzióban való részvétel után? az [Azure Internet Analyzer – gyakori kérdések](internet-analyzer-faq.md)című témakörben olvashat.
2. [Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t.
3. Futtassa a `login` parancsot egy CLI-munkamenet elindításához:
    ```azurecli-interactive
    az login
    ```

    Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.
    Ellenkező esetben nyisson meg egy böngészőt, https://aka.ms/devicelogin és adja meg a terminálon megjelenő engedélyezési kódot.

4. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

5. Válassza ki az előfizetési azonosítót, amely hozzáférést kapott az Internet Analyzer nyilvános előzetes verziójához.

    A bejelentkezést követően megjelenik az Azure-fiókjához társított előfizetések listája. Az előfizetési információ az a `isDefault: true` jelenleg aktivált előfizetés a bejelentkezés után. Egy másik előfizetés kiválasztásához használja az az [Account set](/cli/azure/account#az-account-set) parancsot az előfizetés-azonosítóval a következőre való váltáshoz:. További információ az előfizetés kiválasztásáról: [több Azure-előfizetés használata](/cli/azure/manage-azure-subscriptions-azure-cli).

    Nem interaktív módok is rendelkezésre állnak a bejelentkezéshez, amelyekről részletesen [az Azure CLI-vel történő bejelentkezést](/cli/azure/authenticate-azure-cli) ismertető részben olvashat.

6. **[Nem kötelező]** Hozzon létre egy új Azure-erőforráscsoportot:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Telepítse az Azure CLI Internet Analyzer bővítményt:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Hozzon létre egy új Internet Analyzer-profilt:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Az újonnan létrehozott profilhoz elérhető összes előre konfigurált végpont listázása:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Hozzon létre egy új tesztet az újonnan létrehozott InternetAnalyzer-profilban:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    A fenti parancs feltételezi, hogy mindkettő `www.contoso.com` és `www.microsoft.com` az egyéni elérési utakon található egypixeles rendszerképet ([trans.gif](https://fpc.msedge.net/apc/trans.gif)) üzemelteti. Ha az objektum elérési útja nincs explicit módon megadva, az Internet Analyzer `/apc/trans.gif` alapértelmezés szerint az objektum elérési útjaként fog megjelenni, ami azt eredményezi, hogy az előre konfigurált végpontok az egy képpontos képet használják. Azt is vegye figyelembe, hogy a sémát (https/http) nem kell megadni; Az Internet Analyzer csak HTTPS-végpontokat támogat, ezért a rendszer a HTTPS használatát feltételezi.

11. Az új tesztnek az Internet Analyzer-profil alatt kell megjelennie:
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

12. A mérések létrehozásának megkezdéséhez a teszt **scriptFileUri** mutató JavaScript-fájlt be kell ágyazni a webalkalmazásba. A konkrét utasítások az [Internet Analyzer-ügyfél beágyazása](internet-analyzer-embed-client.md) oldalon találhatók.

13. A teszt előrehaladását a "status" (állapot) érték nyomon követésével figyelheti:
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. A teszt összegyűjtött eredményeit idősor vagy scorecardok létrehozásával ellenőrizheti:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Következő lépések

* A támogatott parancsok és használati példák teljes listájáért keresse fel az [Internet Analyzer CLI-referenciáját](/cli/azure/ext/internet-analyzer/internet-analyzer) .
* Olvassa el az [Internet Analyzer – gyakori kérdések](internet-analyzer-faq.md)című részt.
* További információ az [Internet Analyzer-ügyfél](internet-analyzer-embed-client.md) beágyazásáról és az [Egyéni végpontok](internet-analyzer-custom-endpoint.md)létrehozásáról.