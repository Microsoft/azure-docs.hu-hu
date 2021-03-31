---
title: Gyakori kérdések a Analysis Services hálózati kapcsolatról | Microsoft Docs
description: Ez a cikk válaszokat ad a Analysis Services hálózati kapcsolattal kapcsolatos leggyakoribb kérdésekre.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "82838501"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Gyakori kérdések az Analysis Services hálózati kapcsolatról

Ez a cikk a Storage-fiókokhoz, az adatforrásokhoz, a tűzfalakhoz és az IP-címekhez való csatlakozással kapcsolatos gyakori kérdésekre ad választ.

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

**Kérdés** – hogyan lehet biztonsági másolatot készíteni és visszaállítani egy tűzfal mögött található Storage-fiók használatával?   
A **Válasz** -Azure Analysis Services nem használ rögzített IP-címeket vagy szolgáltatási címkéket. Az Analysis Services-kiszolgálók által használt IP-címtartomány bármilyen lehet az *Azure-régió* IP-címeinek tartományában. Mivel a kiszolgáló IP-címei változóak, és idővel változhatnak, a tűzfalszabályok számára engedélyezni kell az Azure-régió IP-címeinek teljes tartományát a kiszolgálón.

**Kérdés** – az Azure Storage-fiókom a Analysis Services-kiszolgálótól eltérő régióban található. Hogyan konfigurálja a Storage-fiók tűzfala beállításait?   
**Válasz** – ha a Storage-fiók egy másik régióban található, konfigurálja a Storage-fiók tűzfala beállításait, hogy engedélyezze a hozzáférést a **kiválasztott hálózatokból**. A tűzfal **címtartomány** területen válassza ki az IP-címtartományt azon régió számára, amelyen a Analysis Services-kiszolgáló található. Az Azure-régiók IP-tartományának beszerzéséhez tekintse meg az [Azure IP-címtartományok és a szolgáltatás címkék – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519)című témakört. A Storage-fiók tűzfala beállításainak konfigurálása az összes hálózatról való hozzáférés engedélyezéséhez, azonban a kiválasztott hálózatok kiválasztása és az IP-címtartomány megadása javasolt. 

**Kérdés** – az Azure Storage-fiókom a Analysis Services-kiszolgálóval megegyező régióban található. Hogyan lehet konfigurálni a Storage-fiók tűzfala beállításait?   
**Válasz** – mivel a Analysis Services-kiszolgáló és a Storage-fiók ugyanabban a régióban található, a velük folytatott kommunikáció belső IP-címtartományt használ, ezért a tűzfal konfigurálása a kijelölt hálózatok használatára és az IP-címtartomány megadása nem támogatott. Ha a szervezeti szabályzatok tűzfalat igényelnek, azt úgy kell konfigurálni, hogy engedélyezze a hozzáférést az összes hálózatról.


## <a name="data-source-connections"></a>Adatforrás-kapcsolatok

**Kérdés** – VNET az adatforrások rendszeréhez. Hogyan engedélyezhető a Analysis Services-kiszolgálók hozzáférése az adatbázishoz a VNET?   
A **Válasz** -Azure Analysis Services nem tud csatlakozni egy VNET. A legjobb megoldás az, ha egy helyszíni adatátjárót telepít és konfigurál a VNET, majd konfigurálja a Analysis Services-kiszolgálókat a **AlwaysUseGateway** Server tulajdonsággal. További információ: az [átjáró használata adatforrásokhoz Azure-Virtual Network (VNet)](analysis-services-vnet-gateway.md).

**Kérdés** – van egy, a tűzfal mögött található forrásadatbázis. Hogyan állíthatom be a tűzfalat, hogy a Analysis Services-kiszolgáló hozzáférjen?   
A **Válasz** -Azure Analysis Services nem használ rögzített IP-címeket vagy szolgáltatási címkéket. Az Analysis Services-kiszolgálók által használt IP-címtartomány bármilyen lehet az *Azure-régió* IP-címeinek tartományában. Meg kell adnia a kiszolgáló Azure-régiójához tartozó teljes IP- *címtartományt* a forrás-adatbázis tűzfalszabályok között. Egy másik, és valószínűleg biztonságosabb, alternatív megoldás egy helyszíni adatátjáró konfigurálása. Ezután konfigurálhatja a Analysis Services-kiszolgálókat a [AlwaysUseGateway-kiszolgáló tulajdonsággal](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property), és gondoskodhat arról, hogy a helyszíni adatátjáróhoz az adatforrás tűzfalszabályok által engedélyezett IP-cím legyen.

## <a name="azure-apps-with-ip-address"></a>Azure-alkalmazások IP-címmel

**Kérdés** : egy Azure-alapú alkalmazást (például Azure Functions, Azure Data Factory) használok egy menet KÖZBENi IP-címmel. Hogyan kezelhetem a Azure Analysis Services tűzfalszabályok, hogy dinamikusan engedélyezzék az alkalmazás végrehajtásának IP-címét?   
A **Válasz** – Azure Analysis Services nem támogatja a magánhálózati, a virtuális hálózatok vagy a szolgáltatás címkéit. Vannak olyan nyílt forráskódú megoldások (például az https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) ügyfélalkalmazás IP-címének észlelése, valamint a tűzfalszabályok automatikus és ideiglenes frissítése).


## <a name="next-steps"></a>Következő lépések

[Helyszíni adatátjáró telepítése és konfigurálása](analysis-services-gateway-install.md)   
[Csatlakozás helyszíni adatforrásokhoz helyszíni adatátjáróval](analysis-services-gateway.md)   
[Átjáró használata az adatforrásokhoz egy Azure-beli virtuális hálózaton (VNeten)](analysis-services-vnet-gateway.md)
