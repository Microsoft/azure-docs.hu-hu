---
title: IP-tűzfal konfigurálása az Azure Cognitive Search szolgáltatáshoz
titleSuffix: Azure Cognitive Search
description: Konfigurálja az IP-vezérlési házirendeket az Azure Cognitive Search szolgáltatáshoz való hozzáférés korlátozására.
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: de34c2921c7829cb6d7e7354a1ebcff44271efd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545547"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Az Azure Cognitive Search IP-tűzfalának konfigurálása

Az Azure Cognitive Search támogatja az IP-szabályokat a bejövő tűzfalak támogatásához. Ez a modell egy további biztonsági réteget biztosít a keresési szolgáltatáshoz, amely az Azure virtuális hálózati biztonsági csoportban található IP-szabályokhoz hasonló. Ezeknek az IP-szabályoknak a segítségével úgy állíthatja be a keresési szolgáltatást, hogy csak jóváhagyott gépekből és/vagy felhőalapú szolgáltatásokból legyen elérhető. A keresési szolgáltatásban tárolt adatoknak ezen jóváhagyott készletekből és szolgáltatásokból való elérése továbbra is megköveteli, hogy a hívó érvényes engedélyezési jogkivonatot jelentsen.

A Azure Portal IP-szabályait az ebben a cikkben leírtak szerint állíthatja be. Azt is megteheti, hogy a 2020-03-13-es, [Azure PowerShell](/powershell/module/az.search)-es vagy az [Azure CLI](/cli/azure/search)-es [verziójának felügyeleti REST API verzióját](/rest/api/searchmanagement/)használja.

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> IP-tűzfal konfigurálása a Azure Portal használatával

A Azure Portal IP-hozzáférés-vezérlési szabályzatának beállításához nyissa meg az Azure Cognitive Search szolgáltatás lapját, és válassza a **hálózatkezelés** lehetőséget a navigációs menüben. A végponti hálózati kapcsolatnak **nyilvánosnak** kell lennie. Ha a kapcsolat **privát** értékre van állítva, akkor csak privát végponton keresztül érheti el a keresési szolgáltatást.

![A Azure Portal IP-tűzfalának konfigurálását bemutató képernyőkép](./media/service-configure-firewall/azure-portal-firewall.png)

Az Azure Portal lehetővé teszi az IP-címek és IP-címtartományok megadását a CIDR formátumban. A CIDR-jelölés például a 8.8.8.0/24, amely a 8.8.8.0 és a 8.8.8.255 közötti IP-címeket jelöli.

> [!NOTE]
> Miután engedélyezte az Azure Cognitive Search szolgáltatáshoz tartozó IP-hozzáférés-vezérlési házirendet, a rendszer elutasítja az adatsíkon lévő összes, az IP-címtartományok engedélyezési listáján kívüli kérést. Az IP-szabályok konfigurálásakor a Azure Portal egyes funkciói le vannak tiltva. Megtekintheti és kezelheti a szolgáltatási szint adatait, de a portál hozzáférése az adatok indexeléséhez és a szolgáltatás különböző összetevőihez, például az index, az indexelő és a készségkészlet-definíciók biztonsági okokból korlátozottak. A portál alternatívájaként a [vs Code bővítménnyel](https://aka.ms/vscode-search) is használhatja a szolgáltatás különböző összetevőit.

### <a name="requests-from-your-current-ip"></a>Az aktuális IP-címről érkező kérések

A fejlesztés egyszerűsítése érdekében a Azure Portal segíti az ügyfélszámítógép IP-címének azonosítását és hozzáadását az engedélyezett listához. A gépen futó alkalmazások hozzáférhetnek az Azure Cognitive Search szolgáltatáshoz.

A portál automatikusan észleli az ügyfél IP-címét. Ez lehet a számítógép vagy a hálózati átjáró ügyfél IP-címe. Mielőtt éles környezetben üzembe helyezi a munkaterhelést, mindenképp távolítsa el ezt az IP-címet.

Ha a jelenlegi IP-címet az IP-címek listájához szeretné felvenni, **akkor az ügyfél IP-címét adja meg**. Kattintson a **Mentés** gombra.

![Képernyőfelvétel az IP-tűzfalbeállítások konfigurálásáról az aktuális IP-cím engedélyezéséhez](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>IP-hozzáférés-vezérlési házirenddel kapcsolatos problémák elhárítása

Az IP-hozzáférés-vezérlési házirenddel kapcsolatos problémákat a következő beállításokkal lehet elhárítani:

### <a name="azure-portal"></a>Azure Portal

Az Azure Cognitive Search szolgáltatás IP-hozzáférés-vezérlési házirendjének engedélyezése letiltja az IP-címtartományok engedélyezési listáján kívüli összes kérést, beleértve a Azure Portal.  Megtekintheti és kezelheti a szolgáltatási szint adatait, de a portál hozzáférése az adatok indexeléséhez és a szolgáltatás különböző összetevőihez, például az index, az indexelő és a készségkészlet-definíciók biztonsági okokból korlátozottak. 

### <a name="sdks"></a>SDK-k

Ha az SDK-ból olyan gépekről fér hozzá az Azure Cognitive Search Service-hez, amelyek nem szerepelnek az engedélyezési listán, a rendszer általános **403 Tiltott** választ ad vissza további részletek nélkül. Ellenőrizze az engedélyezett IP-listát a fiókjához, és győződjön meg arról, hogy a megfelelő konfiguráció frissült a keresési szolgáltatáshoz.

## <a name="next-steps"></a>Következő lépések

A keresési szolgáltatás privát kapcsolaton keresztüli elérésével kapcsolatos további információkért tekintse meg a következő cikket:

* [Hozzon létre egy privát végpontot biztonságos kapcsolódáshoz az Azure Cognitive Search](service-create-private-endpoint.md)