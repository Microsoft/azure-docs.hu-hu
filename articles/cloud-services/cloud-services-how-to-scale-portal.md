---
title: Felhőalapú szolgáltatás automatikus méretezése a portálon | Microsoft Docs
description: Ebből a témakörből megtudhatja, hogyan konfigurálhat automatikus méretezési szabályokat a Cloud Service webes szerepkör vagy feldolgozói szerepkör számára az Azure-ban a portál használatával.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: e5452e0794083afabbee2759c5b37feb1eed0c88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86223686"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Felhőalapú szolgáltatás automatikus skálázásának konfigurálása a portálon

A Felhőbeli szolgáltatások feldolgozói szerepkörhöz olyan feltételek állíthatók be, amelyek egy méretezési vagy kimenő műveletet indítanak el. A szerepkör feltételei a szerepkör PROCESSZORán, lemezén vagy hálózati terhelésén alapulnak. Beállíthat egy olyan feltételt is, amely egy üzenetsor vagy egy másik, az előfizetéshez társított Azure-erőforrás metrikája alapján is megadható.

> [!NOTE]
> Ez a cikk a Cloud Service webes és feldolgozói szerepköreire koncentrál. Amikor közvetlenül hoz létre egy virtuális gépet (klasszikus), azt egy felhőalapú szolgáltatás üzemelteti. A standard szintű virtuális gépeket a [rendelkezésre állási csoportokhoz](../virtual-machines/windows/classic/configure-availability-classic.md) társíthatja, majd manuálisan be-és kikapcsolhatja őket.

## <a name="considerations"></a>Megfontolandó szempontok
Az alkalmazás skálázásának konfigurálása előtt vegye figyelembe a következő információkat:

* A skálázást az alapszintű használat befolyásolja.

    A nagyobb szerepkörű példányok több magot használnak. Az alkalmazások méretezése csak az előfizetéshez tartozó magok korlátján belül végezhető el. Tegyük fel például, hogy az előfizetése legfeljebb 20 magot tartalmaz. Ha olyan alkalmazást futtat, amely két közepes méretű felhőalapú szolgáltatást használ (összesen 4 mag), akkor a fennmaradó 16 mag esetében csak az előfizetésében lévő többi felhőalapú szolgáltatás üzemelő példányát lehet méretezni. A méretekről további információt a [Cloud Service-méretek](cloud-services-sizes-specs.md)című témakörben talál.

* A várólista-üzenetek küszöbértéke alapján méretezhető. További információ a várólisták használatáról: [a Queue Storage szolgáltatás használata](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Az előfizetéshez társított egyéb erőforrásokat is méretezheti.

* Az alkalmazás magas rendelkezésre állásának engedélyezéséhez győződjön meg arról, hogy az üzembe helyezése két vagy több szerepkör-példánnyal történik. További információ: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

* Az automatikus méretezés csak akkor történik meg, ha minden szerepkör **kész** állapotban van.  


## <a name="where-scale-is-located"></a>Hol található a skála
A felhőalapú szolgáltatás kiválasztása után a Cloud Service panel látható.

1. A Cloud Service panelen a **szerepkörök és példányok** csempén válassza ki a Cloud Service nevét.   
   **Fontos**: Ügyeljen arra, hogy a Cloud Service szerepkörre ne a szerepkör alatt lévő szerepkör-példányra mutasson.

    ![Képernyőkép a szerepkörök és példányok csempéről, ahol a feldolgozói szerepkört az 1. B sorban, a piros színnel kell megválasztani.](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Válassza ki a **Méretezés** csempét.

    ![Képernyőkép az Operations oldalról, amelyen a Sales csempe piros színnel van megjelölve.](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatikus méretezés
A két mód **manuális** vagy **automatikus**beállításával konfigurálhatja a szerepkörök méretezési beállításait. A manuális a vártnál, a példányok abszolút száma állítható be. Az automatikus beállítás lehetővé teszi, hogy olyan szabályokat állítson be, amelyek meghatározzák, hogy mennyit és milyen mértékben kell méreteznie.

Állítsa be a **skálázási** lehetőséget az **ütemezett és a teljesítményi szabályokra**.

![A Cloud Services méretezési beállításai a profil és a szabály szerint](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Egy meglévő profil.
2. Adjon hozzá egy szabályt a szülő profilhoz.
3. Adjon hozzá egy másik profilt.

Válassza a **Profil hozzáadása**lehetőséget. A profil határozza meg, hogy melyik módot szeretné használni a skálához: **mindig**, **Ismétlődés**, **rögzített dátum**.

Miután konfigurálta a profilt és a szabályokat, válassza a felső **Mentés** ikont.

#### <a name="profile"></a>Profil
A profil meghatározza a skála minimális és maximális példányát, valamint azt is, hogy a méretezési tartomány aktív-e.

* **Always** (Mindig)

    Mindig tartsa meg az elérhető példányok számát.  

    ![Felhőbeli szolgáltatás, amely mindig méretezhető](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Ismétlődés**

    Válassza ki a hét napjait a méretezéshez.

    ![Felhőbeli szolgáltatás skálázása ismétlődési ütemtervtel](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Rögzített dátum**

    A szerepkör skálázására szolgáló rögzített dátumtartomány.

    ![Felhőbeli szolgáltatás méretezése rögzített dátummal](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Miután konfigurálta a profilt, kattintson a profil panel alján található **OK** gombra.

#### <a name="rule"></a>Szabály
A rendszer hozzáadja a szabályokat egy profilhoz, és egy olyan feltételt jelöl, amely elindítja a méretezést.

A szabály triggere a Cloud Service (CPU-használat, lemez tevékenység vagy hálózati tevékenység) mérőszámán alapul, amelyhez feltételes értéket adhat hozzá. Emellett az triggert egy üzenetsor vagy egy másik, az előfizetéshez társított Azure-erőforrás metrikája alapján is elvégezheti.

![Képernyőkép a szabály párbeszédpanelről, amelyen a metrika neve beállítás piros színnel van kiválasztva.](./media/cloud-services-how-to-scale-portal/rule-settings.png)

A szabály konfigurálása után kattintson a szabály panel alján található **OK** gombra.

## <a name="back-to-manual-scale"></a>Vissza a manuális méretezéshez
Navigáljon a [méretezési beállításokhoz](#where-scale-is-located) , és állítsa a **méretezési** lehetőséget a **manuálisan megadott példányszámra**.

![A Cloud Services méretezési beállításai a profil és a szabály szerint](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Ezzel a beállítással eltávolíthatja az automatikus skálázást a szerepkörből, majd közvetlenül is beállíthatja a példányszámot.

1. A skála (manuális vagy automatizált) beállítás.
2. A példányok méretezése a következőhöz: szerepkör-példány csúszkája.
3. A szerepkör azon példányai, amelyek méretezése a következőre:.

A méretezési beállítások konfigurálása után kattintson a felül található **Mentés** ikonra.



