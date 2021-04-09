---
title: Privát végpontok használata a hatáskörébe való biztonságos hozzáféréshez
description: Ez a cikk azt ismerteti, hogyan állítható be a saját hatáskörébe tartozó saját végponti pont
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 09fa10e7f7751321601c5c4871b2cf36ccf6f01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720941"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Privát végpontok használata a hatáskörébe tartozó fiókhoz

A hatáskörébe tartozó fiókokhoz privát végpontokat is használhat, amelyek lehetővé teszik, hogy az ügyfelek és a virtuális hálózat (VNet) ügyfelei biztonságosan hozzáférhessenek a katalógushoz privát kapcsolaton keresztül. A privát végpont egy IP-címet használ a hatáskörébe tartozó fiók VNet. A VNet-ügyfelek és a hatáskörébe tartozó fiókok közötti hálózati forgalom a VNet és a Microsoft gerinc hálózatán található privát kapcsolaton keresztül halad, ami kiküszöböli a nyilvános internetről való kitettséget.

## <a name="create-purview-account-with-a-private-endpoint"></a>Privát végponttal rendelkező hatáskörébe tartozó fiók létrehozása

1. Navigáljon a [Azure Portal](https://portal.azure.com) , majd a hatáskörébe tartozó fiókjához.

1. Töltse ki az alapvető információkat, és állítsa be a kapcsolati módszert a magánhálózati végponthoz a **hálózatkezelés** lapon. Állítsa be a betöltési magánhálózati végpontokat a privát végponttal párosítani kívánt **előfizetés, vnet és alhálózat** részleteit megadva.

    > [!NOTE]
    > Csak akkor hozzon létre egy betöltési privát végpontot, ha az Azure-beli és a helyszíni forrásokhoz egyaránt engedélyezni kívánja a teljes körű vizsgálati forgatókönyvek hálózati elkülönítését. Az AWS-forrásokkal együtt jelenleg nem támogatott a betöltési magánhálózati végpontok használata.

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Hozzon létre egy privát végpontot a Azure Portal":::

1. Dönthet úgy is, hogy beállít egy **saját DNS zónát** az egyes betöltési magánhálózati végpontokhoz.

1. Kattintson a Hozzáadás gombra, és vegyen fel egy privát végpontot a hatáskörébe tartozó fiókjához.

1. A privát végpont létrehozása lapon állítsa be a hatáskörébe tartozó alerőforrást a **fiókra**, válassza ki a virtuális hálózatot és az alhálózatot, és válassza ki azt a saját DNS zónát, ahol a DNS regisztrálva lesz (a saját DNS-kiszolgálókat is használhatja, vagy létrehozhat DNS-rekordokat a virtuális gépeken található gazdagép-fájlok használatával).

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Privát végpontok létrehozási beállításai":::

1. Válassza az **OK** lehetőséget.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az Áttekintés és létrehozása lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját.

1. Amikor megjelenik a Megfelelt az ellenőrzésen üzenet, válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Fiók létrehozásakor átadott érvényesítés":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Privát végpont létrehozása a hatáskörébe webes portálhoz

1. Navigáljon az imént létrehozott hatáskörébe-fiókhoz, és válassza ki a privát végpontok kapcsolatait a beállítások szakaszban.

1. Kattintson a + privát végpont lehetőségre egy új privát végpont létrehozásához.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Portál privát végpontjának létrehozása":::

1. Adja meg az alapvető információkat.

1. Az erőforrás lapon válassza ki a **Microsoft. hatáskörébe/fiókokként** használandó erőforrástípust.

1. Válassza ki az újonnan létrehozott hatáskörébe tartozó fiókot, és válassza a cél alerőforrást a **portál** elemre.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="A portál privát végpontjának részletei":::

1. Válassza ki a virtuális hálózat és a saját DNS zónát a konfiguráció lapon. Navigáljon az Összefoglalás lapra, és kattintson a **Létrehozás** elemre a portál privát végpontjának létrehozásához.

## <a name="enabling-access-to-azure-active-directory"></a>Azure Active Directoryhoz való hozzáférés engedélyezése

> [!NOTE]
> Ha a virtuális gép, a VPN-átjáró vagy a VNET-társítási átjáró rendelkezik nyilvános internet-hozzáféréssel, akkor a saját végpontokkal engedélyezett a hatáskörébe-portál és a hatáskörébe tartozó fiók hozzáférése, és nem kell követnie az alábbi utasítások hátralévő részét. Ha a magánhálózat hálózati biztonsági csoportra vonatkozó szabályai úgy vannak beállítva, hogy megtagadják az összes nyilvános internetes forgalmat, akkor hozzá kell adnia néhány szabályt a HRE-hozzáférés engedélyezéséhez. Ehhez kövesse az alábbi utasításokat.

Az alábbi utasítások az Azure-beli virtuális gépekről való biztonságos hozzáféréshez szükségesek. A VPN-vagy más vnet-társítási átjárók használata esetén hasonló lépéseket kell követni.

1. A Azure Portalban navigáljon a virtuális géphez, válassza a hálózatkezelés fület a beállítások szakaszban. Ezután válassza ki a kimenő portok szabályait, és kattintson a kimenő Portszabály hozzáadása elemre.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Kimenő szabály hozzáadása":::

2. A kimenő Portszabály hozzáadása panelen válassza ki a **AzureActiveDirectory** *célhelyet* , a célként megadott szolgáltatási címkét, a célként megadott portok tartományát *, az engedélyezni kívánt műveletet, **a prioritásnak magasabbnak kell lennie az összes internetes forgalmat megtagadó szabálynál**. Hozza létre a szabályt.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Kimenő szabály részleteinek hozzáadása":::

3. Kövesse ugyanezen lépéseket egy másik szabály létrehozásához a "**AzureResourceManager**" szolgáltatás címkéjének engedélyezéséhez. Ha el kell érnie a Azure Portal, hozzáadhat egy szabályt is a "*AzurePortal*" szolgáltatás címkéjéhez.

4. Kapcsolódjon a virtuális géphez, nyissa meg a böngészőt, navigáljon a böngésző konzolra (CTRL + SHIFT + J), és váltson a hálózat lapra a hálózati kérések figyeléséhez. Adja meg a web.purview.azure.com értéket az URL-cím mezőben, és próbálja meg bejelentkezni a HRE hitelesítő adataival. Előfordulhat, hogy a bejelentkezés sikertelen lesz, és a konzol hálózat lapján láthatja, hogy a HRE megpróbál hozzáférni a aadcdn.msauth.net, de letiltva.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Sikertelen bejelentkezések részletei":::

5. Ebben az esetben nyisson meg egy parancssort a virtuális gépen, és Pingelje ezt az URL-címet (aadcdn.msauth.net), szerezze be az IP-címét, majd adjon hozzá egy kimenő portszabály-szabályt az IP-címhez a virtuális gép hálózati biztonsági szabályaiban. Állítsa a célhelyet az IP-címre, és állítsa be a cél IP-címeket a aadcdn IP-címére. A Load Balancer és a Traffic Manager miatt a HRE CDN IP-címe dinamikus lehet. Ha megszerezte az IP-címét, érdemes lehet hozzáadni a virtuális gép gazdagép-fájljához, hogy a böngésző meglátogassa az IP-címet a HRE CDN beszerzéséhez.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Pingelés tesztelése":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="HRE CDN-szabály":::

6. Az új szabály létrehozása után térjen vissza a virtuális gépre, és próbálja meg újból a bejelentkezést a HRE hitelesítő adataival. Ha a bejelentkezés sikeres, a hatáskörébe-portál készen áll a használatra. Bizonyos esetekben azonban a HRE más tartományokra is átirányítja a bejelentkezést az ügyfél fióktípus alapján. Például egy live.com-fiók esetében a HRE átirányítja a live.com-be a bejelentkezéshez, és a rendszer újra letiltja ezeket a kéréseket. A Microsoft Employees-fiókok esetében a HRE a bejelentkezési adatokhoz msft.sts.microsoft.com fog hozzáférni. Ellenőrizze a hálózati kérelmeket a böngésző hálózatkezelés lapján, hogy megtekintse, mely tartományokra vonatkozó kérések le vannak tiltva, megismételve az előző lépést az IP-cím lekéréséhez és a kimenő portszabályok hozzáadásához a hálózati biztonsági csoportban az adott IP-címre vonatkozó kérelmek engedélyezéséhez (ha lehetséges, adja hozzá az URL-címet és az IP-címet a virtuális gép gazdagépének Ha ismeri a bejelentkezési tartomány pontos IP-tartományait, közvetlenül is hozzáadhat hálózati szabályokhoz.

7. Most jelentkezzen be, hogy a HRE sikeres legyen. A hatáskörébe tartozó portál sikeresen betöltődik, de az összes hatáskörébe tartozó fiók nem fog működni, mert csak egy adott hatáskörébe tartozó fiókhoz férhet hozzá. Írja be a *web. hatáskörébe. Azure. com/Resource/{PurviewAccountName}* értéket, hogy közvetlenül keresse meg azt a hatáskörébe tartozó fiókot, amelyhez sikeresen beállította a saját végpontját.
 
## <a name="ingestion-private-endpoints-and-scanning-sources-in-private-networks-vnets-and-behind-private-endpoints"></a>Privát végpontok betöltése és a privát hálózatok virtuális hálózatok és a privát végpontok mögötti keresési forrásai

Ha biztosítani szeretné a hálózati elkülönítést a hatáskörébe DataMap beolvasott forrásból származó metaadatok számára, akkor az alábbi lépéseket kell követnie:
1. A betöltés **privát végpontjának** engedélyezése [a szakasz](#creating-an-ingestion-private-endpoint) lépéseit követve
1. Ellenőrizze a forrást egy **saját** üzemeltetésű IR használatával.
 
    1. A helyszíni források (például az SQL Server, az Oracle, az SAP és egyebek) jelenleg csak saját üzemeltetésű, IR-alapú vizsgálatokon keresztül támogatottak. A saját üzemeltetésű IR-nek a magánhálózaton belül kell futnia, majd a vnet az Azure-ban kell megadnia. Az Azure-vnet ezután engedélyezni kell a betöltési magánhálózati végponton az alábbi [lépések](#creating-an-ingestion-private-endpoint) végrehajtásával 
    1. Az Azure Blob Storage-hoz, Azure SQL Databasehoz és más típusú **Azure** -forrásokhoz explicit módon válassza a vizsgálat futtatása saját üzemeltetésű IR használatával lehetőséget a hálózat elkülönítésének biztosításához. A saját üzemeltetésű integrációs modul beállításához kövesse az [alábbi lépéseket.](manage-integration-runtimes.md) Ezután állítsa be a vizsgálatot az Azure-forrásra úgy, hogy a hálózat elkülönítése érdekében kiválasztja a saját üzemeltetésű IR-t a **Csatlakozás integrációs** modul legördülő menüjéből. 
    
    :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Az Azure Scan futtatása saját üzemeltetésű IR használatával":::

> [!NOTE]
> Jelenleg nem támogatott az MSI hitelesítőadat-módszer, ha saját üzemeltetésű integrációs modult használ az Azure-források vizsgálatához. Az Azure-forráshoz tartozó más támogatott hitelesítő adatok egyikét kell használnia.

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>Privát végpont engedélyezése meglévő hatáskörébe tartozó fiókokon

A hatáskörébe tartozó fiók létrehozása után két módon adhat hozzá a hatáskörébe tartozó privát végpontokat:

- A Azure Portal használata (hatáskörébe tartozó fiók)
- A Private link Center használata

### <a name="using-the-azure-portal-purview-account"></a>A Azure Portal használata (hatáskörébe tartozó fiók)

1. Navigáljon a Azure Portal a hatáskörébe tartozó fiókra, válassza ki a privát végponti kapcsolatokat a **Beállítások** **hálózat** szakaszában.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Fiók magánhálózati végpontjának létrehozása":::

1. Kattintson a + privát végpont lehetőségre egy új privát végpont létrehozásához.

1. Adja meg az alapvető információkat.

1. Az erőforrás lapon válassza ki a **Microsoft. hatáskörébe/fiókokként** használandó erőforrástípust.

1. Válassza ki azt az erőforrást, amely a hatáskörébe tartozik, és válassza ki a cél alerőforrást a **fiókhoz**.

1. Válassza ki a **virtuális hálózat** és a **saját DNS zónát** a konfiguráció lapon. Navigáljon az Összefoglalás lapra, és kattintson a **Létrehozás** elemre a portál privát végpontjának létrehozásához.

> [!NOTE]
> Ugyanezen lépéseket kell követnie a **portálon** kiválasztott cél alerőforráshoz is.

#### <a name="creating-an-ingestion-private-endpoint"></a>Betöltési magánhálózati végpont létrehozása

1. Navigáljon a Azure Portal a hatáskörébe tartozó fiókra, válassza ki a privát végponti kapcsolatokat a **Beállítások** **hálózat** szakaszában.
1. Navigáljon a betöltés **Private Endpoint Connections** lapra, és kattintson az **+ új** elemre új betöltési magánhálózati végpont létrehozásához.

1. Adja meg az alapvető információkat és a vnet részleteit.
 
    :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Privát végpont részleteinek kitöltése":::

1. A beállítás befejezéséhez kattintson a **Létrehozás** gombra.

> [!NOTE]
> A betöltési magánhálózati végpontok csak a fent ismertetett hatáskörébe Azure Portal felhasználói felületen hozhatók létre. Nem hozható létre a privát hivatkozás központjából.

### <a name="using-the-private-link-center"></a>A Private link Center használata

1. Navigáljon a [Azure Portal](https://portal.azure.com).

2. A lap tetején található keresősáv alatt keressen rá a "privát hivatkozás" kifejezésre, és navigáljon a privát kapcsolat panelre az első lehetőségre kattintva.

3. Kattintson a "+ Hozzáadás" gombra, és adja meg az alapvető részleteket.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="PE létrehozása a Private link Centerből":::

4. Válassza ki azt az erőforrást, amely a már létrehozott hatáskörébe tartozik, és válassza a cél alerőforrást a **fiókhoz**.

5. Válassza ki a virtuális hálózat és a saját DNS zónát a konfiguráció lapon. Navigáljon az Összefoglalás lapra, és kattintson a **Létrehozás** elemre a fiók privát végpontjának létrehozásához.

> [!NOTE]
> Ugyanezen lépéseket kell követnie a **portálon** kiválasztott cél alerőforráshoz is.

## <a name="firewalls-to-restrict-public-access"></a>A nyilvános hozzáférés korlátozására szolgáló tűzfalak

Kövesse az alábbi lépéseket a hatáskörébe tartozó fiók teljes hozzáférésének kivágásához a nyilvános internetről. Ez a beállítás a magánhálózati végpontra és a betöltési magánhálózati kapcsolatokra is vonatkozik.

1. Navigáljon a Azure Portal a hatáskörébe tartozó fiókra, válassza ki a privát végponti kapcsolatokat a **Beállítások** **hálózat** szakaszában.
1. Navigáljon a tűzfal lapra, és győződjön meg arról, hogy a váltógomb **megtagadásra** van beállítva.

    :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="Magánhálózati végpont tűzfala – beállítások":::

## <a name="next-steps"></a>Következő lépések

- [Tallózás az Azure-beli hatáskörébe Data Catalog](how-to-browse-catalog.md)

- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)
