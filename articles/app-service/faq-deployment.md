---
title: Üzembe helyezéssel kapcsolatos gyakori kérdések – Azure App Service | Microsoft Docs
description: Választ kaphat a Azure App Service Web Apps funkciójának üzembe helyezésével kapcsolatos gyakori kérdésekre.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 163a6940e50d1f8beacc23855fd1e6f9daad0085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88080473"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Az Azure-beli Web Apps üzembe helyezéssel kapcsolatos gyakori kérdései

Ez a cikk a [Azure App Service Web Apps funkciójának](https://azure.microsoft.com/services/app-service/web/)üzembe helyezésével kapcsolatos gyakori kérdésekre (GYIK) ad válaszokat.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Most App Service Web Apps-t használom. Hogyan közzétenni a kódot?

Íme néhány lehetőség a webalkalmazás kódjának közzétételéhez:

*   Üzembe helyezés a Visual Studióval. Ha a Visual Studio-megoldással rendelkezik, kattintson a jobb gombbal a webalkalmazás-projektre, majd válassza a **Közzététel** lehetőséget.
*   Üzembe helyezés FTP-ügyfél használatával. A Azure Portal töltse le a webalkalmazás közzétételi profilját, amelyre telepíteni kívánja a kódot. Ezután töltse fel a fájlokat a \site\wwwroot-re ugyanazzal a közzétételi profil FTP-hitelesítő adataival.

További információ: [az alkalmazás üzembe helyezése app Service](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Hibaüzenet jelenik meg, amikor megpróbálok üzembe helyezni a Visual studióból. Hogyan a hiba elhárítása?

Ha a következő üzenet jelenik meg, lehet, hogy az SDK egy régebbi verzióját használja: "hiba történt a (z)" YourResourceGroup "erőforráscsoport" YourResourceName "erőforrásának üzembe helyezése során: MissingRegistrationForLocation: az előfizetés nincs regisztrálva az" Components "erőforrástípus számára a (z)" Central US "helyen. Regisztrálja újra a szolgáltatót ahhoz, hogy hozzáférhessen ehhez a helyhez. " 

A hiba megoldásához frissítsen a [legújabb SDK](https://azure.microsoft.com/downloads/)-ra. Ha ezt az üzenetet látja, és rendelkezik a legújabb SDK-val, küldjön be egy támogatási kérést.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Hogyan üzembe helyezhet egy ASP.NET-alkalmazást a Visual studióból a App Serviceba?
<a id="deployasp"></a>

Az oktatóanyag [öt perc alatt hozza létre első ASP.net-webalkalmazását az Azure-](quickstart-dotnetcore.md) ban, amely bemutatja, hogyan helyezhet üzembe egy ASP.net-webalkalmazást egy webalkalmazásban app Service a Visual Studio használatával.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Mik a különböző típusú telepítési hitelesítő adatok?

A App Service kétféle hitelesítő adatot támogat a helyi git üzembe helyezéséhez és az FTP/S környezethez. További információ a központi telepítési hitelesítő adatok konfigurálásáról: [a app Service telepítési hitelesítő adatainak konfigurálása](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Mi a App Service webalkalmazás fájl-vagy címtár-szerkezete?

További információ a App Service alkalmazásának struktúrájáról: a [fájl szerkezete az Azure-ban](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Hogyan feloldása "FTP-hiba 550 – nincs elég hely a lemezen" a fájlok FTP-n való kipróbálásakor?

Ha ezt az üzenetet látja, akkor valószínű, hogy a webalkalmazásához tartozó szolgáltatási csomagban lemezes kvótát használ. Előfordulhat, hogy a lemezterület igénye alapján fel kell mérnie egy magasabb szolgáltatási szintet. A díjszabással és az erőforrás-korlátokkal kapcsolatos további információkért tekintse meg a [app Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Hogyan beállítani a folyamatos üzembe helyezést a saját App Service webalkalmazáshoz?

Több erőforrásból is beállíthat folyamatos üzembe helyezést, többek között az Azure DevOps, a OneDrive, a GitHub, a bitbucket, a Dropbox és más git-Tárházak használatával. Ezek a lehetőségek a portálon érhetők el. A [app Service folyamatos üzembe helyezése](deploy-continuous-deployment.md) hasznos oktatóanyag, amely leírja, hogyan állítható be a folyamatos üzembe helyezés.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Hogyan a GitHubról és a bitbucket folytatott folyamatos üzembe helyezéssel kapcsolatos problémákat.

A GitHub-vagy bitbucket-alapú folyamatos üzembe helyezéssel kapcsolatos problémák kivizsgálásához tekintse meg a [folyamatos üzembe helyezés kivizsgálása](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)című témakört.

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Nem tudok FTP-t a webhelyemre, és közzétenni a kódot. Hogyan a probléma megoldásához?

FTP-hibák elhárítása:

1. Ellenőrizze, hogy a helyes állomásnevet és hitelesítő adatokat adta-e meg. További információk a hitelesítő adatok különböző típusairól és használatáról: [telepítési hitelesítő adatok](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Ellenőrizze, hogy a tűzfal nem blokkolja-e az FTP-portokat. A portoknak a következő beállításokkal kell rendelkezniük:
    * FTP-vezérlőkapcsolati port: 21
    * FTP-adatkapcsolati port: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Hogyan közzétenni a kódot a App Service?

Az Azure rövid útmutató úgy lett kialakítva, hogy segítséget nyújtson az alkalmazás üzembe helyezéséhez az üzembe helyezési verem és a választott módszer használatával. A rövid útmutató használatához a Azure Portal nyissa meg az App Service-t az **üzembe helyezés** alatt, **majd válassza a** rövid útmutató lehetőséget.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Miért indítja újra az alkalmazást az üzembe helyezés után App Service?

Ha meg szeretné tudni, hogy az alkalmazás központi telepítése milyen körülmények között okozhat újraindítást, tekintse meg az [üzembe helyezési és futásidejű problémák](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts")című témakört. Ahogy a cikk leírja, App Service telepíti a fájlokat a WWWroot mappába. Soha nem indítja el közvetlenül az alkalmazást.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Hogyan integrálhatom az Azure DevOps-kódot az App Service szolgáltatásba?

Az Azure DevOps két lehetősége van a folyamatos üzembe helyezés használatára:

*   Használjon git-projektet. Kapcsolódjon App Serviceon keresztül a Deployment Center használatával.
*   A Team Foundation verziókövetés (TFVC) projekt használata. A App Service létrehozásához használja a Build ügynököt.

Mindkét lehetőséghez tartozó folyamatos programkód-telepítés a meglévő fejlesztői munkafolyamatok és bejelentkezési eljárások függvénye. További információért lásd a következő cikkeket: 

*   [Az alkalmazás folyamatos üzembe helyezésének megvalósítása Azure-webhelyeken](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Azure DevOps-szervezet beállítása, hogy üzembe helyezhető legyen egy webalkalmazásban](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Hogyan FTP-t vagy FTPS-t használ az alkalmazásom App Service való üzembe helyezéséhez?

Az FTP-vagy FTPS a webalkalmazás App Service való üzembe helyezésével kapcsolatos információkért lásd: [az alkalmazás telepítése app Service az FTP/S használatával](deploy-ftp.md).
