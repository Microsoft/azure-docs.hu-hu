---
title: Tűzfal-hozzáférési szabályok
description: Szabályok konfigurálása az Azure Container Registry tűzfal mögötti eléréséhez a REST API és az adatvégpont-tartománynevek vagy a szolgáltatás-specifikus IP-címtartományok elérésének lehetővé tételével.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 548d64632c1d726111770dfb49f705d31f5ca714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935988"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Szabályok konfigurálása az Azure Container Registry tűzfal mögötti eléréséhez

Ez a cikk bemutatja, hogyan konfigurálhat szabályokat a tűzfalon az Azure Container Registry elérésének engedélyezéséhez. Előfordulhat például, hogy egy tűzfal mögötti Azure IoT Edge eszköznek hozzá kell férnie egy tároló-beállításjegyzékhez egy tároló rendszerképének lekéréséhez. Emellett előfordulhat, hogy egy helyszíni hálózaton lévő zárolt kiszolgálónak hozzáférésre van szüksége a rendszerkép leküldéséhez.

Ha ehelyett csak egy Azure-beli virtuális hálózaton belül szeretné konfigurálni a bejövő hálózati hozzáférést egy tároló-beállításjegyzékhez, tekintse meg az Azure [Private-hivatkozás konfigurálása Azure Container registryhez](container-registry-private-link.md)című témakört.

## <a name="about-registry-endpoints"></a>Tudnivalók a beállításjegyzékbeli végpontokról

Képek vagy egyéb összetevők Azure Container registrybe való lekéréséhez vagy leküldéséhez egy ügyfélnek, például egy Docker-démonnak a HTTPS-kapcsolaton keresztül kell kommunikálnia két különálló végponttal. Azon ügyfelek esetében, amelyek tűzfal mögött férnek hozzá egy beállításjegyzékhez, mindkét végponthoz be kell állítania a hozzáférési szabályokat. Mindkét végpont a 443-as porton keresztül érhető el.

* A beállításjegyzék **REST API a végpontok** hitelesítése és a beállításjegyzék-kezelési műveletek a beállításjegyzék nyilvános REST API végpontján keresztül kezelhetők. Ez a végpont a beállításjegyzék bejelentkezési kiszolgálójának neve. Például: `myregistry.azurecr.io`

* **Storage-(adat) végpont** – az Azure a [blob Storage](container-registry-storage.md) -fiókokat az egyes beállításjegyzékek nevében a tároló lemezképek és más összetevők adatait kezeli. Amikor egy ügyfél egy Azure Container registryben fér hozzá a képrétegekhez, a a beállításjegyzék által biztosított Storage-fiók végpontján keresztül kéri a kérelmeket.

Ha a beállításjegyzék [földrajzilag replikálódik](container-registry-geo-replication.md), előfordulhat, hogy az ügyfélnek egy adott régióban vagy több replikált régióban lévő adatvégponttal kell kommunikálnia.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Hozzáférés engedélyezése a REST-és az adatvégpontokhoz

