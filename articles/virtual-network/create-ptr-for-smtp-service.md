---
title: Névkeresési zónák konfigurálása SMTP-szalagcímek vizsgálatához
titlesuffix: Azure Virtual Network
description: Útmutató a névkeresési zónák konfigurálásához az Azure-ban található SMTP szalagcím-ellenőrzések esetében
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c8ffadb8d54db0c2a99dc12e45b5990155a0505e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135059"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Névkeresési zónák konfigurálása SMTP-szalagcímek vizsgálatához

Ez a cikk azt ismerteti, hogyan használható egy fordított zóna a Azure DNSban, és hogyan hozható létre fordított DNS (PTR) rekord az SMTP-szalagcímek vizsgálatához.

## <a name="symptom"></a>Hibajelenség

Ha Microsoft Azureban futtat egy SMTP-kiszolgálót, a következő hibaüzenet jelenhet meg a távoli levelezési kiszolgálókról küldött üzenetek küldésekor vagy fogadásakor:

**554: nincs PTR-rekord**

## <a name="solution"></a>Megoldás

Az Azure-beli virtuális IP-címek esetében a fordított rekordok a Microsoft tulajdonában lévő tartományi zónákban jönnek létre, nem egyéni tartományi zónákban.

A PTR-rekordok Microsoft tulajdonú zónákban való konfigurálásához használja a-ReverseFqdn tulajdonságot a PublicIpAddress-erőforráson. További információkért lásd: [fordított DNS konfigurálása az Azure-ban üzemeltetett szolgáltatásokhoz](../dns/dns-reverse-dns-for-azure-services.md).

A PTR-rekordok konfigurálásakor győződjön meg arról, hogy az előfizetés tulajdonosa az IP-cím és a fordított FQDN. Ha olyan fordított FQDN-t próbál beállítani, amely nem tartozik az előfizetéshez, a következő hibaüzenet jelenik meg:

```output
Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                    
1) ReverseFqdn matches fqdn of any public ip resource under the subscription;
2) ReverseFqdn resolves to the fqdn (through CName records chain) of any public ip resource under the subscription;
3) It resolves to the ip address (through CName and A records chain) of a static public ip resource under the subscription.
```

Ha az SMTP-szalagcímet manuálisan módosítja az alapértelmezett fordított FQDN-ként, akkor a távoli levelezési kiszolgáló továbbra is meghiúsulhat, mert várható, hogy az SMTP-szalagcím gazdagépe megfelel a tartomány MX-rekordjának.
