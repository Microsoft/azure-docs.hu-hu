---
title: Azure Spring Cloud üzembe helyezése virtuális hálózaton
description: Azure Spring Cloud üzembe helyezése virtuális hálózaton (VNet injekció).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 82dcd8c59c55a2866b51fd6dee896ea1298b6cf6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877980"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>Azure Spring Cloud üzembe helyezése virtuális hálózaton

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Ez az oktatóanyag azt ismerteti, hogyan helyezhető üzembe egy Azure Spring Cloud-példány a virtuális hálózaton. Ezt az üzembe helyezést néha VNet injekciónak is nevezzük.

Az üzembe helyezés a következőket teszi lehetővé:

* Az Azure Spring Cloud apps és a Service Runtime elkülönítése az internetről a vállalati hálózaton.
* Az Azure Spring Cloud interakciója a helyszíni adatközpontokban vagy más virtuális hálózatokban lévő Azure-szolgáltatásokban található rendszerekkel.
* Az Azure Spring Cloud bejövő és kimenő hálózati kommunikációjának szabályozása az ügyfelek számára.

> [!Note]
> Az Azure-beli virtuális hálózatot csak akkor választhatja ki, ha új Azure Spring Cloud Service-példányt hoz létre. Az Azure Spring Cloud létrehozása után nem lehet másik virtuális hálózat használatára váltani.

## <a name="prerequisites"></a>Előfeltételek

Regisztrálja az Azure Spring Cloud erőforrás-szolgáltatót a **Microsoft. AppPlatform** és a **Microsoft. tárolószolgáltatás** az [erőforrás-szolgáltató regisztrálása a Azure Portalon](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) vagy a következő Azure CLI-parancs futtatásával:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Virtuális hálózati követelmények

Az Azure Spring Cloud-példány üzembe helyezéséhez használt virtuális hálózatnak az alábbi követelményeknek kell megfelelnie:

* **Hely**: a virtuális hálózatnak ugyanazon a helyen kell lennie, mint az Azure Spring Cloud-példánynak.
* **Előfizetés**: a virtuális hálózatnak ugyanabban az előfizetésben kell lennie, mint az Azure Spring Cloud-példánnyal.
* **Alhálózatok**: a virtuális hálózatnak két, Azure Spring Cloud-példányra dedikált alhálózatot kell tartalmaznia:

    * A szolgáltatás futtatókörnyezetének egyike.
    * Az egyik a Spring boot Service-alkalmazásaihoz.
    * Egy-az-egyhez kapcsolat áll fenn ezen alhálózatok és egy Azure Spring Cloud-példány között. Használjon egy új alhálózatot minden egyes telepített szolgáltatási példányhoz. Mindegyik alhálózat csak egyetlen szolgáltatási példányt tartalmazhat.
