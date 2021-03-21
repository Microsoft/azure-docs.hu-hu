---
title: A teljesítmény javítása a fájlok Azure-beli előtérben standard/prémium (előzetes verzió) tömörítésével
description: Ismerje meg, hogyan javíthatja a fájlátvitel sebességét, és hogyan növelheti a lapok terhelését a fájlok Azure-beli bejárati ajtón való tömörítésével.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098990"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>A teljesítmény javítása a fájlok Azure-beli előtérben standard/prémium (előzetes verzió) tömörítésével

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

A fájltömörítés hatékony módszer a fájlátvitel sebességének javítására és az oldal-betöltési teljesítmény növelésére. A tömörítés csökkenti a fájl méretét a kiszolgáló elküldése előtt. A fájltömörítés csökkentheti a sávszélesség-költségeket, és jobb felhasználói élményt biztosíthat a felhasználóknak.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
A fájltömörítés kétféleképpen engedélyezhető:

- A forrás-kiszolgáló tömörítésének engedélyezése. Az Azure bejárati ajtó továbbítja a tömörített fájlokat, és továbbítja azokat az ügyfeleknek, akik ezt kérik.
- A tömörítés engedélyezése közvetlenül az Azure-beli előtérben futó POP-kiszolgálókon (*menet közbeni tömörítés*). Ebben az esetben az Azure bejárati ajtó tömöríti a fájlokat, és elküldi azokat a végfelhasználók számára.

> [!IMPORTANT]
> Az Azure-beli bejárati ajtó konfigurációjának módosításai akár 10 percet is igénybe vesznek a hálózat teljes propagálásához. Ha első alkalommal állítja be a tömörítést a CDN-végponthoz, akkor a hibakeresés előtt 1-2 órát várnia kell, hogy a tömörítési beállítások az összes durranás számára propagálva legyenek.

## <a name="enabling-compression"></a>Tömörítés engedélyezése

> [!Note]
> Az Azure bejárati Ajtójában a tömörítés az útvonalon történő **gyorsítótárazás engedélyezése** része. Csak a **gyorsítótárazás engedélyezése** esetén kihasználhatja a tömörítést az Azure-beli bejárati ajtón.

A tömörítést a következő módokon engedélyezheti:
* Gyors létrehozáskor – a gyorsítótárazás engedélyezésekor engedélyezheti a tömörítést.
* Az egyéni létrehozás során engedélyezze a gyorsítótárazást és a tömörítést útvonal hozzáadásakor. 
* A Endpoint Manager útvonalán.
* Az optimalizálás lapon.

### <a name="enable-compression-in-endpoint-manager"></a>Tömörítés engedélyezése a Endpoint Managerben

1. Az Azure bejárati ajtó standard/prémium profil lapján lépjen a **Endpoint Manager** elemre, és válassza ki azt a végpontot, amely számára engedélyezni kívánja a tömörítést.

1. Válassza a **végpont szerkesztése** lehetőséget, majd válassza ki azt az **útvonalat** , amely számára engedélyezni szeretné a tömörítést. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="A Endpoint Manager kezdőlapjának képernyőképe." lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. Győződjön meg arról, hogy be van jelölve a **gyorsítótárazás engedélyezése** jelölőnégyzet, majd jelölje be a **tömörítés engedélyezése** jelölőnégyzetet.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Tömörítés engedélyezése a Endpoint Managerben.":::   

1. A konfiguráció mentéséhez válassza a **frissítés** lehetőséget.

### <a name="enable-compression-in-optimization"></a>Tömörítés engedélyezése az optimalizálásban

1. Az Azure bejárati ajtó standard/prémium profil lapján lépjen az **optimalizálások** elemre a beállítások területen. Bontsa ki a végpontot az útvonalak listájának megtekintéséhez. 

1. Válassza ki a három pontot azon **útvonal** mellett, amelynél a tömörítés *le van tiltva*. Ezután válassza az **útvonal konfigurálása** lehetőséget.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="A tömörítés engedélyezése az optimalizálási oldalon." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. Győződjön meg arról, hogy be van jelölve a **gyorsítótárazás engedélyezése** jelölőnégyzet, majd jelölje be a **tömörítés engedélyezése** jelölőnégyzetet.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Képernyőfelvétel a tömörítés engedélyezéséről a Endpoint Managerben."::: 

