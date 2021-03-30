---
title: A DevTest Labs használata az Azure Pipelines buildelési és kiadási folyamataiban
description: Ismerje meg, hogyan használhatók a Azure DevTest Labs az Azure-folyamatok létrehozásához és kiadásához.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d04ed5dd7bebac0c8f24deb9145c3d2e4b77122e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88080334"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>A DevTest Labs használata az Azure Pipelines buildelési és kiadási folyamataiban
Ez a cikk azt ismerteti, hogyan használhatók az DevTest Labs az Azure-folyamatokban a folyamatok létrehozásához és kiadásához. 

## <a name="overall-flow"></a>Teljes folyamat
Az alapszintű folyamat egy olyan **build-folyamat** , amely a következő feladatokat végzi el:

1. Hozza létre az alkalmazás kódját.
1. Hozzon létre egy alapszintű környezetet a DevTest Labs szolgáltatásban.
1. Frissítse a környezetet egyéni adatokkal.
1. Az alkalmazás üzembe helyezése a DevTest Labs-környezetben
1. Tesztelje a kódot. 

Miután a Build sikeresen befejeződött, a **kiadási folyamat** a Build összetevők használatával helyezi üzembe az előkészítést vagy a gyártást. 

Az egyik szükséges hely az, hogy a tesztelt ökoszisztéma újbóli létrehozásához szükséges összes információ elérhető legyen a Build-összetevőkön belül, beleértve az Azure-erőforrások konfigurációját is. Az Azure-erőforrások használata esetén a vállalatok ezeket az erőforrásokat szeretnék ellenőrizni vagy nyomon követni. Bizonyos helyzetekben Azure Resource Manager az erőforrások létrehozásához és konfigurálásához használt sablonokat egy másik részleg is felügyelheti. Előfordulhat, hogy ezeket a sablonokat egy másik adattár tárolja. Érdekes helyzetet eredményez, amikor létrehoznak és tesztelnek egy buildet, és a kódot és a konfigurációt is a Build összetevőkön belül kell tárolni, hogy megfelelően újra létrehozza a rendszer éles környezetben történő létrehozását. 

A DevTest Labs az összeállítási/tesztelési fázisban való használatával hozzáadhat Azure Resource Manager sablonokat és támogató fájlokat a Build forrásokhoz, így a kiadási fázisban a teszteléshez használt pontos konfiguráció üzembe helyezése éles környezetben történik. A megfelelő konfigurációval rendelkező **Azure DevTest Labs környezet létrehozása** feladat a Build összetevőkön belül menti a Resource Manager-sablonokat. Ebben a példában a kódot fogja használni az [oktatóanyag: .net Core-és SQL Database-Webalkalmazás létrehozása Azure app Serviceban](../app-service/tutorial-dotnetcore-sqldb-app.md), a webalkalmazás üzembe helyezéséhez és teszteléséhez az Azure-ban.

