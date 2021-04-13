---
title: Azure Certified Device program – oktatóanyag – eszköz adatainak hozzáadása
description: Részletes útmutató az eszközök adatainak a projekthez való hozzáadásához az Azure Certified-eszköz portálon
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: f4f3d045a2530fa54d22bec789918454cba80097
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310427"
---
# <a name="tutorial-add-device-details"></a>Oktatóanyag: eszköz adatainak hozzáadása

Most, hogy létrehozta a projektet az eszközéhez, és készen áll a minősítési folyamat megkezdésére. Először adja hozzá az eszköz adatait. Ezek közé tartoznak azok a műszaki specifikációk, amelyek alapján az ügyfelek megtekinthetik az Azure Certified eszköz katalógusát, valamint azokat a marketing-adatokat, amelyeket a döntés meghozatala után fognak használni.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszköz adatainak hozzáadása az összetevők és a függőségek funkcióinak használatával
> * Az eszközre vonatkozó első lépések útmutatójának feltöltése
> * Adja meg a marketing részleteit az eszköz megvásárlásához
> * Az iparági tanúsítványok opcionális azonosítása

## <a name="prerequisites"></a>Előfeltételek

* Be kell jelentkeznie, és rendelkeznie kell egy projekttel az eszköz számára, amelyet az [Azure Certified Device Portalon](https://certify.azure.com)hozott létre. További információkért tekintse meg az [oktatóanyagot](tutorial-01-creating-your-project.md).
* Az eszköznek PDF formátumban kell megjelennie az első lépések útmutatójában. A minősítési programtól és az Ön által választott nyelvtől függően számos első lépésből álló sablont biztosítunk. A sablonok az első [lépések sablonok](https://aka.ms/GSTemplate "Első lépések sablonok") GitHub-helyén érhetők el.

## <a name="adding-technical-device-details"></a>Technikai eszköz részleteinek hozzáadása

A projekt lap "bemeneti eszköz részletei" nevű első szakasza lehetővé teszi az eszköz alapvető hardveres képességeinek, például az eszköznév, a leírás, a processzor, az operációs rendszer, a csatlakozási lehetőségek, a hardveres felületek, az iparági protokollok, a fizikai méretek és egyéb információk megadását. Habár számos mező nem kötelező, a legtöbb információ elérhetővé válik az Azure Certified-katalógus potenciális ügyfelei számára, ha úgy dönt, hogy a minősítés után közzéteszi az eszközt.

1. Az `Add` eszköz részletei szakasz megnyitásához kattintson a projekt összefoglaló lapján a "bemeneti eszköz részletei" szakaszra. Öt szakaszt fog látni a befejezéshez.

![A projekt részletei lap képe](./media/images/device-details-menu.png)

2. Tekintse át azokat az információkat, amelyeket korábban megadott a projekt a lapon való létrehozásakor `Basics` .
1. Tekintse át az eszközhöz alkalmazott tanúsítványokat a `Certifications` lapon.
1. Nyissa meg a `Product details` fület, és válasszon ki legalább egy operációs rendszert.
1. Adjon hozzá **legalább** egy különálló összetevőt, amely leírja az eszközt. Az összetevő-használattal kapcsolatos további útmutatást [itt](how-to-using-the-components-feature.md)tekintheti meg.
1. Kattintson a `Save` gombra. Ezután szerkesztheti az összetevő eszközét, és további speciális részleteket is hozzáadhat.
1. Az összetevő részletei által nem rögzített további eszköz adatainak listázása `Additional product details` .
1. Ha `Other` az összetevő valamelyik mezőjében meg van jelölve, vagy ha az Azure minősítési csapatával szeretne megtekinteni egy különleges körülményt, hagyja ki a szakasz megjegyzéseit `Comments for reviewer` .
1. A `Dependencies` Tab használatával listázhatja az összes függőséget, ha az eszközön további hardver vagy szolgáltatások szükségesek az Azure-ba való adatküldéshez. [Itt](how-to-indirectly-connected-devices.md)megtekintheti a függőségek listázásával kapcsolatos további útmutatást.
1. Ha elégedett a megadott adatokkal, a lapon megtekintheti az `Review` eszköz összes adatának teljes készletét, amely csak olvasható.
1. Kattintson a `Project summary` lap tetején az Összefoglalás lapra való visszatéréshez.

![A Project details oldalának áttekintése](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>Az első lépések útmutatójának feltöltése

Az első lépések útmutató egy PDF-dokumentum, amely egyszerűbbé teszi a termék beállítását, konfigurálását és felügyeletét. A cél az, hogy az ügyfelek egyszerűen csatlakozhassanak és támogassák az Azure-beli eszközöket az eszköz használatával. A minősítési folyamat részeként partnereinknek meg **kell adniuk az első lépéseket** ismertető útmutatót a legrelevánsabb minősítési programhoz.

1. Ellenőrizze, hogy a megadott [sablonok](https://aka.ms/GSTemplate)alapján megadta-e az összes kért információt az első lépéseket ismertető útmutató PDF-fájljában. A használni kívánt sablonhoz a alkalmazáshoz használt minősítési jelvényt kell meghatározni. (Egy IoT Plug and Play eszköz például a IoT Plug and Play sablont fogja használni. Az *csak* az Azure Certified eszköz alapkonfigurációjának minősítését kérő eszközök az Azure Certified-eszköz sablonját fogják használni.)
1. Kattintson `Add` a projekt összegzése lap "első lépések" útmutató szakaszára.

![GSG gomb képe](./media/images/gsg-menu.png)

2. A PDF feltöltéséhez kattintson a fájl választása gombra.
1. A formázáshoz tekintse át a dokumentumot az előzetes verzióban.
1. Mentse a feltöltést a Save (Mentés) gombra kattintva.
1. Kattintson a `Project summary` lap tetején az Összefoglalás lapra való visszatéréshez.

## <a name="providing-marketing-details"></a>Marketing részleteinek megadása

Ezen a területen megadhatja az eszközre vonatkozó, az ügyfélre vonatkozó marketing-információkat. Ezek a mezők az Azure Certified-eszköz katalógusában jelennek meg, ha a minősített eszköz közzétételét választja.

1. A marketing részletei `Add` lap megnyitásához kattintson a marketing részleteinek hozzáadása szakaszra.

![Marketing részletei szakasz képe](./media/images/marketing-details.png)

1. Töltsön fel egy termék fényképét JPEG vagy PNG formátumban, amelyet a katalógusban fog használni.
1. Írja be az eszköz rövid leírását, amely megjelenik a katalógus Product Description (termékleírás) oldalán.
1. Az eszköz földrajzi elérhetőségének jelzése.
1. Adja meg az eszköz gyártójának marketing lapjára mutató hivatkozást. Ennek egy olyan webhelyre mutató hivatkozásnak kell lennie, amely további információkat nyújt az eszközről.
    > [!Note]
    > Győződjön meg arról, hogy az összes megadott URL-cím érvényes, vagy aktív lesz a közzétételkor a jóváhagyás után. *

1. Legfeljebb 3 olyan célcsoportot jelezhet, amelyet az eszközre optimalizáltak.
1. Adja meg az eszköz legfeljebb 5 forgalmazójának adatait. Ez magában foglalhatja a gyártó saját webhelyét is.

    > [!Note]
    > Ha nincs megadva a forgalmazói termék oldalának URL-címe, akkor a `Shop` katalógusban lévő gomb alapértelmezés szerint a (z) számára megadott hivatkozást fogja megadni `Distributor page` , ami nem feltétlenül vonatkozik az eszközre. Ideális esetben a terjesztő URL-címnek egy adott oldalra kell vezetnie, ahol az ügyfél vásárolhat egy eszközt, de nem kötelező. Ha a terjesztő ugyanaz, mint a gyártó, akkor ez az URL-cím lehet ugyanaz, mint a gyártó marketing lapja. *)

1. Ide kattintva `Save` erősítse meg az adatokat.
1. Kattintson a `Project summary` lap tetején az Összefoglalás lapra való visszatéréshez.

## <a name="declaring-additional-industry-certifications"></a>További iparági minősítések deklarálása

Az eszközhöz esetlegesen kapott további iparági tanúsítványokat is népszerűsítheti. Ezek a minősítések további egyértelművé teszik az eszköz rendeltetésszerű használatát, és az Azure Certified-eszköz katalógusában kereshetők.

1. Kattintson `Add` az "iparági tanúsítványok biztosítása" szakaszra.
1. Kattintson ide az `Add a certification` általános iparági minősítési programok listájának kiválasztásához. Ha a termék nem szerepel a listán, megadhat egy egyéni karakterláncot a lehetőség kiválasztásával `Other (please specify)` .
1. A felülvizsgáló leírását vagy megjegyzéseit opcionálisan is megadhatja. Ezek a jegyzetek azonban nem lesznek nyilvánosan elérhetők a katalógusban való megtekintéshez.
1. Ide kattintva `Save` erősítse meg az adatokat.
1. Kattintson a `Project summary` lap tetején az Összefoglalás lapra való visszatéréshez.

## <a name="next-steps"></a>Következő lépések

Most már elvégezte az eszköz leírásának folyamatát. Ez segít az Azure Certified Device felülvizsgálati csapatának és az ügyfélnek, hogy jobban megértse a terméket. Ha elégedett a megadott adatokkal, most már készen áll a minősítési folyamat tesztelési fázisára való áttérésre.
> [!div class="nextstepaction"]
> [Oktatóanyag: az eszköz tesztelése](tutorial-03-testing-your-device.md)
