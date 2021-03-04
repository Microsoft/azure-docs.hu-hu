---
title: Privát hivatkozás a FHIR készült Azure API-hoz
description: Ez a cikk bemutatja, hogyan állíthat be egy privát végpontot az Azure API FHIR-szolgáltatásokhoz
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/03/2021
ms.author: zxue
ms.openlocfilehash: 5e24c7666fd2ece7d284b7705bc481866d7604de
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097040"
---
# <a name="configure-private-link"></a>Privát hivatkozás konfigurálása

A privát hivatkozás lehetővé teszi az Azure API FHIR elérését egy privát végponton keresztül, egy olyan hálózati adaptert, amely privát és biztonságos módon csatlakoztatja a virtuális hálózatról a magánhálózati IP-címet. A privát hivatkozással biztonságosan érheti el szolgáltatásait a vnet az első féltől származó szolgáltatásként anélkül, hogy nyilvános DNS-t kellene átesnie. Ez a cikk bemutatja, hogyan hozhatja létre, tesztelheti és kezelheti az Azure API FHIR-hez készült privát végpontját.

>[!Note]
>Sem a privát kapcsolat, sem az Azure API for FHIR nem helyezhető át egy erőforráscsoport vagy előfizetés között egy másikra, ha a privát kapcsolat engedélyezve van. Először törölje a privát hivatkozást, majd helyezze át az Azure API-t a FHIR, és hozzon létre egy új privát hivatkozást az áthelyezés befejeződése után. A privát hivatkozás törlése előtt mérje fel a potenciális biztonsági következményeket.
>
>Ha a naplók exportálása és a metrikák engedélyezve vannak a FHIR készült Azure API-hoz, a portálon frissítse az exportálási beállítást a diagnosztikai beállítások segítségével.

## <a name="prerequisites"></a>Előfeltételek

A privát végpontok létrehozása előtt néhány Azure-erőforrást létre kell hoznia először:

- Erőforráscsoport – az Azure-erőforráscsoport, amely a virtuális hálózatot és a magánhálózati végpontot fogja tartalmazni.
- Azure API for FHIR – a FHIR-erőforrás, amelyet privát végpont mögé szeretne helyezni.
- Virtual Network – az a VNet, amelyhez az ügyfélszolgáltatás és a magánhálózati végpont csatlakozni fog.

További információkért tekintse meg a [privát hivatkozás dokumentációját](../private-link/index.yml).

## <a name="disable-public-network-access"></a>Nyilvános hálózati hozzáférés letiltása

A FHIR-erőforráshoz tartozó privát végpontok létrehozása nem tiltja le automatikusan a nyilvános forgalmat. Ehhez frissítenie kell a FHIR-erőforrást, hogy egy új "nyilvános hozzáférés" tulajdonságot "engedélyezve" értékről "Letiltva" értékre állítson. Ügyeljen arra, hogy a nyilvános hálózati hozzáférés letiltásakor a FHIR szolgáltatásnak küldött összes olyan kérést, amely nem a megfelelően konfigurált privát végpontról érkezik, a rendszer megtagadja. A rendszer csak a privát végpontokról érkező forgalmat engedélyezi.

