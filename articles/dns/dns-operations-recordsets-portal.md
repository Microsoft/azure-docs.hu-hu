---
title: DNS-rekordhalmazok és-rekordok kezelése a Azure DNS
description: A Azure DNS lehetővé teszi a DNS-rekordhalmazok és-rekordok kezelését a tartomány üzemeltetése során.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 22ceba69ecf865d906021068a39a9d273b842ca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965731"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>DNS-rekordok és-rekordhalmazok kezelése a Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan kezelheti a DNS-zónához tartozó rekordhalmazokat és rekordokat a Azure Portal használatával.

Fontos megérteni a DNS-rekordhalmazok és az egyes DNS-rekordok közötti különbséget. A rekordhalmaz olyan rekordok gyűjteménye, amelyek azonos nevű és azonos típusú zónában találhatók. További információ: [DNS-rekordhalmazok és-rekordok létrehozása a Azure Portal használatával](./dns-getstarted-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Új rekordazonosító és rekord létrehozása

Ha egy rekordhalmazt szeretne létrehozni a Azure Portalban, tekintse meg [a DNS-rekordok létrehozása a Azure Portal használatával](./dns-getstarted-portal.md)című témakört.

## <a name="view-a-record-set"></a>Rekord megtekintése

1. A Azure Portal lépjen a **DNS-zóna** panelre.
2. Keresse meg a rekordot, és válassza ki. Ekkor megnyílik a rekordazonosító tulajdonságai.

    ![Rekord keresése](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Új rekord hozzáadása egy rekorddal

Bármelyik rekordhalmazhoz legfeljebb 20 rekordot adhat hozzá. Egy rekordhalmaz nem tartalmazhat két azonos rekordot. Üres rekordhalmazok (nulla rekordokkal) hozhatók létre, de nem jelennek meg a Azure DNS névkiszolgálók esetében. A CNAME típusú rekordhalmazok legfeljebb egy rekordot tartalmazhatnak.

1. A DNS-zóna **rekordazonosító tulajdonságai** paneljén kattintson arra a rekordra, amelyhez rekordot kíván hozzáadni.

    ![Válasszon egy rekordot](./media/dns-operations-recordsets-portal/selectset500.png)

2. Adja meg a rekordtípusok tulajdonságait a mezők kitöltésével.

    ![Rekord felvétele](./media/dns-operations-recordsets-portal/addrecord500.png)

3. A beállítások mentéséhez kattintson a panel tetején található **Mentés** gombra. Ezután zárjuk be a panelt.
4. A sarokban láthatja, hogy a rekord mentésre kerül.

    ![Rekord mentése](./media/dns-operations-recordsets-portal/saving150.png)

A rekord mentése után a **DNS-zóna** paneljén lévő értékek az új rekordot fogják tükrözni.

## <a name="update-a-record"></a>Rekord frissítése

Ha egy meglévő rekordazonosító rekordját frissíti, a frissíthető mezők attól függnek, hogy milyen típusú rekordot használ.

1. A rekord készlet **tulajdonságai** paneljén keresse meg a rekordot.
2. Módosítsa a rekordot. Egy rekord módosításakor módosíthatja a rekordhoz elérhető beállításokat. A következő példában az **IP-cím** mező ki van választva, és az IP-cím a módosítás folyamatban van.

    ![Rekordok módosítása](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. A beállítások mentéséhez kattintson a panel tetején található **Mentés** gombra. A jobb felső sarokban megjelenik a rekord mentésének értesítése.

    ![Rekord mentve](./media/dns-operations-recordsets-portal/saved150.png)

A rekord mentését követően a **DNS-zóna** panelen beállított rekord értékei a frissített rekordot tükrözik.

## <a name="remove-a-record-from-a-record-set"></a>Rekord eltávolítása egy adatkészletből

A rekordokat a Azure Portal használatával távolíthatja el. Vegye figyelembe, hogy a rekordok utolsó rekordjának eltávolítása nem törli a készletet.

1. A rekord készlet **tulajdonságai** paneljén keresse meg a rekordot.
2. Kattintson arra a rekordra, amelyet el szeretne távolítani. Ezután válassza az **Eltávolítás** lehetőséget.

    ![Rekord eltávolítása](./media/dns-operations-recordsets-portal/removerecord500.png)

3. A beállítások mentéséhez kattintson a panel tetején található **Mentés** gombra.
4. A rekord eltávolítása után a **DNS-zóna** paneljén lévő rekord értékei az eltávolítást tükrözik.

## <a name="delete-a-record-set"></a><a name="delete"></a>Rekord törlése

1. A rekord készlet **Tulajdonságok** paneljén kattintson a **Törlés** elemre.

    ![Rekord törlése](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Megjelenik egy üzenet, amely megkérdezi, hogy szeretné-e törölni a rekordot.
3. Ellenőrizze, hogy a név megegyezik-e a törölni kívánt rekorddal, majd kattintson az **Igen** gombra.
4. A **DNS-zóna** panelen ellenőrizze, hogy a rekord már nem látható-e.

## <a name="work-with-ns-and-soa-records&quot;></a>NS-és SOA-rekordok használata

Az automatikusan létrehozott NS és SOA rekordokat más bejegyzéstípusoktól eltérően kezeli a rendszer.

### <a name=&quot;modify-soa-records&quot;></a>SOA-rekordok módosítása

Nem adhat hozzá és nem távolíthat el rekordokat az automatikusan létrehozott SOA-rekordhalmazból a zóna csúcspontján (név = &quot; \@ "). A SOA-rekordon belül azonban módosíthatja a paramétereket (a "gazdagép" kivételével) és a rekordhalmazt is.

### <a name="modify-ns-records-at-the-zone-apex"></a>NS rekordok módosítása a zóna csúcsán

A zóna csúcsán beállított NS-rekordhalmaz automatikusan létrejön minden DNS-zónával. A zónához rendelt Azure DNS névkiszolgálók nevét tartalmazza.

Ehhez az NS-rekordhoz további névkiszolgálók hozzáadásával több DNS-szolgáltatóval rendelkező közös üzemeltetésű tartományokat is támogathat. Módosíthatja a rekord ÉLETTARTAMát és metaadatait is. Az előre megadott Azure DNS névkiszolgálók azonban nem távolíthatók el és nem módosíthatók.

Vegye figyelembe, hogy ez csak a zóna csúcsán beállított NS-rekordra vonatkozik. A zónában lévő egyéb NS-rekordhalmazok (a gyermektartomány delegálásához használt módon) korlátozás nélkül módosíthatók.

### <a name="delete-soa-or-ns-record-sets"></a>SOA-vagy NS-rekordhalmazok törlése

A SOA és az NS rekordhalmaz nem törölhető a zóna \@ létrehozásakor automatikusan létrehozott (név = "") zónában. A zóna törlésekor a rendszer automatikusan törli őket.

## <a name="next-steps"></a>Következő lépések

* További információ a Azure DNSről: [Azure DNS áttekintése](dns-overview.md).
* A DNS automatizálásával kapcsolatos további információkért lásd: [DNS-zónák és-rekordhalmazok létrehozása a .net SDK használatával](dns-sdk.md).
* A fordított DNS-rekordokkal kapcsolatos további információkért lásd: [a fordított DNS és a támogatás áttekintése az Azure-ban](dns-reverse-dns-overview.md).
* További információ a Azure DNS alias-rekordokról: [Azure DNS alias-rekordok áttekintése](dns-alias.md).