* **Címterület**: a CIDR a Service Runtime alhálózathoz és a Spring boot Service-alhálózathoz is akár *28-* ot is blokkol.
* **Útválasztási táblázat**: alapértelmezés szerint az alhálózatok nem igényelnek meglévő útválasztási táblákat. [Saját útválasztási táblázatot](#bring-your-own-route-table)is használhat.

Az alábbi eljárások ismertetik a virtuális hálózat telepítését, amely az Azure Spring Cloud példányát tartalmazza.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Ha már rendelkezik virtuális hálózattal egy Azure Spring Cloud-példány üzemeltetéséhez, ugorjon az 1., 2. és 3. lépésre. A 4. lépésből elkezdheti a virtuális hálózat alhálózatának előkészítését.

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Az Azure Marketplace-en válassza a **hálózatkezelés**  >  **virtuális hálózat** lehetőséget.

1. A **virtuális hálózat létrehozása** párbeszédpanelen adja meg vagy válassza ki a következő adatokat:

    |Beállítás          |Érték                                             |
    |-----------------|--------------------------------------------------|
    |Előfizetés     |Válassza ki előfizetését.                         |
    |Erőforráscsoport   |Válassza ki az erőforráscsoportot, vagy hozzon létre egy újat.  |
    |Name             |Adja meg az **Azure-Spring-Cloud-vnet**.                 |
    |Hely         |Válassza az **USA keleti régiója** lehetőséget.                               |

1. Válassza a **Tovább: IP-címek** lehetőséget.

1. Az IPv4-címtartomány mezőben adja meg a **10.1.0.0/16** értéket.

1. Válassza az **alhálózat hozzáadása** lehetőséget. Ezután adja meg a **Service-Runtime-alhálózatot** az **alhálózat neveként** , majd adja meg a **10.1.0.0/28** értéket az **alhálózati címtartomány** számára. Ezután válassza a **Hozzáadás** elemet.

1. Válassza az **alhálózat hozzáadása** újra lehetőséget, majd adja meg az **alhálózat nevét** és az alhálózati **címtartományt**. Adja meg például az **alkalmazások-alhálózat** és a **10.1.1.0/28** értéket. Ezután válassza a **Hozzáadás** elemet.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Hagyja a többi értéket alapértelmezettként, majd válassza a **Létrehozás** lehetőséget.

## <a name="grant-service-permission-to-the-virtual-network"></a>Szolgáltatás engedélyének megadása a virtuális hálózat számára
Az Azure Spring Cloud **tulajdonosi** engedélyre van szüksége a virtuális hálózatához, hogy további üzembe helyezéshez és karbantartáshoz biztosítson egy dedikált és dinamikus szolgáltatásnevet a virtuális hálózaton.

Válassza ki a korábban létrehozott **Azure-Spring-Cloud-vnet** virtuális hálózatot.

1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd válassza a **Hozzáadás**  >  **szerepkör-hozzárendelés hozzáadása** elemet.

    ![A hozzáférés-vezérlési képernyőt megjelenítő képernyőkép.](./media/spring-cloud-v-net-injection/access-control.png)

1. A **szerepkör-hozzárendelés hozzáadása** párbeszédpanelen adja meg vagy válassza ki a következő adatokat:

    |Beállítás  |Érték                                             |
    |---------|--------------------------------------------------|
    |Szerepkör     |Válassza a **tulajdonos** lehetőséget.                                 |
    |Válassza ezt:   |Adja meg az **Azure Spring Cloud erőforrás-szolgáltatót**.   |

    Ezután válassza az **Azure Spring Cloud erőforrás-szolgáltató** elemet, majd kattintson a **Mentés** gombra.

    ![Képernyőkép az Azure Spring Cloud erőforrás-szolgáltató kiválasztásáról.](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Ezt a lépést a következő Azure CLI-parancs futtatásával is elvégezheti:

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-an-azure-spring-cloud-instance"></a>Azure Spring Cloud-példány üzembe helyezése

Azure Spring Cloud-példány üzembe helyezése a virtuális hálózaton:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

1. A felső keresőmezőbe keressen az **Azure Spring Cloud** kifejezésre. Válassza az **Azure Spring Cloud** lehetőséget az eredményből.

1. Az **Azure Spring Cloud** oldalon válassza a **+ Hozzáadás** lehetőséget.

1. Töltse ki az űrlapot az Azure Spring Cloud **create** oldalon.

1. Válassza ki ugyanazt az erőforráscsoportot és régiót, mint a virtuális hálózatot.

1. A  **szolgáltatás részletei** területen válassza az **Azure-Spring-Cloud-vnet** lehetőséget.

1. Válassza a **hálózatkezelés** fület, és válassza ki a következő értékeket:

    |Beállítás                                |Érték                                             |
    |---------------------------------------|--------------------------------------------------|
    |Üzembe helyezés saját virtuális hálózaton     |Válassza az **Igen** lehetőséget.                                   |
    |Virtuális hálózat                        |Válassza az **Azure-Spring-Cloud-vnet** lehetőséget.               |
    |Szolgáltatás futásidejű alhálózata                 |Válassza ki a **Service-futtatókörnyezet-alhálózat** elemet.                |
    |Spring boot Service-alhálózati alkalmazások alhálózata   |Válassza az **alkalmazások-alhálózat** elemet.                           |

    ![Képernyőkép, amely a hálózatkezelés lapot mutatja az Azure Spring Cloud Create oldalon.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Válassza **a felülvizsgálat és létrehozás** lehetőséget.

1. Ellenőrizze a specifikációkat, majd válassza a **Létrehozás** lehetőséget.

    ![A specifikációk ellenőrzését bemutató képernyőkép.](./media/spring-cloud-v-net-injection/verify-specifications.png)

Az üzembe helyezést követően két további erőforráscsoport jön létre az előfizetésben az Azure Spring Cloud-példány hálózati erőforrásainak üzemeltetéséhez. Lépjen a **kezdőlapra**, majd válassza ki az **erőforráscsoportok** elemet a felső menüpontban a következő új erőforráscsoportok megkereséséhez.

A (z) **AP-SVC-rt_ {szolgáltatási példány neve} _ {szolgáltatás-példány terület}** nevű erőforráscsoport hálózati erőforrásokat tartalmaz a szolgáltatási futtatókörnyezethez.

  ![A szolgáltatás futtatókörnyezetét bemutató képernyőkép.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Az **AP-App_ {szolgáltatási példány neve} _ {Service instance region}** nevű erőforráscsoport hálózati erőforrásokat tartalmaz a szolgáltatás példányának Spring boot Service-alkalmazásaihoz.

  ![Képernyőkép, amely megjeleníti az alkalmazások erőforráscsoportot.](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Ezek a hálózati erőforrások az előző képen létrehozott virtuális hálózathoz csatlakoznak.

  ![A csatlakoztatott eszközökkel rendelkező virtuális hálózatot bemutató képernyőkép.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Az erőforráscsoportok teljes mértékben az Azure Spring Cloud szolgáltatással kezelhetők. Ne *töröljön manuálisan semmilyen* erőforrást, vagy ne módosítsa a-t.

## <a name="using-smaller-subnet-ranges"></a>Kisebb alhálózati tartományok használata

Ez a táblázat az Azure Spring Cloud által támogatott, kisebb alhálózati tartományokat használó alkalmazás-példányok maximális számát jeleníti meg.

| Alkalmazás-alhálózat CIDR | Összes IP-cím | Elérhető IP-címek | Alkalmazás-példányok maximális száma                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> Alkalmazás 1 maggal: 96 <br/> Alkalmazás 2 maggal: 48<br/>  Alkalmazás 3 maggal: 32 <br/> Alkalmazás 4 maggal: 24 </p> |
| /27             | 32        | 24            | <p> Alkalmazás 1 maggal: 228<br/> Alkalmazás 2 maggal: 144<br/>  Alkalmazás 3 maggal: 96 <br/>  Alkalmazás 4 maggal: 72</p> |
| /26             | 64        | 56            | <p> Alkalmazás 1 maggal: 500<br/> Alkalmazás 2 maggal: 336<br/>  Alkalmazás 3 maggal: 224<br/>  Alkalmazás 4 maggal: 168</p> |
| /25             | 128       | 120           | <p> Alkalmazás 1 maggal: 500<br> Alkalmazás 2 maggal: 500<br>  Alkalmazás 3 maggal: 480<br>  Alkalmazás 4 maggal: 360</p> |
| /24             | 256       | 248           | <p> Alkalmazás 1 maggal: 500<br/> Alkalmazás 2 maggal: 500<br/>  Alkalmazás 3 maggal: 500<br/>  Alkalmazás 4 maggal: 500</p> |

Az alhálózatok esetében öt IP-cím van fenntartva az Azure-ban, és legalább négy cím szükséges az Azure Spring Cloud számára. Legalább kilenc IP-címet kötelező megadni, így a/29 és/30 nem működik.

A szolgáltatás futásidejű alhálózata esetében a minimális méret/28. Ez a méret nem befolyásolja az alkalmazások példányainak számát.

## <a name="bring-your-own-route-table"></a>Saját útválasztási táblázat használata

Az Azure Spring Cloud támogatja a meglévő alhálózatok és útválasztási táblázatok használatát.

Ha az egyéni alhálózatok nem tartalmaznak útválasztási táblákat, az Azure Spring Cloud létrehozza őket az egyes alhálózatokhoz, és a példányok életciklusa során szabályokkal bővíti őket. Ha az egyéni alhálózatok útválasztási táblákat tartalmaznak, az Azure Spring Cloud elismeri a meglévő útválasztási táblázatokat a példányok műveletei során, és ennek megfelelően hozzáadja a/Updates és/vagy szabályokat a műveletekhez.

> [!Warning] 
> Az egyéni szabályok hozzáadhatók az egyéni útválasztási táblázatokhoz és frissíthetők. A szabályokat azonban az Azure Spring Cloud adja hozzá, és ezeket nem kell frissíteni vagy eltávolítani. A 0.0.0.0/0 szabályoknak mindig léteznie kell egy adott útválasztási táblában, és le kell képeznie az internetes átjáró céljára, például egy NVA vagy más kimenő átjáróra. A szabályok frissítésekor körültekintően járjon el, ha csak az egyéni szabályokat módosítják.


### <a name="route-table-requirements"></a>Útválasztási táblázatra vonatkozó követelmények

Az egyéni vnet társított útválasztási tábláknak meg kell felelniük az alábbi követelményeknek:

* Az Azure Route-táblákat csak akkor társíthatja a vnet, ha új Azure Spring Cloud Service-példányt hoz létre. Az Azure Spring Cloud létrehozása után nem lehet másik útválasztási táblázatot használni.
* A Service Runtime alhálózatának és a szolgáltatási futtatókörnyezet alhálózatának is társítva kell lennie különböző útválasztási táblázatokkal vagy ezek egyikével sem.
* A példány létrehozása előtt engedélyeket kell rendelni. Ügyeljen arra, hogy az Azure *Spring Cloud tulajdonosi* engedélyeit az útválasztási táblázatokhoz adja.
* A társított útválasztási tábla erőforrása nem frissíthető a fürt létrehozása után. Amíg az útválasztási tábla erőforrása nem frissíthető, egyéni szabályok módosíthatók az útválasztási táblázatban.
* A lehetséges ütköző útválasztási szabályok miatt nem lehet felhasználni több példányú útválasztási táblázatot.

## <a name="next-steps"></a>Következő lépések

[Alkalmazás üzembe helyezése az Azure Spring Cloud-ban a VNet](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Lásd még

- [Az Azure Spring Cloud hibaelhárítása a VNET-ben](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Az Azure Spring Cloud VNET való futtatásának felhasználói feladatai](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
