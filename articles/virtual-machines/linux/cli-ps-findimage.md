---
title: A Marketplace vásárlási terv információinak megkerese és használata a CLI használatával
description: Megtudhatja, hogyan használhatja az Azure CLI-t a Marketplace-beli virtuálisgép-rendszerképek kép-URL-címének és vásárlási terv paramétereinek (például közzétevő, ajánlat, termékváltozat és verzió) keresésére.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal, devx-track-azurecli
ms.openlocfilehash: be0535a49b47c45cad49abd1bf720b6347a660b8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484200"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Képinformációk Azure Marketplace az Azure CLI használatával

Ez a témakör bemutatja, hogyan használhatja az Azure CLI-t virtuálisgép-rendszerképek keresésre a Azure Marketplace. Ezen információk alapján megadhat egy Marketplace-rendszerképet, amikor programozott módon hoz létre virtuális gépet a CLI-val, Resource Manager sablonokat vagy más eszközöket használ.

Az elérhető rendszerképeket és ajánlatokat a következő [](../windows/cli-ps-findimage.md)Azure Marketplace vagy [Azure PowerShell.](https://azuremarketplace.microsoft.com/) 

## <a name="terminology"></a>Terminológia

Az Azure Marketplace-beli rendszerkép a következő attribútumokkal rendelkezik:

* **Közzétevő:** A rendszerképet létrehozó szervezet. Példák: Canonical, MicrosoftWindowsServer
* **Ajánlat:** Egy közzétevő által létrehozott kapcsolódó rendszerképek csoportjának neve. Példák: UbuntuServer, WindowsServer
* **Termékváltozat:** Egy ajánlat egy példánya, például egy disztribúció fő kiadása. Példák: 18.04-LTS, 2019-Datacenter
* **Verzió:** Egy rendszerkép-termékváltozat verziószáma. 

Ezek az értékek egyenként vagy *kép-URL-címként* is továbbküldve, a kettősponttal (kettősponttal) elválasztott értékeket kombinálva :). Például: *Közzétevő:**Ajánlat:**Termékváltozat:**Verzió.* A rendszerkép legújabb verziójának használatára lecserélheti az URL-címben a `latest` verziószámot a következőre: . 