* **Rest-végpont** – hozzáférés engedélyezése a teljes beállításjegyzékbeli bejelentkezési kiszolgáló nevéhez, `<registry-name>.azurecr.io` vagy egy társított IP-címtartomány
* **Tárolási (adat) végpont** – engedélyezi az összes Azure Blob Storage-fiókhoz való hozzáférést a helyettesítő karakter `*.blob.core.windows.net` vagy egy társított IP-címtartomány használatával.
> [!NOTE]
> Azure Container Registry [dedikált adatvégpontokat](#enable-dedicated-data-endpoints)vezet be, amelyek lehetővé teszik a beállításjegyzék-tárterület szűk hatókörű szabályainak megkötését. Az űrlap használatával engedélyezheti az adatvégpontokat minden olyan régióban, ahol a beállításjegyzék található vagy replikálva van `<registry-name>.<region>.data.azurecr.io` .

## <a name="allow-access-by-ip-address-range"></a>Hozzáférés engedélyezése IP-címtartomány alapján

Ha a szervezet rendelkezik olyan házirendekkel, amelyek csak adott IP-címekhez vagy címtartományok elérését engedélyezik, töltse le az [Azure IP-címtartományok és a szolgáltatási címkék – nyilvános felhő szolgáltatást](https://www.microsoft.com/download/details.aspx?id=56519).

Azon ACR REST-végpont IP-címtartományok megkereséséhez, amelyekhez engedélyezni szeretné a hozzáférést, keresse meg a **AzureContainerRegistry** a JSON-fájlban.

> [!IMPORTANT]
> Az Azure-szolgáltatások IP-címtartományok változhatnak, és a frissítések hetente lesznek közzétéve. Töltse le rendszeresen a JSON-fájlt, és végezze el a szükséges frissítéseket a hozzáférési szabályokban. Ha a forgatókönyv magában foglalja a hálózati biztonsági csoport szabályainak egy Azure-beli virtuális hálózatban való konfigurálását, vagy ha Azure Firewall használja, használja helyette a **AzureContainerRegistry** [szolgáltatás címkéjét](#allow-access-by-service-tag) .
>

### <a name="rest-ip-addresses-for-all-regions"></a>REST IP-címek az összes régióban

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

### <a name="rest-ip-addresses-for-a-specific-region"></a>Adott régió REST IP-címei

Keresse meg az adott régiót, például a **AzureContainerRegistry. AustraliaEast**.

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

### <a name="storage-ip-addresses-for-all-regions"></a>Az összes régió tárolási IP-címei

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

### <a name="storage-ip-addresses-for-specific-regions"></a>Adott régiók tárolási IP-címei

Keresse meg az adott régiót, például a **Storage. AustraliaCentral**.

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

## <a name="allow-access-by-service-tag"></a>Hozzáférés engedélyezése a Service tag számára

Egy Azure-beli virtuális hálózatban a hálózati biztonsági szabályok használatával szűrheti a forgalmat egy erőforrásból, például egy virtuális gépről egy tároló-beállításjegyzékbe. Az Azure-beli hálózati szabályok létrehozásának egyszerűbbé tételéhez használja a **AzureContainerRegistry** [szolgáltatás címkéjét](../virtual-network/network-security-groups-overview.md#service-tags). A szolgáltatási címke az IP-címek egy csoportját jelöli, amely egy Azure-szolgáltatás globális vagy Azure-régióhoz való elérésére szolgál. A rendszer automatikusan frissíti a címkét a címek változásakor. 

Hozzon létre például egy kimenő hálózati biztonsági csoportra vonatkozó szabályt a cél **AzureContainerRegistry** , hogy engedélyezze a forgalmat egy Azure Container registrybe. Ha csak egy adott régióban szeretné engedélyezni a szolgáltatás címkéhez való hozzáférést, a következő formátumban kell megadnia a régiót: **AzureContainerRegistry**. [*régió neve*].

## <a name="enable-dedicated-data-endpoints"></a>Dedikált adatvégpontok engedélyezése 

> [!WARNING]
> Ha korábban konfigurálta az ügyfél-tűzfalhoz való hozzáférést a meglévő `*.blob.core.windows.net` végpontokhoz, a dedikált adatvégpontokra való áttérés hatással lesz az ügyfél-kapcsolatra, ami lekéréses hibákat okoz. Annak biztosítása érdekében, hogy az ügyfelek konzisztens hozzáféréssel rendelkezzenek, adja hozzá az új adatvégponti szabályokat az ügyfél tűzfalszabályok szabályaihoz. Miután végzett, engedélyezze a dedikált adatvégpontokat a beállításjegyzékhez az Azure CLI vagy más eszközök használatával.

A dedikált adatvégpontok a **prémium** szintű tároló beállításjegyzék-szolgáltatási szintjének választható funkciói. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry szolgáltatási szintek](container-registry-skus.md). 

A dedikált adatvégpontokat a Azure Portal vagy az Azure CLI használatával engedélyezheti. Az adatvégpontok regionális mintát követnek `<registry-name>.<region>.data.azurecr.io` . Földrajzilag replikált beállításjegyzékben az adatvégpontok engedélyezése lehetővé teszi a végpontok használatát az összes replika régióban.

### <a name="portal"></a>Portál

Adatvégpontok engedélyezése a portál használatával:

1. Navigáljon a tároló-beállításjegyzékhez.
1. Válassza a **hálózatkezelés**  >  **nyilvános hozzáférés** lehetőséget.
1. Jelölje be a **dedikált adatvégpont engedélyezése** jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.

Az adatvégpont vagy végpontok megjelennek a portálon.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Dedikált adatvégpontok a portálon":::

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI használatával történő adatvégpontok engedélyezéséhez használja az Azure CLI 2.4.0 vagy újabb verzióját. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

A következő az [ACR Update][az-acr-update] paranccsal dedikált adatvégpontokat engedélyez a beállításjegyzék *myregistry*. 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Az adatvégpontok megtekintéséhez használja az az [ACR show-endpoints][az-acr-show-endpoints] parancsot:

```azurecli
az acr show-endpoints --name myregistry
```

A bemutató céljának kimenete két regionális végpontot mutat be

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

Miután beállította a dedikált adatvégpontokat a beállításjegyzékhez, engedélyezheti az ügyfél-tűzfal hozzáférési szabályait az adatvégpontok számára. Engedélyezze az adatvégpont hozzáférési szabályait az összes szükséges beállításjegyzék-régióhoz.

## <a name="configure-client-firewall-rules-for-mcr"></a>Az MCR tartozó ügyfél-tűzfalszabályok konfigurálása

Ha tűzfal mögött szeretné elérni a Microsoft Container Registryt (MCR), tekintse meg a [MCR-ügyfél tűzfalszabályok](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)konfigurálására vonatkozó útmutatót. A MCR az összes Microsoft által közzétett Docker-rendszerkép elsődleges beállításjegyzéke, például Windows Server-lemezképek.

## <a name="next-steps"></a>Következő lépések

* A [hálózati biztonsággal kapcsolatos Azure ajánlott eljárások](../security/fundamentals/network-best-practices.md) ismertetése

* További információ az Azure Virtual Network [biztonsági csoportjairól](../virtual-network/network-security-groups-overview.md)

* További információ a tároló-beállításjegyzék [privát hivatkozásának](container-registry-private-link.md) beállításáról

* További információ a Azure Container Registry [dedikált adatvégpontokról](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/)



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints