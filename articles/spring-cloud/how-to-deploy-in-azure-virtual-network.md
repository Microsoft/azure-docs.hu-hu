---
title: Virtuális Azure Spring Cloud üzembe helyezése
description: Virtuális Azure Spring Cloud üzembe helyezése (VNet-injektálás).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b0ea5728618c7b69403fcc4c0a3575b70fac6038
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482602"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>Virtuális Azure Spring Cloud üzembe helyezése

**Ez a cikk a következőkre vonatkozik:** ââ"â β Java ââ" megadott C #

Ez az oktatóanyag bemutatja, hogyan helyezhet Azure Spring Cloud egy virtuális hálózatban lévő virtuális példányt. Ezt az üzembe helyezést néha VNet-injektálásnak is nevezik.

Az üzembe helyezés a következőt teszi lehetővé:

* A Azure Spring Cloud alkalmazások és a szolgáltatás-futásidő elkülönítése az internettől a vállalati hálózaton).
* Azure Spring Cloud a helyszíni adatközpontokban vagy más virtuális hálózatokon lévő Azure-szolgáltatásokban lévő rendszerekkel.
* Lehetővé teszi az ügyfelek számára a bejövő és kimenő hálózati kommunikáció szabályozását a Azure Spring Cloud.

> [!Note]
> Az Azure-beli virtuális hálózatot csak akkor választhatja ki, ha új virtuális Azure Spring Cloud hoz létre. A virtuális hálózat létrehozása után nem Azure Spring Cloud másik virtuális hálózat használatára.

## <a name="prerequisites"></a>Előfeltételek

Regisztrálja Azure Spring Cloud **Microsoft.AppPlatform** és **Microsoft.ContainerService** erőforrás-szolgáltatót az Erőforrás-szolgáltató regisztrálása az Azure Portal-ban vagy a következő Azure [CLI-parancs](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) futtatásával:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>A virtuális hálózat követelményei

Annak a virtuális hálózatnak, amelyre üzembe Azure Spring Cloud a virtuális hálózatot, meg kell felelnie a következő követelményeknek:

* **Hely:** A virtuális hálózatnak ugyanazon a helyen kell lennie, mint a Azure Spring Cloud példánynak.
* **Előfizetés:** A virtuális hálózatnak ugyanabban az előfizetésben kell lennie, mint a Azure Spring Cloud példánynak.
* **Alhálózatok:** A virtuális hálózatnak két alhálózatot kell tartalmaznia, amelyek egy Azure Spring Cloud számára:

    * Egy a szolgáltatás-futtatáshoz.
    * Egyet a Spring Boot mikroszolgáltatás-alkalmazásokhoz.
    * Ezek az alhálózatok egy-az-egyhez kapcsolatban vannak egy Azure Spring Cloud között. Minden üzembe helyezett szolgáltatáspéldányhoz használjon új alhálózatot. Minden alhálózat csak egy szolgáltatáspéldányt tartalmazhat.
