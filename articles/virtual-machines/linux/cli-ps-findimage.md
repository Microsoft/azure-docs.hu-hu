---
title: Linuxos virtuálisgép-rendszerképek kiválasztása az Azure CLI-vel
description: Ismerje meg, hogyan határozhatja meg a Piactéri virtuálisgép-rendszerképek közzétevőjét, ajánlatát, SKU-jának és verziószámát az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 0026c70a3a1a6b5e635e6b43e74b557d4218e6d3
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968199"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Linuxos virtuálisgép-lemezképek keresése az Azure Marketplace-en az Azure CLI-vel

Ez a témakör azt ismerteti, hogyan használhatók az Azure parancssori felületi lemezképek az Azure piactéren. Ezen információk segítségével megadhatja a Piactéri rendszerképet, ha programozott módon hoz létre virtuális gépet a CLI-vel, a Resource Manager-sablonokkal vagy más eszközökkel.

Az elérhető lemezképeket és ajánlatokat az [Azure Marketplace](https://azuremarketplace.microsoft.com/) kirakat, a [Azure Portal](https://portal.azure.com)vagy a [Azure PowerShell](../windows/cli-ps-findimage.md)használatával is böngészheti. 

Győződjön meg arról, hogy telepítette a legújabb [Azure CLI](/cli/azure/install-azure-cli) -t, és bejelentkezett egy Azure-fiókba (`az login`).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>Népszerű lemezképek listázása

Futtassa az az [VM Image List](/cli/azure/vm/image) parancsot az `--all` lehetőség nélkül, hogy megtekintse a népszerű virtuálisgép-rendszerképek listáját az Azure Marketplace-en. Például a következő parancs futtatásával jelenítheti meg a népszerű rendszerképek gyorsítótárazott listáját táblázatos formátumban:

```azurecli
az vm image list --output table
```

A kimenet tartalmazza a rendszerkép URN (az *urn* oszlop értékét). Ha virtuális gépet hoz létre ezen népszerű Piactéri rendszerképek valamelyikével, megadhatja a *UrnAlias*, egy rövidített formát, például *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Adott rendszerképek keresése

Ha egy adott virtuálisgép-rendszerképet szeretne keresni a piactéren, használja a `az vm image list` parancsot a `--all` kapcsolóval. A parancs ezen verziója hosszabb időt vesz igénybe, és visszaadja a hosszadalmas kimenetet, így általában `--publisher` vagy egy másik paraméter alapján szűri a listát. 

A következő parancs például megjeleníti az összes Debian-ajánlatot (ne feledje, hogy a `--all` kapcsoló nélkül csak a gyakori lemezképek helyi gyorsítótárában keres):

```azurecli
az vm image list --offer Debian --all --output table 

```

Részleges kimenet: 

```
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

Hasonló szűrőket alkalmazhat a `--location`, `--publisher`és `--sku` beállításokkal. A szűrőkben részleges egyezéseket végezhet, például megkeresheti a `--offer Deb`t az összes Debian-rendszerkép megkereséséhez.

Ha nem ad meg egy adott helyet a `--location` kapcsolóval, a rendszer az alapértelmezett hely értékeit adja vissza. (Egy másik alapértelmezett helyet állítson be `az configure --defaults location=<location>`futtatásával.)

Például a következő parancs felsorolja az összes Debian 8 SKU-t a Nyugat-európai helyen:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Részleges kimenet:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>A képek navigálása
 
Egy másik lehetőség, hogy megkeresse a lemezképet egy helyen, az az [VM lemezkép-közzétevők](/cli/azure/vm/image), [az az VM lemezkép-ajánlatok](/cli/azure/vm/image)és [az az VM lemezkép-listázási](/cli/azure/vm/image) parancsok futtatásával. Ezekkel a parancsokkal meghatározhatja ezeket az értékeket:

1. Listázza a rendszerkép-közzétevőket.
2. Listázza egy adott közzétevő ajánlatait.
3. Listázza egy adott ajánlathoz tartozó termékváltozatokat.

Ezután a kiválasztott SKU esetében kiválaszthatja a telepítendő verziót.

A következő parancs például felsorolja az USA nyugati régiójában található képközzétevőket:

```azurecli
az vm image list-publishers --location westus --output table
```

Részleges kimenet:

```
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

Ezekkel az információkkal megkeresheti az ajánlatokat egy adott közzétevőtől. Az USA nyugati régiójában található *Canonical* Publisher esetében például `azure vm image list-offers`futtatásával kereshet ajánlatokat. Adja át a helyet és a közzétevőt a következő példában látható módon:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Kimenet:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
Láthatja, hogy az USA nyugati régiójában a Canonical közzéteszi a *UbuntuServer* ajánlatot az Azure-ban. De milyen termékváltozatokról van szó? Ezeknek az értékeknek a lekéréséhez futtassa `azure vm image list-skus` és állítsa be a felderített helyet, közzétevőt és ajánlatot:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Kimenet:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

Végül a `az vm image list` parancs használatával keresse meg a kívánt SKU adott verzióját, például *18,04-LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

Részleges kimenet:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

Most kiválaszthatja a használni kívánt rendszerképet úgy, hogy az URN értékét jegyezze fel. Adja át ezt az értéket a `--image` paraméterrel, amikor létrehoz egy virtuális gépet az az [VM Create](/cli/azure/vm) paranccsal. Ne feledje, hogy opcionálisan lecserélheti az URN verziószámát a "legutóbbi" értékre. Ez a verzió mindig a lemezkép legújabb verziója. 

Ha Resource Manager-sablonnal helyez üzembe egy virtuális gépet, akkor a lemezkép paramétereit külön kell beállítania a `imageReference` tulajdonságai között. Tekintse meg a [sablonreferenciát](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Terv tulajdonságainak megtekintése

Ha meg szeretné tekinteni a rendszerkép vásárlási tervének adatait, futtassa az az [VM rendszerkép megjelenítése](/cli/azure/image) parancsot. Ha a kimenetben lévő `plan` tulajdonság nem `null`, akkor a rendszerképnek a programozott telepítés előtt el kell fogadnia a feltételeket.

Például a Canonical Ubuntu Server 18,04 LTS-rendszerkép nem rendelkezik további feltételekkel, mert a `plan` információ `null`:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Kimenet:

```
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

A Bitnami-rendszerkép által hitelesített RabbitMQ hasonló parancs futtatása a következő `plan` tulajdonságokat jeleníti meg: `name`, `product`és `publisher`. (Néhány rendszerképnek `promotion code` tulajdonsága is van.) A rendszerkép üzembe helyezéséhez tekintse át a következő részeket a feltételek elfogadásához és a programozott telepítés engedélyezéséhez.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Kimenet:

```
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

### <a name="accept-the-terms"></a>A feltételek elfogadása

A licencfeltételek megtekintéséhez és elfogadásához használja az az [VM rendszerkép Accept-terms](/cli/azure/vm/image?) parancsot. Ha elfogadja a feltételeket, az előfizetésében engedélyezheti a programozott üzembe helyezést. A rendszerképhez csak egyszer kell elfogadnia a feltételeket. Például:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

A kimenet tartalmaz egy `licenseTextLink` a licencfeltételeket, és azt jelzi, hogy a `accepted` értéke `true`:

```
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

### <a name="deploy-using-purchase-plan-parameters"></a>Üzembe helyezés a vásárlási terv paramétereinek használatával

A rendszerkép feltételeinek elfogadása után üzembe helyezhet egy virtuális gépet az előfizetésben. Ha a lemezképet a `az vm create` parancs használatával szeretné telepíteni, adja meg a vásárlási terv paramétereit a lemezképhez tartozó URN mellett. Ha például egy virtuális gépet szeretne üzembe helyezni a Bitnami-lemezkép által hitelesített RabbitMQ:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

## <a name="next-steps"></a>Következő lépések
Ha gyorsan szeretné létrehozni a virtuális gépet a rendszerkép információi alapján, tekintse meg a Linux rendszerű virtuális [gépek létrehozása és kezelése az Azure CLI-vel](tutorial-manage-vm.md)című témakört.
