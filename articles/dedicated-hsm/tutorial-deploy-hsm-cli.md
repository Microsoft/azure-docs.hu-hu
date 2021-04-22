---
title: Oktatóanyag – Üzembe helyezés meglévő virtuális hálózatban az Azure CLI használatával – Azure Dedicated HSM | Microsoft Docs
description: Oktatóanyag, amely bemutatja, hogyan helyezhet üzembe dedikált HSM-et a CLI használatával egy meglévő virtuális hálózaton
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: b845ecabe74040e154886476a8ba28efecc99325
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868860"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-the-azure-cli"></a>Oktatóanyag: HSM-gépek üzembe helyezése meglévő virtuális hálózatban az Azure CLI használatával

Azure Dedicated HSM fizikai eszközt biztosít kizárólag az ügyfelek számára, teljes körű felügyeleti ellenőrzéssel és teljes felügyeleti felelősséggel. A fizikai eszközök használatával a Microsoftnak szabályozni kell az eszközfoglalást a kapacitás hatékony kezelése érdekében. Ennek eredményeképpen az Azure-előfizetésen belül az Dedicated HSM szolgáltatás általában nem lesz látható az erőforrások kiépítésekor. A Dedicated HSM szolgáltatáshoz hozzáférést igénylő Azure Microsoft-fiók-ügyfélnek először kapcsolatba kell lépnie Microsoft-fiók vezetővel, hogy regisztrációt igényelje a Dedicated HSM szolgáltatáshoz. A kiépítés csak akkor lehetséges, ha a folyamat sikeresen befejeződött. 

Ez az oktatóanyag egy tipikus kiépítési folyamatot mutat be, amelyben:

- Az ügyfél már rendelkezik virtuális hálózattal
- Virtuális gépük van
- HSM-erőforrásokat kell hozzáadnia a meglévő környezethez.

Egy tipikus, magas rendelkezésre állású, többrétű üzembe helyezési architektúra a következőképpen néz ki:

