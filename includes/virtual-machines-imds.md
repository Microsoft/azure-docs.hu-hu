---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 357223751112af03bf797ae9a0e6352a10132ab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464966"
---
Az Azure Instance Metadata Service (IMDS) információt nyújt a jelenleg futó virtuálisgép-példányokról. Használhatja a virtuális gépek felügyeletére és konfigurálására.
Ezen információk közé tartozik a SKU, a Storage, a hálózati konfigurációk és a közelgő karbantartási események. Az elérhető adatmennyiségek teljes listájáért tekintse meg a [végpontok kategóriáinak összegzése](#endpoint-categories)című témakört.

A IMDS virtuális gépek (VM-EK) és virtuálisgép-méretezési csoport példányainak futtatására is használható. Az összes végpont támogatja a [Azure Resource Manager](/rest/api/resources/)használatával létrehozott és kezelt virtuális gépeket. Csak a példány kategóriájának igazolt kategóriája és hálózati része támogatja a klasszikus üzemi modell használatával létrehozott virtuális gépeket. Az igazolt végpont csak korlátozott mértékben működik.

A IMDS olyan REST API, amely jól ismert, nem irányítható IP-címen () érhető el `169.254.169.254` . Csak a virtuális gépről férhet hozzá. A virtuális gép és a IMDS közötti kommunikáció soha nem hagyja el a gazdagépet.
A IMDS lekérdezése során a HTTP-ügyfelek megkerülik a webes proxykat a virtuális gépen, és `169.254.169.254` ugyanazokat a-t kell kezelnie [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Használat

### <a name="access-azure-instance-metadata-service"></a>Az Azure Instance Metadata Service elérése

A IMDS eléréséhez hozzon létre egy virtuális gépet [Azure Resource Manager](/rest/api/resources/) vagy a [Azure Portalból](https://portal.azure.com), és használja a következő mintákat.
További Példákért lásd az [Azure-példány metaadatainak mintáit](https://github.com/microsoft/azureimds)ismertető témakört.

Az alábbi mintakód egy példány metaadatainak beolvasására használható. Egy adott adatforrás eléréséhez tekintse meg a [végpontok kategóriáit](#endpoint-categories) az összes elérhető funkció áttekintéséhez.

**Kérés**

> [!IMPORTANT]
> Ez a példa kihagyja a proxykat. A IMDS lekérdezése során meg **kell** kerülnie a proxykat. További információkért lásd: [proxyk](#proxies) .

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Biztonság és hitelesítés

A Instance Metadata Service csak futó virtuálisgép-példányból érhető el nem irányítható IP-címen. A virtuális gépek a saját magukra vonatkozó metaadatokkal/funkciókkal való interakcióra korlátozódnak. Az API csak HTTP-alapú, és soha nem hagyja el a gazdagépet.

Annak biztosítása érdekében, hogy a kérések közvetlenül a IMDS legyenek kiképezve, és megakadályozzák a kérelmek nem szándékolt vagy nemkívánatos átirányítását:
- Tartalmaznia **kell** a fejlécet`Metadata: true`
- **Nem** tartalmazhat `X-Forwarded-For` fejlécet

A szolgáltatás elutasítja minden olyan kérelmet, amely nem felel meg **mindkét** követelménynek.

> [!IMPORTANT]
> A IMDS **nem** a bizalmas adatok csatornája. Az API nem hitelesített, és nyitva van a virtuális gépen futó összes folyamat számára. A szolgáltatáson keresztül elérhető információkat a virtuális gépen futó összes alkalmazásnak megosztott információnak kell tekinteni.

## <a name="proxies"></a>Legközelebbi

A IMDS **nem** használható proxy mögött, ezért nem támogatott. A legtöbb HTTP-ügyfél lehetőséget biztosít a proxyk letiltására a kérelmekben, és ezt a funkciót a IMDS-vel való kommunikáció során kell használni. A részletekért olvassa el az ügyfél dokumentációját.

> [!IMPORTANT]
> Még ha nem tudja, hogy a környezetben milyen proxybeállítások vannak, **akkor is felül kell bírálnia az alapértelmezett ügyfél-proxybeállításokat**. A rendszer automatikusan felderíti a proxy konfigurációját, és az ilyen konfigurációk megkerülésének sikertelensége után a gép konfigurációjának jövőbeli megváltozása miatt kikapcsolja a kockázatokat.

## <a name="rate-limiting"></a>Sebességkorlátozás

Általánosságban elmondható, hogy a IMDS-kérelmek másodpercenként 5 kérelemre korlátozódnak. A küszöbértéket meghaladó kérések elutasítása 429-válaszokkal történik. A [felügyelt identitások](#managed-identity) kategóriába érkező kérések másodpercenként legfeljebb 20 kérést és 5 egyidejű kérelmet kapnak.

## <a name="http-verbs"></a>HTTP-műveletek

Jelenleg a következő HTTP-műveletek támogatottak:

| Művelet | Leírás |
|------|-------------|
| `GET` | A kért erőforrás beolvasása

## <a name="parameters"></a>Paraméterek

A végpontok a kötelező és/vagy választható paramétereket is támogathatják. További részletekért tekintse meg a [séma](#schema) és a kérdéses végpont dokumentációját.

### <a name="query-parameters"></a>Lekérdezési paraméterek

A IMDS-végpontok támogatják a HTTP-lekérdezési karakterlánc paramétereit. Például: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

A paramétereket adja meg:

| Name | Érték |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

A duplikált lekérdezési paraméterek neveivel rendelkező kérelmek el lesznek utasítva.

### <a name="route-parameters"></a>Útvonal paraméterei

A nagyobb JSON-blobokat visszaadó végpontok esetében támogatjuk az útválasztási paraméterek hozzáfűzését a kérelem végpontjának a válasz egy részhalmazára való szűréshez: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
A paraméterek megfelelnek azon indexeknek/kulcsoknak, amelyeket a JSON-objektum lejárására használ a rendszer egy elemzett ábrázolással való interakció során.

Például a következő `/metatadata/instance` JSON-objektumot adja vissza:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

Ha a választ csak a számítási tulajdonságra szeretné szűrni, a kérést elküldjük: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Hasonlóképpen, ha egy beágyazott tulajdonságra vagy egy adott tömb elemre szeretnénk szűrni, a kulcsok hozzáfűzését tartjuk: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
a tulajdonság első elemére kell szűrnie `Network.interface` , és vissza kell térnie:

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> A levél csomópontra való szűrés `format=json` nem működik. Ezeket a lekérdezéseket `format=text` explicit módon meg kell adni, mivel az alapértelmezett formátum a JSON.

## <a name="schema"></a>Séma

### <a name="data-format"></a>Adatformátum

Alapértelmezés szerint a IMDS JSON formátumban () adja vissza az értékeket `Content-Type: application/json` . Azonban a válaszok szűrését támogató végpontok (lásd az [útvonal paramétereit](#route-parameters)) is támogatják a formátumot `text` .

A nem alapértelmezett válasz formátumának eléréséhez a kérelemben a kért formátumot lekérdezési karakterlánc paraméterként kell megadni. Például:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

A JSON-válaszokban minden primitív típusú `string` , és hiányzó vagy nem alkalmazható értékek mindig szerepelnek, de egy üres sztringre lesznek beállítva.

### <a name="versioning"></a>Verziókezelés

A IMDS verziója, és a HTTP-kérelemben szereplő API-verzió megadása kötelező. Az egyetlen kivétel erre a követelményre a [verziók](#versions) végpontja, amely az elérhető API-verziók dinamikus lekérésére használható.

Ahogy újabb verziók lettek hozzáadva, a régebbi verziók továbbra is elérhetők a kompatibilitás érdekében, ha a parancsfájlok adott adatformátumok függőségeivel rendelkeznek.

Ha nem ad meg verziót, a rendszer hibaüzenetet kap a legújabb támogatott verziók listájáról:
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>Támogatott API-verziók
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01

### <a name="swagger"></a>Swagger

A IMDS teljes hencegő definíciója a következő címen érhető el: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Regionális elérhetőség

A szolgáltatás **általánosan elérhető** az összes Azure-felhőben.

## <a name="root-endpoint"></a>Gyökérszintű végpont

A gyökérszintű végpont `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Végpontok kategóriái

A IMDS API több végponti kategóriát is tartalmaz, amelyek különböző adatforrásokat tartalmaznak, amelyek mindegyike egy vagy több végpontot tartalmaz. A részletekért tekintse meg az egyes kategóriák részleteit.

| Kategória gyökere | Leírás | Verzió bevezetése |
|---------------|-------------|--------------------|
| `/metadata/attested` | [Igazolt](#attested-data) információ | 2018-10-01
| `/metadata/identity` | [Felügyelt identitás megjelenítése a IMDS használatával](#managed-identity) | 2018-02-01
| `/metadata/instance` | Lásd: [példány metaadatainak](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | Lásd: [Load Balancer metaadatok beolvasása a IMDS használatával](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | Lásd: [Scheduled Events a IMDS-on keresztül](#scheduled-events) | 2017-08-01
| `/metadata/versions` | [Verziók](#versions) megjelenítése | N/A

## <a name="versions"></a>Verziók

> [!NOTE]
> Ez a szolgáltatás az 2020-10-01-es verzióval együtt jelent meg, amely jelenleg be van vezetve, és az összes régióban még nem érhető el.

### <a name="list-api-versions"></a>API-verziók listázása

A támogatott API-verziók készletét adja vissza.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Paraméterek

Nincs (a végpont verziója nem érhető el).

#### <a name="response"></a>Reagálás

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Példány metaadatainak

### <a name="get-vm-metadata"></a>VM-metaadatok beolvasása

Elérhetővé teszi a virtuálisgép-példány fontos metaadatait, beleértve a számítást, a hálózatot és a tárterületet. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Paraméterek

| Name | Kötelező vagy nem kötelező | Leírás |
|------|-------------------|-------------|
| `api-version` | Kötelező | A kérelem kiszolgálásához használt verzió.
| `format` | Választható | A válasz formátuma ( `json` vagy `text` ). * Megjegyzés: szükséges lehet a kérelem paramétereinek használatakor

Ez a végpont az [útválasztási paraméterek](#route-parameters)használatával támogatja a válaszadási szűrést.

#### <a name="response"></a>Reagálás

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Séma részletezése:

**Számítás**

| Adatok | Leírás | Verzió bevezetése |
|------|-------------|--------------------|
| `azEnvironment` | Az Azure-környezet, amelyben a virtuális gép fut | 2018-10-01
| `customData` | Ez a funkció jelenleg le van tiltva. Ezt a dokumentációt akkor fogjuk frissíteni, amikor elérhetővé válik | 2019-02-01
| `evictionPolicy` | Beállítja, hogy a rendszer hogyan zárja ki a [helyszíni virtuális](../articles/virtual-machines/spot-vms.md) gépeket. | 2020-12-01
| `isHostCompatibilityLayerVm` | Annak azonosítása, hogy a virtuális gép a gazdagép kompatibilitási rétegén fut-e | 2020-06-01
| `licenseType` | A [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit)licencének típusa. Ez csak a AHB-kompatibilis virtuális gépek esetében jelent meg | 2020-09-01
| `location` | Az Azure-régió, amelyen a virtuális gép fut | 2017-04-02
| `name` | A virtuális gép neve | 2017-04-02
| `offer` | A virtuálisgép-lemezképre vonatkozó információkat nyújtja, és csak az Azure rendszerkép-katalógusból üzembe helyezett rendszerképekhez érhető el | 2017-04-02
| `osProfile.adminUsername` | Megadja a rendszergazdai fiók nevét. | 2020-07-15
| `osProfile.computerName` | A számítógép nevét adja meg. | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Megadja, hogy a jelszó-hitelesítés le van-e tiltva. Ez csak Linux rendszerű virtuális gépek esetén érhető el | 2020-10-01
| `osType` | Linux vagy Windows | 2017-04-02
| `placementGroupId` | A virtuálisgép-méretezési [csoport elhelyezési csoportja](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
| `plan` | Egy virtuális gép nevét, termékét és közzétevőjét tartalmazó [csomag megtervezése](/rest/api/compute/virtualmachines/createorupdate#plan) , ha az Azure Marketplace-rendszerkép | 2018-04-02
| `platformUpdateDomain` |  A virtuális gépet futtató [tartomány frissítése](../articles/virtual-machines/availability.md) | 2017-04-02
| `platformFaultDomain` | A virtuális gép által futtatott tartalék [tartomány](../articles/virtual-machines/availability.md) | 2017-04-02
| `priority` | A virtuális gép prioritása. További információt a [helyszíni virtuális gépek](../articles/virtual-machines/spot-vms.md) című témakörben talál. | 2020-12-01
| `provider` | A virtuális gép szolgáltatója | 2018-10-01
| `publicKeys` | A virtuális géphez és elérési utakhoz rendelt [nyilvános kulcsok gyűjteménye](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
| `publisher` | A virtuális gép rendszerképének közzétevője | 2017-04-02
| `resourceGroupName` | A virtuális géphez tartozó [erőforráscsoport](../articles/azure-resource-manager/management/overview.md) | 2017-08-01
| `resourceId` | Az erőforrás [teljes](/rest/api/resources/resources/getbyid) azonosítója | 2019-03-11
| `sku` | A virtuális gép rendszerképének adott SKU-jának | 2017-04-02
| `securityProfile.secureBootEnabled` | Annak azonosítása, hogy engedélyezve van-e az UEFI biztonságos rendszerindítás a virtuális gépen | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Annak azonosítása, hogy a virtuális platformmegbízhatósági modul (TPM) engedélyezve van-e a virtuális GÉPEN | 2020-06-01
| `storageProfile` | Lásd az alábbi tárolási profilt | 2019-06-01
| `subscriptionId` | Azure-előfizetés a virtuális géphez | 2017-08-01
| `tags` | A virtuális gép [címkéi](../articles/azure-resource-manager/management/tag-resources.md)  | 2017-08-01
| `tagsList` | Az egyszerűbb programozási elemzéshez JSON-tömbként formázott Címkék  | 2019-06-04
| `version` | A VM-rendszerkép verziója | 2017-04-02
| `vmId` | A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
| `vmScaleSetName` | A virtuálisgép-méretezési csoport virtuálisgép-méretezési [készletének neve](../articles/virtual-machine-scale-sets/overview.md) | 2017-12-01
| `vmSize` | [Virtuális gép mérete](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | A virtuális gép [rendelkezésre állási zónája](../articles/availability-zones/az-overview.md) | 2017-12-01

**Tárolóprofil**

A virtuális gépek tárolási profilja három kategóriára oszlik: képhivatkozás, operációsrendszer-lemez és adatlemezek.

A képhivatkozási objektum a következő információkat tartalmazza az operációsrendszer-lemezképpel kapcsolatban:

| Adatok | Leírás |
|------|-------------|
| `id` | Erőforrás-azonosító
| `offer` | A platform vagy a piactér rendszerképének ajánlata
| `publisher` | Rendszerkép kiadója
| `sku` | Rendszerkép SKU
| `version` | A platform vagy a piactér rendszerképének verziója

Az operációsrendszer-lemez objektum a következő információkat tartalmazza a virtuális gép által használt operációsrendszer-lemezről:

| Adatok | Leírás |
|------|-------------|
| `caching` | Gyorsítótárazási követelmények
| `createOption` | Információk a virtuális gép létrehozásáról
| `diffDiskSettings` | Ideiglenes lemez beállításai
| `diskSizeGB` | A lemez mérete GB-ban
| `image`   | Forrás felhasználói lemezkép virtuális merevlemeze
| `lun`     | A lemez logikai egységének száma
| `managedDisk` | Felügyelt lemez paramétereinek
| `name`    | Lemez neve
| `vhd`     | Virtuális merevlemez
| `writeAcceleratorEnabled` | Azt jelzi, hogy engedélyezve van-e a writeAccelerator a lemezen

Az adatlemezek tömb tartalmazza a virtuális géphez csatolt adatlemezek listáját. Minden adatlemez-objektum a következő információkat tartalmazza:

Adatok | Leírás |
-----|-------------|
| `caching` | Gyorsítótárazási követelmények
| `createOption` | Információk a virtuális gép létrehozásáról
| `diffDiskSettings` | Ideiglenes lemez beállításai
| `diskSizeGB` | A lemez mérete GB-ban
| `encryptionSettings` | A lemez titkosítási beállításai
| `image` | Forrás felhasználói lemezkép virtuális merevlemeze
| `managedDisk` | Felügyelt lemez paramétereinek
| `name` | Lemez neve
| `osType` | A lemezen található operációs rendszer típusa
| `vhd` | Virtuális merevlemez
| `writeAcceleratorEnabled` | Azt jelzi, hogy engedélyezve van-e a writeAccelerator a lemezen

**Hálózat**

| Adatok | Leírás | Verzió bevezetése |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | A virtuális gép helyi IPv4-címe | 2017-04-02
| `ipv4.publicIpAddress` | A virtuális gép nyilvános IPv4-címe | 2017-04-02
| `subnet.address` | A virtuális gép alhálózati címe | 2017-04-02
| `subnet.prefix` | Alhálózat előtagja, 24. példa | 2017-04-02
| `ipv6.ipAddress` | A virtuális gép helyi IPv6-címe | 2017-04-02
| `macAddress` | VM MAC-címe | 2017-04-02


#### <a name="sample-1-tracking-vm-running-on-azure"></a>1. példa: az Azure-on futó virtuális gép nyomon követése

Szolgáltatóként szükség lehet a szoftvert futtató virtuális gépek számának nyomon követésére, vagy olyan ügynökökkel, amelyeknek nyomon kell követniük a virtuális gép egyediségét. A virtuális gép egyedi AZONOSÍTÓjának beszerzéséhez használja a `vmId` mezőt instance metadata Service.

**Kérés**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Válasz**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>2. példa: különböző adatreplikák elhelyezése

Bizonyos esetekben a különböző adatreplikák elhelyezése elsődleges fontossággal bír. Előfordulhat például, hogy az [HDFS replika elhelyezése](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) vagy a tároló elhelyezése egy [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) keresztül megkövetelheti `platformFaultDomain` a `platformUpdateDomain` és a virtuális gép futtatását.
Ezen döntések elvégzéséhez [Availability Zones](../articles/availability-zones/az-overview.md) is használhatja a példányokhoz.
Ezeket az adatlekérdezéseket közvetlenül a IMDS-on keresztül kérdezheti le.

**Kérés**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Válasz**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>3. példa: virtuálisgép-címkék beolvasása

A virtuális gépek címkéi a példány/számítás/címkék végpont alatt találhatók a példány API-val.
Előfordulhat, hogy az Azure-beli virtuális gépen a címkék logikailag rendszerezve lettek. A virtuális géphez rendelt címkék az alábbi kérelem használatával kérhetők le.

**Kérés**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Válasz**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

A `tags` mező egy olyan karakterlánc, amelynek a címkéi pontosvesszővel vannak elválasztva. Ez a kimenet akkor lehet probléma, ha a címkékben pontosvesszőt használnak. Ha egy elemzőt a címkék programozott kinyeréséhez kell írni, a mezőre kell támaszkodnia `tagsList` . A `tagsList` mező egy olyan JSON-tömb, amely nem határolójeleket, és így könnyebben elemezhető. A virtuális géphez rendelt tagsList az alábbi kérelem használatával kérhető le.

**Kérés**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Válasz**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>4. példa: további információk a virtuális géppel kapcsolatban a támogatási esetekben

Szolgáltatóként olyan támogatási hívást kaphat, amelyben további információkat szeretne megtudni a virtuális gépről. Ha arra kéri az ügyfelet, hogy ossza meg a számítási metaadatokat, alapvető információkat biztosíthat a támogatási szakember számára az Azure-beli virtuális gépekről.

**Kérés**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>5. példa: az Azure-környezet beszerzése, amelyen a virtuális gép fut

Az Azure számos szuverén felhővel rendelkezik, mint például a [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Időnként szükség van az Azure-környezetre bizonyos futtatókörnyezeti döntések elvégzéséhez. A következő minta bemutatja, hogyan érheti el ezt a viselkedést.

**Kérés**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Válasz**

```
AzurePublicCloud
```

A felhő és az Azure-környezet értékei itt láthatók.

| Felhőbeli | Azure-környezet |
|-------|-------------------|
| [Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Germany](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>6. példa: hálózati információk beolvasása

**Kérés**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Válasz**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>7. minta: nyilvános IP-cím lekérése

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Igazolt adatértékek

### <a name="get-attested-data"></a>Igazolt adatlekérdezés

A IMDS segít biztosítani, hogy a megadott adatok az Azure-ból érkeznek. A Microsoft aláírja ezen információk egy részét, így meggyőződhet arról, hogy az Azure piactéren az Azure-ban futtatott rendszerkép látható.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Paraméterek

| Name | Kötelező vagy nem kötelező | Leírás |
|------|-------------------|-------------|
| `api-version` | Kötelező | A kérelem kiszolgálásához használt verzió.
| `nonce` | Választható | Egy 10 számjegyű karakterlánc, amely kriptográfiai alkalomként szolgál. Ha nincs megadva érték, a IMDS az aktuális UTC időbélyeget használja.

#### <a name="response"></a>Reagálás

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> A IMDS gyorsítótárazási mechanizmusa miatt előfordulhat, hogy a rendszer egy korábban gyorsítótárazott egyszeres értéket ad vissza.

Az aláírási blob a dokumentum egy [PKCS7](https://aka.ms/pkcs7)aláírt verziója. Ez tartalmazza az aláíráshoz használt tanúsítványt, valamint bizonyos virtuálisgép-specifikus részleteket.

A Azure Resource Manager használatával létrehozott virtuális gépek esetében a dokumentum a,,, `vmId` `sku` , a `nonce` `subscriptionId` `timeStamp` dokumentum létrehozásához és lejáratához, valamint a rendszerkép információinak megtervezéséhez tartalmazza a dokumentumot. A csomag adatai csak az Azure Marketplace-lemezképek esetében vannak kitöltve.

A klasszikus üzemi modellel létrehozott virtuális gépek esetében csak a `vmId` garantáltan tölthetők fel. Kinyerheti a tanúsítványt a válaszból, és ezzel ellenőrizheti, hogy a válasz érvényes-e, és az Azure-ból érkezik-e.

A dekódolású dokumentum a következő mezőket tartalmazza:

| Adatok | Leírás | Verzió bevezetése |
|------|-------------|--------------------|
| `licenseType` | A [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit)licencének típusa. Ez csak a AHB-kompatibilis virtuális gépek esetében jelent meg. | 2020-09-01
| `nonce` | Egy karakterlánc, amely opcionálisan megadható a kérelemben. Ha nem `nonce` lett megadva, a rendszer az aktuálisan koordinált világidő-időbélyeget használja. | 2018-10-01
| `plan` | Az [Azure Marketplace-rendszerkép terve](/rest/api/compute/virtualmachines/createorupdate#plan). A csomag AZONOSÍTÓját (név), a termék rendszerképét vagy az ajánlatot (terméket) és a közzétevő AZONOSÍTÓját (kiadó) tartalmazza. | 2018-10-01
| `timestamp.createdOn` | Az aláírt dokumentum létrehozási időpontjának UTC-időbélyege | 2018-20-01
| `timestamp.expiresOn` | Az aláírt dokumentum érvényességi időpontjának UTC-időbélyege | 2018-10-01
| `vmId` | A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2018-10-01
| `subscriptionId` | Azure-előfizetés a virtuális géphez | 2019-04-30
| `sku` | A virtuális gép rendszerképének adott SKU-jának | 2019-11-01

> [!NOTE]
> A klasszikus (nem Azure Resource Manager) virtuális gépek esetében csak a vmId garantáltan kell feltölteni.

Példa dokumentumra:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>1. példa: annak ellenőrzése, hogy a virtuális gép fut-e az Azure-ban

Az Azure piactéren lévő szállítók biztosítják, hogy a szoftverük csak az Azure-ban fusson. Ha valaki átmásolja a virtuális merevlemezt egy helyszíni környezetbe, a gyártónak képesnek kell lennie az észlelésére. A IMDS-en keresztül ezek a szállítók olyan aláírt adatokhoz juthatnak, amelyek csak az Azure-ból választanak.

> [!NOTE]
> Ehhez a mintához telepíteni kell a jQ segédprogramot.

**Érvényesítés**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Ellenőrizze, hogy az aláírás Microsoft Azure-e, és ellenőrizze a tanúsítványlánc hibáit.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Results (Eredmények)**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Ellenőrizze, hogy az aláírás Microsoft Azure-e, és ellenőrizze a tanúsítványlánc hibáit.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> A IMDS gyorsítótárazási mechanizmusa miatt előfordulhat, hogy a rendszer egy korábban gyorsítótárazott `nonce` értéket ad vissza.

Az `nonce` aláírt dokumentumban szereplő érték összehasonlítható, ha `nonce` a kezdeti kérelemben paramétert adott meg.

> [!NOTE]
> A nyilvános felhőhöz és az egyes szuverén felhőhöz tartozó tanúsítvány eltérő lesz.

| Felhőbeli | Tanúsítvány |
|-------|-------------|
| [Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/) | *. metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *. metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *. metadata.azure.cn
| [Azure Germany](https://azure.microsoft.com/overview/clouds/germany/) | *. metadata.microsoftazure.de

> [!NOTE]
> Előfordulhat, hogy a tanúsítványok nem egyeznek pontosan a `metadata.azure.com` nyilvános felhővel. Emiatt a minősítés érvényesítésének minden altartományból engedélyeznie kell a köznapi nevet `.metadata.azure.com` .

Azokban az esetekben, amikor a köztes tanúsítvány nem tölthető le, mert az érvényesítés során hálózati korlátozások miatt nem lehet letölteni, rögzítheti a köztes tanúsítványt. Az Azure átadja a tanúsítványokat, ami a standard PKI-gyakorlat. A rögzített tanúsítványokat frissíteni kell, ha a váltás történik. A köztes tanúsítvány frissítésének megtervezése után az Azure-blog frissül, és az Azure-ügyfelek értesítést kapnak. 

A köztes tanúsítványokat a PKI- [tárházban](https://www.microsoft.com/pki/mscorp/cps/default.htm)találja. Az egyes régiók köztes tanúsítványok különbözőek lehetnek.

> [!NOTE]
> Az Azure China 21Vianet köztes tanúsítvány a Baltimore helyett a DigiCert globális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓTÓL fog származni.
Ha az Azure China köztes tanúsítványokat a legfelső szintű láncbeli hatóság változásának részeként rögzítette, a köztes tanúsítványokat frissíteni kell.


## <a name="managed-identity"></a>Felügyelt identitás

A rendszer által hozzárendelt felügyelt identitás engedélyezhető a virtuális gépen. Egy vagy több felhasználó által hozzárendelt felügyelt identitást is hozzárendelhet a virtuális géphez.
Ezután jogkivonatokat kérhet a felügyelt identitásokhoz a IMDS-ből. Ezekkel a jogkivonatokkal hitelesíthetők más Azure-szolgáltatásokkal, például a Azure Key Vaultokkal.

A szolgáltatás engedélyezésének részletes lépéseiért lásd: [hozzáférési jogkivonat beszerzése](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="load-balancer-metadata"></a>Metaadatok Load Balancer
Ha a virtuális gépet vagy virtuálisgép-készletet állít be egy Azure-standard Load Balancer mögött, a IMDS segítségével lekérheti a terheléselosztó és a példányok metaadatait. További információ: [Load Balancer-információk beolvasása](../articles/load-balancer/instance-metadata-service-load-balancer.md).

## <a name="scheduled-events"></a>Ütemezett események
Az ütemezett események állapotát a IMDS használatával szerezheti be. Ezután a felhasználó megadhatja az eseményeken futtatandó műveletek készletét. További információ: [ütemezett események a Linux](../articles/virtual-machines/linux/scheduled-events.md) vagy [a Windows ütemezett eseményeihez](../articles/virtual-machines/windows/scheduled-events.md).


## <a name="sample-code-in-different-languages"></a>Mintakód különböző nyelveken

A következő táblázat a virtuális gépen belüli különböző nyelvek használatával mutatja be a hívó IMDS mintáit:

| Nyelv | Példa |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Indítás | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Hibák és hibakeresés

Ha nem található adatelem vagy helytelenül formázott kérelem, a Instance Metadata Service szabványos HTTP-hibákat ad vissza. Például:

| HTTP-állapotkód | Ok |
|------------------|--------|
| `200 OK` | A kérelem sikeres volt.
| `400 Bad Request` | Hiányzó `Metadata: true` fejléc vagy hiányzó paraméter `format=json` a levél csomópontjainak lekérdezése során
| `404 Not Found` | A kért elem nem létezik
| `405 Method Not Allowed` | A végponton nem támogatott a HTTP-metódus (művelet).
| `410 Gone` | Némi várakozás után próbálja megismételni a maximális 70 másodpercet
| `429 Too Many Requests` | Túllépte az API- [díjszabási korlátokat](#rate-limiting)
| `500 Service Error` | Némi idő elteltével próbálkozzon újra

## <a name="frequently-asked-questions"></a>Gyakori kérdések

- Hibaüzenetet kapok `400 Bad Request, Required metadata header not specified` . Mit jelent ez?
  - A IMDS megköveteli a fejléc átadását a `Metadata: true` kérelemben. Ha ezt a fejlécet a REST-hívásban adja át, a IMDS hozzáférése is elérhetővé válik.

- Miért nem kapok számítási információt a virtuális géphez?
  - A IMDS jelenleg csak a Azure Resource Manager-vel létrehozott példányokat támogatja.

- Létrehoztam a virtuális gépet Azure Resource Manager néhány évvel ezelőtt. Miért nem jelenik meg a számítási metaadatokkal kapcsolatos információ?
  - Ha a virtuális gépet a 2016 szeptember után hozta létre, vegyen fel egy [címkét](../articles/azure-resource-manager/management/tag-resources.md) a számítási metaadatok megjelenítésének megkezdéséhez. Ha a virtuális gépet 2016 szeptembere előtt hozta létre, akkor a metaadatok frissítéséhez adjon hozzá vagy távolítson el bővítményeket vagy adatlemezeket a virtuálisgép-példányhoz.

- Miért nem látok az új verzióhoz feltöltött összes adatot?
  - Ha a virtuális gépet a 2016 szeptember után hozta létre, vegyen fel egy [címkét](../articles/azure-resource-manager/management/tag-resources.md) a számítási metaadatok megjelenítésének megkezdéséhez. Ha a virtuális gépet 2016 szeptembere előtt hozta létre, akkor a metaadatok frissítéséhez adjon hozzá vagy távolítson el bővítményeket vagy adatlemezeket a virtuálisgép-példányhoz.

- Miért kapok hibát `500 Internal Server Error` vagy `410 Resource Gone` ?
  - Próbálja megismételni a kérést. További információ: [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults). Ha a probléma továbbra is fennáll, hozzon létre egy támogatási problémát a virtuális gép Azure Portaljában.

- Működne a virtuálisgép-méretezési csoport példányai?
  - Igen, a virtuálisgép-méretezési csoport példányaihoz elérhető a IMDS.

- Frissítettem a címkéket a virtuálisgép-méretezési csoportokban, de nem jelennek meg a példányokban (az Egypéldányos virtuális gépektől eltérően). Valami hiba történt?
  - Jelenleg a virtuálisgép-méretezési csoportokhoz tartozó címkék csak a virtuális GÉPRE mutatnak a példány újraindításakor, rendszerképének vagy lemezének változásakor.

- Miért nem jelenik meg a virtuális géphez tartozó SKU-információ a `instance/compute` részletek között?
  - Az Azure Marketplace-en létrehozott egyéni lemezképek esetében az Azure platform nem őrzi meg az egyéni lemezkép SKU-információit és az egyéni rendszerképből létrehozott virtuális gépek részleteit. Ez a kialakítás, így a virtuális gép részletei nem jelennek meg a felületen `instance/compute` .

- Miért van időtúllépés a kérésem a szolgáltatáshoz való meghívásakor?
  - A metaadat-hívásokat a virtuális gép elsődleges hálózati kártyához rendelt elsődleges IP-címről kell elvégezni. Emellett, ha módosította az útvonalakat, a virtuális gép helyi útválasztási táblázatában a 169.254.169.254/32-címnek is szerepelnie kell egy útvonalnak.

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. Írja le a helyi útválasztási táblázatot, és keresse meg a IMDS bejegyzést. Például:
        ```console
        > route print
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
            168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
        169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
        ... (continues) ...
        ```
    1. Ellenőrizze, hogy létezik-e útvonal a következőhöz `169.254.169.254` , és jegyezze fel a megfelelő hálózati adaptert (például `172.16.69.7` ).
    1. Az illesztőfelület konfigurációjának kiírása, valamint az útválasztási táblázatban hivatkozott, a MAC (fizikai) címek megadásával megegyező felület megkeresése.
        ```console
        > ipconfig /all
        ... (continues) ...
        Ethernet adapter Ethernet:

        Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
        Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
        Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
        DHCP Enabled. . . . . . . . . . . : Yes
        Autoconfiguration Enabled . . . . : Yes
        Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
        IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        ... (continues) ...
        ```
    1. Győződjön meg arról, hogy a csatoló megfelel a virtuális gép elsődleges hálózati adapterének és elsődleges IP-címének. Az elsődleges hálózati adapter és az IP-cím megkereséséhez tekintse meg a Azure Portal hálózati konfigurációját, vagy az Azure CLI-vel megtekintve. Jegyezze fel a magánhálózati IP-címeket (és a MAC-címet, ha a CLI-t használja). Íme egy PowerShell CLI-példa:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: wintest767 True 00-0D-3A-E5-1C-C0
        ```
    1. Ha nem egyeznek, frissítse az útválasztási táblázatot úgy, hogy az elsődleges NIC és az IP-címet célozza meg.

    ### <a name="linux"></a>[Linux](#tab/linux/)

    1. Írja le a helyi útválasztási táblázatot egy paranccsal, `netstat -r` és keresse meg a IMDS bejegyzést (például):
        ```console
        ~$ netstat -r
        Kernel IP routing table
        Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
        default         _gateway        0.0.0.0         UG        0 0          0 eth0
        168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
        169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
        172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
        ```
    1. Ellenőrizze, hogy létezik-e útvonal a következőhöz `169.254.169.254` , és jegyezze fel a megfelelő hálózati adaptert (például `eth0` ).
    1. Az útválasztási táblázatban található megfelelő csatoló felületi konfigurációjának kiírása (Megjegyzés: a konfigurációs fájl pontos neve eltérő lehet)
        ```console
        ~$ cat /etc/netplan/50-cloud-init.yaml
        network:
        ethernets:
            eth0:
                dhcp4: true
                dhcp4-overrides:
                    route-metric: 100
                dhcp6: false
                match:
                    macaddress: 00:0d:3a:e4:c7:2e
                set-name: eth0
        version: 2
        ```
    1. Dinamikus IP-cím használata esetén jegyezze fel a MAC-címet. Ha statikus IP-címet használ, akkor a felsorolt IP-címek és/vagy a MAC-cím is megjegyezhető.
    1. Győződjön meg arról, hogy a csatoló megfelel a virtuális gép elsődleges hálózati adapterének és elsődleges IP-címének. Az elsődleges hálózati adapter és az IP-cím megkereséséhez tekintse meg a Azure Portal hálózati konfigurációját, vagy az Azure CLI-vel megtekintve. Jegyezze fel a magánhálózati IP-címeket (és a MAC-címet, ha a CLI-t használja). Íme egy PowerShell CLI-példa:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: ipexample606 True 00-0D-3A-E4-C7-2E
        ```
    1. Ha nem egyeznek, frissítse az útválasztási táblázatot úgy, hogy az elsődleges NIC/IP-címet célozza meg.

    ---

- Feladatátvételi fürtszolgáltatás a Windows Server rendszerben
  - Ha feladatátvételi fürtszolgáltatással kérdez le IMDS, időnként szükség van egy útvonal hozzáadására az útválasztási táblához. Ezt a következőképpen teheti meg:

    1. Nyisson meg egy parancssort rendszergazdai jogosultságokkal.

    1. Futtassa a következő parancsot, és jegyezze fel az `0.0.0.0` IPv4-útválasztási táblázatban található Hálózati célhely () adapterének a címeit.

    ```bat
    route print
    ```

    > [!NOTE]
    > A következő példa kimenete egy Windows Server rendszerű virtuális gép, amelyen engedélyezve van a feladatátvevő fürt. Az egyszerűség kedvéért a kimenet csak az IPv4-útválasztási táblázatot tartalmazza.

    ```
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
            10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
            10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
    127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
        169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
    169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
            224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
    ```

    Futtassa a következő parancsot, és használja a Hálózati célhely () adapterének címe () `0.0.0.0` , amely ebben a példában () van `10.0.1.10` .

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## <a name="support"></a>Támogatás

Ha több kísérlet után nem tud metaadat-választ kapni, létrehozhat egy támogatási problémát a Azure Portal.

## <a name="product-feedback"></a>Termékkel kapcsolatos visszajelzés

A felhasználói visszajelzési csatornára vonatkozó visszajelzéseket és ötleteket a Virtual Machines > Instance Metadata Service [itt](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627) talál

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési jogkivonat beszerzése a virtuális géphez](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Ütemezett események Linuxra](../articles/virtual-machines/linux/scheduled-events.md)

- [Ütemezett események a Windows rendszerhez](../articles/virtual-machines/windows/scheduled-events.md)
