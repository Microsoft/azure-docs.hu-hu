---
title: Oktatóanyag a Azure Portal, adatközpont-környezet előkészítéséhez Azure Stack Edge Pro R üzembe helyezéséhez
description: Az Azure Stack Edge Pro R üzembe helyezésével kapcsolatos első oktatóanyag a Azure Portal előkészítését foglalja magában.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: 5e220759a46ad9098f81a9534fa64145adade2b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104613124"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-r"></a>Oktatóanyag: Felkészülés a Azure Stack Edge Pro R üzembe helyezésére

Ez az oktatóanyag a telepítési oktatóanyagok sorozatának első része, amely a Azure Stack Edge Pro R teljes telepítéséhez szükséges. Ez az oktatóanyag leírja, hogyan készítheti elő a Azure Portal egy Azure Stack Edge-erőforrás üzembe helyezéséhez. Az oktatóanyag egy 1 csomópontos Azure Stack Edge Pro R-eszközt használ egy szünetmentes áramforrással (UPS).

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

### <a name="get-started"></a>Bevezetés

Azure Stack Edge Pro R üzembe helyezéséhez tekintse meg az alábbi oktatóanyagokat az előírt sorozatban.

| Ehhez a lépéshez | Ezeket a dokumentumokat használja |
| --- | --- |
| **Előkészítése** |Ezeket a lépéseket a közelgő üzembe helyezés előkészítésekor kell elvégezni. |
| **[Üzembe helyezési konfigurációs ellenőrzőlista](#deployment-configuration-checklist)** |Ezzel az ellenőrzőlistával információkat gyűjthet és rögzíthet az üzembe helyezés előtt és közben. |
| **[Központi telepítés előfeltételei](#prerequisites)** |Ezek az előfeltételek ellenőrzik, hogy a környezet készen áll-e a telepítésre. |
|  | |
|**Üzembe helyezési oktatóanyagok** |Ezek az oktatóanyagok a Azure Stack Edge Pro R-eszköz éles környezetben történő üzembe helyezéséhez szükségesek. |
|**[1. a Azure Portal előkészítése az eszközre](azure-stack-edge-pro-r-deploy-prep.md)** |Hozza létre és konfigurálja a Azure Stack Edge-erőforrást, mielőtt telepítené a Azure Stack Box Edge fizikai eszközét. |
|**[2. az eszköz telepítése](azure-stack-edge-pro-r-deploy-install.md)**|Ellenőrizze és csatlakoztassa a fizikai eszközét.  |
|**[3. kapcsolódás az eszközhöz](azure-stack-edge-pro-r-deploy-connect.md)** |Az eszköz telepítése után kapcsolódjon az eszköz helyi webes felhasználói felületéhez.  |
|**[4. a hálózati beállítások konfigurálása](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)** |Konfigurálja a hálózatot, beleértve az eszköz számítási hálózatát és WebProxy-beállításait.   |
|**[5. eszközbeállítások megadása](azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)** |Rendeljen hozzá egy eszköznév és egy DNS-tartományt, konfigurálja a frissítési kiszolgálót és az eszköz idejét. |
|**[6. biztonsági beállítások konfigurálása](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)** |Konfigurálja a tanúsítványokat, a VPN-t és a titkosítást az eszközhöz. Használja az eszköz által generált tanúsítványokat, vagy hozza létre saját tanúsítványait.   |
|**[7. aktiválja az eszközt](azure-stack-edge-pro-r-deploy-activate.md)** |Az eszköz aktiválásához használja az aktiválási kulcsot a szolgáltatásból. Az eszköz készen áll az SMB-vagy NFS-megosztások beállítására, vagy a REST-en keresztüli kapcsolódásra. |
|**[8. a számítás konfigurálása](azure-stack-edge-gpu-deploy-configure-compute.md)** |Konfigurálja a számítási szerepkört az eszközön. A rendszer létrehoz egy Kubernetes-fürtöt is. |

Most megkezdheti az Azure Portal beállítását.

## <a name="deployment-configuration-checklist"></a>Üzembehelyezési konfigurációs ellenőrzőlista

Az eszköz üzembe helyezése előtt össze kell gyűjtenie az adatokat a Azure Stack Edge Pro-eszközön lévő szoftver konfigurálásához. Ezen információk némelyikének előzetes előkészítése megkönnyíti az eszköz telepítésének folyamatát a környezetben. Az [Azure stack Edge Pro R Deployment Configuration ellenőrzőlista](azure-stack-edge-pro-r-deploy-checklist.md) használatával jegyezze fel a konfiguráció részleteit az eszköz üzembe helyezése során.

## <a name="prerequisites"></a>Előfeltételek

A következő konfigurációs előfeltételek vonatkoznak az Azure Stack Edge-erőforrásra, az Azure Stack Edge-eszközre és az adatközpont-hálózatra.

### <a name="for-the-azure-stack-edge-resource"></a>Az Azure Stack Edge-erőforráshoz

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Az Azure Stack Edge-eszközhöz

Fizikai eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

- Áttekintette az eszköz biztonsági információit a következő helyen: [biztonsági irányelvek az Azure stack Edge-eszközhöz](azure-stack-edge-pro-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 



### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az adatközpontban található hálózat a Azure Stack Edge-eszköz hálózati követelményeinek megfelelően van konfigurálva. További információ: [Azure stack Edge Pro R rendszerkövetelményei](azure-stack-edge-pro-r-system-requirements.md).

- Az eszköz normál működési feltételei:

    - Legalább 10 MB/s letöltési sávszélesség, hogy az eszköz naprakész maradjon.
    - A fájlok átviteléhez legalább 20 Mbps dedikált feltöltési és letöltési sávszélesség szükséges.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Ha rendelkezik meglévő Azure Stack Edge-erőforrással a fizikai eszköz kezeléséhez, hagyja ki ezt a lépést, és lépjen [az aktiválási kulcs lekérése](#get-the-activation-key)elemre.

### <a name="portal"></a>[Portál](#tab/azure-portal)

Azure Stack peremhálózati erőforrás létrehozásához hajtsa végre a következő lépéseket a Azure Portal.

1. A Microsoft Azure hitelesítő adataival jelentkezzen be a Azure Portalba ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com) .

2. A bal oldali panelen válassza az **+ erőforrás létrehozása** lehetőséget. Keresse meg és válassza ki **Azure stack Edge/Data Box Gateway**. Válassza a **Létrehozás** lehetőséget. 

3. Válassza ki az Azure Stack Edge Pro-eszközhöz használni kívánt előfizetést. Válassza ki azt az országot, ahová a fizikai eszközt el szeretné szállítani. Válassza az **eszközök megjelenítése** lehetőséget.

    ![1. erőforrás létrehozása](media/azure-stack-edge-pro-r-deploy-prep/create-resource-1.png)

4. Válassza az eszköz típusa lehetőséget. Az **Azure stack Edge** területen válassza az **Azure stack Edge Pro R** elemet, majd válassza a **kiválasztás** lehetőséget. Ha bármilyen probléma merül fel, vagy nem tudja kijelölni az eszköz típusát, ugorjon a [rendezési problémák elhárítása](azure-stack-edge-troubleshoot-ordering.md)című témakörre.

    ![2. erőforrás létrehozása](media/azure-stack-edge-pro-r-deploy-prep/create-resource-2.png)

5. Az üzleti igények alapján kiválaszthatja **Azure stack Edge Pro r Single Node** vagy **Azure stack Edge Pro r egyetlen csomópontot a UPS** használatával.  

    ![3. erőforrás létrehozása](media/azure-stack-edge-pro-r-deploy-prep/create-resource-3.png)

6. Az **alapvető beállítások** lapon adja meg vagy válassza ki a következő **projekt részleteit**.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés    |Az előfizetés automatikusan kitöltődik a korábbi kiválasztás alapján. A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/management/overview.md) kapcsolatban.     |

7. Adja meg vagy válassza ki a következő **példány részleteit**.

    |Beállítás  |Érték  |
    |---------|---------|
    |Név   | Az erőforrást azonosító valódi név.<br>A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |
    |Region     |Az Azure Stack Edge-erőforrást tartalmazó régiók listáját itt tekintheti meg: [régiónként elérhető Azure-termékek](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Government használata esetén az összes kormányzati régió elérhető az [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/regions/)látható módon.<br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|

    ![4. erőforrás létrehozása](media/azure-stack-edge-pro-r-deploy-prep/create-resource-4.png)


8. Válassza a **Next (tovább): szállítási címet**.

    - Ha már rendelkezik egy eszközzel, válassza az **Azure stack Edge Pro R-eszközhöz** tartozó kombinált listát.

        ![5. erőforrás létrehozása](media/azure-stack-edge-pro-r-deploy-prep/create-resource-5.png)

    - Ha ez az eszköz a megrendeléshez használt új eszköz, adja meg a kapcsolattartó nevét, a vállalatot, az eszköz szállítását és a kapcsolattartási adatokat.

        ![6. erőforrás létrehozása](media/azure-stack-edge-pro-r-deploy-prep/create-resource-6.png)

9. Kattintson a **Tovább gombra: címkék**. Opcionálisan megadhat címkéket az erőforrások kategorizálása és a számlázás konszolidálása érdekében. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre.

10. A **felülvizsgálat + létrehozás** lapon tekintse át a **díjszabás részleteit**, **használati feltételek** és az erőforrás részleteit. Válassza ki az **adatvédelmi feltételeket áttekintő** kombinált listát.

    ![7. erőforrás létrehozása](media/azure-stack-edge-pro-r-deploy-prep/create-resource-7.png) 

    Arról is értesítést kap, hogy az erőforrás létrehozásakor egy Managed Service Identity (MSI) engedélyezve van, amely lehetővé teszi a felhőalapú szolgáltatásokban történő hitelesítést. Ez az identitás mindaddig létezik, amíg az erőforrás létezik.

11. Válassza a **Létrehozás** lehetőséget.

    Az erőforrás létrehozása néhány percet vesz igénybe. A rendszer létrehoz egy MSI-t is, amely lehetővé teszi, hogy az Azure Stack Edge-eszköz kommunikáljon az Azure-beli erőforrás-szolgáltatóval.

    Az erőforrás sikeres létrehozása és üzembe helyezése után értesítést kap. Válassza az **Erőforrás megnyitása** lehetőséget.

    ![Ugrás az Azure Stack Edge Pro-erőforrásra](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-1.png)

A megrendelés elhelyezése után a Microsoft áttekinti a rendelést és a kapcsolattartási adatokat (e-mailben) a szállítási adatokkal.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png) - If this is restored, it must go above "After the resource is successfully created." The azure-stack-edge-resource-1.png would seem superfluous in that case.--> 

> [!NOTE]
> Ha egyszerre több rendelést kíván létrehozni vagy egy meglévő rendelés klónozását, használhatja a [szkripteket az Azure-mintákban](https://github.com/Azure-Samples/azure-stack-edge-order). További információkért tekintse meg a README fájlt.

Ha a rendelési folyamat során problémákba ütközik, tekintse meg a [rendelési problémák elhárítása](azure-stack-edge-troubleshoot-ordering.md)című témakört.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha szükséges, készítse elő a környezetet az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure Stack Edge-erőforrás létrehozásához futtassa az alábbi parancsokat az Azure CLI-ben.

1. Hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group#az_group_create) paranccsal, vagy használjon egy meglévő erőforráscsoportot:

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Eszköz létrehozásához használja az az [databoxedge Device Create](/cli/azure/databoxedge/device#az_databoxedge_device_create) parancsot:

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgePR_Base
   ```

   Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon. A régió csak az eszközkezelés metaadatait tárolja. A tényleges adatok bármilyen Storage-fiókban tárolhatók.

   Az Azure Stack Edge-erőforrást tartalmazó régiók listáját itt tekintheti meg: [régiónként elérhető Azure-termékek](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Government használata esetén az összes kormányzati régió elérhető az [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/regions/)látható módon.

1. Rendelés létrehozásához futtassa az az [databoxedge Order Create](/cli/azure/databoxedge/order#az_databoxedge_order_create) parancsot:

   ```azurecli
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

Az erőforrás létrehozása néhány percet vesz igénybe. Futtassa az az [databoxedge Order show](/cli/azure/databoxedge/order#az_databoxedge_order_show) parancsot a sorrend megjelenítéséhez:

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

A megrendelés elvégzése után a Microsoft áttekinti a rendelést, és e-mailben kapcsolatba lép Önnel a szállítási adatokkal.

---

## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Az Azure Stack Edge-erőforrás működésének megkezdése után le kell kérnie az aktiválási kulcsot. Ezzel a kulccsal aktiválhatja és összekapcsolhatja Azure Stack Edge Pro-eszközét az erőforrással. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Válassza ki a létrehozott erőforrást, és válassza az **Áttekintés** lehetőséget.

2. A jobb oldali ablaktáblán adja meg a Azure Key Vault nevét, vagy fogadja el az alapértelmezett nevet. A kulcstároló neve 3 – 24 karakter hosszúságú lehet.

   Minden Azure Stack peremhálózati erőforráshoz létrejön egy kulcstartó, amely aktiválva van az eszközével. A Key Vault lehetővé teszi a titkos kulcsok tárolását és elérését, például a szolgáltatáshoz tartozó csatorna integritási kulcsát (a-t) a kulcstartóban tárolja.

   A kulcstároló nevének megadása után válassza az **aktiválási kulcs előállítása** lehetőséget az aktiválási kulcs létrehozásához.

   ![Aktiválási kulcs lekérése](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-3.png)

   Várjon néhány percet, amíg létrejön a Key Vault és az aktiválási kulcs. Kattintson a másolás ikonra a kulcs másolásához és a későbbi használatra mentéséhez.<!--Verify that the new screen has a copy icon.-->

> [!IMPORTANT]
> - Az aktiválási kulcs három nappal a létrehozása után lejár.
> - Ha a kulcs lejárt, állítson be egy új kulcsot. A régebbi kulcs nem lesz érvényes.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a Azure Stack Edge-témaköröket, például a következőket:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan telepítheti Azure Stack Edge-t.

> [!div class="nextstepaction"]
> [Azure Stack Edge telepítése](./azure-stack-edge-pro-r-deploy-install.md)
