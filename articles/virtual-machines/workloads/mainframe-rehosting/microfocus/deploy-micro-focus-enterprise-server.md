---
title: A Micro Focus Enterprise Server 5,0 és az AK üzembe helyezése | Microsoft Docs
description: Az IBM z/OS mainframe-alapú számítási feladatokat az Azure Virtual Machines (VM) Micro Focus fejlesztési és tesztkörnyezet használatával helyezheti át.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 58ba530e434a9dc141ba8cb98120f6325eb06f7e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950473"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>A Micro Focus Enterprise Server 5,0 – AK üzembe helyezése

Egy másik [cikkben](./run-enterprise-server-container.md)a Micro Focus Enterprise Server 5,0 egy Docker-tárolóban való futtatásának lépéseit ismertetjük. Ezt követően azt szeretném megmutatni, hogyan teheti meg még egyszer, és hogyan helyezheti üzembe a Docker-rendszerképet, amelyet az Azure Kubernetes Service (ak) szolgáltatásban hozott létre.

Az Azure Kubernetes Service egy felügyelt Kubernetes-alapú szolgáltatás. Lehetővé teszi a Docker-tárolók (és más tároló-alapú alkalmazások) üzembe helyezését, méretezését és kezelését a Container hosts fürtön keresztül.

Ez egy három lépésből álló folyamat. A következőket kell tennie:

1.  Hozzon létre egy Azure Container Registry a Docker-rendszerkép tárolásához.

2.  Hozzon létre egy Azure Kubernetes-fürtöt a Docker-rendszerkép futtatásához.

3.  Futtassa az alkalmazást.

Ez lehetővé teszi, hogy kibővítse (és lekicsinyítse) a nagyvállalati modernizációs számítási feladatait az Azure-ban, a felhőalapú platform valódi előnyeit kihasználva.

Készen? Kezdjünk hozzá.

## <a name="create-the-azure-container-registry"></a>A Azure Container Registry létrehozása

A Azure Portal válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban. A piactér irányítópultján válassza a **tárolók,** majd a **Container Registry** lehetőséget. Ekkor megjelenik a **create Container Registry (tároló létrehozása** ) panel, ahol ki kell töltenie a **beállításjegyzék nevét**, az **Azure-előfizetést**, az **erőforráscsoportot** és a **helyet**. A **beállításjegyzék nevének** feloldására van szükség, ezért egyedinek kell lennie. Válassza ki az előző blogbejegyzésben használt **erőforráscsoportot** és a megfelelő **helyet**. Válassza a **rendszergazda felhasználó** és **alapszintű** az **SKU** **engedélyezése** lehetőséget. Miután megtörtént az összes kitöltése, válassza a **Létrehozás** lehetőséget.

![Tároló beállításjegyzék-felületének létrehozása](media/deploy-image-1.png)

A beállításjegyzék üzembe helyezését követően válassza az **Ugrás erőforráshoz** lehetőséget. Ekkor megjelenik a Azure Container Registry főpaneljén. A **gyorskonfigurálás** menüpont egy szép funkció. Jelölje ki, és megtekintheti, hogy mit kell tenni a lemezképek a beállításjegyzékbe való küldéséhez és lekéréséhez. Nézzük meg, hogy ezek a következők:

1.  A **Docker telepítése** – nem kell aggódnia, mert már befejeződött.

2.  **Futtassa a "Hello-World" alaprendszerképet** – ez nem kötelező, de próbálja ki.

3.  **Jelentkezzen be a tároló-beállításjegyzékbe** – ezt el kell végeznie a virtuális GÉPRŐL (VM). Másolja a parancsot a vágólapra vagy a Jegyzettömbbe.

    A létrehozott beállításjegyzékben a parancs a következő: **Docker login acrmf50.azurecr.IO**

4.  **Leküldés a beállításjegyzékbe** – ezt a Micro Focus-képhez is el kell végeznie, ha bejelentkezett a virtuális gépre.

5.  **Lekérés a beállításjegyzékből** – ez nem vonatkozik erre az útmutatóra, de jó tudni, ha egy másik Docker-rendszerképet kell futtatnia.

Mielőtt elkezdené a portált, be kell szereznie a beállításjegyzék hitelesítő adatait, hogy be tudja jelentkezni. Lépjen ki a **gyorskonfigurálás** panelről, és válassza a **hozzáférési kulcsok** lehetőséget a beállításjegyzék menüben. Másolja a **felhasználónevet** és az egyik **jelszót** (két) a vágólapra vagy a Jegyzettömbre. A bejelentkezéshez később szüksége lesz rájuk.

Most, hogy már tudja, mit kell tennie, jelentkezzen be a virtuális gépre.

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>RDP a Docker-rendszerkép létrehozásához használt virtuális géphez

Mivel már létrehozta a Docker-rendszerképet egy Windows 2016-kiszolgálón, be kell jelentkeznie az adott virtuális gépre. Ebből a virtuális gépről elküldheti a rendszerképet az imént létrehozott Azure Container Registry. Keresse meg a virtuális gépet a Azure Portal, majd válassza az **Áttekintés** , majd a **Kapcsolódás** lehetőséget. Ez a távoli asztal protokoll (RDP) használatával csatlakozik a virtuális géphez. A virtuális gép létrehozásakor a hitelesítő adatokat kell használnia.