![Nyilvános hálózati hozzáférés letiltása](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Privát végpont létrehozásához a FHIR-erőforráshoz tartozó RBAC-engedélyekkel rendelkező fejlesztő használhatja a Azure Portal, [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)vagy az [Azure CLI](../private-link/create-private-endpoint-cli.md)-t. Ez a cikk végigvezeti a Azure Portal használatának lépésein. A Azure Portal használata ajánlott, mert automatizálja az saját DNS zóna létrehozását és konfigurálását. További részletekért tekintse meg a [privát hivatkozás Gyorskonfigurálás útmutatóit](../private-link/create-private-endpoint-portal.md) .

Egy privát végpontot kétféleképpen lehet létrehozni. Az automatikus jóváhagyási folyamat lehetővé teszi egy olyan felhasználó számára, aki RBAC engedéllyel rendelkezik a FHIR-erőforráshoz, hogy jóváhagyás nélkül hozzon létre egy privát végpontot. A manuális jóváhagyási folyamat lehetővé teszi, hogy a felhasználó a FHIR-erőforrásra vonatkozó engedély nélkül kérjen egy privát végpontot, amelyet a FHIR-erőforrás tulajdonosainak jóvá kell hagyniuk.

### <a name="auto-approval"></a>Automatikus jóváhagyás

Győződjön meg arról, hogy az új privát végpont régiója megegyezik a Virtual Network régiójával. A FHIR-erőforrás régiója eltérő lehet.

![Azure Portal alapjai lap](media/private-link/private-link-portal2.png)

Az erőforrás típusa mezőben keresse meg és válassza a "Microsoft. HealthcareApis/Services" lehetőséget. Az erőforrás mezőben válassza ki a FHIR-erőforrást. A cél alerőforrásnál válassza a "fhir" lehetőséget.

![Azure Portal Erőforrás lap](media/private-link/private-link-portal1.png)

Ha nincs beállítva meglévő saját DNS-zóna, válassza az "(új) privatelink. azurehealthcareapis. com" lehetőséget. Ha már konfigurálta a saját DNS-zónát, kiválaszthatja a listából. Ennek a következő formátumúnak kell lennie: "privatelink.azurehealthcareapis.com".

![Azure Portal konfiguráció lap](media/private-link/private-link-portal3.png)

Az üzembe helyezés befejezése után visszatérhet a "privát végponti kapcsolatok" lapra, amelyen a "jóváhagyva" kapcsolati állapot jelenik meg.

### <a name="manual-approval"></a>Manuális jóváhagyás

A manuális jóváhagyáshoz válassza a második lehetőséget az erőforrás területen, "kapcsolódás Azure-erőforráshoz erőforrás-azonosító vagy alias alapján". A cél alerőforrásnál adja meg az "fhir" értéket az automatikus jóváhagyáshoz.

![Manuális jóváhagyás](media/private-link/private-link-manual.png)

Az üzembe helyezés befejezése után visszatérhet a "privát végponti kapcsolatok" lapra, amelyen jóváhagyhatja, elutasítja vagy eltávolíthatja a kapcsolatot.

![Beállítások](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Privát végpont tesztelése

Annak biztosítása érdekében, hogy a FHIR-kiszolgáló ne kapjon nyilvános forgalmat a nyilvános hálózati hozzáférés letiltása után, próbálja meg a kiszolgáló/metadata-végpontját a számítógépén. Meg kell kapnia egy 403-es tiltott. Vegye figyelembe, hogy a nyilvános hálózati hozzáférési jelző frissítése a nyilvános forgalom letiltása előtt akár 5 percet is igénybe vehet.

Annak biztosítása érdekében, hogy a saját végpontja forgalmat küldjön a kiszolgálónak:

1. Hozzon létre egy olyan virtuális GÉPET, amely csatlakozik a virtuális hálózathoz, és az alhálózat, amelyhez a magánhálózati végpont konfigurálva van. Annak biztosítása érdekében, hogy a virtuális gépről érkező forgalom csak a magánhálózaton legyen használatban, a kimenő internetes forgalom a NSG-szabályon keresztül tiltható le.
2. RDP a virtuális géphez.
3. Próbálja megismételni a FHIR-kiszolgáló/metadata-végpontját a virtuális gépről, és válaszként meg kell kapnia a képességi utasítást.

## <a name="manage-private-endpoint"></a>Privát végpont kezelése

### <a name="view"></a>Nézet

A privát végpontok és a társított NIC Azure Portal láthatók a ben létrehozott erőforráscsoporthoz.

![Megtekintés az erőforrásokban](media/private-link/private-link-view.png)

### <a name="delete"></a>Törlés

A privát végpontok csak Azure Portalból törölhetők az Áttekintés panelről (az alábbi módon) vagy a DELETE lehetőség használatával a hálózatkezelés (előzetes verzió) "privát végponti kapcsolatok" lapján. A Törlés gombra kattintva törölheti a privát végpontot és a hozzá tartozó hálózati adaptert. Ha törli az összes privát végpontot a FHIR-erőforráshoz, és a nyilvános hálózati hozzáférés le van tiltva, a FHIR-kiszolgálónak nem kell kérést készítenie.

![Privát végpont törlése](media/private-link/private-link-delete.png)