1. Kattintson a **Frissítés** parancsra.

## <a name="modify-compression-content-type"></a>Tömörítési tartalom típusának módosítása

Az optimalizálási lapon módosíthatja a MIME-típusok alapértelmezett listáját.

1. Az Azure bejárati ajtó standard/prémium profil lapján lépjen az **optimalizálások** elemre a beállítások területen. Ezután válassza ki azt az **útvonalat** , amelyen *engedélyezve* van a tömörítés.

1. Válassza ki a három pontot azon **útvonal** mellett, amelyen *engedélyezve* van a tömörítés. Ezután válassza a **tömörített fájltípusok megtekintése** lehetőséget.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="Képernyőkép az optimalizálási oldalról." lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. Törölje az alapértelmezett formátumokat, vagy válassza a **Hozzáadás** lehetőséget új tartalomtípusok hozzáadásához.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="Képernyőfelvétel a fájltömörítés testreszabása lapon."::: 

1. A tömörítési konfiguráció frissítéséhez válassza a **Mentés** lehetőséget.

## <a name="disabling-compression"></a>Tömörítés letiltása

A tömörítést a következő módokon tilthatja le:
* Tiltsa le a tömörítést a Endpoint Manager-útvonalon.
* Tömörítés letiltása az optimalizálási lapon.

### <a name="disable-compression-in-endpoint-manager"></a>Tömörítés letiltása a Endpoint Managerben

1. Az Azure bejárati ajtó standard/prémium profil lapján lépjen a beállítások területen található **Endpoint Manager** elemre. Válassza ki azt a végpontot, amelynél le szeretné tiltani a tömörítést.

1. Válassza a **végpont szerkesztése** lehetőséget, majd válassza ki azt az **útvonalat** , amelyről le szeretné tiltani a tömörítést. Törölje a **tömörítés engedélyezése** négyzet jelölését.

1. A konfiguráció mentéséhez válassza a **frissítés** lehetőséget.

### <a name="disable-compression-in-optimizations"></a>Tömörítés letiltása az Optimalizálásokban

1. Az Azure bejárati ajtó standard/prémium profil lapján lépjen az **optimalizálások** elemre a beállítások területen. Ezután válassza ki azt az **útvonalat** , amelyen *engedélyezve* van a tömörítés.

1. Válassza ki a három pontot azon **útvonal** mellett, amelyen *engedélyezve* van a tömörítés, majd válassza az *útvonal konfigurálása* lehetőséget.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="Képernyőkép a tömörítés letiltásáról az optimalizálási lapon."::: 

1. Törölje a **tömörítés engedélyezése** négyzet jelölését.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="Képernyőkép az útvonal frissítése lapról a tömörítés letiltásához."::: 

1. A konfiguráció mentéséhez válassza a **frissítés** lehetőséget.

## <a name="compression-rules"></a>Tömörítési szabályok

Az Azure bejárati ajtaján csak a jogosult fájlok tömörítettek. A tömörítéshez a fájlnak a következő feltételeknek kell megfelelnie:
* MIME-típusnak kell lennie 
* Nagyobbnak kell lennie 1 KB-nál
* Kisebbnek kell lennie, mint 8 MB

Ezek a profilok a következő tömörítési kódolásokat támogatják:
* gzip (GNU zip)
* brotli 

Ha a kérelem több tömörítési típust is támogat, a brotli tömörítés elsőbbséget élvez.

Ha egy adott eszközre vonatkozó kérelem a gzip-tömörítést adja meg, és a kérés gyorsítótár-hiányt eredményez, az Azure-előtérben az eszköz gzip-tömörítése közvetlenül a POP-kiszolgálón történik. Ezt követően a tömörített fájlt a rendszer a gyorsítótárból kézbesíti.

Ha a forrás darabolásos átvitelt (TÁBLAKIFEJEZÉSEK) használ a tömörített adatok küldésére az Azure-beli előtérben lévő POP-ba, akkor a 8 MB-nál nagyobb válaszok mérete nem támogatott. 

## <a name="next-steps"></a>Következő lépések

- Útmutató az első [szabálykészlet](how-to-configure-rule-set.md) konfigurálásához
- További információ a [szabálykészlet egyeztetési feltételeiről](concept-rule-set-match-conditions.md)
- További információ az [Azure-beli bejárati szabályok készletéről](concept-rule-set.md)
