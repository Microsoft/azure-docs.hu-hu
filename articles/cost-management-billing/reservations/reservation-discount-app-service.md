---
title: Foglalási kedvezmények az Azure App Service-hez
description: Ismerje meg, hogyan vonatkoznak a foglalási kedvezmények Azure App Service Premium v3-példányokra és elkülönített bélyegzőre.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: banders
ms.openlocfilehash: c599c64ce4b22bbf7bece77602b22fef6629d07c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369730"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-premium-v3-instances-and-isolated-stamps"></a>A foglalási kedvezmények Azure App Service Premium v3-példányok és elkülönített bélyegek esetében

Ebből a cikkből megtudhatja, hogyan vonatkoznak a kedvezmények a Azure App Service Premium v3-példányokra és az elkülönített bélyegzőre.

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>A foglalási kedvezmények a prémium v3 példányokra vonatkoznak

Miután megvásárolt egy Azure App Service Premium v3 fenntartott példányt, a foglalási kedvezményt a rendszer automatikusan alkalmazza App Service példányokra, amelyek megfelelnek a foglalás attribútumainak és mennyiségének. A foglalás fedezi a prémium v3-példányok költségét. 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>A kedvezmény Azure App Servicere való alkalmazása 

A foglalási kedvezményt a- *it-vagy-Lose-it használja*. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.
Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák elvesznek.

### <a name="reservation-discount-for-premium-v3-instances"></a>Prémium v3 példányok foglalási kedvezménye

Az Azure foglalási kedvezményt a prémium v3 példányok óránkénti futtatására alkalmazzák. A megvásárolt foglalások egyeztetése a Premium v3-példányok futtatásával történik a foglalási kedvezmény alkalmazásához. A teljes órát nem futtató prémium v3-példányok esetén a foglalás a foglalást nem használó más példányokból is betöltődik, beleértve az egyidejű futtatású példányokat is. Az óra végén az óra alatt található példányok foglalási alkalmazása zárolva van. Abban az esetben, ha egy példány nem fut egy órán belül, vagy az egyidejű példányok nem töltik ki a foglalás óráját, a foglalást az adott órára kihasználjuk. A következő diagram egy foglalás számlázható virtuálisgép-használatra való alkalmazását mutatja be. Az ábra egy foglalási vásárláson és két egyező virtuálisgép-példányon alapul.

