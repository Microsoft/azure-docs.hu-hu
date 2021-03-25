---
title: Az Azure HDInsight-fürtcsomópontok gazdagép-nevének beszerzése
description: Ismerje meg, hogyan kérheti le az Azure HDInsight-fürtcsomópontok állomásnevét és FQDN-nevét.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111465"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>A fürtcsomópontok állomásneve

A HDInsight-fürt nyilvános DNS-clustername.azurehdinsight.net lett létrehozva. Amikor SSH-t használ az egyes csomópontokhoz, vagy a fürt csomópontjaihoz való csatlakozást ugyanazzal az egyéni virtuális hálózattal, akkor a fürtcsomópontok állomásnevét vagy teljes tartománynevét (FQDN) kell használnia.

Ebből a cikkből megtudhatja, hogyan kérheti le a fürtcsomópontok állomásnevét. A Ambari webes felhasználói felületén keresztül manuálisan, vagy a Ambari REST API automatikusan keresztül is lekérheti.

> [!WARNING]
> A fürtcsomópontok állomásneve beolvasásához használja az alábbi ajánlott módszereket. Az állomásnévben szereplő számok nem garantáltak a sorozatban, és a HDInsight az állomásnév formátumát úgy változtathatja meg, hogy a kiadási frissítéssel rendelkező virtuális gépekhez illeszkedjen. Ne használja a függőséget minden olyan elnevezési konvención, amely ma már létezik. 
>

Az állomásnevek az Ambari felhasználói felületén vagy a Ambari REST API keresztül szerezhetők be. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Állomásnevek beszerzése a Ambari webes felhasználói felületéről
A Ambari webes felhasználói felületének használatával beolvashatja az állomásnevek nevét, amikor SSH-t használ a csomóponthoz. A Ambari webes felhasználói felületének gazdagépek nézete a HDInsight-fürtön érhető el `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts` , ahol a a `CLUSTERNAME` fürt neve.

![Get-host-names-in-Ambari-UI](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Ambari beolvasása REST API
Az Automation-parancsfájlok létrehozásakor a Ambari REST API segítségével lekérheti a gazdagépek nevét, mielőtt kapcsolatokat hozna létre a gazdagépekhez. Az állomásnévben szereplő számok nem garantáltak a sorban, és a HDInsight megváltoztathatják az állomásnév formátumát a kiadási frissítéssel rendelkező virtuális gépekhez való igazításhoz. Ne használja a függőséget minden olyan elnevezési konvención, amely ma már létezik. 

Íme néhány példa arra, hogyan lehet beolvasni a fürt csomópontjainak teljes tartománynevét. A Ambari REST APIával kapcsolatos további információkért lásd: [HDInsight-fürtök kezelése az Apache Ambari használatával REST API](.\hdinsight-hadoop-manage-ambari-rest-api.md)

A következő példa a [jQ](https://stedolan.github.io/jq/) vagy a [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) használatával elemzi a JSON-válasz dokumentumát, és csak az állomásnevek megjelenítését jeleníti meg.

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```