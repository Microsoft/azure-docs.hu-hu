---
title: A Piactéri vásárlási terv adatainak megkeresése és használata a parancssori felület használatával
description: Ismerje meg, hogyan használhatja az Azure CLI-t a lemezképek urnák és a vásárlási terv paramétereinek, például a kiadó, az ajánlat, az SKU és a verzió megkereséséhez a Piactéri virtuálisgép-lemezképekhez.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 70cb4cc54c6f9a376d3bd38dc8bb6cd3a059a20c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022842"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Azure Marketplace-rendszerkép adatainak megkeresése az Azure CLI használatával

Ez a témakör azt ismerteti, hogyan használhatók az Azure parancssori felületi lemezképek az Azure piactéren. Ezen információk segítségével megadhatja a Piactéri rendszerképet, ha programozott módon hoz létre virtuális gépet a CLI-vel, a Resource Manager-sablonokkal vagy más eszközökkel.

Az elérhető lemezképek és ajánlatok az [Azure Marketplace](https://azuremarketplace.microsoft.com/) vagy a  [Azure PowerShell](../windows/cli-ps-findimage.md)használatával is tallózhatók. 

## <a name="terminology"></a>Terminológia

Az Azure Piactéri rendszerképének jellemzői a következők:

* **Közzétevő**: az a szervezet, amely létrehozta a rendszerképet. Példák: Canonical, MicrosoftWindowsServer
* **Ajánlat**: a közzétevő által létrehozott kapcsolódó rendszerképek csoportjának neve. Példák: UbuntuServer, WindowsServer
* **SKU**: egy ajánlat egy példánya, például egy terjesztés fő kiadása. Példák: 18,04-LTS, 2019-Datacenter
* **Version (verzió**): egy RENDSZERKÉP-SKU verziószáma. 

Ezek az értékek a kettősponttal elválasztott értékek (:). Például: *közzétevő*:*ajánlat*:*SKU*:*Version*. Az URN verziószámát lecserélheti a `latest` lemezkép legújabb verziójának használatára. 

Ha a rendszerkép kiadója további licenceket és vásárlási feltételeket is tartalmaz, akkor a rendszerkép használata előtt el kell fogadnia ezeket.  További információkért tekintse meg [a vásárlási terv információit](#check-the-purchase-plan-information).



## <a name="list-popular-images"></a>Népszerű lemezképek listázása

Az Azure Marketplace-en a népszerű virtuálisgép-rendszerképek listájának megtekintéséhez futtassa az az [VM Image List](/cli/azure/vm/image) parancsot, a `--all` lehetőség nélkül. Például a következő parancs futtatásával jelenítheti meg a népszerű rendszerképek gyorsítótárazott listáját táblázatos formátumban:

```azurecli
az vm image list --output table
```

A kimenet tartalmazza a rendszerkép URN-fájlját. Használhatja a *UrnAlias* is, amely egy rövidített verzió, amely olyan népszerű képekhez készült, mint a *UbuntuLTS*.

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

Ha meg szeretne keresni egy adott virtuálisgép-rendszerképet a piactéren, használja a `az vm image list` parancsot a `--all` kapcsolóval. A parancs ezen verziója hosszabb időt vesz igénybe, és visszaadja a hosszadalmas kimenetet, így általában a listát `--publisher` vagy más paramétert is kiszűri. 

A következő parancs például megjeleníti az összes Debian-ajánlatot (ne feledje, hogy a `--all` kapcsoló nélkül csak a gyakori lemezképek helyi gyorsítótárában keres):

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


## <a name="look-at-all-available-images"></a>Tekintse meg az összes elérhető lemezképet
 
Egy másik lehetőség, hogy megkeresse a lemezképet egy helyen, az az [VM lemezkép-közzétevők](/cli/azure/vm/image), [az az VM lemezkép-ajánlatok](/cli/azure/vm/image)és [az az VM lemezkép-listázási](/cli/azure/vm/image) parancsok futtatásával. Ezekkel a parancsokkal meghatározhatja ezeket az értékeket:

1. Egy hely rendszerkép-közzétevőit sorolja fel. Ebben a példában az *USA nyugati* régióját tekintjük át.
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Listázza egy adott közzétevő ajánlatait. Ebben a példában a *Canonical* -t közzétevőként vesszük fel.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Listázza egy adott ajánlathoz tartozó termékváltozatokat. Ebben a példában a *UbuntuServer* -t ajánlatként vesszük fel.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Egy adott közzétevő, ajánlat és SKU esetében jelenítse meg a rendszerkép összes verzióját. Ebben a példában a *18,04-LTS-* t az SKU-ként vesszük fel.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Ha az az `--image` [VM Create](/cli/azure/vm) paranccsal hoz létre egy virtuális gépet, adja át az urn oszlop értékét a paraméterrel. Az urna verziószámát a "legújabb" értékre cserélve egyszerűen használhatja a lemezkép legújabb verzióját. 

Ha Resource Manager-sablonnal helyez üzembe egy virtuális gépet, a tulajdonságok paramétereit egyenként kell beállítania `imageReference` . Tekintse meg a [sablonreferenciát](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>A vásárlási terv információinak megtekintése

Az Azure Marketplace-en néhány virtuálisgép-rendszerkép további licenccel és vásárlási feltételekkel rendelkezik, amelyeket el kell fogadnia, mielőtt programozott módon telepítené őket.  

Ha egy virtuális gépet egy ilyen rendszerképből szeretne üzembe helyezni, el kell fogadnia a lemezkép első használatakor feltételeit az előfizetés után. Meg kell adnia a *vásárlási terv* paramétereit is a virtuális gép üzembe helyezéséhez a rendszerképből

Ha meg szeretné tekinteni a rendszerkép vásárlási tervének adatait, futtassa az az [VM-rendszerkép megjelenítése](/cli/azure/image) parancsot a rendszerkép urnaával. Ha a `plan` kimenetben szereplő tulajdonság nem `null` , a rendszerképnek a programozott üzembe helyezés előtt el kell fogadnia a feltételeket.

Például a Canonical Ubuntu Server 18,04 LTS-rendszerkép nem rendelkezik további feltételekkel, mert az `plan` információ a következő `null` :

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

A Bitnami-rendszerkép által hitelesített RabbitMQ hasonló parancs futtatása a következő tulajdonságokat jeleníti meg `plan` : `name` , `product` és `publisher` . (Néhány rendszerkép is rendelkezik egy `promotion code` tulajdonsággal.) 

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

A rendszerkép üzembe helyezéséhez el kell fogadnia a feltételeket, és meg kell adnia a vásárlási terv paramétereit, ha az adott lemezkép használatával telepít egy virtuális gépet.

## <a name="accept-the-terms"></a>A feltételek elfogadása

A licencfeltételek megtekintéséhez és elfogadásához használja az az [VM rendszerkép Accept-terms](/cli/azure/vm/image/terms) parancsot. Ha elfogadja a feltételeket, az előfizetésében engedélyezheti a programozott üzembe helyezést. A rendszerképhez csak egyszer kell elfogadnia a feltételeket. Például:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

A kimenet tartalmazza a `licenseTextLink` licencfeltételeket, és azt jelzi, hogy a értéke a következő `accepted` `true` :

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

A képpel kapcsolatos információkat a parancs használatával telepítheti `az vm create` . 

Ha olyan lemezképet szeretne üzembe helyezni, amely nem rendelkezik a csomaggal kapcsolatos információkkal, például a legújabb Ubuntu Server 18,04-lemezképet a Canonical-ből, adja át a következőt `--image` :

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


A vásárlási terv paramétereit tartalmazó rendszerképeknél, például a Bitnami-rendszerkép által hitelesített RabbitMQ esetében adja át az URN-t, `--image` és adja meg a vásárlási terv paramétereit is:

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

Ha megjelenik egy üzenet, amely a rendszerkép feltételeit fogadja el, tekintse át [a feltételek elfogadását](#accept-the-terms)ismertető szakaszt. Győződjön meg arról, hogy a kimenete `az vm image accept-terms` visszaadja azt az értéket, `"accepted": true,` amely azt mutatja, hogy elfogadta a rendszerkép feltételeit.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Meglévő virtuális merevlemez használata a vásárlási terv adataival

Ha meglévő virtuális MEREVLEMEZt használ egy fizetős Azure Marketplace-lemezképpel létrehozott virtuális gépről, előfordulhat, hogy meg kell adnia a vásárlási terv adatait, amikor új virtuális gépet hoz létre a virtuális merevlemezről. 

Ha továbbra is az eredeti virtuális gép vagy egy másik virtuális gép jön létre ugyanazzal a Piactéri képpel, a csomag nevét, közzétevőjét és termékének adatait az [az VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view)paranccsal kérheti le. Ez a példa egy *myVM* nevű virtuális gépet olvas be a *myResourceGroup* -erőforráscsoporthoz, majd megjeleníti a vásárlási terv információit.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Ha nem kapta meg a csomag információit az eredeti virtuális gép törlése előtt, akkor egy [támogatási kérést](https://ms.portal.azure.com/#create/Microsoft.Support)is megadhat. Szükségük lesz a virtuális gép nevére, az előfizetés AZONOSÍTÓJÁRA és a törlési művelet időbélyegzőre.

A csomaggal kapcsolatos információk megadásával létrehozhatja az új virtuális gépet a (z `--attach-os-disk` ) paraméterrel, és megadhatja a VHD-t.

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
Ha gyorsan szeretné létrehozni a virtuális gépet a rendszerkép információi alapján, tekintse meg a Linux rendszerű virtuális [gépek létrehozása és kezelése az Azure CLI-vel](tutorial-manage-vm.md)című témakört.
