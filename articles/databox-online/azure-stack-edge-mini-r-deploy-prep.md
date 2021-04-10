---
title: Oktatóanyag a Azure Portal, adatközpont-környezet előkészítéséhez Azure Stack Edge mini R-eszköz üzembe helyezéséhez | Microsoft Docs
description: Az Azure Stack Edge mini R-eszköz üzembe helyezésének első oktatóanyaga a Azure Portal előkészítését foglalja magában.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 7042d3ede872c5dc854675334b964430671d1306
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065633"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-mini-r"></a>Oktatóanyag: Felkészülés a Azure Stack Edge mini R üzembe helyezésére

Ez az oktatóanyag a telepítési oktatóanyagok sorozatának első része, amely a Azure Stack Edge mini R-eszköz teljes üzembe helyezéséhez szükséges. Ez az oktatóanyag leírja, hogyan készítheti elő a Azure Portal egy Azure Stack Edge-erőforrás üzembe helyezéséhez.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

### <a name="get-started"></a>Bevezetés

Azure Stack Edge mini R üzembe helyezéséhez tekintse meg az alábbi oktatóanyagokat az előírt sorozatban.

| Lépés | Leírás |
| --- | --- |
| **Előkészítése** |Ezeket a lépéseket a közelgő üzembe helyezés előkészítésekor kell elvégezni. |
| **[Üzembe helyezési konfigurációs ellenőrzőlista](#deployment-configuration-checklist)** |Ezzel az ellenőrzőlistával információkat gyűjthet és rögzíthet az üzembe helyezés előtt és közben. |
| **[Központi telepítés előfeltételei](#prerequisites)** |Ezek az előfeltételek ellenőrzik, hogy a környezet készen áll-e a telepítésre. |
|  | |
|**Üzembe helyezési oktatóanyagok** |Ezek az oktatóanyagok a Azure Stack Edge mini R-eszköz éles környezetben történő üzembe helyezéséhez szükségesek. |
|**[1. a Azure Portal előkészítése az eszközre](azure-stack-edge-mini-r-deploy-prep.md)** |Hozza létre és konfigurálja a Azure Stack Edge-erőforrást a fizikai eszköz telepítése előtt. |
|**[2. az eszköz telepítése](azure-stack-edge-mini-r-deploy-install.md)**|Ellenőrizze és csatlakoztassa a fizikai eszközét.  |
|**[3. kapcsolódás az eszközhöz](azure-stack-edge-mini-r-deploy-connect.md)** |Az eszköz telepítése után kapcsolódjon az eszköz helyi webes felhasználói felületéhez.  |
|**[4. a hálózati beállítások konfigurálása](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)** |Konfigurálja a hálózatot, beleértve az eszköz számítási hálózatát és WebProxy-beállításait.   |
|**[5. eszközbeállítások megadása](azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)** |Rendeljen hozzá egy eszköznév és egy DNS-tartományt, konfigurálja a frissítési kiszolgálót és az eszköz idejét. |
|**[6. biztonsági beállítások konfigurálása](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)** |Konfigurálja a tanúsítványokat saját tanúsítványok használatával, VPN-t állíthat be, és konfigurálhatja az eszközön a titkosítást.   |
|**[7. aktiválja az eszközt](azure-stack-edge-mini-r-deploy-activate.md)** |Az eszköz aktiválásához használja az aktiválási kulcsot a szolgáltatásból. Az eszköz készen áll az SMB-vagy NFS-megosztások beállítására, vagy a REST-en keresztüli kapcsolódásra. |
|**[8. a számítás konfigurálása](azure-stack-edge-gpu-deploy-configure-compute.md)** |Konfigurálja a számítási szerepkört az eszközön. A rendszer létrehoz egy Kubernetes-fürtöt is. |

Most megkezdheti az Azure Portal beállítását.

## <a name="deployment-configuration-checklist"></a>Üzembehelyezési konfigurációs ellenőrzőlista

Az eszköz üzembe helyezése előtt össze kell gyűjtenie az adatokat a Azure Stack Edge mini R-eszközön lévő szoftver konfigurálásához. Ezen információk némelyikének előzetes előkészítése megkönnyíti az eszköz telepítésének folyamatát a környezetben. Az [Azure stack Edge mini R Deployment Configuration ellenőrzőlista](azure-stack-edge-mini-r-deploy-checklist.md) használatával jegyezze fel a konfiguráció részleteit az eszköz üzembe helyezése során.

## <a name="prerequisites"></a>Előfeltételek

A következő konfigurációs előfeltételek vonatkoznak az Azure Stack Edge-erőforrásra, az Azure Stack Edge-eszközre és az adatközpont-hálózatra.

### <a name="for-the-azure-stack-edge-resource"></a>Az Azure Stack Edge-erőforráshoz

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Az Azure Stack Edge-eszközhöz

Fizikai eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

- Áttekintette az eszköz biztonsági információit az [Azure stack Edge-eszköz biztonsági iránymutatásaiban](azure-stack-edge-mini-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az adatközpontban található hálózat a Azure Stack Edge-eszköz hálózati követelményeinek megfelelően van konfigurálva. További információ: [Azure stack Edge mini R rendszerkövetelményei](azure-stack-edge-mini-r-system-requirements.md).

- Az Azure Stack Edge normál működési feltételei:

    - Legalább 10 MB/s letöltési sávszélesség, hogy az eszköz naprakész maradjon.
    - A fájlok átviteléhez legalább 20 Mbps dedikált feltöltési és letöltési sávszélesség szükséges.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Ha rendelkezik meglévő Azure Stack Edge-erőforrással a fizikai eszköz kezeléséhez, hagyja ki ezt a lépést, és lépjen [az aktiválási kulcs lekérése](#get-the-activation-key)elemre.

### <a name="portal"></a>[Portál](#tab/azure-portal)

Azure Stack peremhálózati erőforrás létrehozásához hajtsa végre a következő lépéseket a Azure Portal.

1. A Microsoft Azure hitelesítő adataival jelentkezzen be a Azure Portalba ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com) .


2. A bal oldali panelen válassza az **+ Erőforrás létrehozása** elemet. Keresse meg és válassza ki **Azure stack Edge/Data Box Gateway**. Válassza a **Létrehozás** lehetőséget. 

3. Válassza ki az Azure Stack Edge Pro-eszközhöz használni kívánt előfizetést. Válassza ki azt az országot, ahová a fizikai eszközt el szeretné szállítani. Válassza az **eszközök megjelenítése** lehetőséget.

    ![1. erőforrás létrehozása](media/azure-stack-edge-mini-r-deploy-prep/create-resource-1.png)


4. Válassza az eszköz típusa lehetőséget. Az **Azure stack Edge** területen válassza az **Azure stack Edge mini R** elemet, majd válassza a **kiválasztás** lehetőséget. Ha bármilyen probléma merül fel, vagy nem tudja kijelölni az eszköz típusát, ugorjon a [rendezési problémák elhárítása](azure-stack-edge-troubleshoot-ordering.md)című témakörre.

    [![2. erőforrás létrehozása](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png#lightbox)


5. Az **alapvető beállítások** lapon adja meg vagy válassza ki a következő **projekt részleteit**.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés    |Az előfizetés automatikusan kitöltődik a korábbi kiválasztás alapján. A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/management/overview.md) kapcsolatban.     |


6. Adja meg vagy válassza ki a következő **példány részleteit**.

    |Beállítás  |Érték  |
    |---------|---------|
    |Név   | Az erőforrást azonosító valódi név.<br>A név 2 – 50 karakterből állhat, beleértve a betűket, számokat és kötőjeleket.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |
    |Region     |Az Azure Stack Edge-erőforrást tartalmazó régiók listáját itt tekintheti meg: [régiónként elérhető Azure-termékek](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Government használata esetén az összes kormányzati régió elérhető az [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/regions/)látható módon.<br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|

    ![4. erőforrás létrehozása](media/azure-stack-edge-mini-r-deploy-prep/create-resource-4.png)


7. Válassza a **Next (tovább): szállítási címet**.

   - Ha már rendelkezik egy eszközzel, válassza a kombinált listát, amelyhez **már van eszköz**.

     ![5. erőforrás létrehozása](media/azure-stack-edge-mini-r-deploy-prep/create-resource-5.png)

   - Ha ez az új eszköz, amelyet Ön megrendelt, adja meg a kapcsolattartó nevét, a vállalatot, az eszköznek a szolgáltatásba való szállítását és a kapcsolattartási adatokat.

     ![6. erőforrás létrehozása](media/azure-stack-edge-mini-r-deploy-prep/create-resource-6.png)

8. Kattintson a **Tovább gombra: címkék**. Opcionálisan megadhat címkéket az erőforrások kategorizálása és a számlázás konszolidálása érdekében. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre.

9. A **felülvizsgálat + létrehozás** lapon tekintse át a **díjszabás részleteit**, **használati feltételek** és az erőforrás részleteit. Válassza ki az **adatvédelmi feltételeket áttekintő** kombinált listát.

    ![7. erőforrás létrehozása](media/azure-stack-edge-mini-r-deploy-prep/create-resource-7.png)

    Arról is értesítést kap, hogy az erőforrás létrehozásakor egy Managed Service Identity (MSI) van engedélyezve, amely lehetővé teszi a felhőalapú szolgáltatásokban történő hitelesítést. Ez az identitás mindaddig létezik, amíg az erőforrás létezik.

10. Válassza a **Létrehozás** lehetőséget.

    Az erőforrás létrehozása néhány percet vesz igénybe. A rendszer létrehoz egy MSI-t is, amely lehetővé teszi, hogy az Azure Stack Edge-eszköz kommunikáljon az Azure-beli erőforrás-szolgáltatóval.
    
    Az erőforrás sikeres létrehozása és üzembe helyezése után értesítést kap. Válassza az **Erőforrás megnyitása** lehetőséget.
    
    ![Ugrás az Azure Stack Edge Pro-erőforrásra](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-1.png)
    
A megrendelés elhelyezése után a Microsoft áttekinti a rendelést, és elküldi Önt (e-mailben) a szállítási adatokkal.

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
      --device-name myasegpu1 --location eastus --sku EdgeMR_Mini
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

Az Azure Stack Edge-erőforrás működésének megkezdése után le kell kérnie az aktiválási kulcsot. Ezzel a kulccsal aktiválhatja és összekapcsolhatja Azure Stack Edge mini R-eszközét az erőforrással. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Válassza ki a létrehozott erőforrást, és válassza az **Áttekintés** lehetőséget.

   ![Eszköz beállításának kiválasztása](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-2.png)

2. Az **aktiválás** csempén adja meg a Azure Key Vault nevét, vagy fogadja el az alapértelmezett nevet. A kulcstároló neve 3 – 24 karakter hosszúságú lehet. 

    Minden Azure Stack peremhálózati erőforráshoz létrejön egy kulcstartó, amely aktiválva van az eszközével. A Key Vault lehetővé teszi a titkok tárolását és elérését. Például a szolgáltatáshoz tartozó csatorna integritási kulcsát (a () a Key Vault tárolja).

    A kulcstároló nevének megadása után válassza az **aktiválási kulcs előállítása** lehetőséget az aktiválási kulcs létrehozásához.

    [![Aktiválási kulcs lekérése](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png)](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png#lightbox)

    Várjon néhány percet, amíg létrejön a Key Vault és az aktiválási kulcs. Kattintson a másolás ikonra a kulcs másolásához és a későbbi használatra mentéséhez.

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
> [Azure Stack Edge telepítése](./azure-stack-edge-mini-r-deploy-install.md)
