---
title: DNS-rekordhalmazok és rekordok az Azure DNS használata kezelheti
description: Az Azure DNS lehetővé teszi, hogy a DNS-rekordhalmazok és rekordok kezelése, ha a tartomány üzemeltetésének.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: victorh
ms.openlocfilehash: 891adfacde6e46b1d8fe8e2f6b5fb39c90ce27a0
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48853708"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>DNS-rekordok kezelése és a rekordhalmazok az Azure portal használatával

Ez a cikk bemutatja, hogyan rekordhalmazok és rekordok a DNS-zóna kezelése az Azure portal használatával.

Fontos tudni, hogy a DNS-rekordhalmazok és egyéni DNS-rekordok közötti különbség. Rekordhalmaz rekordokat a zónában lévő, azonos nevű és azonos típusú gyűjteménye. További információkért lásd: [hozzon létre DNS-rekordhalmazok és rekordok az Azure portal használatával](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Új rekordhalmaz és rekord létrehozása

Szeretne létrehozni egy rekordot az Azure Portalon, tekintse meg [hozzon létre DNS-rekordok az Azure portal használatával](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Rekordhalmaz megtekintése

1. Az Azure Portalon nyissa meg a **DNS-zóna** panelen.
2. Keresse meg a beállított rekord, és válassza ki azt. Ekkor megnyílik a rekordhalmaz tulajdonságait.

    ![Rekordhalmaz keresése](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Adjon hozzá egy új rekordot rekordhalmazhoz

Minden rekordhalmaz legfeljebb 20 bejegyzést adhat hozzá. Rekordhalmaz nem tartalmazhat két azonos rekordot. Üres rekordhalmazok (a nulla rekord) hozhatók létre, de nem jelennek meg az Azure DNS névkiszolgálóit. CNAME típusú rekordhalmazok legfeljebb több rekordot is tartalmazhat.

1. Az a **rekordhalmaz tulajdonságok** a DNS-zóna panelen kattintson a rekordhalmaz adjon hozzá egy rekordot a kívánt.

    ![Válassza ki a rekordhalmaz](./media/dns-operations-recordsets-portal/selectset500.png)

2. Adja meg a rekord állítsa be a tulajdonságokat, a mezők kitöltésével.

    ![Rekord hozzáadása](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Kattintson a **mentése** a beállítások mentéséhez a panel tetején. Zárja be a panelt.
4. A sarokban jelenik meg, hogy a bejegyzést ment.

    ![Rekordhalmaz mentése folyamatban](./media/dns-operations-recordsets-portal/saving150.png)

A rekord mentése után, az értékek a a **DNS-zóna** panelen jelenik meg az új rekordban.

## <a name="update-a-record"></a>Rekord frissítése

Amikor frissít egy rekordot egy meglévő rekordhalmazt, frissítheti a mezők dolgozik rekord típusa függenek.

1. Az a **rekordhalmaz tulajdonságok** panelen, a rekordhalmaz, keresse meg a rekordot.
2. A rekord módosításához. Amikor módosít egy rekordot, módosíthatja a rendelkezésre álló beállítások a rekord. A következő példában a **IP-cím** mező van kiválasztva, és a módosított folyamatban van az IP-cím.

    ![Rekordok módosítása](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Kattintson a **mentése** a beállítások mentéséhez a panel tetején. A jobb felső sarokban láthatja a értesítést, amelyet a rekord mentése megtörtént.

    ![Rekordhalmaz mentve](./media/dns-operations-recordsets-portal/saved150.png)

A rekord mentése után a rekord értékeit beállítani a **DNS-zóna** panelen jelenik meg a frissített rekord.

## <a name="remove-a-record-from-a-record-set"></a>A rekordhalmaz bejegyzés eltávolítása

Az Azure portal segítségével egy rekordhalmaz rekordok eltávolítása. Vegye figyelembe, hogy az utolsó rekord eltávolítását a rekordhalmaz nem törli a rekordhalmaz.

1. Az a **rekordhalmaz tulajdonságok** panelen, a rekordhalmaz, keresse meg a rekordot.
2. Kattintson az eltávolítani kívánt rekord. Válassza ki **eltávolítása**.

    ![Rekord törlése](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Kattintson a **mentése** a beállítások mentéséhez a panel tetején.
4. Miután a bejegyzés el lett távolítva, az a rekord értékeit a **DNS-zóna** panelen jelenik meg az eltávolítás.

## <a name="delete"></a>A rekordhalmaz törlése

1. Az a **rekordhalmaz tulajdonságok** a rekordhalmaz paneljén kattintson **törlése**.

    ![A rekordhalmaz törlése](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Egy üzenet jelenik meg azzal a kérdéssel, ha azt szeretné, a rekordhalmaz törlése.
3. Győződjön meg arról, hogy a neve egyezik-e a rekordhalmaz törlése, és kattintson a kívánt **Igen**.
4. Az a **DNS-zóna** panelen ellenőrizze, hogy a rekordhalmaz már nem látható.

## <a name="work-with-ns-and-soa-records"></a>NS és SOA típusú rekordoknak használata

A további rekordtípusok NS és SOA típusú rekordoknak, automatikusan létrehozott eltérően kezelik.

### <a name="modify-soa-records"></a>SOA típusú rekordok módosítása

Nem adhat hozzá és rekordok eltávolítása az automatikusan létrehozott, állítsa be a zóna legfelső pontján SOA típusú rekordját (név = "\@"). Azonban módosíthatja a SOA típusú rekordját (kivéve a "Host") belül paraméterek egyikét, és a rekordhalmaz TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Módosítsa a Névkiszolgálói rekordokat a zóna legfelső pontján

Az NS-rekord a zóna legfelső pontján állítsa be a rendszer automatikusan létrehoz minden DNS-zónát. Az Azure DNS névkiszolgálóit, a zóna nevét tartalmazza.

Hozzáadhat további névhez e NS-rekord-kiszolgálókat, támogatja a közös üzemeltetési tartomány több DNS-szolgáltatónál. Az élettartam és a rekordhalmaz metaadatait is módosíthatja. Azonban nem távolítsa el vagy módosítsa az ki van töltve az Azure DNS névkiszolgálóit.

Vegye figyelembe, hogy ez csak érvényes az NS-rekord a zóna legfelső pontján állítsa be. Más Névkiszolgálói rekordhalmazt a zónában (a használt gyermek zónák delegálása) korlátozás nélkül módosíthatók.

### <a name="delete-soa-or-ns-record-sets"></a>A SOA és Névkiszolgálói rekordhalmazt törlése

Nem lehet törölni a SOA és NS-rekord zóna felső pontjánál állítja be (név = "\@"), amely automatikusan létrehozza a zóna létrehozásakor. Ezek automatikusan törlődnek a zóna törlésekor.

## <a name="next-steps"></a>További lépések

* Azure DNS szolgáltatással kapcsolatos további információkért lásd: a [Azure DNS áttekintését biztosító](dns-overview.md).
* DNS automatizálása kapcsolatos további információkért lásd: [létrehozása DNS-zónák és -rekordhalmazok a .NET SDK használatával](dns-sdk.md).
* Fordított DNS-rekordok kapcsolatos további információkért lásd: [fordított DNS és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md).
* Alias rekordokat Azure DNS szolgáltatással kapcsolatos további információkért lásd: [Azure DNS-alias a rekordok áttekintése](dns-alias.md).
