---
title: Önkiszolgáló cseréje és a visszatérítéseket Azure lefoglalási |} A Microsoft Docs
description: Ismerje meg, hogyan lehet exchange vagy az Azure-foglalások visszatérítés.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 54578746ea8029a760663edc456660f98358abc5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615988"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Önkiszolgáló cseréje és a visszatérítéseket Azure lefoglalási

Az Azure-foglalások a változó igényeinek kielégítése érdekében rugalmasságot biztosítanak. A foglalásokat le tudja cserélni azonos típusú foglalásokra. Kérheti egy foglalás díjának visszatérítését is 50 000 USD/év összegig, ha már nincs rá szüksége.

Az önkiszolgáló csere- és lemondási lehetőség nem érhető el az Egyesült Államok kormányának nagyvállalati szerződésével rendelkező ügyfelei számára. Más US Government előfizetéstípusok, beleértve a használatalapú fizetés és a CSP támogatottak.

Exchange-vagy egy meglévő foglalás visszatérítés ahhoz a Foglalás tulajdonosi hozzáféréssel kell rendelkeznie.

## <a name="exchange-an-existing-reserved-instance"></a>Exchange-meglévő fenntartott példány

A Foglalás három gyors lépésben is cserél az [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Válassza a foglalások visszatérítés, és kattintson a kívánt **Exchange**.  
    ![A példában a foglalások való visszatéréshez kép](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Válassza ki a megvásárolni kívánt virtuálisgép-terméket, és adja meg a mennyiséget. Győződjön meg arról, hogy az új vásárlási összes több, mint a visszatérési teljes. [Határozza meg a megfelelő méretű megvásárlása előtt](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Példa a virtuális gép terméket egy exchange-beszerzési képe](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Tekintse át, és a tranzakció elvégzéséhez.  
    ![Meg kell vásárolnia egy Exchange-hez, a visszatérési elvégzése a virtuális gép termék például a kép](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Visszatérítés egy foglalást, lépjen a **foglalás részletei** kattintson **visszatérítés**.

## <a name="how-transactions-are-processed"></a>Tranzakciók feldolgozásának módja

Először a Microsoft megszakítja a meglévő foglalási, és a Foglalás időarányosan összeg visszatérítések. Ha egy exchange, az új vásárlási dolgoz fel. A Microsoft az alábbi módszereket, a fiók típusától függően és a fizetési mód használatával visszatérítések folyamatok:

### <a name="enterprise-agreement-customers"></a>Nagyvállalati szerződéssel rendelkező ügyfelek

Pénzt adnak hozzá a pénzügyi kötelezettségvállalást a cseréje és díjainak visszatérítése, ha az eredeti vásárlás történt egy. Nincsenek kerettúllépési számlák óta az eredeti vásárlási újra meg, és győződjön meg arról, hogy a pénzügyi kötelezettségvállalás, rerated szolgál. Ha a pénzügyi kötelezettségvállalás kifejezés használatával a Foglalás megvásárlásának már nem aktív, majd kredit hozzáadódik az aktuális nagyvállalati szerződés pénzügyi kötelezettségvállalási időszak.

Ha az eredeti vásárlást kerettúllépésként történt, a Microsoft jóváírás bocsát ki.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Használatalapú fizetéses számlás fizetést, és a CSP program

Az eredeti foglalás beszerzési számlát meg lett szakítva, és akkor jön létre az új számla a visszatérítés ellenében. Cserék az új számla mutatja be, és az új vásárlási visszatérítés. A visszatérítés összege módosul a vásárlás ellen. Csak térítjük egy foglalást, ha a időarányos összeg marad a Microsoft, és egy későbbi foglalásvásárlás ellen úgy kell beállítani.

### <a name="pay-as-you-go-credit-card-customers"></a>Használatalapú fizetés a hitelkártyás ügyfelekre

Az eredeti számla meg lett szakítva, és a egy új számlát hoznak létre. A költséget takaríthat meg, amelyet az eredeti vásárlás használt hitelkártyára van térítjük. Ha módosította a kártya [forduljon az ügyfélszolgálathoz](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="exchange-policies"></a>Exchange-szabályzatok

- Több azonos típusú új foglalást vásárolhat meglévő foglalások adhat vissza. Egy másik típusú foglalások nem váltanak. Például egy virtuális gép foglalást vásárolhat egy SQL-foglalást, nem adhat vissza.
- Foglalás tulajdonosai csak egy exchange tud feldolgozni. [Ismerje meg, hogyan hozzáadása vagy módosítása egy foglalást kezelésére jogosult felhasználók](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation).
- Egy exchange visszatérítés és visszavásárlási feldolgozása – különböző tranzakció jön létre a megszakítás és az új vásárlási. A időarányos foglalási mennyiség van térítjük zárolások, hogy Ön beszámítását. Díjkötelesek teljesen új vásárlására. Időarányos foglalás tárterülete a foglalást, a visszaadott napi ténylegesen fennmaradó értéke.
- Tudjon cserélni vagy visszatérítés foglalások akkor is, ha a nagyvállalati Szerződéssel való megvásárolja a foglalást használt lejárt, és egy új megállapodás megújítani.
- Módosíthatja például a méret, régió, mennyiség és kifejezés egy exchange-foglalás tulajdonságokat.
- Az új vásárlási összes kell egyenlő vagy nagyobb, mint a visszaadott összeg lehet.
- Az új exchange részeként vásárolt foglaláshoz exchange időpontjától új kifejezést.
- Nincs napján belül pótdíj vagy éves korlátok cseréjére.

## <a name="refund-policies"></a>Visszatérítés házirendek

- A teljes visszatérítés összege legfeljebb 50 000 USD-12 hónapos gördülő ablakban.
- Csak a Foglalás tulajdonosok visszatérítést tud feldolgozni. [Ismerje meg, hogyan hozzáadása vagy módosítása egy foglalást kezelésére jogosult felhasználók](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- A Microsoft fenntartja a jogot bármely értéket ad vissza, a 12 %-os napján belül pótdíj díja szerint számítjuk fel, bár a büntetés nem jelenleg díjat számítunk fel.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Exchange a nem prémium szintű storage prémium szintű tárfiók

A virtuális gép méretét, amely nem támogatja a premium storage egy megfelelő Virtuálisgép-méretet, amelyet a megvásárolt foglalás tudjon cserélni. Ha például egy _F1_ számára egy _F1s_. Ahhoz, hogy az exchange, ugorjon a Foglalás részleteit, és kattintson **Exchange**. Az Exchange-hez nem időszak a fenntartott példány visszaállítása, vagy hozzon létre egy új tranzakciót.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan kezelheti a foglalást, lásd: [kezelése az Azure-foglalások](billing-manage-reserved-vm-instance.md).
- Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:
    - [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
    - [Az Azure-ban foglalások kezelése](billing-manage-reserved-vm-instance.md)
    - [Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt](billing-understand-vm-reservation-charges.md)
    - [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
    - [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
    - [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)
    - [A Partner Center Felhőszolgáltató (CSP) program Azure foglalások](/partner-center/azure-reservations)
