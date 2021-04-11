---
title: 'Gyors útmutató: Azure Storage-fiók integrálása Azure CDN'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használható az Azure Content Delivery Network (CDN) a nagy sávszélességű tartalmak továbbítására az Azure Storage-ban.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 444639d9e36b67f25ed9b82bd68102273c66ae00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951232"
---
# <a name="quickstart-integrate-an-azure-storage-account-with-azure-cdn"></a>Gyors útmutató: Azure Storage-fiók integrálása Azure CDN

Ebben a rövid útmutatóban engedélyezi az [azure Content Delivery Network (CDN)](cdn-overview.md) számára a tartalom gyorsítótárazását az Azure Storage-ból. Az Azure CDN egy globális megoldást kínál a fejlesztők számára a tartalmak nagy sávszélességű kézbesítéséhez. Képes arra, hogy fizikai csomópontokon gyorsítótárazza a blobokat és a számítási példányok statikus tartalmát az Amerikai Egyesült Államok, Európa, Ázsia, Ausztrália és Dél-Amerika területén.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A tárfiókok hozzáférést biztosítanak az Azure Storage szolgáltatásaihoz, és ők képviselik az Azure Storage szolgáltatási összetevőihez – Azure Blob, Queue és Table Storage – való hozzáférés legmagasabb szintű névterét. További információkért lásd: [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md).

Tárfiók létrehozásához a társított előfizetés szolgáltatás-rendszergazdájának vagy társadminisztrátorának kell lennie.

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban. Ekkor megnyílik az **Új** panel.

1. Keressen rá a **Storage-fiókra** , és válassza a **Storage Account-blob, fájl, tábla, üzenetsor** lehetőséget a legördülő listából. Ezután válassza a **Létrehozás** elemet:
    
    ![Tárolási erőforrás kiválasztása](./media/cdn-create-a-storage-account-with-cdn/cdn-select-new-storage-account.png)

1. A **Storage-fiók létrehozása panelen** adja meg a következő adatokat:

    | Beállítás | Érték | 
    | --- | --- |
    | Projekt részletei > erőforráscsoport | Válassza az **új létrehozása** elemet, és használja a *CDNQuickstart-RG* nevet. Ha szeretné, használhat egy meglévő erőforráscsoportot is. |
    | Példány részletei > Storage-fiók neve | Adja meg a fiók nevét a 3-24 kisbetűs betűk és számok használatával. A névnek egyedinek kell lennie az Azure-ban, és annak az URL-címnek kell lennie, amely az előfizetés blob, üzenetsor vagy tábla erőforrásainak kezelésére szolgál. Ha egy tároló-erőforrást szeretne kezelni a blob Storage-ban, használjon egy URI-t a következő formátumban: http://*&lt; storageaccountname &gt;*. blob.Core.Windows.net/*&lt; Container-name &gt;*.
    | Példány részletei > helye | Válasszon ki egy közeli Azure-régiót a legördülő listából. |
    
    Hagyja meg az összes többi adatot az alapértelmezett értékekre, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.

1. A Storage-fiók létrehozása több percet is igénybe vehet. A létrehozás befejezése után válassza az **erőforrás** megnyitása lehetőséget a Storage-fiók oldalának megnyitásához a következő lépéshez.

## <a name="enable-azure-cdn-for-the-storage-account"></a>Az Azure CDN engedélyezése a tárfiókhoz

1. A Storage-fiók lapján válassza   >  a bal oldali menü blob Service **Azure CDN** elemét. Megjelenik az **Azure CDN** oldal.

    ![CDN-végpont létrehozása](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)
    
1. Az **új végpont** szakaszban adja meg a következő adatokat:

    | Beállítás  | Érték |
    | -------- | ----- |
    | **CDN-profil** | Válassza az **új létrehozása** elemet, és adja meg a profil nevét, például: *CDN-profil-123*. A profil végpontok gyűjteménye. |
    | **Tarifacsomag** | Válasszon az egyik **szabványos** lehetőség közül, például a **Microsoft szabványos**. |
    | **CDN-végpont neve** | Adja meg a végpont nevét, például: *CDN-Endpoint-123*. Ennek a névnek globálisan egyedinek kell lennie az Azure-ban, mert a gyorsítótárazott erőforrásaihoz az URL _&lt; -végpont – name &gt;_. azureedge.NET hozzáfér. |
    | **Forrás gazdaneve** | Alapértelmezés szerint egy új CDN-végpont a tárfiók eszköznevét használja forráskiszolgálóként. |

