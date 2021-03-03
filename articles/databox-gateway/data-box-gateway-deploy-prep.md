---
title: 'Oktatóanyag: az Azure Portal előkészítése a Data Box Gateway üzembe helyezésére | Microsoft Docs'
description: Az Azure Data Box Gateway üzembe helyezésével foglalkozó első oktatóanyag részét képezi az Azure Portal előkészítése is.
services: databox-edge-gateway
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: ceda5015770ad0b9898def181fa7199f119920db
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706095"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Oktatóanyag: Felkészülés a Azure Data Box Gateway üzembe helyezésére

A jelen oktatóanyag az első az Azure Data Box Gateway teljes üzembe helyezéséhez szükséges üzembehelyezési oktatóanyagok sorozatában. Ez az oktatóanyag az Azure Portal előkészítésének módját ismerteti a Data Box Gateway-erőforrás üzembe helyezéséhez.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Új erőforrás létrehozása
> * A virtuáliseszköz-rendszerkép letöltése
> * Az aktiválási kulcs lekérése

## <a name="get-started"></a>Bevezetés

A Data Box Gateway üzembe helyezéséhez tekintse meg az alábbi oktatóanyagokat a megadott sorrendben.

| **#** | **Ebben a lépésben** | **Ezeket a dokumentumokat használja** |
| --- | --- | --- | 
| 1. |**[Az Azure Portal előkészítése a Data Box Gatewayhez](data-box-gateway-deploy-prep.md)** |Virtuális Data Box Gateway-eszköz üzembe helyezése előtt létre kell hoznia és konfigurálnia kell a Data Box Gateway-erőforrást. |
| 2. |**[A Data Box Gateway üzembe helyezése a Hyper-V-ben](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[A Data Box Gateway üzembe helyezése a VMware-ben](data-box-gateway-deploy-provision-vmware.md)**|Hyper-V esetén a virtuális Data Box Gateway-eszköz üzembe helyezéséhez, majd az eszközhöz való csatlakozáshoz a Hyper-V-t Windows Server 2016 vagy Windows Server 2012 R2 rendszeren futtató gazdarendszert használjon. <br><br><br> VMware esetén a VMware ESXi 6,0, 6,5 vagy 6,7 rendszert futtató gazdagépen hozzon létre és kapcsolódjon Data Box Gateway virtuális eszközhöz.<br></br> |
| 3. |**[A Data Box Gateway csatlakoztatása, konfigurálása és aktiválása](data-box-gateway-deploy-connect-setup-activate.md)** |Csatlakozzon a helyi webes felhasználói felülethez, végezze el az eszköz konfigurálását, majd aktiválja az eszközt. Ezután üzembe helyezheti az SMB-megosztásokat.  |
| 4. |**[Adatok átvitele a Data Box Gatewayjel](data-box-gateway-deploy-add-shares.md)** |Vegyen fel megosztásokat, és csatlakozzon a megosztásokhoz SMB vagy NFS használatával. |

Most megkezdheti az Azure Portal beállítását.

## <a name="prerequisites"></a>Előfeltételek

Az alábbiakban a Data Box Gateway-erőforrással, a Data Box Gateway-eszközzel és az adatközponti hálózattal kapcsolatos konfigurációs előfeltételeket láthatja.

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A Microsoft Azure-előfizetés engedélyezve lett egy Azure Stack Edge-erőforráshoz. Győződjön meg arról, hogy olyan támogatott előfizetést használt, mint például a [Microsoft nagyvállalati szerződés (EA)](https://azure.microsoft.com/overview/sales-number/), a [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp)vagy a [Microsoft Azure szponzorálás](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Az Azure Stack Edge/Data Box Gateway, a IoT Hub és az Azure Storage-erőforrások esetében tulajdonosi vagy közreműködői hozzáférése van az erőforráscsoport szintjén.
    - Ha Azure Stack Edge/Data Box Gateway erőforrást szeretne létrehozni, akkor az erőforrás-csoport szintjén a közreműködő (vagy magasabb szintű) jogosultsággal kell rendelkeznie. Győződjön meg arról is, hogy a `Microsoft.DataBoxEdge` szolgáltató regisztrálva van. A regisztrálás módjával kapcsolatos információkat az [erőforrás-szolgáltatók regisztrálásával](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers) foglalkozó témakörben tekintheti meg.
    - A Storage-fiók erőforrásának létrehozásához ismét közreműködői vagy magasabb szintű hozzáférési hatókörre van szükség az erőforráscsoport szintjén. Az Azure Storage alapértelmezés szerint regisztrált erőforrás-szolgáltató.
- Rendszergazdai vagy felhasználói hozzáféréssel rendelkezik Microsoft Graph API-hoz. További információ: [Microsoft Graph engedélyek referenciája](/graph/permissions-reference).
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.

### <a name="for-the-data-box-gateway-device"></a>Data Box Gateway-eszköz

A virtuális eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

- A Hyper-V-t futtató gazdagéprendszer a Windows Server 2012 R2 vagy újabb, vagy a VMware (ESXi 6,0, 6,5 vagy 6,7) operációs rendszerhez érhető el, amely használható egy eszköz kiépítéséhez.
- A gazdarendszernek képesnek kell lennie az alábbi erőforrásokat a virtuális Data Box-eszköz üzembe helyezésére elkülöníteni:
  
  - Legalább 4 virtuális processzor.
  - Legalább 8 GB RAM. Legalább 16 GB RAM használatát javasoljuk.
  - Egy hálózati adapter.
  - 250 GB-os operációsrendszer-lemez.
  - 2 TB-os virtuális lemez a rendszeradatok számára.

### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az adatközpont hálózata a Data Box Gateway-eszköz hálózati követelményeinek megfelelően lett konfigurálva. További információt a [Data Box Gateway rendszerkövetelményei](data-box-gateway-system-requirements.md)című témakörben talál.

- A Data Box Gateway normál működési feltételei:

    - Legalább 10 MB/s letöltési sávszélesség, hogy az eszköz naprakész maradjon.
    - A fájlok átviteléhez legalább 20 Mbps dedikált feltöltési és letöltési sávszélesség szükséges.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Ha rendelkezik meglévő Data Box Gateway-erőforrással a virtuális eszközök kezeléséhez, hagyja ki ezt a lépést, és ugorjon [Az aktiválási kulcs lekérése](#get-the-activation-key) lépésre.

Data Box Gateway-erőforrás létrehozásához hajtsa végre a következő lépéseket a Azure Portal.

1. A Microsoft Azure hitelesítő adataival jelentkezzen be a következő portálokra:

    - A Azure Portal ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com) .
    - A Azure Government portál ezen az URL-címen: [https://portal.azure.us](https://portal.azure.us) . Részletekért lépjen a [kapcsolódás Azure Government a portál használatával](../azure-government/documentation-government-get-started-connect-with-portal.md).
    
2. Válassza a **+ Erőforrás létrehozása** lehetőséget.

    ![Erőforrás-Azure Data Box Gateway létrehozása gomb](media/data-box-gateway-deploy-prep/data-box-gateway-create-a-resource.png)

3. A keresőmezőbe írja be a **Data Box Gateway** kifejezést, majd nyomja le az ENTER billentyűt.

    ![A Data Box Gateway szolgáltatás keresése](media/data-box-gateway-deploy-prep/data-box-gateway-search-box.png)

    Ezután válassza a **Azure Data Box Gateway** lehetőséget.

    ![Válassza ki a Data Box Gateway SKU-t](media/data-box-gateway-deploy-prep/data-box-gateway-sku.png)

4. Válassza a **Létrehozás** lehetőséget.

    ![A Data Box Gateway erőforrás létrehozásához kattintson a Létrehozás gombra.](media/data-box-gateway-deploy-prep/data-box-gateway-create.png)

5. Az **Alapszintű beállítások** lapon:

    Adja meg vagy válassza ki a következő **projekt részleteit**.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés    |Válassza ki a Data Box Gateway eszközéhez használni kívánt előfizetést. Az előfizetés az Ön számlázási fiókjához lesz társítva.|
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/management/overview.md) kapcsolatban.|

   Adja meg vagy válassza ki a következő **példány részleteit**.

    |Beállítás  |Érték  |
    |---------|---------|
    |Név   |Az erőforrást azonosító valódi név.<br>A név 2 – 50 karakterből áll, és betűket, számokat és kötőjeleket tartalmaz. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie. |
    |Region  |Válassza ki azt a régiót, ahol üzembe szeretné helyezni az erőforrást. Válassza ki azt a földrajzi régiót, ahová az eszközt telepíteni szeretné. <br> Az összes olyan régió listájának megtekintéséhez, ahol az adatok alapátjáró/Azure Stack peremhálózati erőforrásai elérhetők, tekintse meg az [Azure-termékek régió alapján elérhetővé](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)tételeit. <br> A Azure Government az [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/regions/) felsorolt összes kormányzati régió elérhető.|

   Ezután válassza a **felülvizsgálat + létrehozás** lehetőséget a megrendelés áttekintéséhez.

   ![A projekt-és példányok részletes bejegyzése egy Data Box Gateway rendeléshez](media/data-box-gateway-deploy-prep/data-box-gateway-basics.png)

7. A **felülvizsgálat + létrehozás** lapon tekintse át a **díjszabás részleteit**, **használati feltételek** és az erőforrás részleteit. Válassza a **Létrehozás** lehetőséget.

    ![Data Box Gateway az erőforrás részletei a felülvizsgálathoz](media/data-box-gateway-deploy-prep/data-box-gateway-resource-review-create.png)

Az erőforrás létrehozása néhány percet vesz igénybe. Az erőforrás sikeres létrehozása és üzembe helyezése után értesítést kap. Válassza az **Erőforrás megnyitása** lehetőséget.

![Befejezett Data Box Gateway sorrend](media/data-box-gateway-deploy-prep/data-box-gateway-completed-order.png)

## <a name="download-the-virtual-device-image"></a>A virtuáliseszköz-rendszerkép letöltése

A Data Box Gateway-erőforrás létrehozása után töltse le a megfelelő virtuáliseszköz-rendszerképet a virtuális eszköz a gazdarendszeren való üzembe helyezéséhez. A virtuális eszköz lemezképei egy adott operációs rendszerre vonatkoznak.

> [!IMPORTANT]
> A Data Box Gatewayen futó szoftver csak a Data Box Gateway-erőforrással használható.

A virtuális eszköz rendszerképének letöltéséhez kövesse az alábbi lépéseket a [Azure Portal](https://portal.azure.com/) .

1. A létrehozott erőforrásban, majd válassza az **Áttekintés** lehetőséget. Ha meglévő Azure Data Box Gateway erőforrással rendelkezik, válassza ki az erőforrást, és lépjen az **Áttekintés** elemre. Válassza az **eszköz beállítása** lehetőséget.

    ![Új Data Box Gateway-erőforrás](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. A **rendszerkép letöltése** csempén válassza ki a virtuális gép kiépítéséhez használt gazdagép operációs rendszerének megfelelő virtuális eszköz rendszerképet. A képfájlok körülbelül 5,6 GB méretűek.
   
   * [Windows Server 2012 R2 vagy újabb rendszer futó Hyper-V VHDX-fájlja](https://aka.ms/dbe-vhdx-2012).
   * [VMDK VMware ESXi 6,0, 6,5 vagy 6,7](https://aka.ms/dbe-vmdk).

    ![Data Box Gateway virtuális eszköz rendszerképének letöltése](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Töltse le a fájlt egy helyi meghajtóra, csomagolja ki, és jegyezze fel, hol található a kicsomagolt fájl.


## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Miután a Data Box Gateway erőforrás működik, le kell kérnie az aktiválási kulcsot. Ezzel a kulccsal aktiválhatja, majd csatlakoztathatja a Data Box Gateway-eszközt az erőforráshoz. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Válassza ki a létrehozott erőforrást, majd válassza az **Áttekintés** lehetőséget. Az **eszköz beállítása** területen lépjen a **Konfigurálás és aktiválás** csempére.

    ![Csempék konfigurálása és aktiválása](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Az aktiválási kulcs létrehozásához válassza a **kulcs létrehozása** lehetőséget. Kattintson a másolás ikonra a kulcs másolásához és a későbbi használatra mentéséhez.

    ![Aktiválási kulcs lekérése](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Az aktiválási kulcs három nappal a létrehozása után lejár.
> - Ha a kulcs lejárt, állítson be egy új kulcsot. A régebbi kulcs nem lesz érvényes.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a Data Box Gatewayjel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * A virtuáliseszköz-rendszerkép letöltése
> * Az aktiválási kulcs lekérése

A következő oktatóanyag azt mutatja be, hogyan helyezhet üzembe egy virtuális gépet a Data Box Gateway számára. A gazdagép operációs rendszerétől függően az alábbi témakörökben tekintheti meg a részletes utasításokat:

> [!div class="nextstepaction"]
> [A Data Box Gateway üzembe helyezése a Hyper-V-ben](./data-box-gateway-deploy-provision-hyperv.md)

OR

> [!div class="nextstepaction"]
> [A Data Box Gateway üzembe helyezése a VMware-ben](./data-box-gateway-deploy-provision-vmware.md)