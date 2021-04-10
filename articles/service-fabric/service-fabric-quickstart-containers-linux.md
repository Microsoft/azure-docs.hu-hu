---
title: Linuxos Container-alkalmazás létrehozása Service Fabric az Azure-ban
description: Ebben a rövid útmutatóban egy Docker-rendszerképet készít az alkalmazásával, amelyet leküld egy tárolóregisztrációs adatbázisba, majd üzembe helyezi a tárolót egy Service Fabric-fürtön.
ms.topic: quickstart
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f58b8feda0e88d8a5e7cddaabbc650b0f0ab3973
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788162"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Rövid útmutató: Linux-tárolók üzembe helyezése a Service Fabricben

Az Azure Service Fabric egy elosztott rendszerplatform, amely skálázható és megbízható mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál.

Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe Linux-tárolókat egy Azure-beli Service Fabric-fürtön. Miután elkészült, egy szavazóalkalmazást kap, amely egy Python-alapú webes előtérrendszert és egy Redis-alapú háttérrendszert tartalmaz, amely egy Service Fabric-fürtben fut. Azt is megismeri, hogyan végezheti el egy alkalmazás feladatátvételét és hogyan skálázhatja a fürtben lévő alkalmazásokat.

![Szavazóalkalmazás weboldala][quickstartpic]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. Hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) , mielőtt elkezdené, ha nem rendelkezik előfizetéssel.

2. Az [Azure CLI](/cli/azure/install-azure-cli-apt) telepítése

