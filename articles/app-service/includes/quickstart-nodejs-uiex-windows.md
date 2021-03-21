---
title: 'Rövid útmutató: Node.js Webalkalmazás létrehozása – Windows'
description: A Windows platformon percek alatt üzembe helyezheti az első Node.js Hello World Azure App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 324919c1d085302e675e0bc9a370ce35f52a0f1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102109370"
---
<!-- advanced for windows -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. üzembe helyezés Azure App Service a Visual Studio Code-ból

1. Nyissa meg az alkalmazás mappáját a Visual Studio Code-ban.

    ```bash
    code .
    ```

1. Az **Azure app Service** Explorerben válassza a bejelentkezés az Azure-ba **...** lehetőséget, és kövesse az utasításokat. A bejelentkezést követően a tallózónak meg kell jelennie az Azure-előfizetés nevének.

    ![Bejelentkezés az Azure-ba](../media/quickstart-nodejs/sign-in.png)

    <details>
    <summary>Azure-bejelentkezés hibaelhárítása</summary>
    
    Ha az Azure-ba való bejelentkezéskor a **"nem található az előfizetés neve [előfizetés-azonosító]"** hibaüzenet jelenik meg, annak oka az lehet, hogy a proxy mögött van, és nem érhető el az Azure API. Konfigurálja a `HTTP_PROXY` és a `HTTPS_PROXY` környezeti változókat a saját proxy-adataival a használatával `export` .
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [Probléma bejelentése](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. Az **Azure app Service** Explorerben válassza a kék felfelé mutató nyíl ikont az alkalmazás üzembe helyezéséhez az Azure-ban. 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="Képernyőkép az Azure app Service-ről a VS Code-ban, amelyen a kék nyíl ikon van kiválasztva.":::

1. Válassza ki a jelenleg megnyitott könyvtárat `nodejs-docs-hello-world` .

1. Válassza az **új Webalkalmazás létrehozása... lehetőséget. Speciális**, a Windows rendszeren való app Service telepítéséhez.

1. Globálisan egyedi típus megadása <abbr title="Az alkalmazás nevének érvényes karaktereinek neve: "a-z", "0-9" és "-".">name</abbr> a webalkalmazáshoz, és nyomja le az **ENTER billentyűt**. 
1. Válassza az **Új erőforráscsoport létrehozása** lehetőséget, majd adja meg az erőforráscsoport nevét, például: `AppServiceQS-rg` .
1. Válassza ki a **Node.js verzióját**, az LTS használata javasolt.

    Az értesítési csatorna az alkalmazáshoz létrehozott Azure-erőforrásokat jeleníti meg.
1. Válassza a **Windows** lehetőséget az operációs rendszer számára.
1. Válassza az **új App Service terv létrehozása** lehetőséget, majd adja meg a csomag nevét (például `AppServiceQS-plan` ), majd válassza az **ingyenes F1** lehetőséget a díjszabási szinten.
1. Válassza **a Kihagyás lehetőséget,** amikor a rendszer rákérdez a Application Insightsra.
1. Válasszon egy Önhöz közeli régiót vagy az elérni kívánt erőforrások közelében.

1. Válassza az **Igen** lehetőséget, ha a rendszer kéri a munkaterület frissítését, hogy a későbbi központi telepítések automatikusan ugyanazt a app Service webalkalmazást célozzák meg. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Képernyőkép a munkaterület frissítéséről a kiválasztott igen gombbal.":::

1. Kattintson a jobb gombbal az App Service-csomópontra, és válassza a **telepítés a webalkalmazásba** lehetőséget.

1. Kattintson a jobb gombbal az App Service-csomópontra, és válassza a **Tallózás webhely** lehetőséget.

    [Probléma bejelentése](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

1. Miután az üzembe helyezés befejeződött, a kérdésben válassza a **Tallózás webhely** lehetőséget a frissen telepített webalkalmazás megtekintéséhez.

<br/>
<details>
<summary><strong>Hibaelhárítás</strong></summary>

Ha nem tudta végrehajtani a következő lépéseket, ellenőrizze a következőket:

* Győződjön meg arról, hogy az alkalmazás figyeli a PORT környezeti változó által biztosított portot: `process.env.PORT` .

* Ha a következő hibaüzenet jelenik meg: **"nincs engedélye a könyvtár vagy lap megtekintésére."**, akkor az alkalmazás valószínűleg nem indult el megfelelően. A hiba megtalálásához és kijavításához tekintse át a napló kimenetét. 

</details>

<br>

[Probléma bejelentése](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br/>
<hr/>
