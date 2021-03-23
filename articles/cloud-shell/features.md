---
title: Azure Cloud Shell funkciók | Microsoft Docs
description: A Azure Cloud Shell szolgáltatásainak áttekintése
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: c707a6522d037ba3926b55579e501eadd2764416
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798272"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Szolgáltatások & eszközök Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell fut `Ubuntu 16.04 LTS` .

## <a name="features"></a>Funkciók

### <a name="secure-automatic-authentication"></a>Biztonságos automatikus hitelesítés

Cloud Shell biztonságosan és automatikusan hitelesíti a fiókhoz való hozzáférést az Azure CLI-hez és a Azure PowerShellhoz.

### <a name="home-persistence-across-sessions"></a>$HOME adatmegőrzés a munkamenetek között

A fájlok munkamenetek közötti megőrzéséhez Cloud Shell végigvezeti egy Azure-fájlmegosztás első indításkor való csatlakoztatásán.
Ha elkészült, Cloud Shell automatikusan csatolja a tárolót (csatolva `$HOME\clouddrive` ) az összes jövőbeli munkamenethez.
Emellett a `$HOME` címtára. img néven is megmarad az Azure-fájlmegosztás számára.
A és a `$HOME` gép állapotán kívüli fájlok nem maradnak meg a munkamenetek között. Ajánlott eljárások használata titkos kódok, például SSH-kulcsok tárolásához. Olyan szolgáltatások [, mint a Azure Key Vault a telepítőhöz kapcsolódó oktatóanyagok](../key-vault/general/manage-with-cli2.md#prerequisites).

[További információ a fájlok megőrzéséről a Cloud Shellban.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure-meghajtó (Azure:)

A Cloud Shell PowerShell-je biztosítja az Azure-meghajtót ( `Azure:` ). Átválthat az Azure-ra, és visszatérhet a saját kezdőkönyvtárának a saját `cd Azure:` könyvtárába `cd  ~` .
Az Azure-meghajtó az Azure-erőforrások (például számítás, hálózat, tárolás stb.) egyszerű felderítését és navigálását teszi lehetővé a fájlrendszerhez hasonló módon.
Továbbra is használhatja az ismerős [Azure PowerShell parancsmagokat](/powershell/azure) az erőforrások kezeléséhez, függetlenül attól, hogy melyik meghajtóról van.
Az Azure-erőforrásokon végrehajtott, közvetlenül Azure Portal vagy Azure PowerShell parancsmagokkal végzett módosítások az Azure-meghajtón is megjelennek.  A futtatásával `dir -Force` frissítheti az erőforrásokat.

![Képernyőkép egy Azure Cloud Shell inicializálásáról és a címtár erőforrásainak listájáról.](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Exchange Online kezelése

A Cloud Shell PowerShell az Exchange Online modul privát összeállítását tartalmazza.  `Connect-EXOPSSession`Az Exchange-parancsmagok beszerzéséhez futtassa a parancsot.

![Képernyőkép az Connect-EXOPSSession és a Get-user parancsot futtató Azure Cloud Shellról.](media/features-powershell/exchangeonline.png)

 Az `Get-Command -Module tmp_*` parancs futtatása
> [!NOTE]
> A modul nevének a (z) értékkel kell kezdődnie `tmp_` , ha azonos előtaggal rendelkező modulokat telepített, a parancsmagok is felszínre kerülnek. 

![Képernyőkép a parancs Get-Command-Module tmp_ * parancsot futtató Azure Cloud Shellról.](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Mélyreható integráció nyílt forráskódú eszközökkel

A Cloud Shell a nyílt forráskódú eszközök, például a Terraform, a Ansible és a Chef inspec előre konfigurált hitelesítését tartalmazza. Próbálja ki a példákat a forgatókönyvek közül.

## <a name="tools"></a>Eszközök

|Kategória   |Name   |
|---|---|
|Linux-eszközök            |bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Azure-eszközök            |[Azure CLI](https://github.com/Azure/azure-cli) és [klasszikus Azure CLI](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](../storage/common/storage-use-azcopy-v10.md)<br> [Azure Functions parancssori felület](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric parancssori felület](../service-fabric/service-fabric-cli.md)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Szövegszerkesztők           |Code (Cloud Shell Editor)<br> vim<br> nano<br> emacs    |
|Verziókövetés         |git                    |
|Buildelő eszközök            |make<br> maven<br> npm<br> pip         |
|Tárolók             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS PARANCSSORI FELÜLET](https://github.com/dcos/dcos-cli)         |
|Adatbázisok              |MySQL-ügyfél<br> PostgreSql-ügyfél<br> [sqlcmd segédprogram](/sql/tools/sqlcmd-utility)<br> [MSSQL – parancsfájl](https://github.com/Microsoft/sql-xplat-cli) |
|Egyéb                  |iPython-ügyfél<br> [Cloud Foundry parancssori felület](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef Inspect](https://www.chef.io/inspec/)<br> [Báb-bolt](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp-csomagoló](https://www.packer.io/)<br> [Office 365 parancssori felület](https://pnp.github.io/office365-cli/)|

## <a name="language-support"></a>Nyelvi támogatás

|Nyelv   |Verzió   |
|---|---|
|.NET Core  |[3.1.302](https://github.com/dotnet/core/blob/master/release-notes/3.1/3.1.6/3.1.302-download.md)       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2,7 és 3,7 (alapértelmezett)|

## <a name="next-steps"></a>Következő lépések
[Bash Cloud Shell rövid útmutatóban](quickstart.md) <br>
[PowerShell Cloud Shell rövid útmutató](quickstart-powershell.md) <br>
[További tudnivalók az Azure CLI-ről](/cli/azure/) <br>
[Tudnivalók a Azure PowerShell](/powershell/azure/) <br>
