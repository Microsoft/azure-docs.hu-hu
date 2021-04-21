---
title: Tűzfal-hozzáférési szabályok
description: Úgy konfigurálhatja a szabályokat, hogy tűzfal mögül férnek hozzá az Azure Container Registryhez, REST API és adatvégpont tartománynevéhez vagy szolgáltatásspecifikus IP-címtartományához való hozzáférés engedélyezésével.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 6aea4415468eb21e8d010b74597fc68e4ebf573f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783934"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Szabályok konfigurálása tűzfal mögötti Azure Container Registry eléréséhez

Ez a cikk azt ismerteti, hogyan konfigurálhatja a tűzfalon a szabályokat az Azure Container Registryhez való hozzáférés engedélyezése érdekében. Előfordulhat például, hogy Azure IoT Edge tűzfal vagy proxykiszolgáló mögötti tárolóeszköznek hozzá kell férni egy tárolójegyzékhez egy tároló rendszerképének lekért eléréséhez. Vagy egy helyszíni hálózat zárolt kiszolgálójának hozzáférésre lehet szüksége a rendszerkép leküldéséhez.

Ha ehelyett egy tároló-beállításjegyzék bejövő hálózati hozzáférését csak egy Azure-beli virtuális hálózaton belül szeretné konfigurálni, tekintse meg a következőt: Configure Azure Private Link for an Azure container registry (Tároló-adatbázis konfigurálása [Azure-beli tároló-beállításjegyzékhez).](container-registry-private-link.md)

## <a name="about-registry-endpoints"></a>A beállításjegyzék-végpontok

Ahhoz, hogy rendszerképeket vagy más összetevőkkel kapcsolatban lekért vagy lekért egy Azure-beli tároló-beállításjegyzéket, az ügyfélnek, például a Docker-démonnak KÉT különböző végponttal kell https-kapcsolaton keresztül interakcióba lépnie. A tűzfal mögötti beállításjegyzékhez hozzáférő ügyfelek esetében mindkét végponthoz konfigurálnia kell a hozzáférési szabályokat. Mindkét végpont a 443-as porton keresztül érhető el.

* **Beállításjegyzékbeli REST API végpont** – A hitelesítési és beállításjegyzék-kezelési műveleteket a beállításjegyzék nyilvános REST API kezeli. Ez a végpont a regisztrációs adatbázis bejelentkezési kiszolgálójának neve. Például: `myregistry.azurecr.io`

* **Tárolási (adat-) végpont** – Az [Azure](container-registry-storage.md) blobtárolót foglal le az Azure Storage-fiókokban az egyes tárolójegyzékek nevében a tároló-rendszerképek és egyéb összetevők adatainak kezeléséhez. Amikor egy ügyfél hozzáfér egy Azure Container Registry rendszerképrétegéhez, a regisztrációs adatbázis által biztosított tárfiókvégpont használatával kéréseket tesz elérhetővé.

Ha a [](container-registry-geo-replication.md)beállításjegyzék georeplikált , előfordulhat, hogy az ügyfélnek egy adott régióban vagy több replikált régióban kell interakcióba lépnie az adatvégponttal.

## <a name="allow-access-to-rest-and-data-endpoints"></a>REST- és adatvégpont-hozzáférés engedélyezése

