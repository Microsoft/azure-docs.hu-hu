---
title: A Azure Lab Services kapacitásának korlátai
description: További információ a kapacitási korlátokról (a virtuális gépek korlátairól) Azure Lab Services.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 9866628cd11ec8df67e6fe16ae8806f0f30ae9a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491018"
---
# <a name="capacity-limits-in-azure-lab-services"></a>A Azure Lab Services kapacitásának korlátai
Azure Lab Services az Azure-előfizetések alapértelmezett kapacitása korlátozza az Azure számítási kvótájának korlátozásait és a csalások enyhítését. Az Azure-előfizetések kezdeti kapacitási korláttal rendelkeznek, amely az előfizetés típusa, a normál számítási magok száma és a Azure Lab Services belül elérhető GPU magok alapján változhat. Ez korlátozza, hogy hány virtuális gép hozható létre a laborban, mielőtt a korlát növelésére lenne szüksége.  

Ha a vagy az előfizetés virtuálisgép-magokra vonatkozó korlátja eléri vagy elérte az előfizetést, akkor a rendszer üzeneteket fog látni Azure Lab Services amikor további virtuális gépeket létrehozó műveleteket próbál végrehajtani. Például: 

- Labor létrehozása
- Tesztkörnyezet közzététele
- A tesztkörnyezet kapacitásának beállítása további virtuális gépek meglévő laborba való felvételéhez

Ezek a műveletek akkor is letilthatók, ha már elérte a magok korlátját. 

![Alapvető korlátozások – figyelmeztető üzenet](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>Alapértelmezett korláttal rendelkező előfizetések nulla maggal
A csaláshoz leggyakrabban használt ritka előfizetések esetében az alapértelmezett határérték 0 standard maggal és 0 GPU-mag lehet. Ha ezen előfizetési típusok egyikét használja, akkor a labor-fiókját létrehozó rendszergazdának a Azure Lab Services használata előtt meg kell adnia a korlát növelését. 

A rendszergazda az alábbi lépéseket követve kérheti a korlát növelését:  

1.  Az előfizetésében [hozzon létre egy Lab-fiókot](tutorial-setup-lab-account.md).
2.  A Lab-fiók **Áttekintés** lapján kattintson a felső **korlát növelés** gombra. 
3.  A korlát növeléséhez kövesse az űrlapon található lépéseket a támogatási kérelem elküldéséhez.

## <a name="request-a-limit-increase"></a>Korlát növelésének kérése
Ha eléri a magok korlátját, akkor a Azure Lab Services használatának folytatásához korlátot kérhet. A kérelem folyamata egy ellenőrzőpont, amely biztosítja, hogy az előfizetés semmilyen csalás vagy véletlen, hirtelen nagy léptékű üzembe helyezés esetén sem vesz részt.

A Azure Lab Services-portálon található virtuálisgép-magok korlátozásával kapcsolatos üzenetek a korlát növelését kérő hivatkozást tartalmaznak. A hivatkozás egy új böngésző lapot nyit meg, amelyen létrehozhat egy új támogatási kérést. A probléma típusát, az előfizetést és a kvóta típusát automatikusan kitölti a rendszer, ahogyan az a következő képen is látható: 

![Új támogatási kérelem](./media/capacity-limits/new-support-request.png)


Ezt követően a rendszer kérni fogja, hogy adjon meg további információkat a korlát növeléséről. A **Leírás** mezőben adja meg a következő adatokat:

- Mit szeretne tenni (például egy labor létrehozásával egy számítógép-tudományi osztály tanításához, egy Hackathon futtatásához stb.)
- A laborhoz használt virtuális gép mérete
- A szükséges virtuális gépek száma

A támogatási kérelem elküldése után a rendszer áttekinti a kérelmet. Ha szükséges, felvesszük Önnel a kapcsolatot, hogy további részleteket kapjon. 

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikket:
- [Rendszergazdai útmutató – virtuális gép méretezése](administrator-guide.md#vm-sizing).
- [Gyakran ismételt kérdések](classroom-labs-faq.md).