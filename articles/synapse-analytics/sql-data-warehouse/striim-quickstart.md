---
title: Striim – első lépések
description: Ismerkedjen meg gyorsan a Striim és az Azure szinapszis Analytics szolgáltatással.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d6b5fa1a42201479c5d426a5bbb9188d0cb8e03d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89485974"
---
# <a name="striim-azure-synapse-analytics-marketplace-offering-install-guide"></a>Striim Azure szinapszis Analytics Marketplace ajánlat telepítési útmutató

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik az Azure szinapszis Analytics már meglévő példányával.

Keresse meg a Striim az Azure piactéren, és válassza ki a Striim az Azure szinapszis Analytics (szakaszos) beállításhoz való adatintegrációhoz 

![A Striim telepítése][install]

Konfigurálja a Striim virtuális gépet a megadott tulajdonságokkal, és jegyezze fel a Striim-fürt nevét, jelszavát és rendszergazdai jelszavát.

![Striim konfigurálása][configure]

Az üzembe helyezést követően kattintson a masternode elemre \<VM Name> a Azure Portal, kattintson a kapcsolat lehetőségre, és másolja a bejelentkezést a VM helyi fiók használatával. 

![A Striim és az Azure szinapszis Analytics összekötése][connect]

Töltse le a sqljdbc42. jar fájlt a számítógépről <https://www.microsoft.com/en-us/download/details.aspx?id=54671> a helyi gépre. 

Nyisson meg egy parancssori ablakot, és változtassa meg a könyvtárakat a JDBC jar letöltésére. SCP a jar-fájlt a Striim virtuális géphez, és a Azure Portal címről és jelszaváról

![Jar-fájl másolása a virtuális gépre][copy-jar]

Nyisson meg egy másik parancssori ablakot, vagy használjon SSH-segédprogramot SSH-ba a Striim-fürtön

![SSH a fürtbe][ssh]

A következő parancsok végrehajtásával helyezze át a JDBC jar-fájlt a Striim lib könyvtárába, és indítsa el és állítsa le a kiszolgálót.

   1. sudo Su
   2. CD-/tmp
   3. MV sqljdbc42. jar/opt/striim/lib
   4. systemctl leállítása striim-Node
   5. systemctl leállítása striim – adatbázis-kezelő
   6. systemctl Start striim – adatbázis-kezelő
   7. systemctl-striim indítása – csomópont

![A Striim-fürt elindítása][start-striim]

Most nyissa meg a kedvenc böngészőjét, és navigáljon ide \<DNS Name> : 9080

![Lépjen a bejelentkezési képernyőre][navigate]

Jelentkezzen be a Azure Portalban beállított felhasználónévvel és jelszóval, és válassza ki a kívánt varázslót a kezdéshez, vagy lépjen az alkalmazások oldalra az egérrel a fogd és vidd felhasználói felület használatának megkezdéséhez.

![Bejelentkezés kiszolgálói hitelesítő adatokkal][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
