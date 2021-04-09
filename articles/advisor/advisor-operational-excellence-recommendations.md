---
title: Az operatív Uram fejlesztése az Advisor szolgáltatással
description: Az Azure-előfizetések működési kiválóságának optimalizálása Azure Advisor használatával.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 0b938a0c7a42182bb8d2a50b48d65a0844d952a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579954"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Az operatív kiválóság elérése Azure Advisor használatával

A Azure Advisor működési kiválósági javaslatai a következőket segítik: 
- Folyamatok és munkafolyamatok hatékonysága.
- Erőforrás-kezelhetőség.
- Ajánlott eljárások az üzembe helyezéshez. 

Ezeket a javaslatokat az Advisor irányítópultjának **működési kiválóság** lapján érheti el.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure Service Health riasztások létrehozása, amelyekről értesítést kap, ha az Azure-problémák érintik Önt

Javasoljuk, hogy állítson be Azure Service Health riasztásokat, hogy értesítést kapjon, ha az Azure-szolgáltatással kapcsolatos problémák érintik Önt. A [Azure Service Health](https://azure.microsoft.com/features/service-health/) egy ingyenes szolgáltatás, amely személyre szabott útmutatást és támogatást nyújt, ha az Azure-szolgáltatással kapcsolatos problémát érint. Az Advisor olyan előfizetéseket azonosít, amelyek nem rendelkeznek konfigurált riasztásokkal, és azt javasolja, hogy konfigurálja azokat.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Tervezze meg a Storage-fiókokat, hogy megakadályozza a maximális előfizetési korlát elérését

Egy Azure-régió legfeljebb 250 Storage-fiókot tud támogatni előfizetésre. A korlát elérése után nem hozhat létre Storage-fiókokat az adott régióban/előfizetési kombinációban. Az Advisor ellenőrzi az előfizetéseket, és javaslatokat tesz arra, hogy kevesebb Storage-fiókot tervezzen meg a korlát eléréséhez közel álló bármely régióhoz vagy előfizetéshez.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ellenőrizze, hogy van-e hozzáférése az Azure Cloud experthez, amikor szüksége van rá

Üzleti szempontból kritikus fontosságú számítási feladatok futtatásakor fontos a technikai támogatáshoz való hozzáférés, amikor szüksége van rá. Az Advisor olyan potenciális üzleti szempontból kritikus előfizetéseket azonosít, amelyek nem rendelkeznek technikai támogatással a támogatási csomagban. Javasoljuk, hogy frissítsen egy olyan lehetőségre, amely technikai támogatást is tartalmaz.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>A készlet törlése és újbóli létrehozása egy elavult belső összetevő eltávolításához

Ha a készlet elavult belső összetevőt használ, törölje, majd hozza létre újra a készletet a jobb stabilitás és teljesítmény érdekében.

## <a name="repair-invalid-log-alert-rules"></a>Érvénytelen naplózási riasztási szabályok javítása

Azure Advisor észleli azokat a riasztási szabályokat, amelyekben a feltételek szakaszban megadott lekérdezések érvénytelenek. A naplózási riasztási szabályokat a Azure Monitorban hozhatja létre, és a segítségével meghatározott időközönként futtathatja az elemzési lekérdezéseket. A lekérdezés eredményei határozzák meg, hogy egy riasztást aktiválni kell-e. Az elemzési lekérdezések a hivatkozott erőforrások, táblák vagy parancsok változásai miatt változhatnak az idő múlásával. Az Advisor azt javasolja, hogy javítsa ki a lekérdezést a riasztási szabályban, hogy megakadályozza, hogy a rendszer automatikusan letiltsa, és biztosítsa az Azure-beli erőforrások lefedettségét. [További információ a riasztási szabályok hibaelhárításáról.](../azure-monitor/alerts/alerts-troubleshoot-log.md)

## <a name="use-azure-policy-recommendations"></a>Azure Policy javaslatok használata

Azure Policy egy Azure-szolgáltatás, amely szabályzatok létrehozására, hozzárendelésére és kezelésére használható. Ezek a szabályzatok az erőforrásokra vonatkozó szabályokat és hatásokat kényszerítik ki. A következő Azure Policy javaslatok segíthetnek a működési Uram elérésében: 

**Címkék kezelése.** Ez a szabályzat az erőforrások minden létrehozásakor vagy frissítésekor hozzáadja vagy lecseréli a megadott címkét és értéket. A meglévő erőforrásokat szervizelési feladat aktiválásával javíthatja. Ez a szabályzat nem módosítja az erőforráscsoportok címkéit.

**A Geo-megfelelőségi követelmények betartatása.** Ezzel a szabályzattal korlátozható azon helyek köre, amelyeket a szervezet megadhat az erőforrások üzembe helyezésekor. 

**Az engedélyezett virtuálisgép-SKU-ket adhatja meg a központi telepítésekhez.** Ezzel a szabályzattal megadhatók a virtuális gépek azon termékváltozatai, amelyeket a szervezet üzembe helyezhet.

***Felügyelt lemezeket nem használó virtuális gépek naplózása*.**

**Engedélyezi *a címke öröklését az erőforráscsoportok közül*.** Ez a szabályzat az erőforrások minden létrehozásakor vagy frissítésekor hozzáadja vagy lecseréli a megadott címkét és értéket a fölérendelt erőforráscsoportból. A meglévő erőforrásokat szervizelési feladat aktiválásával javíthatja.

Az Advisor néhány egyéni Azure-szabályzatot javasol, amelyek segítségével az ügyfelek operatív kiválóságot érhetnek el az ajánlott eljárások bevezetésével. Ha az ügyfél úgy dönt, hogy egy javasolt szabályzatot rendel hozzá, akkor a rendszer letiltja a javaslatot. Ha az ügyfél úgy dönt, hogy később eltávolítja a szabályzatot, az Advisor továbbra is letiltja a javaslatot, mert az eltávolítását a következők erős jelzésének megfelelően értelmezi:

1.  Az ügyfél eltávolította a szabályzatot, mert az Advisor javaslata ellenére nem vonatkozik az adott használati esetre. 
2.  Az ügyfél ismeri és ismeri a szabályzatot a hozzárendelés és a törlés után, és szükség esetén újra hozzárendelheti vagy eltávolíthatja azt anélkül, hogy ez a használati eset későbbi részévé válik. Ha az ügyfél megtalálta a legjobb érdeklődését arra, hogy ismét ugyanazt a szabályzatot rendelje hozzá, akkor Azure Policy az Advisor javaslata nélkül is megteheti. Vegye figyelembe, hogy ez a logika kifejezetten az Operational Excellence kategóriába tartozó szabályzati javaslatra vonatkozik. Ezek a szabályok nem vonatkoznak a biztonsági javaslatokra.  


## <a name="no-validation-environment-enabled"></a>Nincs engedélyezve ellenőrzési környezet
Azure Advisor megállapítja, hogy a jelenlegi előfizetésben nincs engedélyezve az érvényesítési környezet. A gazdagépek létrehozásakor \" \" \" \" a Properties (Tulajdonságok) lapon a nem for érvényesítési környezet lehetőséget választotta. Ha legalább egy, engedélyezett ellenőrzési környezettel rendelkező gazdagép biztosítja az üzleti folytonosságot a Windows Virtual Desktop szolgáltatás üzemelő példányán, a lehetséges problémák korai észlelésével. [További információ](../virtual-desktop/create-validation-host-pool.md)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>Biztosítsa az éles (nem ellenőrzési) környezet stabil működését
Azure Advisor észleli, hogy a gazdagép-készletek túl sok engedélyezett ellenőrzési környezettel rendelkezik. Ahhoz, hogy az ellenőrzési környezetek a legjobban kiszolgálják a céljukat, rendelkeznie kell legalább egy, de soha nem több mint fele a gazdagép-készletek érvényesítési környezetben. Ha a gazdagép-készletek és az általa letiltott ellenőrzési környezet között kifogástalan egyensúlyt biztosít, akkor a legjobb megoldás, ha kihasználja a Windows virtuális asztal által az egyes frissítésekkel elérhető többfokozatú központi telepítések előnyeit. A probléma megoldásához nyissa meg a gazdagép készletének tulajdonságait, és válassza \" a nem lehetőséget \" az \" érvényesítési környezet \" beállítása mellett.

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>A Traffic Analytics engedélyezése az Azure-erőforrások forgalmi mintáinak közelebbi vizsgálatához
A Traffic Analytics egy felhőalapú megoldás, amely gondoskodik róla, hogy át lehessen tekinteni a felhasználók és az alkalmazások Azure-beli tevékenységeit. A Traffic Analytics a Network Watcher hálózati biztonsági csoport (NSG) forgalomnaplóinak az elemzésével biztosít betekintést a forgalom részleteibe. A Traffic Analytics segítségével megtekintheti az Azure-beli és nem Azure-beli üzemelő példányok legforgalmasabb szereplőit, megvizsgálhatja a környezet nyitott portjait, protokolljait és kártékony folyamatait, és optimalizálhatja a hálózati üzemelő példányt a teljesítmény érdekében. A forgalomnaplókat 10 és 60 perces időközönként dolgozhatja fel, így gyorsabban hozzájut az elemzési eredményekhez. Ajánlott az Azure-erőforrások Traffic Analyticsának engedélyezése. 


## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további tudnivalókért tekintse meg a következő témakört:
* [Az Advisor bemutatása](advisor-overview.md)
* [Első lépések](advisor-get-started.md)
* [Az Advisor költségekkel kapcsolatos javaslatai](advisor-cost-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor megbízhatósági javaslatok](advisor-high-availability-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
