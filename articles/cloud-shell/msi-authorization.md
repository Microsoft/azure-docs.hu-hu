---
title: Felügyelt identitások használata a Azure Cloud Shell erőforrásaihoz
description: Kód hitelesítése MSI-vel Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: 0fb19524079f84e92e1ddbc98a61917026492663
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89469898"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Felügyelt identitások használata az Azure-erőforrásokhoz Azure Cloud Shell

Azure Cloud Shell támogatja az Azure-erőforrások felügyelt identitásokkal való engedélyezését. Ezt kihasználva hozzáférési jogkivonatokat kérhet le az Azure-szolgáltatásokkal való biztonságos kommunikációhoz.

## <a name="about-managed-identities-for-azure-resources"></a>Tudnivalók az Azure-erőforrások felügyelt identitásairól
A felhőalapú alkalmazások kiépítése során gyakori kihívás, hogy miként lehet biztonságosan kezelni azokat a hitelesítő adatokat, amelyeknek a kódjában kell lenniük a felhőalapú szolgáltatásokhoz való hitelesítéshez. Cloud Shell előfordulhat, hogy a parancsfájlok beolvasását hitelesíteni kell a Key Vaultból egy olyan hitelesítő adathoz, amelyre szükség lehet.

Az Azure-erőforrások felügyelt identitásai megkönnyítik a probléma megoldását azáltal, hogy az Azure-szolgáltatások automatikusan felügyelt identitást biztosítanak Azure Active Directory (Azure AD). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

## <a name="acquire-access-token-in-cloud-shell"></a>Hozzáférési jogkivonat beszerzése Cloud Shell

Futtassa a következő parancsokat az MSI hozzáférési jogkivonat környezeti változóként való beállításához `access_token` .
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Kezelési jogkivonat lejárata

A helyi MSI alrendszer gyorsítótárazza a jogkivonatokat. Így a lehető legtöbbször hívhatja az Azure AD-t, és csak akkor, ha:
- Hiba történt a gyorsítótárban, mert nincs jogkivonat a gyorsítótárban.
- a jogkivonat lejárt

Ha gyorsítótárba helyezi a tokent a kódban, fel kell készülnie az olyan forgatókönyvek kezelésére, amelyekben az erőforrás azt jelzi, hogy a jogkivonat lejárt.

A jogkivonat-hibák kezeléséhez látogasson el az [MSI-lapra a CURLING MSI hozzáférési jogkivonatokkal kapcsolatban](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#error-handling).

## <a name="next-steps"></a>Következő lépések
[További információ az MSI-ről](../active-directory/managed-identities-azure-resources/overview.md)  
[Hozzáférési jogkivonatok beszerzése MSI-alapú virtuális gépekről](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)