3. A [Service FABRIC SDK és parancssori](service-fabric-get-started-linux.md#installation-methods) felület telepítése

4. A [git](https://git-scm.com/) telepítése


## <a name="get-the-application-package"></a>Az alkalmazáscsomag beszerzése

A tárolók a Service Fabric szolgáltatásban való üzembe helyezéséhez jegyzékfájlok egy készletére lesz szükség (az alkalmazásdefinícióra), amelyek leírják az egyes tárolókat és az alkalmazást.

A konzolon a git használatával klónozott az alkalmazás definíciójának másolatát; Ezután módosítsa a címtárakat a `Voting` klónozott könyvtárba.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Service Fabric-fürt létrehozása

Ha az alkalmazást üzembe szeretné helyezni az Azure-ban, szüksége lesz egy Service Fabric-fürtre a futtatásához. Az alábbi parancsok egy öt csomópontos fürtöt hoznak létre az Azure-ban.  A parancsok létrehoznak egy önaláírt tanúsítványt is, hozzáadják azt egy kulcstartóhoz, és helyileg töltik le a tanúsítványt. A rendszer az új tanúsítványt használja a fürt biztonságossá tételéhez az üzembe helyezés során, és az ügyfelek hitelesítésére szolgál.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName='containertestcluster' 
ClusterName='containertestcluster' 
Location='eastus' 
Password='q6D7nN%6ck@6' 
Subject='containertestcluster.eastus.cloudapp.azure.com' 
VaultName='containertestvault' 
VmPassword='Mypa$$word!321'
VmUserName='sfadminuser'

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> A webes előtérrendszer a konfigurációja szerint a 80-as porton figyeli a bejövő forgalmat. Alapértelmezés szerint a 80-es port nyitva van a fürtön futó virtuális gépeken és az Azure Load balanceren.
>

## <a name="configure-your-environment"></a>A környezet konfigurálása

A Service Fabric számos eszközt nyújt, amelyekkel kezelheti a fürtöket és azok alkalmazásait:

- A Service Fabric Explorert, amely egy böngészőalapú eszköz.
- A Service Fabric parancssori felületet (CLI-t), amely az Azure CLI-n fut. 
- PowerShell-parancsokat.

Ebben a rövid útmutatóban az Service Fabric CLI és a Service Fabric Explorer (webalapú eszköz) használatával. A Service Fabric Explorer használatához importálnia kell a tanúsítvány PFX-fájlját a böngészőbe. Alapértelmezés szerint a PFX-fájlnak nincs jelszava.

A Mozilla Firefox az Ubuntu 16,04 alapértelmezett böngészője. A tanúsítvány a Firefoxba importálásához kattintson a böngésző jobb felső sarkában lévő menügombra, majd a **Beállítások** gombra. A **Beállítások** oldalon a keresőmezővel keressen rá a „tanúsítványok” kifejezésre. Kattintson a **Tanúsítványkezelő** gombra, válassza a **Saját tanúsítványok** fület, kattintson az **Importálás** lehetőségre, és kövesse az utasításokat a tanúsítvány importálásához.

   ![Tanúsítvány telepítése Firefoxon](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>A Service Fabric-alkalmazás üzembe helyezése

1. Kapcsolódjon a Service Fabric-fürthöz az Azure-ban a parancssori felület használatával. A végpont a fürt felügyeleti végpontja. A PEM-fájlt az előző szakaszban hozta létre. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Használja a telepítési szkriptet a szavazóalkalmazás definíciójának a fürtbe való másolásához, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.  A PEM-tanúsítványfájl a *install.sh* -fájllal megegyező könyvtárban kell lennie.

    ```bash
    ./install.sh
    ```

3. Nyisson meg egy webböngészőt, majd navigáljon a fürt Service Fabric Explorer-végpontjára. A végpont formátuma a következő:  **https:// \<my-azure-service-fabric-cluster-url> : 19080/Explorer**; például: `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer` . </br>

4. Bontsa ki az **alkalmazások** csomópontot, és ellenőrizze, hogy most már van-e bejegyzés a szavazási alkalmazás típusához és a létrehozott példányhoz.

    ![Service Fabric Explorer][sfx]

5. A futó tárolóhoz való csatlakozáshoz nyisson meg egy webböngészőt, és navigáljon a fürt URL-címére – például: `http://containertestcluster.eastus.cloudapp.azure.com:80`. Ekkor a szavazóalkalmazásnak kell megjelennie a böngészőben.

    ![Szavazóalkalmazás weboldala][quickstartpic]

> [!NOTE]
> A Docker Compose használatával is üzembe helyezhet Service Fabric-alkalmazásokat. A következő paranccsal például üzembe helyezheti és telepítheti az alkalmazást a fürtön a Docker Compose használatával.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Tároló feladatátvétele a fürtben

A Service Fabric biztosítja, hogy meghibásodás esetén a tárolópéldányok automatikusan áthelyeződjenek a fürt más csomópontjaira. Manuálisan is ürítheti a csomópontokat a tárolók tekintetében, és azokat zökkenőmentesen helyezheti át a fürt más csomópontjaira. A Service Fabric több módot nyújt a szolgáltatások méretezésére. A következő lépésekben a Service Fabric Explorert használjuk.

Az előtértároló feladatátvételéhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`.
2. Kattintson a **fabric:/Voting/azurevotefront** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli). Figyelje meg a csomópont nevét a fanézetben, amely azokat a csomópontokat mutatja, amelyeken a tároló jelenleg fut; például: `_nodetype_1`.
3. Bontsa ki a **Csomópontok** csomópontot a fanézetben. Kattintson a három pontra (...) a tárolót futtató csomópont mellett.
4. Válassza az **Újraindítás** lehetőséget a csomópont újraindításához, majd erősítse meg az újraindítási műveletet. Az újraindítás kikényszeríti a tároló feladatátvételét a fürt egy másik csomópontjára.

    ![Csomópont nézet a Service Fabric Explorerben][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Alkalmazások és szolgáltatások méretezése a fürtökben

A Service Fabric-szolgáltatások könnyen méretezhetők egy adott fürtben a szolgáltatások terhelésének megfelelően. A szolgáltatások méretezése a fürtben futó példányok számának módosításával történik.

A webes előtér-szolgáltatás méretezéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `https://containertestcluster.eastus.cloudapp.azure.com:19080`.
2. Kattintson a három pontra a fanézetben a **fabric:/Voting/azurevotefront** csomópont mellett, és válassza a **Szolgáltatás méretezése** lehetőséget.

    ![A Service Fabric Explorer méretezési szolgáltatásának elindítása][containersquickstartscale]

    Most már méretezheti a webes előtér-szolgáltatás példányainak számát.

3. Módosítsa a számot **2**-re, és kattintson a **Szolgáltatás méretezése** gombra.
4. Kattintson a **fabric:/Voting/azurevotefront** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli).

    ![A Service Fabric Explorer méretezési szolgáltatása befejezve][containersquickstartscaledone]

    Most láthatja, hogy a szolgáltatás két példánnyal rendelkezik. A fanézetben megtekintheti, hogy a példányok melyik csomópontokon futnak.

Ezzel az egyszerű felügyeleti eljárással megduplázta az előtér-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos tudni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány elinduljon a fürtben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használja a sablonban megadott eltávolítási szkriptet (uninstall.sh) az alkalmazáspéldánynak a fürtről történő törléséhez, és törölje az alkalmazástípus regisztrációját. A szkriptnek némi időre van szüksége a példány kiürítéséhez, ezért a telepítési szkriptet nem érdemes azonnal ezen szkript után futtatni. A Service Fabric Explorerrel megállapíthatja, hogy mikor lett eltávolítva a példány és mikor lett megszüntetve az alkalmazástípus regisztrációja.

```bash
./uninstall.sh
```

A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése.

Jelentkezzen be az Azure-ba, és válassza ki azt az előfizetés-azonosítót, amellyel el szeretné távolítani a fürtöt. Az Azure Portalra bejelentkezve keresheti meg az előfizetés azonosítóját. Törölje az erőforráscsoportot és az összes fürterőforrás-erőforrást az az [Group delete paranccsal](/cli/azure/group).

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Ha végzett a fürt használatával, eltávolíthatja a tanúsítványt a tanúsítványtárolóból. Például:
- Windows rendszeren: Használja a [Tanúsítványok MMC beépülő modult](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Ügyeljen arra, hogy a **Saját felhasználói fiókot** válassza a beépülő modul hozzáadásakor. Lépjen a `Certificates - Current User\Personal\Certificates` helyre, és távolítsa el a tanúsítványt.
- Mac rendszeren: Használja a Keychain alkalmazást.
- Ubuntu rendszeren: Kövesse a tanúsítványok megtekintéséhez használt lépéseket, majd távolítsa el a tanúsítványt.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Linux-alapú tárolóalkalmazást egy Service Fabric-fürtön az Azure-ban, elvégezte az alkalmazás feladatátvételét és méretezte azt a fürtben. Ha bővebb információra van szüksége a Linux-tárolók Service Fabricben való használatával kapcsolatban, lépjen tovább a Linux-tárolóalkalmazásokról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Linux rendszerű tárolóalkalmazás létrehozása](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
