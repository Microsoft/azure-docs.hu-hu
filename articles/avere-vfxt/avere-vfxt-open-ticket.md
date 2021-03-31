---
title: Hogyan kérhet támogatást az Azure-hoz készült avere-vFXT
description: Ismerje meg, hogy miként merülhetnek fel az Azure-hoz készült avere-vFXT telepítésekor vagy használatakor felmerülő problémák a Azure Portalon keresztüli támogatási jegy létrehozásával.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 8159d83624cdd474d91030d5376a3db447beffe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008429"
---
# <a name="get-help-with-your-system"></a>Segítség kérése a rendszeren

Ha segítségre van az Azure-rendszer avere-vFXT, az alábbi módon kérhet támogatást:

* **Avere vFXT-probléma** – a Azure Portal segítségével nyisson meg egy támogatási jegyet az avere-vFXT az [alább](#open-a-support-ticket-for-your-avere-vfxt)leírtak szerint.
* **Kvóta** – ha kvótával kapcsolatos probléma merül fel, [a kvóta növelését kéri](#request-a-quota-increase)
* **Dokumentáció és példák** – ha problémák merülnek fel ezzel a dokumentációval vagy példákkal kapcsolatban, görgessen a lap aljára, és használja a **visszajelzés** szakaszt a meglévő problémák megkereséséhez, és szükség esetén új fájl létrehozásához.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Támogatási jegy megnyitása a avere-vFXT

Ha a avere vFXT telepítése vagy használata során problémák merülnek fel, kérjen segítséget a Azure Portal.

Kövesse az alábbi lépéseket annak biztosításához, hogy a támogatási jegye a fürt egy erőforrásával legyen címkézve. A jegy címkézése segít a megfelelő támogatási erőforráshoz való átirányításban.

1. [https://portal.azure.com](https://portal.azure.com)Válassza ki az **erőforráscsoportok** lehetőséget. Tallózással keresse meg azt a vFXT-fürtöt tartalmazó erőforráscsoportot, ahol a probléma felmerült, és kattintson az egyik avere-fürt virtuális gépre.

    ![képernyőfelvétel: Azure Portal erőforráscsoport – áttekintés panel egy adott virtuális géppel](media/avere-vfxt-ticket-vm.png)

1. A virtuális gép lapon görgessen le a bal oldali panel aljára, és kattintson az **új támogatási kérelem** elemre.

    ![Képernyőkép a virtuális gép Azure Portal virtuális gép oldaláról az előző képernyőképen. A bal oldali menü az aljára, az új támogatási kérelem pedig kör alakú.](media/avere-vfxt-ticket-request.png)

1. A támogatási kérelem első oldalán válassza ki a probléma típusát, és ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.

   A **szolgáltatás** alatt kattintson a **minden szolgáltatás** elemre, és keresse **meg a** **avere vFXT** lehetőséget.

   Adjon hozzá egy rövid összefoglalót, és válassza ki a probléma típusát.

    ![képernyőkép a Azure Portal egy új támogatási kérés képernyőjéről. Az alapvető beállítások lap van kiválasztva. A képernyő elemei közé tartozik a probléma típusa, az előfizetés, a szolgáltatás, az összefoglalás és a probléma típusa.](media/ticket-basics.png)

   A folytatáshoz kattintson a **Tovább** gombra.

1. A támogatási űrlap második lapja az összefoglalás leírása alapján a probléma kijavítására vonatkozó javaslatokat tartalmaz. Ha továbbra is támogatási jegyet kell létrehoznia, kattintson a lenti **Next (tovább** ) gombra.

   ![képernyőkép az új támogatási kérés képernyőről, ahol a megoldások lap van kiválasztva. A középső szövegmezőben szerepel a "javasolt megoldás" cím, és megmagyarázza a lehetséges jogorvoslatokat.](media/ticket-solutions.png)

1. A harmadik lapon adja meg a részleteket – ez tartalmazza a fürtre vonatkozó információkat, a probléma előfordulásának idejét, a súlyosságot és az Önnel való kapcsolatfelvétel módját. Adja meg az adatokat, és kattintson a lenti **Next (tovább** ) gombra.

   ![képernyőkép az új támogatási kérés képernyőről, ahol a Részletek lap van kiválasztva. Az információs mezők a "probléma részletei", a "támogatási módszer" és a "kapcsolattartási adatok" kategóriába vannak rendezve.](media/ticket-details.png)

1. Tekintse át a végső oldalon található információkat, majd kattintson a **Létrehozás** gombra. A rendszer visszaigazolja az e-mail-címre, és felveszi Önnel a kapcsolatot a támogatási személyzet tagjaival.

## <a name="request-a-quota-increase"></a>Kvóta növelésének kérése

Olvassa el [a kvótát a vFXT-fürthöz](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) , hogy megtudja, milyen összetevőkre van szükség a avere-vFXT Azure-beli telepítéséhez. [Kvótát is igényelhet](../azure-portal/supportability/resource-manager-core-quotas-request.md) a Azure Portal.