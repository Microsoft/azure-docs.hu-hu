---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66111698"
---
## <a name="download-the-source-code"></a>Letöltheti a forráskódot

A távoli figyelési forráskódtárházak forráskódját és a Docker konfigurációs fájlokat kell futtatnia a mikroszolgáltatások Docker-rendszerképeket tartalmazza.

Klónozza, és hozza létre a tárház helyi verzióját, a parancssori környezetet használatával keresse meg a megfelelő mappát a helyi gépen. Ezután futtassa a következő eljárások egyikét parancsokat vagy klónozásához a .NET-adattárban:

Töltse le a legújabb verzióját a .NET mikroszolgáltatás-megvalósítások, futtassa:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Ezek a parancsok letöltheti a forráskódot, a mikroszolgáltatások helyi futtatásához használja a parancsfájlok mellett a mikroszolgáltatások. Már nincs szüksége a forráskódra futtassa a mikroszolgáltatások a Dockerben, bár a forráskód hasznos, ha később kívánja módosítani a megoldásgyorsító és módosítások helyi tesztelése.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások üzembe helyezése

Bár ez a cikk bemutatja, hogyan helyileg történő futtatása a mikroszolgáltatás-alapú, Azure-szolgáltatások a felhőben futó függenek. Használja a következő szkriptet az Azure-szolgáltatások üzembe helyezéséhez. A következő parancsfájl példák feltételezik, hogy egy Windows-gépen használja a .NET-adattárban. Ha egy másik környezetben dolgozik, állítsa be az elérési utak, fájlkiterjesztések, és elérési út elválasztók megfelelően.

### <a name="create-new-azure-resources"></a>Új Azure-erőforrások létrehozása

Ha még nem hozott a szükséges Azure-erőforrásokat, kövesse az alábbi lépéseket:

1. A parancssori környezetben, keresse meg a **\services\scripts\local\launch** mappájához klónozott másolatának a tárházban.

1. Futtassa az alábbi parancsokat a **számítógépek** parancssori eszközt, és jelentkezzen be az Azure-fiókjával:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Futtassa a **start.cmd** parancsfájlt. A parancsfájl kérni fogja, hogy a következő információkat:
   * A megoldás nevét.
   * A használandó előfizetés.
   * Használata az Azure-adatközpont helyét.

     A parancsfájl hoz létre, a megoldás nevű erőforráscsoportot az Azure-ban. Ez az erőforráscsoport tartalmazza az Azure-erőforrások a megoldásgyorsító használja. Ez az erőforráscsoport már nincs szüksége a megfelelő erőforrások után törölheti.

     A parancsfájl is hozzáad egy előtaggal rendelkező környezeti változók készletét **számítógépek** a helyi gépen. Ezek a környezeti változók adja meg a távoli Monitorozáshoz tudják olvasni az Azure Key Vault erőforrás részleteit. A Key Vault erőforrás, ahol távoli megfigyelés fogja beolvasni a konfigurációs értékeket az.

     > [!TIP]
     > Miután a parancsfájl futása befejeződött, azt is menti a környezeti változók nevű fájlba  **\<a kezdőmappa\>\\.pcs\\\<megoldásnevet\>.env** . A jövőbeli megoldás megoldásgyorsító központi telepítések használhatja őket. Vegye figyelembe, hogy a helyi gépen állítsa be a környezeti változók felülbírálási értékeket a **szolgáltatások\\parancsfájlok\\helyi\\.env** fájl futtatásakor **docker-compose**.

1. Lépjen ki a parancssori környezetből.

### <a name="use-existing-azure-resources"></a>A meglévő Azure-erőforrások

Ha már létrehozta a szükséges Azure-erőforrások, a helyi gépén hozzon létre a megfelelő környezeti változókat.
Állítsa be a környezeti változókat a következőket:
* **PCS_KEYVAULT_NAME** – az Azure Key Vault-erőforrás neve
* **PCS_AAD_APPID** – az AAD-Alkalmazásazonosító
* **PCS_AAD_APPSECRET** – az AAD-alkalmazás titkos kulcs

Konfigurációs értékeket az Azure Key Vault erőforrás lesz olvasható. Előfordulhat, hogy menteni ezeket a környezeti változókat a  **\<a kezdőmappa\>\\.pcs\\\<megoldásnevet\>.env** a központi telepítési fájlt. Vegye figyelembe, hogy a helyi gépen beállított környezeti változókat felülbírálási értékeket a **szolgáltatások\\parancsfájlok\\helyi\\.env** fájl futtatásakor **docker-compose**.

Az egyes konfigurációs szükség szerint a mikroszolgáltatás egy példányát tárolja **Key Vault** , amely a kezdeti telepítés jött létre. Igény szerint módosítani kell a megfelelő változók kulcstartóban.