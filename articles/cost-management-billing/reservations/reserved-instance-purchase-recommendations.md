---
title: Azure foglalási javaslatok
description: További tudnivalók az Azure foglalási javaslatokról.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: d70580a34e832d6465571adbc8f0524abeba609a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767924"
---
# <a name="reservation-recommendations"></a>Foglalási javaslatok

Az Azure-beli fenntartott példányok (RI) vásárlására vonatkozó javaslatokat az Azure Consumption [Reservation Recommendation API](/rest/api/consumption/reservationrecommendations), az [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) és az Azure Portal foglalásvásárlási folyamata hozza létre.

A javaslatkalkulációs módszer magadásának lépései:

1. A javaslati motor az elmúlt 7, 30 és 60 napra vonatkozóan kiértékeli az erőforrások óránkénti használatát az adott hatókörben.
2. A használati adatok alapján a motor szimulálja a költségeket a foglalásokkal és azok nélkül is.
3. A költségek szimulálása különböző mennyiségek esetében történik, és a maximális megtakarítást eredményező mennyiség a javasolt mennyiség.
4. Ha az erőforrásai rendszeresen le vannak állítva, a szimuláció nem fog megtakarítási lehetőséget találni, és nem ad vásárlási javaslatot.
5. A javaslatszámítások tartalmazzák az igény szerinti használati díjakra vonatkozó esetleges különleges kedvezményeket.

## <a name="recommendations-in-the-azure-portal"></a>Javaslatok az Azure Portalon

Foglalásvásárlási javaslatok a vásárlási folyamat során is megjelennek az Azure Portalon. A javaslatok az **Ajánlott mennyiség** alatt jelennek meg. Az Azure által javasolt mennyiség megvásárlása teszi lehetővé a legnagyobb megtakarítást. Bár bármilyen mennyiséget vásárolhat, ha más mennyiséget vásárol, a megtakarításai nem lesznek optimálisak.

Lássunk néhány példát, miért.

Az alábbi szemléltető képen a kiválasztott javaslathoz az Azure 6 darab vásárlását javasolja.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="A foglalásvásárlási javaslatot bemutató példa" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Ha a Részletek megtekintése lehetőséget választja, megjelenik a **javaslatra vonatkozó további információ.** Az alábbi képen a javaslat részletei láthatók. Az ajánlott mennyiséget a rendszer a lehető legnagyobb kihasználtság alapján számítja ki, és a korábbi használat alapján számítja ki. Egyenetlen használat esetén előfordulhat, hogy az Önnek szóló javaslat nem 100%-os kihasználtságot céloz meg. A példában figyelje meg, hogy a kihasználtság idővel ingadozott. Látható a foglalás költsége, a lehetséges megtakarítási lehetőségek és a százalékos kihasználtság.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Példa egy foglalásvásárlási javaslat részleteire " :::

A diagram és a becsült értékek a javasolt mennyiség növelésekor változnak. A foglalás mennyiségének növelésével a megtakarítások csökkennek, mivel a foglalási használat csökkenni fog. Más szóval a nem teljes mértékben kihasznált foglalásokért fizet.

Ha csökkenti a foglalási mennyiséget, a megtakarítás is csökken. Bár nagyobb a kihasználtság, valószínűleg lesznek olyan időszakok, amikor a foglalások nem fedezik teljesen a használatot. A foglalási mennyiségen túli használatot drágább használat alapján fizetett erőforrások fogják fedezni. A következő kép szemlélteti a lényeget. A foglalási mennyiséget manuálisan 4-re csökkentettük. A foglalás kihasználtsága nő, de a teljes megtakarítás csökken, mivel használat alapján kell fizetni.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="A módosított foglalásvásárlási javaslat részleteit bemutató példa" :::

A foglalásokon való megtakarítás maximalizálása érdekében a javaslathoz a lehető legközelebb álló foglalási mennyiséget érdemes választani.

## <a name="recommendations-in-azure-advisor"></a>Javaslatok az Azure Advisorban

Az Azure Advisorben is elérhetők foglalásvásárlási javaslatok. Tartsa szem előtt az alábbi szempontokat:

- Az Advisorban csak egyelőfizetéses hatókörre érvényes javaslatok érhetők el. Ha a teljes számlázási hatókörre (számlázási fiók vagy számlázási profil) vonatkozó javaslatokat szeretne látni, akkor:
  -  A Azure Portal a **Reservations** Add (Foglalások hozzáadása) gombra, majd válassza ki azt a típust, amely számára a  >   javaslatokat látni szeretné.
- Az Advisorban elérhető javaslatok figyelembe veik az elmúlt 30 napos használati trendet.
- A javaslatok mennyisége és megtakarítása egy három éves foglalásra szolgál, ahol elérhető. Ha a szolgáltatáshoz nem ad el egy három éves foglalást, a rendszer az egyéves foglalási ár alapján számítja ki a javaslatot.
- A javaslatszámítások tartalmazzák az igény szerinti használati díjakra vonatkozó esetleges különleges kedvezményeket.
- Ha megosztott hatókörű foglalást vásárol, az Advisor foglalásvásárlási javaslatai akár öt napig is eltűnhetnek.

## <a name="other-expected-api-behavior"></a>Egyéb várt API-viselkedés

- Amikor hétnapos visszatekintési időszakot alkalmaz, előfordulhat, hogy nem kap javaslatokat, ha a virtuális gépek egy napnál tovább vannak leállítva.

## <a name="next-steps"></a>További lépések
- Foglalási [javaslatokat kap REST API-k használatával.](/rest/api/consumption/reservationrecommendations/list)
- Ismerkedjen meg [az Azure-foglalási kedvezmény virtuális gépekre történő alkalmazásával](../manage/understand-vm-reservation-charges.md).
