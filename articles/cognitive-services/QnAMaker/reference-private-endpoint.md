---
title: Privát végpont beállítása – QnA Maker
description: A QnA Maker felügyelt privát végpontok létrehozásának megismerése.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 7907c81e45680de49f6653891fb4204a59db1002
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710549"
---
# <a name="private-endpoints"></a>Privát végpontok

Az Azure privát végpont egy hálózati adapter, amely privát és biztonságos módon csatlakoztatja Önt egy Azure privát kapcsolat által működtetett szolgáltatáshoz. A QnA Maker mostantól támogatja a privát végpontok létrehozását a Azure Search szolgáltatáshoz. Ez a funkció QnA Maker felügyelt alkalmazásban érhető el. 

A privát végpontokat az [Azure privát kapcsolata](../../private-link/private-link-overview.md)külön szolgáltatásként biztosíthatja. A költségekkel kapcsolatos további információkért tekintse meg a [díjszabási oldalt.](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>Előfeltételek
> [!div class="checklist"]
> * Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/) a virtuális gép létrehozásának megkezdése előtt.
> * A Azure Portalban létrehozott QnA Maker [felügyelt erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) . Jegyezze fel Azure Active Directory AZONOSÍTÓját, előfizetését, az erőforrás létrehozásakor kiválasztott QnA-erőforrás nevét.

## <a name="steps-to-enable-private-endpoint"></a>A privát végpont engedélyezésének lépései
1. Rendeljen *hozzá közreműködői* szerepkört QnA Maker felügyelt szolgáltatáshoz az Azure Search Service-példányban. Ehhez a művelethez *tulajdonosi* hozzáféréssel kell rendelkeznie az előfizetéshez. Az identitás beszerzéséhez nyissa meg a szolgáltatás erőforrásának Identity (identitás) lapját.
![Felügyelt szolgáltatás identitása](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Adja hozzá a fenti identitást a *közreműködőhöz* a Azure Search szolgáltatás iam lapon. ![ Felügyelt szolgáltatás IAM](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Kattintson a *szerepkör-hozzárendelések hozzáadása* elemre, adja hozzá az identitást, és kattintson a *Mentés* gombra.
![Felügyelt szerepkör-hozzárendelés](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Most nyissa meg a *hálózatkezelés* lapot az Azure Search Service-példányban, és kapcsolja a végponti kapcsolati adatait *nyilvánosról* *magánjellegűre*. Ez a művelet hosszú ideig futó folyamat, és akár 30 percig is eltarthat. 
![Felügyelt Azure Search hálózatkezelés](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Nyissa meg QnA Maker felügyelt szolgáltatás *hálózatkezelés* lapját, és a *hozzáférés engedélyezése* területen válassza a *kiválasztott hálózatok és magánhálózati végpontok* lehetőséget, és kattintson a *Save (Mentés*) gombra. 
![Felügyelt QnA Maker newtorking](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Ez létrehoz egy privát végponti kapcsolatot a QnA Maker szolgáltatás és az Azure kognitív keresési szolgáltatás példánya között. Azure Search szolgáltatási példány *hálózatkezelés* lapján ellenőrizheti a magánhálózati végpont kapcsolatát. A teljes művelet befejezését követően érdemes a QnA Maker szolgáltatást használni. 
![Felügyelt hálózatkezelési szolgáltatás](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Támogatás részletei
 * A QnAMaker szolgáltatáshoz való privát hozzáférés engedélyezése után nem támogatjuk a Azure Search szolgáltatás módosítását. Ha a privát hozzáférés engedélyezése után megváltoztatja a Azure Search szolgáltatást a "Configuration" (konfiguráció) lapon, a QnAMaker szolgáltatás használhatatlanná válik.
 * Ha a privát végponti kapcsolat létesítése után Azure Search szolgáltatás hálózatkezelését a "nyilvános" értékre vált, nem fogja tudni használni a QnAMaker szolgáltatást. Azure Search szolgáltatás hálózatkezelésének "Private"-nek kell lennie a személyes végponti kapcsolat működéséhez