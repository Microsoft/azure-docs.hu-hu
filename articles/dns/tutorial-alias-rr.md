---
title: 'Oktatóanyag: Aliasrekord létrehozása egy zónában található erőforrásrekordra való hivatkozáshoz'
titleSuffix: Azure DNS
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhat egy Azure DNS-aliasrekordot a zónán belüli erőforrásrekordra való hivatkozáshoz.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: c20f079fdbccdf003d96a1b2683060c2cd6d0e5a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737369"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Oktatóanyag: Hozzon létre egy aliasrekordot a zónában lévő erőforrásrekordra való hivatkozáshoz

Az aliasrekordok hivatkozhatnak más azonos típusú rekordhalmazokra. Például rendelkezhet egy DNS CNAME-rekordhalmazzal, amely egy ugyanolyan típusú másik CNAME-rekordhalmaz aliasa. Ez a képesség akkor hasznos, ha aliasként és nem aliasként működő rekordok halmazait is létre kívánja hozni.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Aliasrekord létrehozása egy erőforrásrekordhoz a zónában.
> * Az aliasrekord tesztelése.


Ha nem rendelkezik Azure-előfizetéssel, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) kezdés előtt hozzon létre egy ingyenes fiókot.

## <a name="prerequisites"></a>Előfeltételek
Elérhetőnek kell lennie egy tartománynévnek, amelyet üzemeltethet az Azure DNS-ben a teszteléshez. Teljes körű irányítással kell rendelkeznie a tartomány felett. A teljes körű irányításba beletartozik a tartomány névkiszolgálói (NS-) rekordjainak beállítására való képesség.

A tartomány Azure DNS-ben való üzemeltetésére vonatkozó utasításokért lásd az [Oktatóanyag: A tartomány üzemeltetése az Azure DNS-ben](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Aliasrekord létrehozása

Aliasrekord létrehozása, amely egy erőforrásrekordra mutat a zónában.

### <a name="create-the-target-resource-record"></a>A célként megadott erőforrásrekord létrehozása
1. Válassza ki az Azure DNS-zónát a zóna megnyitásához.
2. Válassza a **Rekordhalmaz** elemet.
3. A **Név** szövegmezőbe írja be a **kiszolgáló** nevet.
4. A **Típus** számára válassza az **A** értéket.
5. Az **IP-CÍM** szövegmezőbe írja be a **10.10.10.10** címet.
6. Válassza az **OK** lehetőséget.

### <a name="create-the-alias-record"></a>Az aliasrekord létrehozása
1. Válassza ki az Azure DNS-zónát a zóna megnyitásához.
2. Válassza a **Rekordhalmaz** elemet.
3. A **Név** szövegmezőbe írja be a **teszt** nevet.
4. A **Típus** számára válassza az **A** értéket.
5. Az **Aliasrekord-halmaz** jelölőnégyzetben válassza az **Igen** elemet. Ezután válassza a **Zónarekordhalmaz** lehetőséget.
6. A **Zóna-rekordhalmaz** beállításaként, jelölje be a **kiszolgáló** rekordot.
7. Válassza az **OK** lehetőséget.

## <a name="test-the-alias-record"></a>Az aliasrekord tesztelése

1. Indítsa el a kedvenc nslookup eszközét. Az egyik lehetőség a webhely [https://network-tools.com/nslook](https://network-tools.com/nslook) tallózása.
2. Állítsa be az A rekordok lekérdezéstípusát, és keresse meg a **teszt adatokat. \<your domain name\>**. A válasz: **10.10.10.10**.
3. Az Azure Portalon módosítsa a **kiszolgáló** A rekordjának értékét a következőre: **10.11.11.11**.
4. Várjon néhány percig, és használja újra az nslookup eszközt a **teszt** rekordhoz. A válasz: **10.11.11.11**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a jelen oktatóanyagban létrehozott erőforrásokra, törölheti a zónában lévő **kiszolgáló** és **teszt** erőforrásrekordokat.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy aliasrekordot, amely a zónában található egyik erőforrásrekordra hivatkozik. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
