---
title: Ügyféleszközök telepítése
description: Telepítse a azdata, a kubectl, az Azure CLI, a psql, a Azure Data Studio (bennfentesek) és a Azure Data Studio ív bővítményét
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3eb41b868aba7e7350f54594fa43f01eeeabebc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940677"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Ügyféleszközök telepítése az Azure Arc-kompatibilis adatszolgáltatások üzembe helyezéséhez és felügyeletéhez

> [!IMPORTANT]
> Ha új havi kiadásra frissít, kérjük, frissítsen a Azure Data Studio legújabb verziójára, az Azure-beli adatcli-(azdata-) eszközre, valamint az Azure-beli adatcli-re és az Azure arc-bővítményekre a Azure Data Studiohoz.

Ez a dokumentum végigvezeti az Azure-beli adatcli (azdata), az Azure Data Studio, az Azure CLI (az) és a Kubernetes CLI-eszköz (kubectl) telepítésének lépésein az ügyfélszámítógépen.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Eszközök az Azure arc-kompatibilis adatszolgáltatások létrehozásához és kezeléséhez 

Az alábbi táblázat az Azure arc-kompatibilis adatszolgáltatások létrehozásához és kezeléséhez, valamint az eszközök telepítéséhez szükséges általános eszközöket sorolja fel:

| Eszköz | Kötelező | Leírás | Telepítés |
|---|---|---|---|
| Azure-adatcli (azdata) | Igen | Big data-fürt telepítésére és felügyeletére szolgáló parancssori eszköz. Az Azure-beli adatcli tartalmaz egy parancssori segédprogramot is, amely az Azure SQL-és SQL Server-példányok és a postgres-kiszolgálók `azdata sql query` (egyetlen lekérdezés futtatása a parancssorból), `azdata sql shell` (interaktív rendszerhéj) és a parancsokkal való kapcsolódáshoz és lekérdezésekhez használható `azdata postgres query` `azdata postgres shell` . | [Telepítse](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | Igen | Sokoldalú eszköz a különböző adatbázisok, például az Azure SQL, a SQL Server, a PostrgreSQL és a MySQL összekapcsolásához és lekérdezéséhez. A Azure Data Studio bővítmények felügyeleti élményt nyújtanak az Azure arc-kompatibilis adatszolgáltatásokhoz. | [Telepítse](https://aka.ms/getazuredatastudio) |
| Azure-adatcli-bővítmény a Azure Data Studio | Igen | Azure Data Studio bővítmény, amely telepíti az Azure-beli adatcli-t, ha még nem tette meg.| Telepítés a bővítmények katalógusában Azure Data Studio.|
| Azure arc-bővítmény a Azure Data Studiohoz | Igen | Az Azure arc-kompatibilis adatszolgáltatások kezelési élményét biztosító Azure Data Studio bővítménye. A Azure Data Studiohoz tartozó Azure-adatcli-bővítmény függőséggel rendelkezik. | Telepítés a bővítmények katalógusában Azure Data Studio.|
| PostgreSQL-bővítmény Azure Data Studio | Nem | PostgreSQL-bővítmény Azure Data Studio számára, amely felügyeleti funkciókat biztosít a PostgreSQL-hez. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Telepítés a bővítmények katalógusában Azure Data Studio.|
| Azure CLI (az)<sup>1</sup> | Igen | Modern parancssori felület az Azure-szolgáltatások kezeléséhez. AK-alapú üzemelő példányokkal és az Azure arc-kompatibilis adatszolgáltatások leltárának és számlázási adatoknak az Azure-ba való feltöltésére használható. ([További információ](/cli/azure/?view=azure-cli-latest&preserve-view=true)). | [Telepítse](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) |
| Kubernetes CLI (kubectl)<sup>2</sup> | Igen | Parancssori eszköz a Kubernetes-fürt kezeléséhez ([További információ](https://kubernetes.io/docs/tasks/tools/install-kubectl/)). | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linuxos](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| <sup>3</sup> . curl | Néhány minta parancsfájlhoz szükséges. | Parancssori eszköz az adatátvitelhez URL-címekkel. | [Windows](https://curl.haxx.se/windows/) \| Linux: a curl-csomag telepítése |
| oC | A Red Hat OpenShift és az Azure RedHat OpenShift üzemelő példányokhoz szükséges. |`oc` a megnyitott SHIFT parancssori felület (CLI). | [A parancssori felület telepítése](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> az Azure CLI 2.0.4 vagy újabb verzióját kell használnia. `az --version`Ha szükséges, futtassa a parancsot a verzió megkereséséhez.

<sup>2</sup> a `kubectl` 1,13-es vagy újabb verziót kell használnia. Emellett a verziójának a `kubectl` Kubernetes-fürt egy alverziójának kell lennie. Ha az ügyfélen egy adott verziót szeretne telepíteni `kubectl` , olvassa el [a `kubectl` bináris fájl telepítése a curl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) használatával (windows 10 rendszeren, cmd.exe használata és nem a Windows PowerShell futtatása a curl futtatásához) című témakört.

<sup>3</sup> ha a PowerShellt használja, a curl a Invoke-WebRequest parancsmag aliasa.

## <a name="next-steps"></a>Következő lépések

[Az Azure arc-adatkezelő létrehozása](create-data-controller.md)