![Teljes folyamat](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Az Azure-erőforrások beállítása
Már van néhány olyan elem, amelyet előzőleg létre kell hozni:

- Két tárház. Az első az oktatóanyagból és egy Resource Manager-sablonból álló kóddal, két további virtuális géppel. A második az alap Azure Resource Manager sablont (meglévő konfiguráció) fogja tartalmazni.
- Egy erőforráscsoport az üzemi kód és a konfiguráció üzembe helyezéséhez.
- A labort be kell állítani a létrehozási folyamat [konfigurációs tárházához való kapcsolódással](devtest-lab-create-environment-from-arm.md) . A Resource Manager-sablonnak be kell jelentkeznie a konfigurációs adattárba azuredeploy.jsként a metadata.json, hogy lehetővé tegye a DevTest Labs számára a sablon felismerését és üzembe helyezését.

A létrehozási folyamat létrehoz egy DevTest Labs-környezetet, és üzembe helyezi a kódot a teszteléshez.

## <a name="set-up-a-build-pipeline"></a>Build folyamat beállítása
Az Azure-folyamatokban hozzon létre egy összeállítási folyamatot az [oktatóanyag használatával: .net Core-és SQL Database-Webalkalmazás létrehozása Azure app Serviceban](../app-service/tutorial-dotnetcore-sqldb-app.md). Használja a **ASP.net Core** sablont, amely feltölti a szükséges feladatot a kód létrehozásához, teszteléséhez és közzétételéhez.

![ASP.NET-sablon kiválasztása](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Három további feladatot kell felvennie a környezet létrehozásához a DevTest Labs szolgáltatásban, és üzembe kell helyezni a környezetet.

![Folyamat három feladattal](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Környezeti feladat létrehozása
A környezet létrehozása feladatban (**Azure DevTest Labs környezet létrehozása** feladat) a legördülő lista segítségével válassza ki a következő értékeket:

- Azure-előfizetés
- a labor neve
- a tárház neve
- a sablon neve (amely azt a mappát mutatja, ahol a környezet tárolva van). 

Javasoljuk, hogy az adatok manuális megadása helyett a lapon lévő legördülő listát használja. Ha manuálisan adja meg az adatokat, adja meg a teljes körű Azure-erőforrás-azonosítókat. A feladat az erőforrás-azonosítók helyett a felhasználóbarát neveket jeleníti meg. 

A környezet neve a DevTest Labs szolgáltatásban megjelenített név. Minden egyes Build esetében egyedi névnek kell lennie. Például: **TestEnv $ (Build. BuildId)**. 

Megadhatja az adatoknak a Resource Manager-sablonba való továbbítására szolgáló paramétereket tartalmazó fájlt vagy paramétereket. 

Válassza a **kimeneti változók létrehozása a környezeti sablon kimenete beállítás alapján** lehetőséget, és adjon meg egy hivatkozási nevet. Ebben a példában a **BaseEnv** adja meg a hivatkozási nevet. Ezt a BaseEnv fogja használni a következő feladat konfigurálásakor. 

![Azure DevTest Labs környezeti feladat létrehozása](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Környezeti feladat feltöltése
A második feladat (**Azure DevTest Labs a környezeti feladat kitöltése** ) a meglévő DevTest Labs-környezet frissítése. A környezet létrehozása feladat kimenete **BaseEnv. environmentResourceId** , amely a feladat nevének konfigurálására szolgál. A példában szereplő Resource Manager-sablon két paraméterrel rendelkezik: **adminUserName** és **adminPassword**. 

![Azure DevTest Labs környezeti feladat feltöltése](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>App Service üzembe helyezése feladat
A harmadik feladat a **Azure app Service üzembe helyezése** feladat. Az alkalmazás típusa a következő: **webalkalmazás** , és a app Service neve **$ (webhely)** értékre van állítva.

![App Service üzembe helyezése feladat](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Kiadási folyamat beállítása
A kiadási folyamat két feladattal hozható létre: **Azure-telepítés: erőforráscsoport létrehozása vagy frissítése** és **Azure app Service üzembe helyezése**. 

Az első feladathoz adja meg az erőforráscsoport nevét és helyét. A sablon helye egy csatolt összetevő. Ha a Resource Manager-sablon csatolt sablonokat tartalmaz, egy egyéni erőforráscsoport-telepítést kell megvalósítani. A sablon a közzétett drop-összetevőben van. A Resource Manager-sablonhoz tartozó sablon paramétereinek felülbírálása. A többi beállítás alapértelmezett értékekkel is meghagyható. 

A második feladathoz **Azure app Service üzembe helyezéséhez** adja meg az Azure-előfizetést, válassza a **webalkalmazás** lehetőséget az **alkalmazás típusaként**, a **$ (webhely)** pedig a **app Service nevet**. A többi beállítás alapértelmezett értékekkel is meghagyható. 

## <a name="test-run"></a>Teszt futtatása
Most, hogy mindkét folyamat be van állítva, manuálisan hozza létre a buildet, és nézze meg, hogy működik-e. A következő lépés a megfelelő trigger beállítása a létrehozáshoz és a build és a kiadási folyamat összekapcsolása.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Azure DevTest Labs integrálása az Azure-folyamatok folyamatos integrációs és szállítási folyamatával](devtest-lab-integrate-ci-cd.md)
- [Környezetek integrálása az Azure-folyamatokba CI/CD-folyamatokban](integrate-environments-devops-pipeline.md)
