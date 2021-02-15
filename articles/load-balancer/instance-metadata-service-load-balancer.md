---
title: Terheléselosztó információinak beolvasása az Azure Instance Metadata Service használatával
titleSuffix: Azure Load Balancer
description: Ismerkedjen meg az Azure Instance Metadata Service használatának megismerésével a terheléselosztó adatainak lekéréséhez.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: dcc9f71404e5a7c6509e4a8e821d66831ba02382
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417822"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>Terheléselosztó információinak beolvasása az Azure Instance Metadata Service használatával

A IMDS (Azure Instance Metadata Service) információt nyújt a jelenleg futó virtuálisgép-példányokról. A szolgáltatás egy REST API, amely jól ismert, nem irányítható IP-címen (169.254.169.254) érhető el. 

Ha a virtuális gépet vagy virtuálisgép-készletet állít be egy Azure-standard Load Balancer mögött, a IMDS segítségével lekérheti a terheléselosztó és a példányok metaadatait.

A metaadatok a következő információkat tartalmazzák a virtuális gépekhez vagy a virtuálisgép-méretezési csoportokhoz:

* Standard SKU nyilvános IP-címe.
* A hálózati adapter minden magánhálózati IP-címéhez tartozó terheléselosztó bejövő szabályának konfigurációi.
* A hálózati adapter minden magánhálózati IP-címéhez tartozó terheléselosztó kimenő szabályának konfigurációi.

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>A terheléselosztó metaadatainak elérése a IMDS használatával

További információ a terheléselosztó metaadatainak eléréséről: [Az Azure instance metadata Service használata a terheléselosztó adatainak eléréséhez.](howto-load-balancer-imds.md) a terheléselosztó adatainak eléréséhez.

## <a name="troubleshoot-common-error-codes"></a>Gyakori hibakódok – problémamegoldás

A gyakori hibakódokról és azok enyhítési módszereiről további információt a [gyakori hibakódok hibaelhárítása a IMDS használata esetén](troubleshoot-load-balancer-imds.md)című témakörben talál. 

## <a name="support"></a>Támogatás

Ha több kísérlet után nem tud metaadat-választ beolvasni, hozzon létre egy támogatási problémát a Azure Portal.

## <a name="next-steps"></a>Következő lépések
További információ az [Azure instance metadata Service](/virtual-machines/windows/instance-metadata-service)

[Standard Load Balancer üzembe helyezése](quickstart-load-balancer-standard-public-portal.md)

