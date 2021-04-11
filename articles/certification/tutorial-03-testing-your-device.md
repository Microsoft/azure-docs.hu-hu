---
title: Azure Certified Device program – oktatóanyag – az eszköz tesztelése
description: Lépésenkénti útmutató az eszközök AICS szolgáltatással való teszteléséhez az Azure Certified-eszköz portálon
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 4cc9e37e95c6402bc535d818e994327e7d526047
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969682"
---
# <a name="tutorial-test-and-submit-your-device"></a>Oktatóanyag: az eszköz tesztelése és elküldése

A minősítési folyamat következő jelentős szakasza (bár az eszköz adatainak hozzáadása előtt is elvégezhető) magában foglalja az eszköz tesztelését. A portálon az Azure IoT Certification Service (AICS) segítségével bizonyíthatja, hogy az eszköz teljesítménye megfelel a tanúsítási követelményeknek. Miután sikeresen elvégezte a tesztelési fázist, elküldheti az eszközét az Azure minősítési csapatának végső felülvizsgálatára és jóváhagyására.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz csatlakoztatása IoT Hub a Device kiépítési szolgáltatás (DPS) használatával
> * Az eszköz tesztelése a kiválasztott minősítési program (ok) szerint
> * Az eszköz beküldése az Azure minősítési csapat általi felülvizsgálatra

## <a name="prerequisites"></a>Előfeltételek