* **Címtér:** A CIDR *akár /28-at* is letilt mind a szolgáltatás-, mind a Spring Boot mikroszolgáltatás-alkalmazások alhálózatán.
* **Útválasztási táblázat:** Alapértelmezés szerint az alhálózatokhoz nincs szükség meglévő útvonaltáblákra. Használhatja [a saját útválasztási táblázatát.](#bring-your-own-route-table)

Az alábbi eljárások a virtuális hálózat beállítását ismertetik úgy, hogy az tartalmazza a virtuális Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Ha már rendelkezik virtuális hálózattal egy virtuális Azure Spring Cloud, hagyja ki az 1., 2. és 3. lépést. A 4. lépéstől kezdheti, hogy előkészítse az alhálózatokat a virtuális hálózathoz.

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. A Azure Marketplace válassza **a Hálózat virtuális**  >  **hálózat lehetőséget.**

1. A **Virtuális hálózat létrehozása párbeszédpanelen** adja meg vagy válassza ki a következő adatokat:

    |Beállítás          |Érték                                             |
    |-----------------|--------------------------------------------------|
    |Előfizetés     |Válassza ki előfizetését.                         |
    |Erőforráscsoport   |Válassza ki az erőforráscsoportot, vagy hozzon létre egy újat.  |
    |Name             |Írja be **az azure-spring-cloud-vnet szolgáltatásokat.**                 |
    |Hely         |Válassza az **USA keleti régiója** lehetőséget.                               |

1. Válassza **a Tovább: IP-címek lehetőséget.**

1. Az IPv4-címtérnek adja meg a **következőt: 10.1.0.0/16.**

1. Válassza **az Alhálózat hozzáadása lehetőséget.** Ezután adja meg **a service-runtime-subnet** nevet az Alhálózat neveként, és adja meg a **10.1.0.0/28** címet az Alhálózat **címtartományaként.**  Ezután válassza a **Hozzáadás** elemet.

1. Válassza **ismét az Alhálózat hozzáadása** lehetőséget, majd adja meg az **Alhálózat neve és** az **Alhálózat címtartománya nevet.** Írja be például a **következőt: apps-subnet és** **10.1.1.0/28.** Ezután válassza a **Hozzáadás** elemet.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A többit hagyja az alapértelmezett értéken, és válassza a **Létrehozás lehetőséget.**

## <a name="grant-service-permission-to-the-virtual-network"></a>Szolgáltatásengedély megadása a virtuális hálózathoz
Azure Spring Cloud **tulajdonosi engedélyre** van szüksége a virtuális hálózathoz, hogy dedikált és dinamikus szolgáltatásnévként adjon a virtuális hálózaton további üzembe helyezéshez és karbantartáshoz.

Válassza ki a korábban **létrehozott azure-spring-cloud-vnet** virtuális hálózatot.

1. Válassza **a Hozzáférés-vezérlés (IAM)** lehetőséget, majd a **Szerepkör-hozzárendelés**  >  **hozzáadása lehetőséget.**

    ![Képernyőkép a Hozzáférés-vezérlés képernyőről.](./media/spring-cloud-v-net-injection/access-control.png)

1. A **Szerepkör-hozzárendelés hozzáadása** párbeszédpanelen adja meg vagy válassza ki a következő adatokat:

    |Beállítás  |Érték                                             |
    |---------|--------------------------------------------------|
    |Szerepkör     |Válassza a **Tulajdonos lehetőséget.**                                 |
    |Válassza ezt:   |Adja **Azure Spring Cloud erőforrás-szolgáltatót.**   |

    Ezután válassza **Azure Spring Cloud erőforrás-szolgáltatót,** majd kattintson a **Mentés gombra.**

    ![Az erőforrás-szolgáltató kiválasztását Azure Spring Cloud képernyőkép.](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Ezt a lépést a következő Azure CLI-parancs futtatásával is használhatja:

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

## <a name="deploy-an-azure-spring-cloud-instance"></a>Új Azure Spring Cloud üzembe helyezése

Új virtuális Azure Spring Cloud üzembe helyezése a virtuális hálózaton:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

1. A felső keresőmezőben keressen rá a **következőre: Azure Spring Cloud.** Válassza **Azure Spring Cloud** lehetőséget az eredményből.

1. A **Azure Spring Cloud** válassza a **+ Hozzáadás lehetőséget.**

1. Töltse ki az űrlapot a Azure Spring Cloud **lapon.**

1. Válassza ki ugyanazt az erőforráscsoportot és régiót, mint a virtuális hálózat.

1. A **Szolgáltatás részletei** alatt a Név **mezőben** válassza az **azure-spring-cloud-vnet lehetőséget.**

1. Válassza a **Hálózat lapfület,** és válassza ki a következő értékeket:

    |Beállítás                                |Érték                                             |
    |---------------------------------------|--------------------------------------------------|
    |Üzembe helyezés saját virtuális hálózaton     |Válassza az **Igen** lehetőséget.                                   |
    |Virtuális hálózat                        |Válassza **az azure-spring-cloud-vnet lehetőséget.**               |
    |Szolgáltatás-futásidejű alhálózat                 |Válassza **a service-runtime-subnet lehetőséget.**                |
    |Spring Boot mikroszolgáltatás-alkalmazások alhálózatának használata   |Válassza **az apps-subnet lehetőséget.**                           |

    ![A Létrehozás lap Hálózat Azure Spring Cloud képernyőképe.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Válassza az Áttekintés lehetőséget, és hozza létre a **et.**

1. Ellenőrizze a specifikációkat, majd válassza a **Létrehozás lehetőséget.**

    ![A specifikációk ellenőrzését bemutató képernyőkép.](./media/spring-cloud-v-net-injection/verify-specifications.png)

Az üzembe helyezés után két további erőforráscsoport jön létre az előfizetésben a példány hálózati erőforrásainak Azure Spring Cloud számára. A Kezdőlap **menüben** válassza az **Erőforráscsoportok** elemet a felső menüelemek közül, és keresse meg a következő új erőforráscsoportokat.

Az **ap-svc-rt_{szolgáltatáspéldány neve}_{szolgáltatáspéldány régiója}** nevű erőforráscsoport a szolgáltatáspéldány szolgáltatás-futásidejéhez szükséges hálózati erőforrásokat tartalmazza.

  ![A szolgáltatás-futtatás képernyőképe.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Az **ap-app_{szolgáltatáspéldány neve}_{szolgáltatáspéldány régiója}** nevű erőforráscsoport hálózati erőforrásokat tartalmaz a szolgáltatáspéldány Spring Boot mikroszolgáltatás-alkalmazásához.

  ![Az alkalmazások erőforráscsoportját bemutató képernyőkép.](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Ezek a hálózati erőforrások az előző képen létrehozott virtuális hálózathoz csatlakoznak.

  ![A virtuális hálózatot csatlakoztatott eszközökkel bemutató képernyőkép.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Az erőforráscsoportokat teljes mértékben az Azure Spring Cloud kezeli. Ne *töröljön* vagy módosítson manuálisan egy erőforrást sem.

## <a name="using-smaller-subnet-ranges"></a>Kisebb alhálózati tartományok használata

Ez a táblázat a kisebb alhálózati tartományok Azure Spring Cloud támogatott alkalmazáspéldányok maximális számát mutatja.

| Alkalmazás alhálózata – CIDR | Ip-k összesen | Elérhető IP-k | Alkalmazáspéldányok maximális száma                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> Alkalmazás 1 maggal: 96 <br/> Alkalmazás 2 maggal: 48<br/>  Alkalmazás 3 maggal: 32 <br/> Alkalmazás 4 maggal: 24 </p> |
| /27             | 32        | 24            | <p> Alkalmazás 1 maggal: 228<br/> Alkalmazás 2 maggal: 144<br/>  Alkalmazás 3 maggal: 96 <br/>  Alkalmazás 4 maggal: 72</p> |
| /26             | 64        | 56            | <p> Alkalmazás 1 maggal: 500<br/> Alkalmazás 2 maggal: 336<br/>  Alkalmazás 3 maggal: 224<br/>  Alkalmazás 4 maggal: 168</p> |
| /25             | 128       | 120           | <p> Alkalmazás 1 maggal: 500<br> Alkalmazás 2 maggal: 500<br>  Alkalmazás 3 maggal: 480<br>  Alkalmazás 4 maggal: 360</p> |
| /24             | 256       | 248           | <p> Alkalmazás 1 maggal: 500<br/> Alkalmazás 2 maggal: 500<br/>  Alkalmazás 3 maggal: 500<br/>  Alkalmazás 4 maggal: 500</p> |

Az alhálózatok esetén az Azure öt IP-címet foglal le, és legalább négy címet igényel a Azure Spring Cloud. Legalább kilenc IP-cím szükséges, ezért a /29 és a /30 nem működik.

A szolgáltatás-futásidejű alhálózatok minimális mérete /28. Ez a méret nincs hatással az alkalmazáspéldányok számára.

## <a name="bring-your-own-route-table"></a>Saját útvonaltábla

Azure Spring Cloud támogatja a meglévő alhálózatok és útvonaltáblák használata.

Ha az egyéni alhálózatok nem tartalmaznak útvonaltáblákat, a Azure Spring Cloud létrehozza őket az egyes alhálózatokhoz, és szabályokat ad hozzájuk a példány életciklusa során. Ha az egyéni alhálózatok útvonaltáblákat tartalmaznak, a Azure Spring Cloud a példányműveletek során nyugtázza a meglévő útvonaltáblákat, és ennek megfelelően hozzáadja/frissíti és/vagy szabályokat a műveletekhez.

> [!Warning] 
> Egyéni szabályok hozzáadhatóak az egyéni útvonaltáblákhoz, és frissíthetők. A szabályok azonban a Azure Spring Cloud nem frissíthetők és nem távolíthatók el. Az olyan szabályoknak, mint a 0.0.0.0/0, mindig léteznie kell egy adott útvonaltáblában, és leképezni kell az internetes átjáró célját, például egy NVA-t vagy egy egyéb bejövő átjárót. Amikor csak az egyéni szabályokat módosítja, körültekintően járjon el a szabályok frissítésekekor.


### <a name="route-table-requirements"></a>Az útvonaltábla követelményei

Az útvonaltábláknak, amelyekhez az egyéni virtuális hálózat társítva van, az alábbi követelményeknek kell megfelelniük:

* Az Azure útválasztási táblázatokat csak akkor társíthatja a virtuális hálózatával, ha új Azure Spring Cloud szolgáltatáspéldányt. Az útvonal létrehozása után nem Azure Spring Cloud másik útvonaltáblát használni.
* A mikroszolgáltatás-alkalmazás alhálózatának és a szolgáltatás-futásidejű alhálózatnak is társítva kell lennie különböző útvonaltáblákhoz, vagy egyikhez sem.
* Az engedélyeket a példány létrehozása előtt hozzá kell rendelni. Győződjön meg arról, hogy az Azure *Spring Cloud tulajdonosi engedélyt ad* az útvonaltáblákhoz.
* A társított útvonaltábla-erőforrás nem frissíthető a fürt létrehozása után. Bár az útvonaltábla erőforrása nem frissíthető, az egyéni szabályok módosíthatók az útvonaltáblán.
* Az útválasztási táblázatokat nem használhatja újra több példányban a potenciálisan ütköző útválasztási szabályok miatt.

## <a name="next-steps"></a>Következő lépések

[Alkalmazás üzembe helyezése Azure Spring Cloud virtuális hálózatban](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Lásd még

- [Hibaelhárítási Azure Spring Cloud virtuális hálózatban](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Az ügyfelek feladatkörei a virtuális Azure Spring Cloud való futtatásért](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
