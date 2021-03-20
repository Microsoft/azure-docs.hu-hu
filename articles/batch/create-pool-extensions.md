---
title: Bővítmények használata batch-készletekkel
description: A bővítmények olyan kisméretű alkalmazások, amelyek megkönnyítik a kiépítés utáni konfigurálást és a telepítést a Batch számítási csomópontokon.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 1bf9847af57347c143ee3d790d89988ba7cd48e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417449"
---
# <a name="use-extensions-with-batch-pools"></a>Bővítmények használata batch-készletekkel

A bővítmények olyan kisméretű alkalmazások, amelyek megkönnyítik a kiépítés utáni konfigurálást és a telepítést a Batch számítási csomópontokon. Kiválaszthatja azokat a bővítményeket, amelyeket a Azure Batch engedélyez, és azokat a számítási csomópontokon telepítik, amelyeket kiépítenek. Ezt követően a bővítmény végrehajthatja a kívánt műveletet.

Megtekintheti a használt bővítmények élő állapotát, és lekérheti az általuk visszaadott adatokat, hogy bármilyen észlelési, javítási vagy diagnosztikai képességet érjenek el.

## <a name="prerequisites"></a>Előfeltételek

- A bővítményekkel rendelkező készleteknek a [virtuálisgép-konfigurációt](nodes-and-pools.md#virtual-machine-configuration)kell használniuk.
- A CustomScript-bővítmény típusa a Azure Batch szolgáltatás számára van fenntartva, és nem bírálható felül.

### <a name="supported-extensions"></a>Támogatott bővítmények

A következő bővítmények jelenleg a Batch-készlet létrehozásakor telepíthetők. 

- Azure Key Vault-bővítmény [Linux](../virtual-machines/extensions/key-vault-linux.md) és [Windows rendszerhez](../virtual-machines/extensions/key-vault-windows.md)
- A log Analytics és a monitorozási bővítmény [Linux](../virtual-machines/extensions/oms-linux.md) és [Windows rendszereken](../virtual-machines/extensions/oms-windows.md) egyaránt
- Azure Security Pack

Támogatási kérés megnyitásával további közzétevők és/vagy kiterjesztési típusok támogatását kérheti.

## <a name="create-a-pool-with-extensions"></a>Készlet létrehozása bővítményekkel

Az alábbi példa olyan Linux-csomópontok batch-készletét hozza létre, amelyek a Azure Key Vault-bővítményt használják.

REST API URI

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

Kérelem törzse

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Bővítmény adatainak beolvasása készletből

Az alábbi példa az Azure Key Vault-bővítmény adatait kérdezi le.

REST API URI

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Válasz törzse

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Következő lépések

- Ismerje meg az [alkalmazások és az adatkészletek csomópontjaira másolásának](batch-applications-to-pool-nodes.md)különböző módjait.
- További információ a [csomópontok és a készletek](nodes-and-pools.md)használatáról.
