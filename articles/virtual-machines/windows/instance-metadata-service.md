---
title: Az Azure Instance Metadata szolgáltatás |} A Microsoft Docs
description: RESTful interfészről Windows virtuális gép számítási, hálózati és események a közelgő karbantartásokról információt szeretne kapni.
services: virtual-machines-windows
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/28/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: cc2a78dd547681a4b20fea14cd8cd7f4fd9c2df5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711232"
---
# <a name="azure-instance-metadata-service"></a>Az Azure Instance Metadata szolgáltatás

Az Azure Instance Metadata szolgáltatás fut, amelyek segítségével kezelheti és konfigurálhatja a virtuális gépek virtuálisgép-példányok információkat biztosít.
Ide tartoznak a Termékváltozat, a hálózati konfiguráció vagy az események a közelgő karbantartásokról. Milyen típusú információkat érhető el a további információkért lásd: [metaadat-kategóriák](#instance-metadata-data-categories).

Az Azure Instance Metadata szolgáltatás REST-végponton keresztül létrehozott IaaS virtuális gépek számára elérhető a [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
A végpont egy jól ismert nem átirányítható IP-címen érhető el (`169.254.169.254`), amelyek elérhetők csak a virtuális gépen.

> [!IMPORTANT]
> Ez a szolgáltatás **általánosan elérhető** az összes Azure-régióban.  Rendszeresen megkapja az új információ a virtuálisgép-példányok elérhetővé a frissítéseket. Ezen a lapon tükrözi a legfrissebb [adatkategóriák](#instance-metadata-data-categories) érhető el.

## <a name="service-availability"></a>Elérhető szolgáltatások

A szolgáltatás általánosan elérhető Azure-régióban érhető el. Lehet, hogy nem minden API-verzió az összes Azure-régióban érhető el.

Régiók                                        | Rendelkezésre állási?                                 | Támogatott verziók
-----------------------------------------------|-----------------------------------------------|-----------------
[Az összes általánosan elérhető globális Azure-régiók](https://azure.microsoft.com/regions/)     | Mindenki számára elérhető   | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure China](https://www.azure.cn/)                                                           | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01

Ez a táblázat frissítésekor a szolgáltatásfrissítések, és vagy új támogatott verziók érhetők el.

Próbálja ki a Instance Metadata szolgáltatás, hozzon létre egy virtuális gépről [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) vagy a [az Azure portal](https://portal.azure.com) az újabb régiókban, és kövesse az alábbi példák.

## <a name="usage"></a>Használat

### <a name="versioning"></a>Verziókezelés

A Instance Metadata szolgáltatás verziószámmal. Verziók a következők kötelező, és a globális Azure-ban a jelenlegi verzió `2018-10-01`. Aktuális verziók a következők (2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01)

Növelése újabb verziók hozzáadásakor, régebbi verzióit továbbra is elérhető kompatibilitás, ha a parancsfájlok adott adatformátumok a célnyelven függőségekkel rendelkezik.

Ha nincs megadva verziószám, hibát ad vissza a legújabb támogatott verziók listáját.

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa választ, Igen nyomtatott az olvashatóság érdekében.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Válasz**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>Fejlécek használata

Előfordulhat, hogy a Instance Metadata szolgáltatás, meg kell adnia a fejléc `Metadata: true` annak érdekében, hogy a kérés nem volt szándékos átirányítva.

### <a name="retrieving-metadata"></a>Metaadatok beolvasása

Példány-metaadat érhető el a futó virtuális gépek létrehozása és kezelése az alábbi [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Hozzáférés az összes adatkategóriákat a virtuálisgép-példányt használja a következő kérelmet:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Az összes példány metaadatok lekérdezés-és nagybetűk.

### <a name="data-output"></a>Kimeneti adatok

Alapértelmezés szerint a Instance Metadata szolgáltatás adatokat ad vissza JSON formátumban (`Content-Type: application/json`). Azonban más API-k adja vissza, adatok különböző formátumokban.
Az alábbi táblázat a más API-kkal támogathatják adatformátumok a célnyelven eszköztáblára.

API | Alapértelmezett adatformátum | További formátumok
--------|---------------------|--------------
/instance | JSON | szöveg
/scheduledevents | JSON | nincs
/attested | JSON | nincs

Egy nem alapértelmezett válaszformátum eléréséhez adja meg a kért formátum a kérelem lekérdezési karakterlánc paramétereként. Példa:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Levél csomópontok a `format=json` nem működik. A lekérdezések `format=text` explicit módon kell megadni az alapértelmezett formátum json-e.

### <a name="security"></a>Biztonság

Instance Metadata szolgáltatás végpont elérésére csak a futó virtuálisgép-példány nem átirányítható IP-címen belül. Emellett a kérelmet egy `X-Forwarded-For` fejléc elutasította a szolgáltatást.
Kérelmek is tartalmaznia kell egy `Metadata: true` fejléc, győződjön meg arról, hogy a tényleges kérelmező közvetlenül: tervezett és nem véletlen átirányítás része.

### <a name="error"></a>Hiba

Ha nem található egy adatelemre, vagy hibás kérés, a Instance Metadata szolgáltatás standard HTTP-hibák adja vissza. Példa:

HTTP-állapotkód | Ok
----------------|-------
200 OK |
400 Hibás kérés | Hiányzó `Metadata: true` fejléc, vagy hiányzik a formátum Levélcsomópont lekérdezésekor
404 – Nem található | A kért elem nem létezik.
405 Metoda není Povolena | Csak `GET` és `POST` kérelmek támogatottak.
429 túl sok kérelem | Az API jelenleg legfeljebb 5 lekérdezések másodpercenként
500 Service Error     | Némi várakozás után próbálkozzon újra

### <a name="examples"></a>Példák

> [!NOTE]
> Minden API-válasz JSON-sztringek. Az összes, a következő példa válaszok olyan pretty nyomtatott az olvashatóság érdekében.

#### <a name="retrieving-network-information"></a>Hálózati adatok beolvasása

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa választ, Igen nyomtatott az olvashatóság érdekében.

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

#### <a name="retrieving-public-ip-address"></a>Nyilvános IP-cím beolvasása

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Minden metaadat-példány beolvasása

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa választ, Igen nyomtatott az olvashatóság érdekében.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
    "location": "westus",
    "name": "jubilee",
    "offer": "Windows-10",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftWindowsDesktop",
    "resourceGroupName": "myrg",
    "sku": "rs4-pro",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "17134.345.59",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Windows virtuális gép metaadatainak beolvasása

**Kérés**

Példány-metaadat beolvasható a Windows-n keresztül a `curl` program:

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2018-10-01 | select -ExpandProperty Content
```

Vagy a `Invoke-RestMethod` PowerShell-parancsmagot:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2018-10-01 -Method get
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa választ, Igen nyomtatott az olvashatóság érdekében.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftSQLServer",
    "resourceGroupName": "myrg",
    "sku": "Enterprise",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmScaleSetName": "",
    "vmSize": "Standard_DS2",
    "zone": ""
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Példány-metaadat adatkategóriák

Az alábbi adatkategóriákat a Instance Metadata szolgáltatás keresztül érhetők el:

Adatok | Leírás | Bevezetett verzió
-----|-------------|-----------------------
azEnvironment | Ahol a virtuális gép fut az Azure-környezet | 2018-10-01
location | Azure-régió, a virtuális gép fut. | 2017-04-02
név | A virtuális gép neve | 2017-04-02
az ajánlat | A Virtuálisgép-lemezkép információkat kínálnak. Ez az érték csak megtalálható az Azure lemezkép-katalógus-ről üzembe helyezett rendszerképeket. | 2017-04-02
publisher | A Virtuálisgép-lemezkép közzétevője | 2017-04-02
termékváltozat | Adott Termékváltozat a Virtuálisgép-lemezkép | 2017-04-02
version | A Virtuálisgép-lemezkép verziója | 2017-04-02
osType | Linux vagy Windows | 2017-04-02
platformUpdateDomain |  [Frissítési tartomány](manage-availability.md) a virtuális gép fut. | 2017-04-02
platformFaultDomain | [Tartalék tartomány](manage-availability.md) a virtuális gép fut. | 2017-04-02
vmId | [Egyedi azonosító](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) a virtuális gép | 2017-04-02
vmSize | [Virtuális gép mérete](sizes.md) | 2017-04-02
subscriptionId | A virtuális gép Azure-előfizetés | 2017-08-01
tags | [A címkék](../../azure-resource-manager/resource-group-using-tags.md) a virtuális gép  | 2017-08-01
resourceGroupName | [Erőforráscsoport](../../azure-resource-manager/resource-group-overview.md) a virtuális gép | 2017-08-01
placementGroupId | [Elhelyezési csoport](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) a virtuálisgép-méretezési csoport beállítása | 2017-08-01
csomag | [Terv](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) a virtuális gép az Azure Piactéri lemezkép neve, a termékek és a közzétevő tartalmaz | 2018-04-02
szolgáltató | A virtuális gép szolgáltató | 2018-10-01
publicKeys | [Nyilvános kulcsok gyűjteményét](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) rendelve a virtuális gép és az elérési út | 2018-04-02
vmScaleSetName | [Virtuálisgép-méretezési csoport neve](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) a virtuálisgép-méretezési csoport beállítása | 2017-12-01
zóna | [Rendelkezésre állási zónában](../../availability-zones/az-overview.md) a virtuális gép | 2017-12-01
ipv4/privateIpAddress | A virtuális gép helyi IPv4-cím | 2017-04-02
ipv4/publicIpAddress | A virtuális gép nyilvános IPv4-cím | 2017-04-02
subnet/address | Alhálózati cím a virtuális gép | 2017-04-02
alhálózat/előtag | Alhálózati előtag, például 24 | 2017-04-02
ipv6/ipAddress | A virtuális gép helyi IPv6-cím | 2017-04-02
macAddress | Virtuális gép mac-cím | 2017-04-02
scheduledevents | Lásd: [ütemezett események](scheduled-events.md) | 2017-08-01
identity | Felügyelt identitások az Azure-erőforrásokhoz. Lásd: [hozzáférési jogkivonat beszerzése](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
attested | Lásd: [igazolt adatok](#attested-data) | 2018-10-01

## <a name="attested-data"></a>Igazolt adatok

Instance Metadata válaszol 169.254.169.254 a http-végponton. Instance Metadata szolgáltatás által kiszolgált forgatókönyv részét, hogy garantálja, hogy az Azure-ból válaszolt az adatok várható. Ez az információ része tudjuk beléptetni, így a marketplace-rendszerképek arról, hogy a rendszerképet az Azure-ban.

### <a name="example-attested-data"></a>Példa igazolt adatok

 > [!NOTE]
> Minden API-válasz JSON-sztringek. A rendszer a következő példa válaszok pretty nyomtatott az olvashatóság érdekében.

 **Kérés**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

API-verzió kötelező mező pedig az adatok igazolt támogatott verzió 2018-10-01.
Egyszeri egy nem kötelező a megadott 10 számjegyű karakterlánc. Egyszeri segítségével nyomon követheti a kérelmet, és ha nincs megadva, a válasz kódolású karakterlánc az aktuális UTC időbélyeget adja vissza.

 **Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa választ, Igen nyomtatott az olvashatóság érdekében.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Az aláírás blob van egy [pkcs7](https://aka.ms/pkcs7) aláírt dokumentum verzióját. A létrehozása és a dokumentum és a tervet a kép lejárati együtt a virtuális gép adatait, például vmId, egyszeri, időbélyegző aláírására szolgáló tanúsítvány tartalmazza. A terv adatainak Azure piaci helyen lemezképek csak fel van töltve. A tanúsítvány a válasz kinyert is, és a rendszer azt ellenőrzi, hogy a válasz érvényes, és az Azure-ból várható.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Windows virtuális gép metaadatainak igazolt beolvasása

 **Kérés**

Példány-metaadat-n keresztül a PowerShell-segédprogram beolvasható a Windows `curl`:

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Vagy a `Invoke-RestMethod` parancsmagot:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

API-verzió kötelező mező pedig az adatok igazolt támogatott verzió 2018-10-01.
Egyszeri egy nem kötelező a megadott 10 számjegyű karakterlánc. Egyszeri segítségével nyomon követheti a kérelmet, és ha nincs megadva, a válasz kódolású karakterlánc az aktuális UTC időbélyeget adja vissza.

 **Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa választ, Igen nyomtatott az olvashatóság érdekében.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Az aláírás blob van egy [pkcs7](https://aka.ms/pkcs7) aláírt dokumentum verzióját. A létrehozása és a dokumentum és a tervet a kép lejárati együtt a virtuális gép adatait, például vmId, egyszeri, időbélyegző aláírására szolgáló tanúsítvány tartalmazza. A terv adatainak Azure piaci helyen lemezképek csak fel van töltve. A tanúsítvány a válasz kinyert is, és a rendszer azt ellenőrzi, hogy a válasz érvényes, és az Azure-ból várható.

## <a name="example-scenarios-for-usage"></a>Példa használati forgatókönyvek  

### <a name="tracking-vm-running-on-azure"></a>Az Azure-ban futó virtuális gép nyomon követése

Szolgáltatóként nyomon követheti a szoftvereket futtató virtuális gépek számát, illetve szeretne nyomon követni a virtuális gép egyedisége ügynökök lehet szükség. Hogy egyedi azonosító beszerzése egy virtuális géphez, használja a `vmId` Instance Metadata szolgáltatás mezőt.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Válasz**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Tárolók elhelyezése, adatpartíciókon alapuló hibák/frissítési tartomány 

Az egyes forgatókönyvek, különböző adatok replikáit elhelyezésére van elsődleges fontosságú. Például [HDFS replika elhelyezésének](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) vagy keresztül tároló elhelyezése egy [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) előfordulhat, hogy szükséges tudni a `platformFaultDomain` és `platformUpdateDomain` a virtuális gép fut-e.
Is [rendelkezésre állási zónák](../../availability-zones/az-overview.md) a példányok ezen döntések.
Ezeket az adatokat közvetlenül a Instance Metadata szolgáltatás használatával kérdezheti le.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Válasz**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>További információk beszerzése a virtuális gépről a támogatási eset során

Szolgáltatóként előfordulhat, hogy kap egy támogatási hívás hol szeretné tudni, hogy a virtuális gép további információt. A számítási metaadatok megosztani az ügyfél kéri alapvető tudnivalók az Azure-beli virtuális gép milyen professzionális támogatási információkat nyújtanak. 

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa választ, Igen nyomtatott az olvashatóság érdekében.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Azon Azure-környezet lekérése, amelyben a virtuális gép fut

Az Azure rendelkezik a különböző szuverén felhők például [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Néha szüksége a Azure-környezet néhány futásidejű döntéseket hozhat. A következő minta bemutatja, hogyan érheti el ezt a viselkedést.

**Kérés**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Válasz**
```bash
AZUREPUBLICCLOUD
```

### <a name="getting-the-tags-for-the-vm"></a>A címkék beolvasása a virtuális gép

A címkék előfordulhat, hogy alkalmazása az Azure virtuális géphez logikailag szervezés érdekében egy besorolást. Az alábbi kérelem használatával lehet beolvasni a virtuális géphez hozzárendelt címkék.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Válasz**

```text
Department:IT;Environment:Test;Role:WebRole
```

### <a name="validating-that-the-vm-is-running-in-azure"></a>Annak ellenőrzése, hogy a virtuális gép fut-e az Azure-ban

 Marketplace-en szállítók szeretne győződjön meg arról, hogy a szoftvert csak az Azure-ban futtatni. Ha valaki másolja a VHD-t a helyszíni, akkor azok kell azt észleli, hogy lehetővé teszi. Példány-metaadat Service Marketplace-be irányuló hívás szállítók juthat aláírt adatokat, amely garantálja a választ csak az Azure-ból.
**Kérés**
> [!NOTE]
> Jq telepíteni kell.

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2018-10-01 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Válasz**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34"
}
```

Adatok | Leírás
-----|------------
egyszeri | Felhasználók megadott karakterlánc nem kötelező, a kérelem. Ha nincs egyszeri lett megadva a kérelemben szereplő, a rendszer visszaadja-e az aktuális UTC-időbélyeg
csomag | [Csomag](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) egy virtuális Géphez, az Azure Marketplace-lemezkép van, neve, termék és közzétevő tartalmaz.
timestamp/createdOn | Az időbélyeg, amely az első aláírt dokumentum létrehozása
timestamp/expiresOn | Az időbélyeg az aláírt dokumentum jár le
vmId |  [Egyedi azonosító](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) a virtuális gép

#### <a name="verifying-the-signature"></a>Az aláírás ellenőrzéséhez

Miután a fenti aláírás, ellenőrizheti, hogy van-e az aláírás a Microsoft. Emellett ellenőrizheti a köztes tanúsítványt és a tanúsítványlánc.

> [!NOTE]
> A tanúsítvány a nyilvános felhő és a szuverén felhő eltérő lesz.

 Régiók | Tanúsítvány
---------|-----------------
[Az összes általánosan elérhető globális Azure-régiók](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure China](https://www.azure.cn/)                                                           | metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

### <a name="failover-clustering-in-windows-server"></a>A Feladatátvételi fürtszolgáltatás a Windows Server

Az egyes forgatókönyvek esetén Instance Metadata szolgáltatás feladatátvételi fürtszolgáltatás, a lekérdezés esetén szükséges útvonal hozzáadása az útválasztási táblázathoz.

1. Nyissa meg a parancssort rendszergazdai jogosultságokkal.

2. Futtassa a következő parancsot, és jegyezze fel a címet a cél hálózati adapter (`0.0.0.0`) IPv4-útválasztási táblázatához.

```bat
route print
```

> [!NOTE] 
> Az alábbi példa kimenetében a feladatátvevő fürt engedélyezve van a Windows Server virtuális gép csak az IPv4 útválasztási táblázatot az egyszerűség kedvéért tartalmazza.

```bat
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
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Futtassa a következő parancsot, és használja a címet a felület hálózati cél (`0.0.0.0`) amely (`10.0.1.10`) ebben a példában.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Példák a virtuális gép több különböző nyelvet használó metadata szolgáltatás hívása 

Nyelv | Példa
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Indítás  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>GYIK

1. A hibaüzenetet kapok `400 Bad Request, Required metadata header not specified`. Ez mit jelent?
   * A Instance Metadata szolgáltatás szükséges a fejléc `Metadata: true` átadni a kérelmet. Ezt a fejlécet ad át a REST-hívást lehetővé teszi a Instance Metadata szolgáltatás elérését.
2. Miért nem jelenik meg a virtuális gépem számítási adatait?
   * A Instance Metadata szolgáltatás jelenleg csak az Azure Resource Managerrel létrehozott példányok támogatják. A Cloud Service virtuális gépeken hozzá lehet adva a jövőben támogatása.
3. A virtuális gép az Azure Resource Manageren keresztül létrehozott egy while vissza. Miért nem tudok lásd: számítási metaadat-információkat?
   * Minden 2016. szeptember. után létrehozott virtuális gépek hozzáadása egy [címke](../../azure-resource-manager/resource-group-using-tags.md) , azokat a számítási metaadatait. A régebbi virtuális gépek (2016. szeptember előtt létrehozott) hozzáadása/eltávolítása adatok és a teljes lemezt a virtuális Gépet a metaadatok frissítése.
4. Nem látok feltöltve az új verzió minden adat
   * Minden 2016. szeptember. után létrehozott virtuális gépek hozzáadása egy [címke](../../azure-resource-manager/resource-group-using-tags.md) , azokat a számítási metaadatait. A régebbi virtuális gépek (2016. szeptember előtt létrehozott) hozzáadása/eltávolítása adatok és a teljes lemezt a virtuális Gépet a metaadatok frissítése.
5. Miért jelenik meg a hiba `500 Internal Server Error`?
   * Ismételje meg a kérelmet, a rendszer exponenciális visszatartási alapján. Ha a probléma továbbra is fennáll, forduljon az Azure ügyfélszolgálatához.
6. Ha meg a további kérdéseimmel/megjegyzéseimmel?
   * A Megjegyzések küldése https://feedback.azure.com.
7. Működne ez virtuálisgép-méretezési csoport beállítása példányt?
   * Igen Metadata szolgáltatás érhető el a méretezési csoport beállítása példányokhoz.
8. Hogyan kérhet támogatást a szolgáltatás?
   * Segítségre van szüksége a szolgáltatáshoz, hozzon létre egy támogatási probléma az Azure Portalon a virtuális gép, ahol Ön nem sikerült beolvasni a metaadatok válasz hosszú újrapróbálkozás után.
9. Kérelem túllépte az időkorlátot a szolgáltatás a hívások kapok?
   * Metaadatok hívások kell tenni a hálózati kártyát a virtuális gép hozzárendelt elsődleges IP-címről, emellett abban az esetben, ha módosította az ott kell lenniük egy útvonalat a hálózati kártya ki 169.254.0.0/16 cím.
10. Saját virtuálisgép-méretezési csoportban lévő címkék frissítése, de azok nem jelennek meg a virtuális gépek ellentétben a példányok?
    * Jelenleg a ScaleSets címkék csak akkor látható, a virtuális Gépet egy újraindítás vagy rendszerképét alaphelyzetbe állítani/egy lemezt, vagy módosítsa a példány a.

    ![Példány metaadatok támogatása](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [ütemezett események](scheduled-events.md)