- Be kell jelentkeznie, és rendelkeznie kell egy projekttel az eszköz számára, amelyet az [Azure Certified Device Portalon](https://certify.azure.com)hozott létre. További információkért tekintse meg az [oktatóanyagot](tutorial-01-creating-your-project.md).
- Választható Javasoljuk, hogy készítse elő az eszközt, és manuálisan ellenőrizze a teljesítményt a minősítési követelmények szerint. Ennek az az oka, hogy ha a különböző eszköz-kóddal vagy minősítési programmal újra szeretne tesztelni, létre kell hoznia egy új projektet.

## <a name="connecting-your-device-using-dps"></a>Az eszköz csatlakoztatása a DPS használatával

Az összes tanúsítvánnyal rendelkező eszköznek bizonyítania kell, hogy képes legyen csatlakozni a IoT Hubhoz a DPS használatával. A következő lépések végigvezetik az eszköznek a portálon való teszteléséhez való sikeres csatlakoztatásának lépésein.

1. A tesztelési fázis megkezdéséhez válassza a `Connect & test` projekt összegzése lapon található hivatkozást:  

    ![Csatlakozás és tesztelési hivatkozás](./media/images/connect-and-test-link.png)

1. A kiválasztott minősítés (ek) től függően a szükséges tesztek megjelennek a "kapcsolat & teszt" oldalon. Tekintse át ezeket, és győződjön meg arról, hogy a megfelelő minősítési programot alkalmazza.  

    ![Csatlakozási és tesztelési oldal](./media/images/connect-and-test.png)

1. Az eszköz csatlakoztatása IoT Hub a Device kiépítési szolgáltatás (DPS) használatával. A DPS támogatja a szimmetrikus kulcsok, az X. 509 minősítés és a platformmegbízhatósági modul (TPM) csatlakozási lehetőségeit. Ez az összes tanúsítvány esetében kötelező.

    - *Az eszköznek az Azure IoT Hub a DPS-vel való csatlakoztatásával kapcsolatos további információkért tekintse meg a [kiépítési eszközök áttekintése](../iot-dps/about-iot-dps.md "Eszközök kiépítési szolgáltatásának áttekintése")című témakört.*
    
1. Szimmetrikus kulcsok használata esetén a rendszer kérni fogja a DPS konfigurálását a megadott DPS-azonosító hatókörével, az eszköz azonosítójával, a hitelesítési kulccsal és a DPS-végponttal. Ellenkező esetben a rendszer az X. 509 tanúsítvány vagy a hátirat kulcs megadását kéri.

1. Miután konfigurálta az eszközt a DPS-vel, a lap alján található gombra kattintva erősítse meg a kapcsolatokat `Connect` . A sikeres csatlakozás után a gombra kattintva léphet a tesztelési fázisra `Next` .  

    ![Csatlakozás és teszt csatlakoztatva](./media/images/connected.png)

## <a name="testing-your-device"></a>Az eszköz tesztelése

Miután sikeresen csatlakoztatta az eszközt a AICS-hez, most már készen áll arra, hogy az Ön által alkalmazott minősítési programhoz tartozó minősítési teszteket futtassa.

1. **Azure Certified minősítésű eszköz minősítése**: az "eszköz funkciójának kiválasztása" lapon áttekintheti és kiválaszthatja, hogy mely tesztek fussanak az eszközön.
1. **IoT Plug and Play minősítés esetén**: körültekintően tekintse át azokat a paramétereket, amelyeket a rendszer az eszköz modelljében deklarált tesztek során ellenőrizni fog.
1. **Az Edge által felügyelt minősítés esetén: a** kapcsolat szemléltetése után nincs szükség további lépésekre.
1. Miután elvégezte a szükséges előkészületeket a megadott minősítési programhoz, válassza a `Next` "teszt" fázis folytatását.
1. Válassza ki `Run tests` a lapot a AICS az eszközön való futtatásának megkezdéséhez.
1. Ha értesítést kapott a tesztek elvégzéséről, válassza a lehetőséget, `Finish` Ha vissza szeretne térni az Összefoglalás lapra.

![Teszt átadva](./media/images/test-pass.png)

7. Ha további kérdései vannak, vagy hibaelhárítási segítségre van szüksége a AICS-mel kapcsolatban, tekintse meg a hibaelhárítási útmutatót.

> [!NOTE]
> Habár a IoT Plug and Play és az Edge által felügyelt online minősítési folyamat elvégzése anélkül végezhető el, hogy az eszközt manuális ellenőrzésre kellene küldenie, az automatizálási szolgáltatáson keresztül megjelenő további eszközök ellenőrzéséhez felveheti a kapcsolatot az Azure Certified Device csapat tagjával.

## <a name="submitting-your-device-for-review"></a>Az eszköz elküldése véleményezésre

Miután elvégezte az összes kötelező mezőt a "Device details" szakaszban, és sikeresen átadta az automatikus tesztelést a "csatlakoztatási & teszt" folyamat során, most már értesítheti az Azure Certified Device csapatát, hogy készen áll a minősítés felülvizsgálatára.

1. Válassza a `Submit for review` projekt összegzése lapon:  

    ![Hivatkozás áttekintése és hitelesítése](./media/images/review-and-certify.png)

1. Erősítse meg a beküldést az előugró ablakban. Az eszköz elküldése után az összes eszköz adatai csak olvashatóak lesznek, amíg a rendszer nem kéri a szerkesztést. (Lásd: [az eszköz adatainak szerkesztése a közzététel után](./how-to-edit-published-device.md).)  

    ![A minősítés áttekintése párbeszédpanel elindítása](./media/images/start-certification-review.png)

1. A projekt elküldése után a projekt összegzése lap azt jelzi, hogy a projekt az `Under Certification Review` Azure minősítési csapata:  

    ![Áttekintés alatt](./media/images/review-and-certify-under-review.png)

1. Az 5-7 munkanapon belül e-mailben választ küld az Azure minősítési csapattól a céges profilban megadott címre az eszköz beküldésének állapotával kapcsolatban.

    - Jóváhagyott küldés  
        A projekt felülvizsgálata és jóváhagyása után egy e-mailt fog kapni. Az e-mail tartalmazza a fájlok egy készletét, beleértve az Azure Certified-eszköz jelvényét, a jelvény használati irányelveit, valamint az eszköz tanúsítványának kibővítésével kapcsolatos egyéb információkat is. Gratulálunk!

    - Küldés függőben  
        Abban az esetben, ha a projekt nincs jóváhagyva, módosíthatja a projekt részleteit, majd újból elküldheti az eszközt a minősítésre való felkészüléshez. A rendszer elküld egy e-mailt, amely arról tájékoztat, hogy miért nem hagyták jóvá a projektet, és milyen lépéseket kell elküldeni a minősítéshez.

## <a name="next-steps"></a>Következő lépések

Gratulálunk! Az eszköz sikeresen átadta az összes tesztet, és jóváhagyta az Azure Certified eszköz programját. Mostantól közzéteheti az eszközét az Azure Certified-katalógusban, ahol az ügyfelek az Azure-ban is biztosak lehetnek abban, hogy az Ön termékeinek biztonságban vannak.
> [!div class="nextstepaction"]
> [Oktatóanyag: az eszköz közzététele](tutorial-04-publishing-your-device.md)