## <a name="log-in-and-push-the-image-to-the-registry"></a>Jelentkezzen be, és küldje le a rendszerképet a beállításjegyzékbe

Miután bejelentkezett, nyisson meg egy parancssort, és kezdeményezzen a következő Docker-parancsokat:

-   **Docker-rendszerképek** – Itt láthatja a virtuális gépen jelenleg telepített rendszerképek listáját. Jegyezze fel a **Focus/es-acctdemo** , mert ez a következő:.

-   **Docker-bejelentkezési acrmf50.azurecr.IO** – a megfelelő formátum itt a *Docker \<registry name\> -Bejelentkezés*. Helyettesítse be a beállításjegyzék létrehozásakor használt nevet.

    -   Szüksége lesz a Azure Portalból másolt **felhasználónévre** és **jelszóra** . Ekkor az alábbihoz hasonlónak kell megjelennie.

    ![A rendszergazdai parancssor képernyőképe](media/deploy-image-2.png)

-   **Docker-címke-fókusz/es-acctdemo acrmf50.azurecr.IO/es-acctdemo** – ez a címke a megfelelő képet adja meg az adattár nevével. **Megjegyzés**: Ha a név nem \<microfocus/es-acctdemo\> működik, próbálkozzon a teljes rendszerkép-azonosító használatával. A parancs végrehajtása után írja be a következőt: **Docker images – No-TRUNC**. A következő képhez hasonlóan kell megjelennie. Figyelje meg, hogy a rendszerkép megfelelően van címkézve.

    ![Rendszergazdai parancssor kiválasztása képernyő](media/deploy-image-3.png)

-   **Docker push acrmf50.azurecr.IO/es-acctdemo** – ez elküldi a tényleges leküldést a tárházba. Mivel a méret 15 GB, a Futtatás néhány percet vesz igénybe. Ha minden rendben van, az alábbi képhez hasonlóan kell megjelennie.

    ![Rendszergazdai parancssor képernyő](media/deploy-image-4.png)

Most térjen vissza a Azure Portalra, kifejezetten a **tárházhoz**. Az **adattár** menüjében válassza a **adattárak** lehetőséget, és tekintse meg a felsorolt **es-acctdemo** . Most hozzon létre egy AK-fürtöt.

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Az Azure Kubernetes (ak) fürt létrehozása

A Azure Portal válassza az **erőforrás létrehozása** , majd a **tárolók/Kubernetes szolgáltatás** lehetőséget a **piactér** menüben. Ezután ki kell töltenie a Kubernetes- **fürt létrehozása** panelt. Ügyeljen arra, hogy a fürtöt az Ön által használt régióba és erőforráscsoporthoz is megtartsa. Elfogadhatja az alapértelmezett értékek hátralévő részét, kivéve a **csomópontok számát,** amelyeknek csak 1 értékkel kell rendelkezniük. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet.

![Kubernetes-fürt létrehozása képernyő](media/deploy-image-5.png)

Ha elkészült, az **üzemelő** példány a Kubernetes a panelen kiválasztott **erőforráscsoporthoz** helyezi el. Azonban a tényleges fürt saját erőforráscsoport lesz létrehozva a telepítés során. Ha a bal oldali menüben kiválasztja az **erőforráscsoportok** lehetőséget, az elnevezési konvenció alapján találhatja meg. Itt látható egy rendszerképet tartalmazó fájl, amely az utolsó a listában.

![Az erőforráscsoportok képernyőképe](media/deploy-image-6.png)

**A rendszerkép futtatása**

Itt az ideje, hogy lekérje a képet, és az AK-ban futtassa azt. A Azure Portal a legegyszerűbb módszer a Cloud Shell használatára. A Azure Portal jobb felső sarkában található ikon jelenik meg. Vegye figyelembe, hogy ebben az útmutatóban a bash rendszerhéjt használom.

![A Cloud Shell ikon képernyőképe](media/deploy-image-7.png)

A rendszerhéj betöltését követően írja be a következő parancsot:

**kubectl Run es-acctdemo--rendszerkép acrmf50.azurecr.io/es-acctdemo--port = 9040**

Ezzel lekéri a rendszerképet a **Acrmf50.azurecr.IO** adattárból, és betölti azt az AK-ba. Ezután futtatja a rendszerképet a 9040-as porton. Előfordulhat, hogy ezt a portot már megnyitotta a Docker-rendszerképhez. Ehhez a vállalati kiszolgálóhoz kell hozzáférnie.

A Kubernetes a telepítés létrejöttét jelző üzenettel kell válaszolnia.

![A központi telepítési üzenet képernyőképe](media/deploy-image-8.jpg)

Ha szeretné megtekinteni, hogy a tároló valóban fut-e, írja be a következőt: **kubectl Get hüvely**.

Az es-acctdemo-t futó Pod-ként kell látnia, ahogy az az alábbi képen is látható.

![Képernyőkép es-acctdemo futó Pod-ként](media/deploy-image-9.png)

Gratulálunk! Most már futtatja a vállalati kiszolgálót az Azure Kubernetes szolgáltatásban. A következő cikkben bemutatjuk, hogyan érheti el a vállalati kiszolgáló felügyeleti konzolját, valamint azt is, hogyan használhatja a Kubernetes az üzemelő példány skálázására.