1. Válassza a **Létrehozás** lehetőséget. A létrejött végpont megjelenik a végpontok listájában.

    ![Tároló új CDN-végpontja](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!TIP]
> Ha szeretne speciális konfigurációkat beállítani a CDN-végponthoz (például a [nagyméretű fájl letöltésének optimalizálását](cdn-optimization-overview.md#large-file-download)), az [Azure CDN bővítményt](cdn-create-new-endpoint.md) is használhatja CDN-profil és végpont létrehozásához.


## <a name="enable-additional-cdn-features"></a>További CDN-szolgáltatások engedélyezése

A tárfiók **Azure CDN** oldalán válassza ki a CDN-végpontot, hogy megnyissa annak konfigurációs lapját.

Ezen a lapon engedélyezhet további CDN-szolgáltatásokat a kézbesítéshez, például a [tömörítést](cdn-improve-performance.md), [a lekérdezési sztringek gyorsítótárazását](cdn-query-string.md) és a [geoszűrést](cdn-restrict-access-by-country.md). 
    
## <a name="enable-sas"></a>A SAS engedélyezése

Ha korlátozott hozzáférést szeretne biztosítani a privát tárolók számára, használhatja az Azure Storage-fiókjának megosztott hozzáférés-aláírási (SAS) funkcióját is. A SAS olyan URI, amely az Azure Storage erőforrásainak korlátozott hozzáférési jogosultságát biztosítja anélkül, hogy közzétenné a fiók kulcsát. További információk: [Az Azure CDN használata a SAS-szal](cdn-sas-storage-support.md).

## <a name="access-cdn-content"></a>Hozzáférés a CDN tartalmához

A CDN-en lévő gyorsítótárazott tartalmakhoz való hozzáféréshez használja a CDN portálon megadott URL-címét. A gyorsítótárazott blobok címének formátuma a következő:

http://<*Endpoint-Name* \> . azureedge.net/<*myPublicContainer* \> /< *BlobName*\>

> [!NOTE]
> Miután engedélyezte a tárfiókhoz való hozzáférést az Azure CDN számára, az összes nyilvánosan elérhető objektum jogosult a CDN POP gyorsítótárazásra. Ha módosítja a CDN egyik gyorsítótárazott objektumát, az új tartalom nem lesz elérhető az Azure CDN-en keresztül, amíg az Azure CDN nem frissíti a tartalmát a gyorsítótárazott tartalom élettartamának lejártát követően.

## <a name="remove-content-from-azure-cdn"></a>Tartalmak eltávolítása az Azure CDN-ről

Ha egy objektumot nem szeretne a továbbiakban gyorsítótárazni az Azure CDN-ben, hajtsa végre a következő műveletek valamelyikét:

- Állítsa a tárolót privátra (nyilvános helyett). További információ: [Névtelen olvasási hozzáférés tárolók és Blobok kezelésére](../storage/blobs/anonymous-read-access-configure.md).
- Tiltsa le vagy törölje a CDN-végpontot az Azure Portalon.
- Módosítsa az üzemeltetett szolgáltatást, hogy ne válaszoljon az objektumra vonatkozó kérelmekre.

Egy, az Azure CDN-ben már gyorsítótárazott objektum mindaddig gyorsítótárazva marad, amíg az adott objektum élettartama le nem jár, vagy amíg a végpontot [véglegesen nem törli](cdn-purge-endpoint.md). Amikor az élettartam lejár, az Azure CDN megállapítja, hogy a CDN-végpont továbbra is érvényes, az objektum pedig névtelenül továbbra is elérhető-e. Ha nem, az objektum a továbbiakban nem lesz gyorsítótárazva.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A korábbi lépésekben létrehozott egy CDN-profilt és egy végpontot egy erőforráscsoportban. Mentse ezeket az erőforrásokat, ha a [Következő lépésekre](#next-steps) szeretne lépni, és meg szeretné tudni, hogyan adhat egyéni tartományt a végpontjához. Ugyanakkor ha a jövőben nem várható ezen erőforrások használata, törölheti őket az erőforráscsoport törlésével, így elkerülheti a további díjakat:

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget, majd válassza a * CDNQuickstart-RG * * elemet.

2. Az **erőforráscsoport** lapon válassza az **erőforráscsoport törlése** elemet, írja be a *CDNQuickstart-RG* karakterláncot a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

    Ezzel törli a gyors útmutatóban létrehozott erőforráscsoportot, profilt és a végpontot.

3. A törlendő tárfiókot jelölje ki az irányítópulton, majd válassza ki a **Törlés** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: a CDN használata a statikus tartalmak kiszolgálására egy webalkalmazásból](cdn-add-to-web-app.md)
