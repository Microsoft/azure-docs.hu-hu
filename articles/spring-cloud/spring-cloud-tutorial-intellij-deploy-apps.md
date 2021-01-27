---
title: 'Oktatóanyag: Azure Spring Cloud-alkalmazások üzembe helyezése a IntelliJ használatával'
description: Alkalmazások üzembe helyezése az Azure Spring Cloud IntelliJ használatával.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: a808e37c0f0aae2816eb536dc43485c125354b81
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916201"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>IntelliJ használata az Azure Spring Cloud-alkalmazások üzembe helyezéséhez

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Az Azure Spring Cloud IntelliJ beépülő modulja támogatja az alkalmazások telepítését a IntelliJ ÖTLETből.  

A példa futtatása előtt próbálja ki az [alapszintű](spring-cloud-quickstart.md)rövid útmutatót.

## <a name="prerequisites"></a>Előfeltételek
* [IntelliJ IDEA, Community/Ultimate Edition, verzió 2020.1/2020.2](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>A beépülő modul telepítése
Az Azure Toolkit for IntelliJ IDEA 3.43.0 az IntelliJ **plugins** felhasználói felületéről adhatja hozzá.

1. Indítsa el a IntelliJ.  Ha korábban már megnyitott egy projektet, a projekt bezárásával jelenítheti meg az üdvözlő párbeszédpanelt. Válassza a **Konfigurálás** a jobb alsó sarokban lehetőséget, majd kattintson a **plugins** gombra a beépülő modul konfigurálása párbeszédpanel megnyitásához, majd válassza a **plugins telepítése lemezről** lehetőséget.

    ![Konfigurálás kiválasztása](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Azure Toolkit for IntelliJ keresése.  Kattintson az **Install** (Telepítés) gombra.

    ![Beépülő modul telepítése](media/spring-cloud-intellij-howto/install-plugin.png)

1. Kattintson az **ide újraindítása** elemre.

## <a name="tutorial-procedures"></a>Oktatóanyag-eljárások
Az alábbi eljárások a IntelliJ IDEA használatával telepítenek egy Hello World alkalmazást.

* A GS-Spring-boot projekt megnyitása
* Üzembe helyezés az Azure Spring Cloud-on
* Folyamatos átviteli naplók megjelenítése

## <a name="open-gs-spring-boot-project"></a>A GS-Spring-boot projekt megnyitása

1. Töltse le és csomagolja ki az oktatóanyag forrás-tárházát, vagy klónozott a git használatával: git Clone https://github.com/spring-guides/gs-spring-boot.git 
1. CD into GS-Spring-boot\complete.
1. Nyissa meg az IntelliJ **üdvözlő** párbeszédpanelt, és válassza a **Projekt importálása** elemet az Importálás varázsló megnyitásához.
1. Válassza a `gs-spring-boot\complete` mappa lehetőséget.

    ![Projekt importálása](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Üzembe helyezés az Azure Spring Cloud-on
Az Azure-ba való üzembe helyezéshez be kell jelentkeznie az Azure-fiókjával, és ki kell választania az előfizetését.  A bejelentkezés részleteiért lásd: [telepítés és bejelentkezés](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Kattintson a jobb gombbal a projektre a IntelliJ Project Explorerben, és válassza az **Azure**-  ->  **üzembe helyezés az Azure Spring Cloud**-ban lehetőséget.

    ![Üzembe helyezés az Azure 1-ben](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Fogadja el az alkalmazás nevét a **név** mezőben. A **név** a konfigurációra hivatkozik, nem az alkalmazás nevére. A felhasználóknak általában nem kell módosítaniuk.
1. Fogadja el az azonosítót a projektből **az összetevőhöz.**
1. Válassza az **alkalmazás elemet:** kattintson az **alkalmazás létrehozása...** elemre.

    ![Üzembe helyezés az Azure 2-ban](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Adja meg az **alkalmazás nevét**, majd kattintson **az OK** gombra.

    ![Üzembe helyezés az Azure-ban OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. A telepítés indításához kattintson a **Futtatás** gombra. 

    ![Üzembe helyezés az Azure 3-ban](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. A beépülő modul futtatja a parancsot a `mvn package` projekten, majd létrehozza az új alkalmazást, és telepíti a parancs által generált jar-t `package` .

1. Ha az alkalmazás URL-címe nem jelenik meg a kimeneti ablakban, szerezze be a Azure Portal. Navigáljon az erőforráscsoporthoz az Azure Spring Cloud-példányra.  Ezután kattintson az **alkalmazások** elemre.  A futó alkalmazás fel lesz sorolva.

    ![Teszt URL-címének beolvasása](media/spring-cloud-intellij-howto/get-test-url.png)

1. Nyissa meg az URL-címet a böngészőben.

    ![Navigáljon a 2. böngészőben](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Folyamatos átviteli naplók megjelenítése
A naplók beszerzése:
1. Válassza az **Azure Explorer**, majd a **Spring Cloud** elemet.
1. Kattintson a jobb gombbal a futó alkalmazásra.
1. Válassza a **folyamatos átviteli naplók** lehetőséget a legördülő listából.

    ![Adatfolyam-naplók kiválasztása](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Válassza a példány lehetőséget.

    ![Példány kiválasztása](media/spring-cloud-intellij-howto/select-instance.png)

1. A folyamatos átviteli napló a kimeneti ablakban jelenik meg.

    ![Adatfolyam-napló kimenete](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Következő lépések
* [Spring-alkalmazás előkészítése az Azure Spring Cloud-hoz](./spring-cloud-tutorial-prepare-app-deployment.md)
* [További információ a Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/)