Ha a rendszerkép-közzétevő további licenc- és vásárlási feltételeket biztosít, akkor ezeket el kell fogadnia a rendszerkép használata előtt.  További információ: A vásárlási [terv információinak ellenőrzése.](#check-the-purchase-plan-information)



## <a name="list-popular-images"></a>Népszerű rendszerképek listása

Futtassa [az az vm image list](/cli/azure/vm/image) parancsot az kapcsoló nélkül, hogy a népszerű virtuálisgép-rendszerképek listája `--all` megjelenik a Azure Marketplace. Futtassa például a következő parancsot a népszerű képek táblázatos formátumú gyorsítótárazott listájának megjelenítéséhez:

```azurecli
az vm image list --output table
```

A kimenet tartalmazza a kép URL-ját. Használhatja az *UrnAlias* rövid verzióját is, amely népszerű képekhez, például *az UbuntuLTS-hez lett létrehozva.*

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>Adott rendszerképek keresése

Ha egy adott virtuálisgép-rendszerképet keres a Marketplace-en, használja `az vm image list` az parancsot a `--all` kapcsolóval. A parancs ezen verziója némi időt vesz igénybe, és hosszú kimenetet ad vissza, ezért általában a vagy egy másik paraméter alapján `--publisher` szűri a listát. 

A következő parancs például megjeleníti az összes Debian-ajánlatot (ne feledje, hogy a kapcsoló nélkül csak a gyakori rendszerképek helyi gyorsítótárában `--all` keres):

```azurecli
az vm image list --offer Debian --all --output table 
```

Részleges kimenet: 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>Az összes elérhető kép
 
Egy hely lemezképének megkeresére egy másik módszer az [az vm image list-publishers,](/cli/azure/vm/image) [az vm image list-offers](/cli/azure/vm/image)és [az az vm image list-skus](/cli/azure/vm/image) parancsok egymás után való futtatása. Ezekkel a parancsokkal a következő értékeket határozza meg:

1. Listába sorolja egy adott hely rendszerkép-közzétevőit. Ebben a példában az USA nyugati *régióját keressük.*
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Listázza egy adott közzétevő ajánlatait. Ebben a példában a *Canonicalt adjuk hozzá* közzétevőként.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Listázza egy adott ajánlathoz tartozó termékváltozatokat. Ebben a példában az *UbuntuServert adjuk* hozzá ajánlatként.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Egy adott közzétevő, ajánlat és termékváltozat számára a rendszerkép összes verzióját meg kell mutatni. Ebben a példában a *18.04-LTS SKU-t* adjuk hozzá.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Adja át az URN oszlopnak ezt az értékét a paraméterrel, amikor az az vm create paranccsal hoz létre egy virtuális `--image` [gépet.](/cli/azure/vm) Az URL-címben a verziószámot lecserélheti a "latest" (legújabb) szövegre, hogy egyszerűen a rendszerkép legújabb verzióját használja. 

Ha virtuális gépet helyez üzembe egy Resource Manager sablonnal, a rendszerkép paramétereit egyenként kell beállítania a `imageReference` tulajdonságokban. Tekintse meg a [sablonreferenciát](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>A vásárlási terv információinak ellenőrzése

A virtuálisgép-Azure Marketplace további licenccel és vásárlási feltételekkel rendelkezik, amelyek elfogadása szükséges a programozott üzembe helyezésük előtt.  

Egy virtuális gép ilyen rendszerképből való üzembe helyezéséhez el kell fogadnia a rendszerkép feltételeit az első használatkor, előfizetésenként egyszer. Meg kell adnia a vásárlási terv *paramétereit* is a virtuális gép a rendszerképből való üzembe helyezéséhez

A rendszerkép vásárlási tervinformációinak megtekintéséhez futtassa [az az vm image show](/cli/azure/image) parancsot a rendszerkép URL-címével. Ha a kimenet tulajdonsága nem , a rendszerkép olyan kifejezéseket is biztosít, amelyek elfogadása szükséges `plan` `null` a programozott üzembe helyezés előtt.

Például a Canonical Ubuntu Server 18.04 LTS rendszerkép nem tartalmaz további kifejezéseket, mert az `plan` információ `null` a következő:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Kimenet:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

A RabbitMQ Certified by Bitnami képhez hasonló parancs futtatása a következő tulajdonságokat `plan` mutatja: `name` , és `product` `publisher` . (Egyes képek is tartalmaznak `promotion code` tulajdonságot.) 

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Kimenet:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

A rendszerkép üzembe helyezéséhez el kell fogadnia a feltételeket, és meg kell adnia a vásárlási terv paramétereit, amikor virtuális gépet helyez üzembe a rendszerkép használatával.

## <a name="accept-the-terms"></a>A feltételek elfogadása

A licencszerződések megtekintéséhez és elfogadásához használja [az az vm image accept-terms](/cli/azure/vm/image/terms) parancsot. Amikor elfogadja a feltételeket, engedélyezi a programozott üzembe helyezést az előfizetésében. A rendszerképhez előfizetésenként csak egyszer kell feltételeket elfogadnia. Például:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

A kimenet tartalmaz egy értéket a licenc feltételeihez, és azt jelzi, hogy `licenseTextLink` a értéke `accepted` `true` :

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

A feltételek elfogadásához írja be a következőt:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Új virtuális gép üzembe helyezése a rendszerkép paramétereinek használatával

A lemezképpel kapcsolatos információkkal az paranccsal helyezheti `az vm create` üzembe. 

Ha olyan rendszerképet szeretne üzembe helyezni, amely nem rendelkezik tervinformációval, például a Canonical legfrissebb Ubuntu Server 18.04-es rendszerképét, adja meg a URL-ját: `--image`

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


A csomagparaméterekkel rendelkező képek, például a RabbitMQ Certified by Bitnami rendszerkép esetén át kell adnia az URL-eket a számára, és meg kell adnia a vásárlási terv `--image` paramétereit is:

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

Ha üzenetet kap a kép szerződésének elfogadásáról, tekintse át a [Feltételek elfogadása szakaszt.](#accept-the-terms) Győződjön meg arról, hogy a kimenete visszaadja azt az értéket, amely azt mutatja, hogy elfogadta a `az vm image accept-terms` `"accepted": true,` kép feltételeit.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Meglévő VHD használata a vásárlási terv adataival

Ha egy fizetős rendszerkép használatával létrehozott virtuális gépről meglévő virtuális merevlemeze van, előfordulhat, hogy meg kell adnunk Azure Marketplace a vásárlási terv adatait, amikor új virtuális gépet hoz létre abból a VHD-ból. 

Ha továbbra is az eredeti virtuális géppel vagy egy másik virtuális géppel hozta létre ugyanazt a piactéri rendszerképet, a csomag nevét, kiadóját és termékinformációit az [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view)használatával kaphatja meg. Ez a példa lekért egy *myVM* nevű virtuális gépet a *myResourceGroup* erőforráscsoportban, majd megjeleníti a vásárlási terv adatait.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Ha az eredeti virtuális gép törlése előtt nem szerezték be a tervinformációt, nyújthatja be a [támogatási kérést.](https://ms.portal.azure.com/#create/Microsoft.Support) Szükségük lesz a virtuális gép nevére, az előfizetés azonosítójára és a törlési művelet időbélyegre.

Ha már megvan a tervinformációk, létrehozhatja az új virtuális gépet a `--attach-os-disk` paraméterrel a virtuális merevlemez megadásához.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```


## <a name="next-steps"></a>Következő lépések
A virtuális gépek rendszerkép-információkkal való gyors létrehozásához lásd: Linux rendszerű virtuális gépek létrehozása és [kezelése az Azure CLI-vel.](tutorial-manage-vm.md)