* **REST-végpont** – Hozzáférés engedélyezése a regisztrációs adatbázis teljes bejelentkezési kiszolgálójának nevéhez vagy egy társított `<registry-name>.azurecr.io` IP-címtartományhoz
* **Tárolási (adat-) végpont** – Engedélyezi a hozzáférést az összes Azure Blob Storage-fiókhoz a helyettesítő karakter vagy `*.blob.core.windows.net` egy társított IP-címtartomány használatával.
> [!NOTE]
> Azure Container Registry bevezeti a [](#enable-dedicated-data-endpoints)dedikált adatvégpontokat, amelyek lehetővé teszik a beállításjegyzék-tároló ügyféloldali tűzfalszabályainak szigorú hatókörét. Az űrlap használatával engedélyezheti az adatvégpontokat minden olyan régióban, ahol a beállításjegyzék található vagy replikálva `<registry-name>.<region>.data.azurecr.io` van.

## <a name="allow-access-by-ip-address-range"></a>Hozzáférés engedélyezése IP-címtartomány alapján

Ha a szervezete szabályzatokkal rendelkezik, amelyek csak bizonyos IP-címekhez vagy címtartományokhoz engedélyezik a hozzáférést, töltse le az [Azure IP-tartományok](https://www.microsoft.com/download/details.aspx?id=56519)és szolgáltatáscímkék – nyilvános felhő .

Az ACR REST-végpont IP-tartományai, amelyekhez hozzáférést kell engedélyeznie, keresse meg az **AzureContainerRegistry** szolgáltatásokat a JSON-fájlban.

> [!IMPORTANT]
> Az Azure-szolgáltatások IP-címtartományai változhatnak, a frissítéseket pedig hetente közzéteszik. Töltse le rendszeresen a JSON-fájlt, és tegye meg a szükséges frissítéseket a hozzáférési szabályokban. Ha a forgatókönyvben hálózati biztonsági csoportokra vonatkozó szabályokat kell konfigurálni egy Azure-beli virtuális hálózaton, Azure Firewall az **AzureContainerRegistry** [szolgáltatáscímkét használja.](#allow-access-by-service-tag)
>

### <a name="rest-ip-addresses-for-all-regions"></a>REST IP-címek az összes régióhoz

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>ADOTT régió REST IP-címei

Keresse meg az adott régiót, például **AzureContainerRegistry.AustraliaEast.**

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Tárolási IP-címek az összes régióhoz

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Tárolási IP-címek adott régiókhoz

Keressen rá az adott régióra, például **Storage.AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Hozzáférés engedélyezése szolgáltatáscímke alapján

Az Azure-beli virtuális hálózatokban hálózati biztonsági szabályok használatával szűrheti az erőforrások, például a virtuális gépek és a tároló-beállításjegyzékek forgalmát. Az Azure hálózati szabályok létrehozásának egyszerűsítése érdekében használja az **AzureContainerRegistry** [szolgáltatáscímkét.](../virtual-network/network-security-groups-overview.md#service-tags) A szolgáltatáscímke ip-címelőtagok egy csoportját jelöli egy Azure-szolgáltatás globális vagy Azure-régiónkénti eléréséhez. A címek módosításakor a címke automatikusan frissül. 

Hozzon létre például egy kimenő hálózati biztonságicsoport-szabályt **az AzureContainerRegistry** céladatbázissal, hogy engedélyezze az Azure Container Registrybe irányuló forgalmat. Ha csak egy adott régióban engedélyezi a szolgáltatáscímkéhez való hozzáférést, adja meg a régiót a következő formátumban: **AzureContainerRegistry**. [*régió neve*].

## <a name="enable-dedicated-data-endpoints"></a>Dedikált adatvégpont engedélyezése 

> [!WARNING]
> Ha korábban konfigurálta az ügyfél tűzfalának hozzáférését a meglévő végpontok számára, a dedikált adatvégpontra való váltás hatással lesz az ügyfélkapcsolatra, ami le `*.blob.core.windows.net` pull-hibákat okoz. Az ügyfelek konzisztens hozzáférésének biztosításához adja hozzá az új adatvégpont-szabályokat az ügyfél tűzfalszabályihoz. Ha elkészült, engedélyezze a regisztrációs jegyzékek dedikált adatvégpontjait az Azure CLI vagy más eszközök használatával.

A dedikált adatvégpont a **Prémium** tároló-beállításjegyzék szolgáltatásszint egyik választható funkciója. A beállításjegyzék szolgáltatásszintekkel és korlátozásokkal kapcsolatos további információkért lásd: Azure Container Registry [szolgáltatási szintek.](container-registry-skus.md) 

A dedikált adatvégpontokat a Azure Portal Azure CLI használatával engedélyezheti. Az adatvégpont egy regionális mintát követ: `<registry-name>.<region>.data.azurecr.io` . Egy georeplikált beállításjegyzékben az adatvégpont engedélyezése minden replikarégiában engedélyezi a végpontokat.

### <a name="portal"></a>Portál

Adatvégpont engedélyezése a portálon:

1. Lépjen a tároló-beállításjegyzékhez.
1. Válassza **a Hálózat nyilvános** hozzáférés  >  **lehetőséget.**
1. Jelölje be **a Dedikált adatvégpont engedélyezése** jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.

Az adatvégpont vagy -végpontok megjelennek a portálon.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Dedikált adatvégpont a portálon":::

### <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI használatával szeretné engedélyezni az adatvégpontokat, használja az Azure CLI 2.4.0-s vagy újabb verzióját. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az alábbi [az acr update parancs][az-acr-update] engedélyezi a dedikált adatvégpontokat a *myregistry beállításjegyzékben.* 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Az adatvégpont megtekintéséhez használja az [az acr show-endpoints][az-acr-show-endpoints] parancsot:

```azurecli
az acr show-endpoints --name myregistry
```

A bemutató jellegű kimenet két regionális végpontot mutat be

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

Miután beállította a dedikált adatvégpontokat a beállításjegyzékhez, engedélyezheti az ügyfél tűzfal-hozzáférési szabályait az adatvégponthoz. Adatvégpont-hozzáférési szabályok engedélyezése az összes szükséges beállításjegyzék-régióhoz.

## <a name="configure-client-firewall-rules-for-mcr"></a>Ügyfél tűzfalszabályainak konfigurálása az MCR-hez

Ha tűzfal mögül kell Microsoft Container Registry MCR-t elérnie, tekintse meg az útmutatót az MCR-ügyfél tűzfalszabályainak [konfigurálásról.](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md) Az MCR az összes Microsoft által közzétett Docker-rendszerkép, például a Windows Server-rendszerképek elsődleges regisztrációs adatbázisa.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [Azure ajánlott hálózati biztonsági eljárásait](../security/fundamentals/network-best-practices.md)

* További információ az [Azure-beli](../virtual-network/network-security-groups-overview.md) virtuális hálózatokban lévő biztonsági csoportokról

* További információ a tároló-beállításjegyzék [Private Link](container-registry-private-link.md) beállításával kapcsolatban

* További információ a dedikált [adatvégpontokkal kapcsolatban a](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) Azure Container Registry



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-show-endpoints]: /cli/azure/acr#az_acr_show_endpoints