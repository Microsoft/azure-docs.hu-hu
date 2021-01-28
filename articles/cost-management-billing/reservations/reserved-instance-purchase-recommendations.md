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
ms.openlocfilehash: 4f6187ccb143f065fed236495128add7a2ab1ee4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928534"
---
# <a name="reservation-recommendations"></a>Foglalási javaslatok

Az Azure-beli fenntartott példányok (RI) vásárlására vonatkozó javaslatokat az Azure Consumption [Reservation Recommendation API](/rest/api/consumption/reservationrecommendations), az [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) és az Azure Portal foglalásvásárlási folyamata hozza létre.

A javaslatkalkulációs módszer magadásának lépései:

1. A javaslati motor az elmúlt 7, 30 és 60 napra vonatkozóan kiértékeli az erőforrások óránkénti használatát az adott hatókörben.
2. A használati adatok alapján a motor szimulálja a költségeket a foglalásokkal és azok nélkül is.
3. A költségek szimulálása különböző mennyiségek esetében történik, és a maximális megtakarítást eredményező mennyiség a javasolt mennyiség.
4. Ha az erőforrásai rendszeresen le vannak állítva, a szimuláció nem fog megtakarítási lehetőséget találni, és nem ad vásárlási javaslatot.
5. Az ajánlott számítások közé tartoznak az igény szerinti használati díjszabásban esetlegesen felmerülő speciális kedvezmények.

## <a name="recommendations-in-the-azure-portal"></a>Javaslatok az Azure Portalon

Foglalásvásárlási javaslatok a vásárlási folyamat során is megjelennek az Azure Portalon. A javaslatok az **Ajánlott mennyiség** alatt jelennek meg. Az Azure által javasolt mennyiség megvásárlása teszi lehetővé a legnagyobb megtakarítást. Bár vásárolhat olyan mennyiséget, amelyet szeretne, ha más mennyiséget vásárol, a megtakarítás nem lesz optimális.

Lássunk néhány példát, miért.

Az alábbi szemléltető képen a kiválasztott javaslathoz az Azure 6 darab vásárlását javasolja.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="A foglalásvásárlási javaslatot bemutató példa" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Ha a **részletek** megjelenítése lehetőségre kattint, a javaslattal kapcsolatos további információk jelennek meg. Az alábbi képen a javaslat részletei láthatók. Az ajánlott mennyiség kiszámítása a lehető legnagyobb használat érdekében történik, és a rendszer a korábbi használat alapján számítja ki. Egyenetlen használat esetén előfordulhat, hogy az Önnek szóló javaslat nem 100%-os kihasználtságot céloz meg. A példában látható, hogy a kihasználtság az idő múlásával ingadozik. Látható a foglalás költsége, a lehetséges megtakarítási lehetőségek és a százalékos kihasználtság.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="A foglalások vásárlására vonatkozó javaslat részleteit bemutató példa " :::

A diagram és a becsült értékek a javasolt mennyiség növelésével változnak. A foglalási mennyiség növelésével csökken a megtakarítások száma, mivel a foglalás a lejárati időszakot is csökkenti. Más szóval a nem teljes mértékben kihasznált foglalásokért fizet.

Ha csökkenti a foglalások mennyiségét, a megtakarítás is csökkenni fog. Bár nagyobb a kihasználtság, valószínűleg lesznek olyan időszakok, amikor a foglalások nem fedezik teljesen a használatot. A foglalási mennyiségen túli használatot drágább használat alapján fizetett erőforrások fogják fedezni. A következő kép szemlélteti a lényeget. A foglalási mennyiséget manuálisan 4-re csökkentettük. A foglalás kihasználtsága megnövekszik, de a teljes megtakarítás csökken, mivel a díjköteles költségek jelennek meg.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="A módosított foglalásvásárlási javaslat részleteit bemutató példa" :::

A foglalásokon való megtakarítás maximalizálása érdekében a javaslathoz a lehető legközelebb álló foglalási mennyiséget érdemes választani.

## <a name="recommendations-in-azure-advisor"></a>Javaslatok az Azure Advisorban

Az Azure Advisorben is elérhetők foglalásvásárlási javaslatok. Tartsa szem előtt az alábbi szempontokat:

- Az Advisorban csak egyelőfizetéses hatókörre érvényes javaslatok érhetők el. Ha szeretné megtekinteni a teljes számlázási hatókörre vonatkozó javaslatokat (Számlázási fiók vagy számlázási profil), akkor:
  -  A Azure Portal keresse meg a **foglalások**  >  **Hozzáadás** elemet, majd válassza ki azt a típust, amelyre vonatkozóan meg szeretné tekinteni a javaslatokat.
- Az Advisorban elérhető javaslatok figyelembe veszik az elmúlt 30 napos használati trendet.
- A javaslatok mennyisége és megtakarítása egy hároméves foglalásra szolgál, ahol elérhető. Ha a szolgáltatáshoz nem értékesítenek hároméves foglalást, akkor a javaslatot az egyéves foglalási díj alapján számítjuk ki.
- Az ajánlott számítások közé tartoznak az igény szerinti használati díjszabásban esetlegesen felmerülő speciális kedvezmények.
- Ha megosztott hatókörű foglalást vásárol, az Advisor foglalási vásárlási javaslatai akár öt napot is igénybe vehetnek.

## <a name="other-expected-api-behavior"></a>Egyéb várt API-viselkedés

- Amikor hétnapos visszatekintési időszakot alkalmaz, előfordulhat, hogy nem kap javaslatokat, ha a virtuális gépek egy napnál tovább vannak leállítva.

## <a name="next-steps"></a>További lépések

- Ismerkedjen meg [az Azure-foglalási kedvezmény virtuális gépekre történő alkalmazásával](../manage/understand-vm-reservation-charges.md).