![Az a kép, amely egy, a számlázható virtuális gép használatára vonatkozó foglalás alkalmazását mutatja](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  A foglalást meghaladó használatért a normál használatalapú fizetési díjszabás szerint kell fizetni. A foglalást meg nem haladó használatért nem számítunk fel díjat, mert az a foglalás vásárlásának részeként már be lett fizetve.
2.  Az 1. órában az 1. példány 0,75 órán keresztül, a 2. példány pedig 0,5 órán keresztül fut. Az 1. óra összesített használata 1,25 óra. A fennmaradó 0,25 óráért használatalapú díjszabás szerint kell fizetni.
3.  A 2. és a 3. óra esetében mindkét példány 1 órán keresztül futott. Az egyik példány használatát a foglalás fedezi, a másikra használatalapú fizetési díjszabást alkalmazunk.
4.  A 4. órában az 1. példány 0,5 órán keresztül, a 2. példány pedig 1 órán keresztül fut. A foglalás az 1. példány használatát teljesen, a 2. példány használatából pedig 0,5 órát fedez. A fennmaradó 0,5 óráért használatalapú díjszabás szerint kell fizetni.

Az Azure Reservations számlázási használati jelentésekben történő alkalmazásának megismeréséhez és megtekintéséhez lásd [a foglalások használatának ismertetését](understand-reserved-instance-usage-ea.md).

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>A foglalási kedvezmények izolált bélyegekre való alkalmazása

Miután az App Service izolált tranzakcióinak számára fenntartott kapacitást vásárolt, a foglalási kedvezmény automatikusan érvényesül az adott régió tranzakciódíján. A foglalási kedvezmény az izolált tranzakció díjszabási mérőszáma által mért használatra vonatkozik. A feldolgozókért, a további előtérrendszerekért és a tranzakcióhoz kapcsolódó minden más erőforrásért továbbra is a normál díjszabás szerint kell fizetni.

### <a name="reservation-discount-application"></a>A foglalási kedvezmény alkalmazása

Az App Service izolált tranzakciódíj-kedvezményt a rendszer óránként alkalmazza a futó izolált tranzakciókra. Ez azt jelenti, hogy ha nem üzemel végig valamely tranzakció az óra időtartama alatt, akkor az arra az órára vonatkozó lefoglalt kapacitás kárba vész. A kapacitás nem vihető át a következő órára.

A vásárlás után a megvásárolt foglalást a rendszer megfelelteti egy izolált tranzakciónak, amely egy adott régióban fut. Ha leállítja ezt a tranzakciót, a foglalási kedvezmények automatikusan a régióban futó más tranzakciókra lesznek érvényesítve. Ha nincsenek tranzakciók, a foglalás a régióban következőként létrehozott tranzakción lesz érvényesítve.

Ha a tranzakciók nem futnak végig az óra időtartama alatt, a foglalás automatikusan más megfelelő tranzakciókon lesz érvényesítve ugyanabban a régióban és órában.

### <a name="choose-a-stamp-type---windows-or-linux"></a>Tranzakciótípus választása – Windows vagy Linux

Az üres izolált tranzakciók alapértelmezés szerint a Windows-tranzakciómérőszámot bocsátják ki. Ez például akkor van így, ha nincsenek üzembe helyezve feldolgozók. A tranzakció továbbra is ezt a mérőszámot bocsátja ki, ha Windows-feldolgozók lesznek üzembe helyezve. Ha üzembe helyez egy Linux-feldolgozót, akkor a mérő a Linux-tranzakciómérőszámra vált át. Ha Linux- és Windows-feldolgozók is üzembe vannak helyezve, a tranzakció Windows-mérőszámot bocsát ki.

Ennek eredményeképpen a tranzakció mérőszáma a Windows és a Linux között váltakozhat a tranzakció élettartama során. A foglalások operációsrendszer-specifikusak. Olyan foglalást vásároljon, amely támogatja a tranzakción üzembe helyezni kívánt feldolgozókat. A csak Windows-alapú és a vegyes tranzakciók a Windows-foglalást használják. A csak Linux-feldolgozókkal rendelkező tranzakciók a Linux-foglalást használják.

Linux-alapú tranzakciófoglalást csak akkor érdemes vásárolnia, ha a tranzakción _kizárólag_ Linux-feldolgozókkal szeretne rendelkezni.

### <a name="discount-examples"></a>Példák a kedvezményre

Az alábbi példák bemutatják, hogyan érvényesül a fenntartott példányokra vonatkozó izolált tranzakciódíj-kedvezmény az üzemelő példányoktól függően.

- **1. példa**: A lefoglalt izolálttranzakció-kapacitás egy példányát vásárolja meg egy App Service izolált tranzakcióval nem rendelkező régióban. Új tranzakciót helyez üzembe a régióban, és a foglalásnak megfelelő díjszabás szerint fizet a tranzakcióért.
- **2. példa**: A lefoglalt izolálttranzakció-kapacitás egy példányát vásárolja meg egy üzembe helyezett App Service izolált tranzakcióval már rendelkező régióban. Az üzembe helyezett tranzakcióért innentől a foglalásnak megfelelő díjszabás szerint fizet.
- **3. példa**: A lefoglalt izolálttranzakció-kapacitás egy példányát vásárolja meg egy üzembe helyezett App Service izolált tranzakcióval már rendelkező régióban. Az üzembe helyezett tranzakcióért innentől a foglalásnak megfelelő díjszabás szerint fizet. Később törli a tranzakciót, és újat helyez üzembe. Az új tranzakcióért innentől a foglalásnak megfelelő díjszabás szerint fizet. Az üzembe helyezett tranzakciók nélküli időtartamok esetében a kedvezmények nem vihetők át.
- **4. példa**: A lefoglalt Linux-alapú izolálttranzakció-kapacitás egy példányát vásárolja meg egy régióban, majd új tranzakciót helyez üzembe a régióban. Amikor a tranzakciót kezdetben feldolgozók nélkül helyezi üzembe, az a Windows-tranzakciómérőszámot bocsátja ki. Ekkor nem részesül kedvezményben. Amint az első Linux-feldolgozót üzembe helyezi a tranzakción, az a Linux-tranzakciómérőszámot bocsátja ki, és a foglalási kedvezmény érvénybe lép. Ha később Windows-feldolgozót helyez üzembe a tranzakción, a tranzakció-mérőszám Windowsra vált vissza. Ettől fogva nem részesül kedvezményben a Linux-alapú izolálttranzakció-foglalásért.

## <a name="next-steps"></a>További lépések

- A foglalások kezelésének megismerése érdekében tekintse meg [Az Azure Reservations kezelése](manage-reserved-vm-instance.md) szakaszt.
- Ha többet szeretne megtudni az előzetes vásárlási App Service Premium v3 és az elkülönített Stamp fenntartott kapacitásról a pénz megtakarítása érdekében, tekintse meg a [fenntartott kapacitással rendelkező Azure app Service előre fizetését](prepay-app-service.md).
- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
  - [A Reservations kezelése az Azure-ban](manage-reserved-vm-instance.md)
  - [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](understand-reserved-instance-usage.md)
  - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
