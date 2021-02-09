---
title: A mért számlázás rendellenességének észlelése | Azure piactér
description: Ismerje meg, hogy a mért számlázások automatikus anomáliának észlelése Hogyan biztosítja ügyfelei számára a kereskedelmi Piactéri ajánlat mért használati díjainak megfelelő számlázást.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 1/09/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: d4fb88854359dcd6e383b47d2a8ce4e9c91f867a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989364"
---
# <a name="anomaly-detection-for-metered-billing"></a>A mért számlázás rendellenességének észlelése

Ez a cikk részletesen ismerteti a piactér-mérési szolgáltatást és a kapcsolódó automatikus anomália-észlelési képességet, amellyel biztosítható, hogy az ügyfelek a mért használatnak megfelelően legyenek kiszámlázva. A mért számlázási lehetőség jelenleg a [szolgáltatott szoftverek](plan-saas-offer.md) (SaaS) és az [Azure-alkalmazások](plan-azure-application-offer.md#types-of-plans) számára érhető el egy felügyelt alkalmazási csomaggal. Ez a beállítás lehetővé teszi, hogy a partnerek a kereskedelmi piactér programban olyan ajánlatokat hozzanak létre, amelyek a nem standard egységek szerint vannak felszámítva.

Az SaaS-és felügyelt alkalmazásokhoz használt egyéni mérőszámokkal rendelkező partnerek a partner Center adott _Egyéni_ mérőszámai _esetében a várt_ használati viselkedéstől való eltérést láthatják. A kockázat enyhítése érdekében a partner Center olyan anomália-észlelési szolgáltatást alkalmaz, amely a gépi tanulási algoritmusok alapján határozza meg a normál mért számlázási viselkedést, elemzi a mért számlázási használatot, és felderíti a rendellenességeket a minimális felhasználói beavatkozással. A mért számlázási használati adatkészletekben az _anomáliák észlelési modelljeinek_ használatával a partneri Központ célja, hogy tájékoztassa a közzétevőt, amikor a jelentett használat meghaladja a várt használatot.

## <a name="usability-experience"></a>Használhatósági élmény

A Microsoft a partnert arra támaszkodik, hogy az ügyfelet a SaaS vagy az Azure által felügyelt alkalmazásra vonatkozó ajánlatai alapján jelentse be, mielőtt a Microsoft számlázza az ügyfelet. Ha helytelen használatot jelentettek, az ügyfél esetleg helytelen számlát kaphat, és alááshatja a Microsoft és a partner hitelességét is.

Ennek enyhítése érdekében az SaaS-alkalmazások és az Azure-alkalmazások által felügyelt alkalmazások csomagjainak automatikus anomália-észlelési funkciója is elérhető. Ez a funkció egy gépi tanulási modell, amely proaktív módon figyeli a használatot a mért számlázással szemben, és a várt tartományon belül előre jelzi a használat várható értékét. Ha a használat a várt tartományon kívül esik, akkor a rendszer anomália-ként kezeli, és riasztási értesítés jelenik meg az ajánlat áttekintés lapján a partner Center kereskedelmi piactér programban.

A Machine learning-modell napi rendszerességgel elemzi a használatot. A közzétevő láthatja, hogy az ügyfeleken fellépő összes rendellenesség az ajánlat egyéni mérőszámának méretei szerint jelenik meg.

### <a name="view-and-manage-metered-usage-anomalies"></a>Mért használati rendellenességek megtekintése és kezelése

1. Jelentkezzen be a [partner központjába](https://partner.microsoft.com/dashboard/home).
1. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **elemzése** elemet.
1. Válassza ki a **mért használat Anomális** fület.

    [![A mért használat Anomális lapjának bemutatása a használat lapon.](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***1. ábra: a mért használat Anomális lapja***

1. A mért számlázással kapcsolatban észlelt használati rendellenességek esetén a közzétevőnek meg kell vizsgálnia a vizsgálatot, és meg kell erősítenie, hogy a rendellenesség igaz vagy sem. A diagnosztika megerősítéséhez válassza a **megjelölés anomália** lehetőséget.

     [![A megjelölés anomália párbeszédpanelt mutatja be.](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***2. ábra: a megjelölés anomália párbeszédpanelként***

1. Ha úgy véli, hogy a felderített túlhasználati anomália nem valódi, a visszajelzések megadásához válassza ki a partner Center **megjelölt rendellenességét** az adott túlhasználatnál.

    [![Szemlélteti a miért nem anomália párbeszédpanelt.](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***3. ábra: Miért nem anomália? párbeszédpanel***

1. Az oldal görgetésével megjelenítheti a nem nyugtázott rendellenességek listáját. A lista a nem nyugtázott rendellenességek leltárát tartalmazza. Dönthet úgy is, hogy a partner Center által megjelölt rendellenességek bármelyikét kijelöli valódi vagy hamis értékként.

   [![A partner Center nem nyugtázott anomáliák listáját mutatja a használat lapon.](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***4. ábra: a partneri központ nem nyugtázott rendellenességek listája***

1. Olyan anomália-műveleti naplót is látni fog, amely megjeleníti a túlterhelési használat során végrehajtott műveleteket. A műveleti naplóban láthatja, hogy mely túlhasználati események lettek megjelölve valódi vagy hamis értékként.

   A [ ![ használati oldalon látható anomália művelet naplóját mutatja be.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox) 
    ***5. ábra: anomália műveleti napló***

1. A partner Center Analytics nem támogatja a túlhasználati használati események átadását az exportálási jelentésekben. A partner Center lehetővé teszi, hogy egy anomália korrigált túlterhelési felhasználását adja meg, és a rendszer a részleteket a Microsoft Teams for vizsgálat céljából adja át. A vizsgálat alapján a Microsoft a megfelelő módon kiadja a kreditek visszatérítését a túlterhelt ügyfélnek. Ha bármelyik megjelölt rendellenességet kijelöli, akkor a **megjelölés anomália** lehetőség kiválasztásával megjelölheti a használaton túli anomáliát valódi értékként.

   [ ![ A megjelölés anomália párbeszédpanel megjelenítése.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox) 
    ***6. ábra: rendellenességként való megjelölés párbeszédpanel***

Ha az első alkalommal, amikor egy túlhasználatot szabálytalanként jelölt meg a partner Centerben, egy 30 napos időszakot kap az adott példánytól, hogy a rendellenességet valódi vagy hamis értékként jelölje meg. A 30 napos időszak után a közzétevő nem fogja tudni fellépni a rendellenességeket.

> [!IMPORTANT]
> A jelentett kereten túli használati egységek nem jogosultak az újraértékelésre és a pénzügyi módosításra.

A kiválasztott számítási időszakra vonatkozó összes rendellenesség (elismert vagy egyéb) látható. A különböző számítási időszakok az elmúlt 30 nap, az utolsó 60 nap és az utolsó 90 nap.

> [!IMPORTANT]
> A rendszer arra kéri, hogy az észlelt rendellenességektől számított 30 napon belül járjon el, amikor az első jelentések a partner Centerben jelennek meg.

A widget szűrője lehetővé teszi az egyéni ajánlatok és egyéni mérőszámok kiválasztását.

Ha a túlhasználatot rendellenességként jelölte meg, vagy egy olyan modellt fogad el, amely a valódi anomáliát jelölte meg, akkor a kijelölés nem módosítható "nem anomália" értékre.

> [!IMPORTANT]
> Túlterhelő körülmények esetén újra elküldheti a túlhasználatot.

## <a name="see-also"></a>Lásd még
- [Díjköteles számlázás az SaaS-hez a kereskedelmi Piactéri mérési szolgáltatás használatával](./partner-center-portal/saas-metered-billing.md)
- [Felügyelt alkalmazás mért számlázása](./partner-center-portal/azure-app-metered-billing.md)