![többrépontos üzembe helyezés](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Ez az oktatóanyag két HSM-et és egy szükséges ExpressRoute-átjárót (lásd a fenti 1. alhálózatot) egy meglévő virtuális hálózatba integrálva van (lásd a fenti VNET 1-et).  Minden más erőforrás standard Azure-erőforrás. Ugyanez az integrációs folyamat használható a fenti 3. virtuális hálózat 4. alhálózatában található HSM-hez is.

## <a name="prerequisites"></a>Előfeltételek

Azure Dedicated HSM jelenleg nem érhető el a Azure Portal. A szolgáltatással való összes interakció parancssorból vagy a PowerShell használatával fog lekért műveleteket. Ez az oktatóanyag a parancssori felületet fogja használni a Azure Cloud Shell. Ha még nem használja az Azure CLI-t, kövesse az első lépésekre vonatkozó utasításokat itt: [Azure CLI 2.0 – Első lépések.](/cli/azure/get-started-with-azure-cli)

Előfeltételek:

- Befejezte a regisztrációs Azure Dedicated HSM folyamatot
- Ön a szolgáltatás használatára lett jóváhagyva. Ha nem, a részletekért forduljon Microsoft-fiók képviselőjéhez.
- Létrehozott egy erőforráscsoportot ezekhez az erőforrásokhoz, és az oktatóanyagban üzembe helyezett újak is csatlakoznak ehhez a csoporthoz.
- Már létrehozta a szükséges virtuális hálózatot, alhálózatot és virtuális gépeket a fenti ábrán látható módon, és most 2 HSM-et szeretne integrálni ebbe az üzembe helyezésbe.

Az alábbi utasítások feltételezik, hogy már navigált a Azure Portal, és megnyitotta a Cloud Shell (válassza a " " lehetőséget a portál jobb felső \> \_ részen).

## <a name="provisioning-a-dedicated-hsm"></a>Üzembe Dedicated HSM

A HSM-ek kiépítése és meglévő virtuális hálózatba expressroute-átjárón keresztüli integrálása ssh használatával lesz érvényesítve. Ez az ellenőrzés segít biztosítani a HSM-eszköz elérhetőségét és alapszintű rendelkezésre állását a további konfigurációs tevékenységekhez.

### <a name="validating-feature-registration"></a>Szolgáltatásregisztráció érvényessége

Ahogy korábban említettük, minden kiépítési tevékenységhez Dedicated HSM szolgáltatásnak regisztrálva kell az előfizetéséhez. Ennek ellenőrzéshez futtassa a következő parancsokat a Azure Portal Cloud Shell.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

A parancsoknak a "Registered" (Regisztrált) állapotot kell visszaadni (az alább látható módon). Ha a parancsok nem a "Regisztrált" nevére térnek vissza, regisztrálnia kell erre a szolgáltatásra, ha kapcsolatba lép a Microsoft-fiók képviselőjével.

![előfizetés állapota](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM-erőforrások létrehozása

A HSM-erőforrások létrehozása előtt szükség van néhány előfeltételként szükséges erőforrásra. A számításhoz, HSM-hez és átjáróhoz alhálózati tartományokkal kell virtuális hálózattal lennie. Az alábbi parancsok példaként szolgálnak arra, hogy mi hoz létre ilyen virtuális hálózatot.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16 \
  --subnet-name compute \
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>A virtuális hálózat legfontosabb konfigurációja az, hogy a HSM-eszköz alhálózatának a "Microsoft.HardwareSecurityModules/dedicatedHSMs" delegálásokkal kell lennie.  A HSM-kiépítés nem működik a beállítás beállítása nélkül.

A hálózat konfigurálása után ezekkel az Azure CLI-parancsokkal kiépíti a HSM-eket.

1. Az [az dedicated-hsm create paranccsal](/cli/azure/dedicated-hsm#az_dedicated_hsm_create) hozza létre az első HSM-et. A HSM neve hsm1. Helyettesítse be előfizetését:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm1 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

   Ennek az üzembe helyezésnek körülbelül 25–30 percet kell igénybe vennie, és az idő nagy részét a HSM-eszközök jelentik.

1. Az aktuális HSM-et az [az dedicated-hsm show](/cli/azure/dedicated-hsm#az_dedicated_hsm_show) paranccsal láthatja:

   ```azurecli
   az dedicated-hsm show --resource group myRG --name hsm1
   ```

1. A második HSM kiépítése a következő paranccsal:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm2 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

1. Futtassa [az az dedicated-hsm list parancsot](/cli/azure/dedicated-hsm#az_dedicated_hsm_list) az aktuális HSM-ekkel kapcsolatos részletek megtekintéséhez:

   ```azurecli
   az dedicated-hsm list --resource-group myRG
   ```

Vannak más parancsok is, amelyek hasznosak lehetnek. A HSM frissítéséhez használja az [az dedicated-hsm update](/cli/azure/dedicated-hsm#az_dedicated_hsm_update) parancsot:

```azurecli
az dedicated-hsm update --resource-group myRG –name hsm1
```

HSM törléséhez használja [az az dedicated-hsm delete](/cli/azure/dedicated-hsm#az_dedicated_hsm_delete) parancsot:

```azurecli
az dedicated-hsm delete --resource-group myRG –name hsm1
```

## <a name="verifying-the-deployment"></a>Az üzemelő példány ellenőrzése

Az eszközök kiépítésének ellenőrzéséhez és az eszközattribútumok ellenőrzéséhez futtassa a következő parancskészletet. Győződjön meg arról, hogy az erőforráscsoport megfelelően van beállítva, és az erőforrás neve pontosan meg van adva a paraméterfájlban.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

A kimenet az alábbihoz hasonló:

```json
{
    "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSMl",
    "identity": null,
    "kind": null,
    "location": "westus",
    "managedBy": null,
    "name": "HSM1",
    "plan": null,
    "properties": {
        "networkProfile": {
            "networkInterfaces": [
            {
            "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/networkInterfaces/HSMl_HSMnic", "privatelpAddress": "10.0.2.5",
            "resourceGroup": "HSM-RG"
            }
            L
            "subnet": {
                "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/virtualNetworks/demo-vnet/subnets/hsmsubnet", "resourceGroup": "HSM-RG"
            }
        },
        "provisioningState": "Succeeded",
        "stampld": "stampl",
        "statusMessage": "The Dedicated HSM device is provisioned successfully and ready to use."
    },
    "resourceGroup": "HSM-RG",
    "sku": {
        "capacity": null,
        "family": null,
        "model": null,
        "name": "SafeNet Luna Network HSM A790",
        "size": null,
        "tier": null
    },
    "tags": {
        "Environment": "prod",
        "resourceType": "Hsm"
    },
    "type": "Microsoft.HardwareSecurityModules/dedicatedHSMs"
}
```

Most már az Azure Erőforrás-kezelővel is láthatja az [erőforrásokat.](https://resources.azure.com/)   Az Explorerben bontsa ki az "előfizetések" elemet a bal oldalon, bontsa ki az Dedicated HSM-előfizetését, bontsa ki az "erőforráscsoportok" elemet, bontsa ki a használt erőforráscsoportot, és végül válassza ki az "erőforrások" elemet.

## <a name="testing-the-deployment"></a>Az üzembe helyezés tesztelése

Az üzembe helyezés teszteléséhez olyan virtuális géphez kell csatlakozni, amely hozzáfér a HSM(ök)hez, majd közvetlenül a HSM-eszközhöz csatlakozik. Ezek a műveletek megerősítik, hogy a HSM elérhető.
Az ssh eszközzel csatlakozhat a virtuális géphez. A parancs az alábbihoz lesz hasonló, de a paraméterben megadott rendszergazdai névvel és DNS-névvel.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

A fenti parancsban a virtuális gép IP-címe is használható a DNS-név helyére. Ha a parancs sikeres, a rendszer jelszót kér, és meg kell adnia azt. Miután bejelentkezett a virtuális gépre, bejelentkezhet a HSM-be a HSM-hez társított hálózati adapter erőforrás portálján található magánhálózati IP-címmel.

![összetevők listája](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Figyelje meg a "Rejtett típusok megjelenítése" jelölőnégyzetet, amely ha be van jelölve, megjeleníti a HSM-erőforrásokat.

A fenti képernyőképen a "HSM1_HSMnic" vagy a "HSM2_HSMnic" elemre kattintva a megfelelő magánhálózati IP-cím jelenik meg. Ellenkező esetben `az resource show` a fent használt parancs a megfelelő IP-cím azonosítására használható. 

Ha a megfelelő IP-címmel van meg, futtassa a következő parancsot, és helyettesítse be ezt a címet:

`ssh tenantadmin@10.0.2.4`

Ha a művelet sikeres, a rendszer kérni fogja a jelszót. Az alapértelmezett jelszó a PASSWORD , és a HSM először megkéri, hogy változtassa meg a jelszavát, ezért állítson be erős jelszót, és használja azt a mechanizmust, amely a szervezet számára a jelszó tárolására és a veszteség megelőzésére előnyben részesíti.

>[!IMPORTANT]
>Ha elveszíti ezt a jelszót, a HSM-et alaphelyzetbe kell állítani, ami a kulcsok elvesztését jelenti.

Ha ssh-kapcsolaton keresztül csatlakozik a HSM-hez, futtassa a következő parancsot annak biztosításához, hogy a HSM működőképes legyen.

`hsm show`

A kimenetnek az alábbi képen látható módon kell kinéznie:

![A PowerShell ablakának kimenetét bemutató képernyőkép.](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Ezen a ponton minden erőforrást lefoglalt egy magas rendelkezésre állású, két HSM-telepítéshez, valamint ellenőrzött hozzáféréshez és működési állapothoz. Minden további konfiguráció vagy tesztelés magában foglalja a HSM-eszközzel végzett további munkát. Ehhez kövesse a Thales Luna 7 HSM felügyeleti útmutató 7. fejezetében található utasításokat a HSM inicializálása és partíciók létrehozása érdekében. Miután regisztrált a Thales ügyfélszolgálati portálján, és már van ügyfél-azonosítója, az összes dokumentáció és szoftver letölthető közvetlenül a [Thalesből.](https://supportportal.thalesgroup.com/csm) Töltse le az ügyfélszoftver 7.2-es verzióját az összes szükséges összetevő letöltéséhez.

## <a name="delete-or-clean-up-resources"></a>Erőforrások törlése vagy törlése

Ha csak a HSM-eszközt használja, akkor az erőforrásként törölhető, és vissza lehet térni az ingyenes készlethez. Ez nyilvánvalóan az eszközön található bizalmas ügyféladatokra vonatkozik. Az eszközök "nulláztatásának" legjobb módja, ha a HSM rendszergazdai jelszavát 3 alkalommal helytelenül kapják meg (megjegyzés: ez nem berendezés-rendszergazda, hanem tényleges HSM-rendszergazda). A kulcsanyagokat védő biztonsági intézkedésként az eszköz nem törölhető Azure-erőforrásként, amíg a rendszer nullázott állapotba nem kerül.

> [!NOTE]
> Ha bármilyen Thales-eszközkonfigurációval kapcsolatos problémája van, lépjen kapcsolatba a [Thales ügyfélszolgálatával.](https://supportportal.thalesgroup.com/csm)

Ha az erőforráscsoport összes erőforrásával végzett, az alábbi paranccsal távolíthatja el őket:

```azurecli
az group delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose
```

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagban található lépések elvégzése után a Dedicated HSM kiépíti az erőforrásokat, és rendelkezik egy virtuális hálózattal, amely rendelkezik a HSM-fel való kommunikációhoz szükséges HSM-ekkel és további hálózati összetevőkkel.  Most már abban a helyzetben van, hogy ezt az üzembe helyezést az előnyben részesített üzembe helyezési architektúra által megkövetelt több erőforrással egészbe tudja adni. Az üzembe helyezés tervezésének segítéséről az Alapfogalmak dokumentumban található további információ.
A rendszer két HSM-et ajánl egy elsődleges régióban, amelyek az állvány szintjén biztosítják a rendelkezésre állást, és két HSM-et egy másodlagos régióban, amelyek a regionális rendelkezésre állást célják meg. 

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Figyelés](monitoring.md)
