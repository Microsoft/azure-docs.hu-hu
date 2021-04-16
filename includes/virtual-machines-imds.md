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
ms.openlocfilehash: 3da4f8f946b11985d93be35fa2748e7f25015a71
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564513"
---
Az Azure Instance Metadata Service (IMDS) a jelenleg futó virtuálisgép-példányokkal kapcsolatos információkat biztosít. Ezzel kezelheti és konfigurálhatja a virtuális gépeket.
Ezek az információk tartalmazzák a termékváltozatot, a tárolást, a hálózati konfigurációkat és a közelgő karbantartási eseményeket. Az elérhető adatok teljes listáját a Végpontkategóriák [összegzése tartalmazza.](#endpoint-categories)

Az IMDS virtuálisgép-példányok (virtuális gépek) és virtuálisgép-méretezésikészlet-példányok futtatásához érhető el. Minden végpont támogatja a használatával létrehozott és kezelt virtuális gépeket [Azure Resource Manager.](/rest/api/resources/) A klasszikus üzembe helyezési modell használatával létrehozott virtuális gépeket csak a Példány kategória Tanúsítva kategória és Hálózat része támogatja. Az igazolt végpont ezt csak korlátozott mértékben teszi meg.

Az IMDS egy REST API, amely egy jól ismert, nem átirányítható IP-címen () érhető `169.254.169.254` el. Csak a virtuális gépen belülről férhet hozzá. A virtuális gép és az IMDS közötti kommunikáció soha nem hagyja el a gazdagépet.
Az IMDS lekérdezésekor a HTTP-ügyfelek megkerüljék a virtuális gépen belüli webes proxykat, és ugyanúgy `169.254.169.254` kezelje őket, mint [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) a .

## <a name="usage"></a>Használat

### <a name="access-azure-instance-metadata-service"></a>Az Azure Instance Metadata Service

Az IMDS eléréséhez hozzon [](/rest/api/resources/) létre egy virtuális gépet a Azure Resource Manager vagy a [Azure Portal,](https://portal.azure.com)és használja az alábbi mintákat.
További példákat az [Azure Instance Metadata Samples (Azure-példányok metaadat-mintái) tartalmaz.](https://github.com/microsoft/azureimds)

Itt egy mintakód, amely lekéri egy példány összes metaadatát. Egy adott adatforrás eléréséhez [](#endpoint-categories) tekintse meg a végpontkategóriákat ismertető témakört az összes elérhető funkció áttekintéséhez.

**Kérés**

> [!IMPORTANT]
> Ez a példa megkerüli a proxykat. Az  IMDS lekérdezésekor meg kell kerülnie a proxykat. További [információ: Proxyk.](#proxies)

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
> A válasz egy JSON-sztring. Az alábbi példaválasz az olvashatóság érdekében meglehetősen ki van nyomtatva.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Biztonság és hitelesítés

A Instance Metadata Service csak egy futó virtuálisgép-példányon belülről érhető el egy nem átirányítható IP-címen. A virtuális gépek csak a saját magukra vonatkozó metaadatokkal/funkciókkal kommunikálhatnak. Az API csak HTTP, és soha nem hagyja el a gazdagépet.

Annak érdekében, hogy a kérések közvetlenül az IMDS-hez valók, és megelőzhető legyen a kérések és kérések nem kívánt vagy nemkívánatos átirányítása:
- **Tartalmaznia kell** a fejlécet `Metadata: true`
- Nem **tartalmazhat** `X-Forwarded-For` fejlécet

A szolgáltatás elutasít  minden olyan kérelmet, amely nem felel meg mindkét követelménynek.

> [!IMPORTANT]
> Az IMDS **nem** a bizalmas adatok csatornája. Az API nem hitelesített, és a virtuális gép összes folyamata számára nyitva van. A szolgáltatáson keresztül elérhetővé tért információkat megosztott információnak kell tekinteni a virtuális gépen futó összes alkalmazás számára.

## <a name="proxies"></a>Legközelebbi

Az IMDS **nem** használható proxy mögött, és ez nem támogatott. A legtöbb HTTP-ügyfél lehetőséget biztosít a proxyk letiltására a kérések esetében, és ezt a funkciót használni kell az IMDS-sel való kommunikáció során. A részletekért tekintse meg az ügyfél dokumentációját.

> [!IMPORTANT]
> Még ha nem is ismeri a környezet proxykonfigurációját, akkor is felül kell bírálni az alapértelmezett **ügyfélproxy-beállításokat.** A proxykonfigurációk automatikusan felderítendők, és ha nem kerüli meg ezeket a konfigurációkat, kimaradási kockázatoknak vannak kitéve, ha a gép konfigurációját a jövőben módosítani kellene.

## <a name="rate-limiting"></a>Sebességkorlátozás

Általánosságban elmondható, hogy az IMDS-nek másodpercenként legfeljebb 5 kérésre van lehetőség. Az ezt a küszöbértéket meghaladó kérelmeket a rendszer 429 válasz esetén elutasítja. A felügyelt identitás [kategóriájába](#managed-identity) másodpercenként legfeljebb 20 kérelem és 5 egyidejű kérelem tartozik.

## <a name="http-verbs"></a>HTTP-műveletek

Jelenleg a következő HTTP-műveletek támogatottak:

| Művelet | Description |
|------|-------------|
| `GET` | A kért erőforrás lekérése

## <a name="parameters"></a>Paraméterek

A végpontok támogathatnak kötelező és/vagy választható paramétereket. A [részletekért](#schema) tekintse meg a szóban forgó végpont sémáját és dokumentációját.

### <a name="query-parameters"></a>Lekérdezési paraméterek

Az IMDS-végpontok támogatják a HTTP-lekérdezési sztring paramétereit. Például: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

A paramétereket adja meg:

| Name | Érték |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

A rendszer elutasítja az ismétlődő lekérdezési paraméternevű kérelmeket.

### <a name="route-parameters"></a>Útvonal-paraméterek

Egyes, nagyobb JSON-blobokat visszaküldő végpontok esetén támogatjuk az útvonalparaméterek hozzáfűzését a kérésvégponthoz a válasz egy részéhez való szűréshez: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
A paraméterek megegyeznek az indexekkel/kulcsokkal, amelyek a JSON-objektumon végigküldve egy elemezett megjelenítéssel lépkednek le.

A például `/metatadata/instance` a következő json-objektumot adja vissza:
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

Ha a választ csak a compute tulajdonságra szeretnénk szűrni, elküldjük a kérést: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Hasonlóképpen, ha beágyazott tulajdonságra vagy adott tömbelemre szeretnénk szűrni, továbbra is hozzáfűzünk kulcsokat: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
A a tulajdonság első elemére szűr, és `Network.interface` a következőt adja vissza:

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
> Levélcsomópontra való szűréskor a `format=json` nem működik. Ezekhez a `format=text` lekérdezésekhez explicit módon kell megadni, mivel az alapértelmezett formátum a json.

## <a name="schema"></a>Séma

### <a name="data-format"></a>Adatformátum

Alapértelmezés szerint az IMDS JSON formátumban () adja vissza az `Content-Type: application/json` adatokat. A válaszszűrést támogató végpontok azonban a formátumot is támogatják [(lásd:](#route-parameters)Útvonal-paraméterek). `text`

A nem alapértelmezett válaszformátum eléréséhez adja meg a kért formátumot lekérdezési sztring paraméterként a kérésben. Például:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

A JSON-válaszokban minden primitív típusú lesz, és a hiányzó vagy nem alkalmazható értékek mindig szerepelnek, de üres `string` sztringre lesznek beállítva.

### <a name="versioning"></a>Verziókezelés

Az IMDS verziószámozása és az API-verzió megadása kötelező a HTTP-kérésben. Ez alól az egyetlen [](#versions) kivétel a verzióvégpont, amely dinamikusan lekéri az elérhető API-verziókat.

Az újabb verziók hozzáadásakor a régebbi verziók továbbra is elérhetők a kompatibilitás érdekében, ha a szkriptek adott adatformátumtól függnek.

Ha nem ad meg verziót, hibaüzenet jelenik meg a legújabb támogatott verziók listájával:
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
- 2021-01-01

### <a name="swagger"></a>Swagger

Az IMDS teljes Swagger-definíciója itt érhető el: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Regionális elérhetőség

A szolgáltatás **általánosan elérhető az** összes Azure-felhőben.

## <a name="root-endpoint"></a>Gyökérvégpont

A gyökérvégpont `http://169.254.169.254/metadata` a következő: .

## <a name="endpoint-categories"></a>Végpontkategóriák

Az IMDS API több végpontkategóriát tartalmaz, amelyek különböző adatforrásokat képviselnek, és mindegyik egy vagy több végpontot tartalmaz. Részletekért tekintse meg az egyes kategóriákat.

| Kategóriagyökér | Description | Bevezetett verzió |
|---------------|-------------|--------------------|
| `/metadata/attested` | Lásd: [Igazolt adatok](#attested-data) | 2018-10-01
| `/metadata/identity` | Lásd: [Felügyelt identitás IMDS-en keresztül](#managed-identity) | 2018-02-01
| `/metadata/instance` | Példány [metaadatainak megtekintése](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | Lásd: [Metaadatok Load Balancer IMDS-en keresztül](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | Lásd: [Scheduled Events IMDS-en keresztüli alkalmazás](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Lásd: [Verziók](#versions) | N/A

## <a name="versions"></a>Verziók

> [!NOTE]
> Ez a funkció a 2020-10-01-es verzióval együtt jelent meg, amely jelenleg is használatban van, és előfordulhat, hogy még nem érhető el minden régióban.

### <a name="list-api-versions"></a>API-verziók listozása

A támogatott API-verziók készletét adja vissza.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Paraméterek

Nincs (ez a végpont nemverziós).

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

## <a name="instance-metadata"></a>Példány metaadatai

### <a name="get-vm-metadata"></a>Virtuális gép metaadatainak lekérte

Elérhetővé teszi a virtuálisgép-példány fontos metaadatait, beleértve a számítást, a hálózatot és a tárolást. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Paraméterek

| Name | Kötelező vagy nem kötelező | Leírás |
|------|-------------------|-------------|
| `api-version` | Kötelező | A kérés kiszolgálására használt verzió.
| `format` | Nem kötelező* | A válasz formátuma ( `json` vagy `text` ) . *Megjegyzés: Szükség lehet a kérelemparaméterek használata esetén

Ez a végpont támogatja a válaszszűrés [útvonal-paramétereken keresztüli szűrését.](#route-parameters)

#### <a name="response"></a>Reagálás

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Sémalebontás:

**Számítás**

| Adatok | Description | Bevezetett verzió |
|------|-------------|--------------------|
| `azEnvironment` | Az Azure-környezet, amelyben a virtuális gép fut | 2018-10-01
| `customData` | Ez a funkció elavult és le van tiltva. A következővel lett átiratva: `userData` | 2019-02-01
| `evictionPolicy` | Beállítja a [spot virtuális](../articles/virtual-machines/spot-vms.md) gépek kiesésének a beállítását. | 2020-12-01
| `isHostCompatibilityLayerVm` | Megállapítja, hogy a virtuális gép a gazdagép kompatibilitási rétegében fut-e | 2020-06-01
| `licenseType` | A licenc típusa a [Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-benefit) Ez csak a AHB-kompatibilis virtuális gépek esetén van jelen | 2020-09-01
| `location` | A virtuális gép által futtatott Azure-régió | 2017-04-02
| `name` | A virtuális gép neve | 2017-04-02
| `offer` | Információk a virtuálisgép-rendszerképről, és csak az Azure-rendszerképgyűjteményből üzembe helyezett rendszerképekhez érhető el | 2017-04-02
| `osProfile.adminUsername` | Megadja a rendszergazdai fiók nevét | 2020-07-15
| `osProfile.computerName` | A számítógép nevét adja meg | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Megadja, hogy a jelszó-hitelesítés le van-e tiltva. Ez csak Linux rendszerű virtuális gépeken van jelen | 2020-10-01
| `osType` | Linux vagy Windows | 2017-04-02
| `placementGroupId` | [A virtuálisgép-méretezési](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) csoport elhelyezési csoportja | 2017-08-01
| `plan` | [Virtuális](/rest/api/compute/virtualmachines/createorupdate#plan) gép nevét, termékét és közzétevőját tartalmazó terv, ha az egy Azure Marketplace rendszerkép | 2018-04-02
| `platformUpdateDomain` |  [A virtuális gép](../articles/virtual-machines/availability.md) által futtatott tartomány frissítése | 2017-04-02
| `platformFaultDomain` | [A tartalék tartomány,](../articles/virtual-machines/availability.md) amelyben a virtuális gép fut | 2017-04-02
| `priority` | A virtuális gép prioritása. További [információ: Spot virtuális](../articles/virtual-machines/spot-vms.md) gépek | 2020-12-01
| `provider` | A virtuális gép szolgáltatója | 2018-10-01
| `publicKeys` | [A virtuális géphez és](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) az elérési utakhoz rendelt nyilvános kulcsok gyűjteménye | 2018-04-02
| `publisher` | A virtuálisgép-rendszerkép közzétevője | 2017-04-02
| `resourceGroupName` | [A virtuális gép](../articles/azure-resource-manager/management/overview.md) erőforráscsoportja | 2017-08-01
| `resourceId` | Az [erőforrás teljes](/rest/api/resources/resources/getbyid) azonosítója | 2019-03-11
| `sku` | A virtuálisgép-rendszerkép adott termékváltozata | 2017-04-02
| `securityProfile.secureBootEnabled` | Megállapítja, hogy az UEFI biztonságos rendszerindítás engedélyezve van-e a virtuális gépen | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Megállapítja, hogy a virtuális platformmegbízhatósági modul (TPM) engedélyezve van-e a virtuális gépen | 2020-06-01
| `storageProfile` | Lásd alább a Storage-profilt | 2019-06-01
| `subscriptionId` | Azure-előfizetés a virtuális géphez | 2017-08-01
| `tags` | [A](../articles/azure-resource-manager/management/tag-resources.md) virtuális gép címkéi  | 2017-08-01
| `tagsList` | JSON-tömbként formázott címkék az egyszerűbb programozott elemezés érdekében  | 2019-06-04
| `userData` | A virtuális gép kiépítése során vagy után való használatra megadott adatkészlet (Base64 kódolású)  | 2021-01-01
| `version` | A virtuálisgép-rendszerkép verziója | 2017-04-02
| `vmId` | [A virtuális gép](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) egyedi azonosítója | 2017-04-02
| `vmScaleSetName` | [Virtuálisgép-méretezési készlet A](../articles/virtual-machine-scale-sets/overview.md) virtuálisgép-méretezési készlet neve | 2017-12-01
| `vmSize` | [Virtuális gép mérete](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [A virtuális gép rendelkezésre](../articles/availability-zones/az-overview.md) állási zónája | 2017-12-01

**Tárolóprofil**

A virtuális gépek tárolási profilja három kategóriába sorolható: rendszerkép-referencia, operációsrendszer-lemez és adatlemezek.

A rendszerképre hivatkozó objektum az alábbi információkat tartalmazza az operációs rendszer lemezképével kapcsolatban:

| Adatok | Description |
|------|-------------|
| `id` | Erőforrás-azonosító
| `offer` | Platform- vagy Marketplace-rendszerkép ajánlata
| `publisher` | Rendszerkép-közzétevő
| `sku` | Rendszerkép termékváltozata
| `version` | A platform vagy a Marketplace rendszerképének verziója

Az operációsrendszer-lemez objektum a következő információkat tartalmazza a virtuális gép által használt operációsrendszer-lemezről:

| Adatok | Description |
|------|-------------|
| `caching` | Gyorsítótárazás követelményei
| `createOption` | Információk a virtuális gép létrehozási mikéntről
| `diffDiskSettings` | Aphemerális lemez beállításai
| `diskSizeGB` | A lemez mérete GB-ban
| `image`   | Forrásfelhasználói rendszerkép virtuális merevlemeze
| `lun`     | A lemez logikaiegység-száma
| `managedDisk` | Felügyelt lemez paraméterei
| `name`    | Lemez neve
| `vhd`     | Virtuális merevlemez
| `writeAcceleratorEnabled` | Azt határozza meg, hogy a writeAccelerator engedélyezve van-e a lemezen

Az adatlemezek tömbje a virtuális géphez csatolt adatlemezek listáját tartalmazza. Minden adatlemez-objektum a következő információkat tartalmazza:

Adatok | Description |
-----|-------------|
| `caching` | Gyorsítótárazás követelményei
| `createOption` | Információk a virtuális gép létrehozási mikéntről
| `diffDiskSettings` | Aphemerális lemez beállításai
| `diskSizeGB` | A lemez mérete GB-ban
| `encryptionSettings` | A lemez titkosítási beállításai
| `image` | Forrásfelhasználói rendszerkép virtuális merevlemeze
| `managedDisk` | Felügyelt lemez paraméterei
| `name` | Lemez neve
| `osType` | A lemezen található operációs rendszer típusa
| `vhd` | Virtuális merevlemez
| `writeAcceleratorEnabled` | Azt határozza meg, hogy a writeAccelerator engedélyezve van-e a lemezen

**Hálózat**

| Adatok | Description | Bevezetett verzió |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | A virtuális gép helyi IPv4-címe | 2017-04-02
| `ipv4.publicIpAddress` | A virtuális gép nyilvános IPv4-címe | 2017-04-02
| `subnet.address` | A virtuális gép alhálózati címe | 2017-04-02
| `subnet.prefix` | Alhálózati előtag, 24. példa | 2017-04-02
| `ipv6.ipAddress` | A virtuális gép helyi IPv6-címe | 2017-04-02
| `macAddress` | Virtuális gép mac-címe | 2017-04-02

### <a name="get-user-data"></a>Felhasználói adatok lekérte

Új virtuális gép létrehozásakor megadhatja a virtuális gép kiépítése során vagy után használni kívánt adatkészletet, és lekérheti azokat az IMDS-en keresztül. A felhasználói adatok beállítását a gyorsindítási sablonnal itt [állíthatja be.](https://aka.ms/ImdsUserDataArmTemplate) Az alábbi minta bemutatja, hogyan lehet lekérni az adatokat az IMDS-ről.

> [!NOTE]
> Ez a funkció verzióval érhető el, és az Azure platform frissítésétől függ, amely jelenleg is használatban van, és előfordulhat, hogy még nem érhető el minden `2021-01-01` régióban.

> [!NOTE]
> Biztonsági figyelmeztetés: Az IMDS a virtuális gépen található összes alkalmazás számára nyitva van, a bizalmas adatokat nem szabad a felhasználói adatokba helyezni.


#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

---


#### <a name="sample-1-tracking-vm-running-on-azure"></a>1. minta: Az Azure-ban futó virtuális gépek nyomon követése

Szolgáltatóként szükség lehet a szoftvert futtató virtuális gépek számának nyomon követésére, vagy olyan ügynökökre, amelyek nyomon követik a virtuális gép egyediségét. A virtuális gép egyedi azonosítójának lekért értékhez használja a virtuális gép `vmId` Instance Metadata Service.

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

#### <a name="sample-2-placement-of-different-data-replicas"></a>2. minta: Különböző adatreplikák elhelyezése

Bizonyos esetekben kiemelten fontos a különböző adatreplikák elhelyezése. Előfordulhat például, hogy a [HDFS-replika elhelyezése](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) vagy a tárolók vezénylőn keresztüli elhelyezése megköveteli, hogy tudja, és a virtuális gép [](https://kubernetes.io/docs/user-guide/node-selection/) `platformFaultDomain` `platformUpdateDomain` fut.
A példányokhoz [Availability Zones](../articles/availability-zones/az-overview.md) is használhatja ezeket a döntéseket.
Ezek az adatok közvetlenül az IMDS-en keresztül is lekérdezhetőek.

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

#### <a name="sample-3-get-vm-tags"></a>3. minta: Virtuálisgép-címkék lekérte

A virtuálisgép-címkék a példány API-ját tartalmazzák a instance/compute/tags végpont alatt.
Előfordulhat, hogy címkéket alkalmaztak az Azure-beli virtuális gépre, hogy logikailag rendszerezze őket egy taxonómiába. A virtuális géphez rendelt címkék az alábbi kérés használatával kérhetők le.

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

A `tags` mező egy pontosvesszővel elválasztott címkékkel elválasztott sztring. Ez a kimenet problémát jelent, ha maguk a címkék pontosvesszőket használnak. Ha egy elemző programozott módon kinyeri a címkéket, akkor a mezőre kell `tagsList` támaszkodnia. A mező egy JSON-tömb, amely nem tartalmaz elválasztó karaktert, és így könnyebben `tagsList` elemez. A virtuális géphez rendelt tagsList az alábbi kérés használatával kérhető le.

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


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>4. minta: További információ a virtuális gépről a támogatási eset során

Szolgáltatóként előfordulhat, hogy kap egy támogatási hívást, amelyben további információkat szeretne tudni a virtuális gépről. Ha megkéri az ügyfelet, hogy ossza meg a számítási metaadatokat, az alapvető információkat nyújthat a támogatási szakembernek, hogy tudja, milyen típusú virtuális gép található az Azure-ban.

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
> A válasz egy JSON-sztring. Az alábbi példaválasz az olvashatóság érdekében meglehetősen ki van nyomtatva.

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

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>5. minta: Szerezze be azt az Azure-környezetet, amelyben a virtuális gép fut

Az Azure számos szuverén felhővel rendelkezik, például a [Azure Government.](https://azure.microsoft.com/overview/clouds/government/) Néha az Azure-környezetre is szükség van a futásidejű döntések meghozatalához. Az alábbi minta bemutatja, hogyan érheti el ezt a viselkedést.

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

A felhő és az Azure-környezet értékei itt vannak felsorolva.

| Felhőbeli | Azure-környezet |
|-------|-------------------|
| [Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Germany](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>6. minta: Hálózati adatok lekérése

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

#### <a name="sample-7-retrieve-public-ip-address"></a>7. minta: Nyilvános IP-cím lekérése

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Tanúsított adatok

### <a name="get-attested-data"></a>Tanúsítvált adatok lekérte

Az IMDS segítségével garantálható, hogy a megadott adatok az Azure-ból érkeziknek. A Microsoft aláírja ezen információk egy részét, így ön meggyőződhet arról, hogy a Azure Marketplace rendszerképet futtatja az Azure-ban.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Paraméterek

| Name | Kötelező vagy nem kötelező | Leírás |
|------|-------------------|-------------|
| `api-version` | Kötelező | A kérés kiszolgálására használt verzió.
| `nonce` | Választható | Egy 10 jegyű sztring, amely kriptográfiai nonce-ként szolgál. Ha nincs megadva érték, az IMDS az aktuális UTC időbélyeget használja.

#### <a name="response"></a>Reagálás

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> Az IMDS gyorsítótárazási mechanizmusa miatt a rendszer egy korábban gyorsítótárazott nonce értéket ad vissza.

Az aláírásblob a [dokumentum pkcs7-aláírt](https://aka.ms/pkcs7)verziója. Ez tartalmazza az aláíráshoz használt tanúsítványt, valamint bizonyos virtuálisgép-specifikus adatokat.

Az Azure Resource Manager használatával létrehozott virtuális gépek esetén a dokumentum tartalmazza a , , , és adatokat a dokumentum létrehozásához és lejárati dátumhoz, valamint a rendszerkép `vmId` `sku` `nonce` `subscriptionId` `timeStamp` tervinformációit. A tervinformációk csak a rendszerképekhez Azure Marketplace ki.

A klasszikus üzembe helyezési modellel létrehozott virtuális gépek esetében csak a lesz `vmId` kitöltve. Kinyerheti a tanúsítványt a válaszból, és annak használatával megerősítheti, hogy a válasz érvényes, és az Azure-ból érkezik.

A dekódolt dokumentum a következő mezőket tartalmazza:

| Adatok | Description | Bevezetett verzió |
|------|-------------|--------------------|
| `licenseType` | A licenc típusa a [Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-benefit) Ez csak a AHB-kompatibilis virtuális gépek esetén van jelen. | 2020-09-01
| `nonce` | A kérelemhez opcionálisan meg lehet adni egy sztringet. Ha nem lett megadva, a egyezményes világidő `nonce` időbélyeg lesz használva. | 2018-10-01
| `plan` | A [Azure Marketplace Rendszerkép csomag.](/rest/api/compute/virtualmachines/createorupdate#plan) Tartalmazza a csomagazonosítót (név), a termékképet vagy az ajánlatot (termék) és a közzétevő azonosítóját (közzétevő). | 2018-10-01
| `timestamp.createdOn` | Az aláírt dokumentum létrejöttének UTC-időbélyege | 2018-20-01
| `timestamp.expiresOn` | Az aláírt dokumentum lejáratának UTC-időbélyege | 2018-10-01
| `vmId` | [A virtuális gép](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) egyedi azonosítója | 2018-10-01
| `subscriptionId` | Azure-előfizetés a virtuális géphez | 2019-04-30
| `sku` | A virtuálisgép-rendszerkép adott termékváltozata | 2019-11-01

> [!NOTE]
> Klasszikus (nem Azure Resource Manager) virtuális gépek esetén csak a vmId lesz kitöltve.

Példadokumentum:
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

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>1. minta: Annak ellenőrzése, hogy a virtuális gép fut-e az Azure-ban

A Azure Marketplace szeretnék biztosítani, hogy a szoftverük csak az Azure-ban fusson. Ha valaki a vhD-t egy helyszíni környezetbe másolja, a szállítónak képesnek kell lennie észlelni. Az IMDS-ekkel ezek a szállítók olyan aláírt adatokat kaphatnak, amelyek csak az Azure-tól kaphatnak választ.

> [!NOTE]
> Ehhez a mintához telepíteni kell a jq segédprogramot.

**Érvényesítés**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Ellenőrizze, hogy az aláírás a Microsoft Azure és ellenőrizze, hogy a tanúsítványlánc tartalmaz-e hibákat.

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

Ellenőrizze, hogy az aláírás a Microsoft Azure-e, és ellenőrizze, hogy a tanúsítványlánc tartalmaz-e hibákat.

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
> Az IMDS gyorsítótárazási mechanizmusa miatt a rendszer egy korábban gyorsítótárazott értéket `nonce` ad vissza.

Az `nonce` aláírt dokumentumban található érték akkor hasonlítható össze, ha megadott egy paramétert a `nonce` kezdeti kérésben.

> [!NOTE]
> A nyilvános felhő és az egyes szuverén felhők tanúsítványa eltérő lesz.

| Felhőbeli | Tanúsítvány |
|-------|-------------|
| [Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure Germany](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> Előfordulhat, hogy a tanúsítványok nem egyeznek pontosan a nyilvános `metadata.azure.com` felhővel. Ezért a tanúsítványérvényesítésnek minden altartományból engedélyeznie kell egy `.metadata.azure.com` köznevet.

Ha a köztes tanúsítvány az érvényesítés során hálózati korlátozások miatt nem tölthető le, rögzítheti a köztes tanúsítványt. Az Azure átveszi a tanúsítványokat, ami szabványos PKI-eljárás. A visszaállításkor frissítenie kell a rögzített tanúsítványokat. A köztes tanúsítvány frissítésének tervezett módosításakor frissül az Azure blogja, és értesítést kap az Azure-ügyfelekről. 

A köztes tanúsítványokat a [PKI-adattárban találja.](https://www.microsoft.com/pki/mscorp/cps/default.htm) Az egyes régiók köztes tanúsítványai eltérőek is lehet.

> [!NOTE]
> A tanúsítvány köztes tanúsítványa Azure China 21Vianet Baltimore helyett a DigiCert globális legfelső szintű hitelesítésszolgáltatótól lesz.
Ha az Azure China köztes tanúsítványait a legfelső szintű lánc hitelesítésszolgáltatói módosítása részeként rögzítette, a köztes tanúsítványokat frissíteni kell.


## <a name="managed-identity"></a>Felügyelt identitás

A rendszer által hozzárendelt felügyelt identitás engedélyezhető a virtuális gépen. Egy vagy több felhasználó által hozzárendelt felügyelt identitást is hozzárendelhet a virtuális géphez.
Ezután jogkivonatokat kérhet a felügyelt identitásokhoz az IMDS-ről. Ezekkel a jogkivonatokkal más Azure-szolgáltatásokkal, például az Azure Key Vault.

A funkció engedélyezésének részletes lépéseiért lásd: [Hozzáférési jogkivonat beszerzése.](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

## <a name="load-balancer-metadata"></a>Load Balancer metaadatok
Amikor egy Azure-beli virtuális gép vagy virtuálisgép-készlet példányait egy Azure standard Load Balancer mögé helyezzük, az IMDS használatával lekérheti a terheléselosztással és a példányokkal kapcsolatos metaadatokat. További információ: [Terheléselosztási információk lekérése.](../articles/load-balancer/instance-metadata-service-load-balancer.md)

## <a name="scheduled-events"></a>Ütemezett események
Az ütemezett események állapotát az IMDS használatával szerezheti be. Ezután a felhasználó megadhat egy műveletkészletet, amely ezeken az eseményeken fog futni. További információkért lásd a [Linux vagy](../articles/virtual-machines/linux/scheduled-events.md) a [Windows ütemezett eseményeit.](../articles/virtual-machines/windows/scheduled-events.md)


## <a name="sample-code-in-different-languages"></a>Mintakód különböző nyelveken

Az alábbi táblázatban az IMDS virtuális gépen belüli különböző nyelveken való hívásának mintáit soroljuk fel:

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

Ha nem található adatelem vagy helytelenül formázott kérelem, a Instance Metadata Service HTTP-hibákat ad vissza. Például:

| HTTP-állapotkód | Ok |
|------------------|--------|
| `200 OK` | A kérés sikeres volt.
| `400 Bad Request` | Hiányzó `Metadata: true` fejléc vagy hiányzó paraméter `format=json` levélcsomópont lekérdezésekor
| `404 Not Found` | A kért elem nem létezik
| `405 Method Not Allowed` | A HTTP-metódus (ige) nem támogatott a végponton.
| `410 Gone` | Újrapróbálkozás egy idő után legfeljebb 70 másodpercig
| `429 Too Many Requests` | Túllépte [az](#rate-limiting) API-sebességkorlátokat
| `500 Service Error` | Újrapróbálkozás egy idő után

## <a name="frequently-asked-questions"></a>Gyakori kérdések

- A következő hibaüzenet jelenik `400 Bad Request, Required metadata header not specified` meg: . Mit jelent ez?
  - Az IMDS megköveteli, hogy a `Metadata: true` fejlécet át kell a kérésben. Ennek a fejlécnek a REST-hívásban való átadása lehetővé teszi az IMDS-hez való hozzáférést.

- Miért nem kapok számítási adatokat a virtuális gépemről?
  - Az IMDS jelenleg csak a létrehozott példányokat Azure Resource Manager.

- Egy ideje már létrehoztam a Azure Resource Manager a virtuális gépemet. Miért nem látom a számítási metaadatok adatait?
  - Ha 2016 szeptembere után hozta létre a virtuális gépet, adjon hozzá egy [címkét,](../articles/azure-resource-manager/management/tag-resources.md) amely elkezdi látni a számítási metaadatokat. Ha 2016 szeptembere előtt hozta létre a virtuális gépet, a metaadatok frissítéséhez adjon hozzá vagy távolítson el bővítményeket vagy adatlemezeket a virtuálisgép-példányból.

- A felhasználói adatok ugyanazok, mint az egyéni adatok?
  - A felhasználói adatok az egyéni adatokhoz hasonló funkciókat kínálnak, így a saját metaadatait is át lehet adni a virtuálisgép-példánynak. A különbség az, hogy a felhasználói adatokat az IMDS-en keresztül lehet lekérni, és a virtuálisgép-példány teljes élettartama során állandóak maradnak. A meglévő egyéni adatok funkció továbbra is működni fog a cikkben [leírtak szerint.](https://docs.microsoft.com/azure/virtual-machines/custom-data) Egyéni adatokat azonban csak a helyi rendszermappán keresztül kaphat, az IMDS-en keresztül nem.

- Miért nem látom az új verzióhoz feltöltve az összes adatot?
  - Ha 2016 szeptembere után hozta létre a virtuális gépet, adjon hozzá egy [címkét,](../articles/azure-resource-manager/management/tag-resources.md) amely elkezdi látni a számítási metaadatokat. Ha 2016 szeptembere előtt hozta létre a virtuális gépet, a metaadatok frissítéséhez adjon hozzá vagy távolítson el bővítményeket vagy adatlemezeket a virtuálisgép-példányhoz.

- Miért jelenik meg a vagy a `500 Internal Server Error` `410 Resource Gone` hibaüzenet?
  - Próbálja újra a kérést. További információ: Átmeneti [hibák kezelése.](/azure/architecture/best-practices/transient-faults) Ha a probléma továbbra is fennáll, hozzon létre egy támogatási problémát a Azure Portal virtuális géphez.

- Működik ez a virtuálisgép-méretezésihalmaz-példányok esetén?
  - Igen, az IMDS elérhető a virtuálisgép-méretezésikészlet-példányokhoz.

- Frissítettem a címkéket a virtuálisgép-méretezési készletekben, de nem jelennek meg a példányban (az egypéldányos virtuális gépektől eltérően). Valami nem megfelelőt végzek?
  - A virtuálisgép-méretezési készletek címkéi jelenleg csak újraindításkor, rendszerként való rendszerként való visszaváltáskor vagy a példány lemezének módosításakor adatokat mutatnak a virtuális gépnek.

- Miért nem látom a virtuális gép termékváltozat-információit a részletek `instance/compute` között?
  - Az egyéni rendszerképek Azure Marketplace azure platform nem őrzi meg az egyéni rendszerkép termékváltozat-adatait és az egyéni rendszerképből létrehozott virtuális gépek adatait. Ez a kialakításból ered, és ezért nem a virtuális gép részleteiben van `instance/compute` kivetve.

- Miért van a kérésem időkorrekta a szolgáltatás hívása miatt?
  - A metaadat-hívásokat a virtuális gép elsődleges hálózati kártyájára rendelt elsődleges IP-címről kell kezdeményezni. Ha módosította az útvonalakat, a virtuális gép helyi útválasztási táblázatában lennie kell a 169.254.169.254/32 címnek.

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. Memóriaképet a helyi útválasztási táblázatból, és keresse meg az IMDS bejegyzést. Például:
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
    1. Ellenőrizze, hogy létezik-e útvonal a számára, és jegyezze fel a megfelelő hálózati `169.254.169.254` adaptert `172.16.69.7` (például).
    1. A mac (fizikai) cím megírása után memóriaképet kell kiírással keresse meg az illesztő konfigurációját, amely az útválasztási táblázatban hivatkozott illesztőnek felel meg.
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
    1. Győződjön meg arról, hogy az interfész megfelel a virtuális gép elsődleges hálózati adapterének és elsődleges IP-címének. Az elsődleges hálózati adaptert és IP-címet megkeresheti a hálózati konfigurációban a Azure Portal, vagy megkeresheti az Azure CLI-val. Jegyezze fel a magánhálózati IP-címeket (és a MAC-címet, ha a CLI-t használja). Példa a PowerShell parancssori felületre:
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
    1. Ha nem egyeznek, frissítse az útválasztási táblázatot úgy, hogy az elsődleges hálózati adaptert és az IP-címet célozza meg.

    ### <a name="linux"></a>[Linux](#tab/linux/)

    1. Írja ki a helyi útválasztási táblázatot egy paranccsal, például és keresse meg az `netstat -r` IMDS bejegyzést (például):
        ```console
        ~$ netstat -r
        Kernel IP routing table
        Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
        default         _gateway        0.0.0.0         UG        0 0          0 eth0
        168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
        169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
        172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
        ```
    1. Ellenőrizze, hogy létezik-e útvonal a számára, és jegyezze fel a megfelelő hálózati `169.254.169.254` adaptert (pl. `eth0` ).
    1. Jegyezze fel az útválasztási táblázatban a megfelelő interfész csatoló konfigurációját (vegye figyelembe, hogy a konfigurációs fájl pontos neve eltérő lehet)
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
    1. Ha dinamikus IP-címet használ, jegyezze fel a MAC-címet. Ha statikus IP-címet használ, jegyezze fel a felsorolt IP-cím(ek)et és/vagy MAC-címet.
    1. Ellenőrizze, hogy az interfész megfelel-e a virtuális gép elsődleges hálózati adapterének és elsődleges IP-címének. Az elsődleges hálózati adaptert és IP-címet megkeresheti a hálózati konfigurációban a Azure Portal, vagy megkeresheti az Azure CLI-val. Jegyezze fel a magánhálózati IP-címeket (és a MAC-címet, ha a CLI-t használja). Példa a PowerShell parancssori felületre:
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
    1. Ha nem egyeznek, frissítse úgy az útválasztási táblázatot, hogy az elsődleges hálózati adaptert/IP-címet célozza meg.

    ---

- Feladatátvételi fürtszolgáltatás a Windows Serveren
  - Feladatátvételi fürtszolgáltatással lekérdezi az IMDS-t, néha szükség van egy útvonal hozzáadására az útválasztási táblához. Ezt a következőképpen teheti meg:

    1. Nyisson meg egy parancssort rendszergazdai jogosultságokkal.

    1. Futtassa a következő parancsot, és jegyezze fel az IPv4 útválasztási táblázat hálózati céljához ( `0.0.0.0` ) használt adapter címét.

    ```bat
    route print
    ```

    > [!NOTE]
    > Az alábbi példakimenet egy Windows Server rendszerű virtuális gépről származik, amelynél engedélyezve van a feladatátvevő fürt. Az egyszerűség kedvéért a kimenet csak az IPv4 útválasztási táblázatot tartalmazza.

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

    Futtassa a következő parancsot, és használja a hálózati célhely () adapterének címét, amely ebben a `0.0.0.0` példában a ( `10.0.1.10` ) .

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## <a name="support"></a>Támogatás

Ha több kísérlet után nem tud metaadat-választ kapni, létrehozhat egy támogatási problémát a Azure Portal.

## <a name="product-feedback"></a>Termékkel kapcsolatos visszajelzés

Termékkel kapcsolatos visszajelzéseket és ötleteket adhat a felhasználói visszajelzési csatornánknak a Virtual Machines > Instance Metadata Service [alatt](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627)

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési jogkivonat beszerzése a virtuális géphez](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Linuxhoz ütemezett események](../articles/virtual-machines/linux/scheduled-events.md)

- [Ütemezett események Windowshoz](../articles/virtual-machines/windows/scheduled-events.md)
