---
title: StorSimple 8000 sorozat Update 1,2 kibocsátási megjegyzések | Microsoft Docs
description: A StorSimple 8000 Series Update 1,2 új funkcióit, problémáit és megkerülő megoldásait ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a5ba4b27ae19999237edb75c50db36ef57a10f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956687"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>A StorSimple 8000 Series-eszközre vonatkozó 1,2-es kibocsátási megjegyzések frissítése

## <a name="overview"></a>Áttekintés
A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 Series Update 1,2-es verziójának kritikus megnyitási problémáit. Emellett tartalmazzák a jelen kiadásban található StorSimple szoftver, illesztőprogram és lemez belső vezérlőprogram-frissítéseinek listáját is. 

Az 1,2-es frissítés bármely StorSimple-eszközön (GA), a 0,1-es frissítésen, a 0,2-as verzión vagy az 0,3 szoftver frissítésén is alkalmazható. Az 1,2-es frissítés nem érhető el, ha az eszközön az 1. frissítés vagy a 1,1-es frissítés fut. Ha az eszközön a kiadás (GA) fut, [forduljon a Microsoft ügyfélszolgálatahoz](./storsimple-8000-contact-microsoft-support.md) , és segítse a frissítés telepítését.

A következő táblázat az 1., 1,1-es és 1,2-es frissítésekhez tartozó eszközök szoftvereit sorolja fel.

| Frissítés futtatásakor... | Ez az eszköz szoftverének verziója. |
| --- | --- |
| 1.2-es frissítés |6.3.9600.17584 |
| 1.1-es frissítés |6.3.9600.17521 |
| 1,0-es frissítés |6.3.9600.17491 |

Mielőtt telepítené a frissítést a StorSimple-megoldásban, tekintse át a kibocsátási megjegyzésekben található információkat. További információ: a 1,2-es [frissítés telepítése a StorSimple-eszközön](./storsimple-8000-install-update-5.md). 

> [!IMPORTANT]
> * A frissítés telepítése körülbelül 5-10 órát vesz igénybe (beleértve a Windows-frissítéseket is). 
> * Az 1,2-es frissítés szoftverrel, LSI-illesztőprogrammal és lemezes belső vezérlőprogram-frissítésekkel rendelkezik. A telepítéséhez kövesse az 1,2-es [frissítés telepítése a StorSimple-eszközön](./storsimple-8000-install-update-5.md)című témakör utasításait.
> * Az új kiadások esetében előfordulhat, hogy a frissítések nem jelennek meg azonnal, mert a frissítések fokozatos bevezetését hajtjuk végre. Frissítéseket kereshet néhány nap múlva, mivel ezek hamarosan elérhetők lesznek.
> 
> 

## <a name="whats-new-in-update-12"></a>Az 1,2-es frissítés újdonságai
Ezek a funkciók először az 1. frissítéssel lettek közzétéve, amelyet korlátozott számú felhasználó számára elérhetővé tettek. A 1,2-es frissítéssel a legtöbb StorSimple-felhasználó a következő új szolgáltatásokat és fejlesztési funkciókat látja:

* **Migrálás az 5000-7000 sorozatból a 8000 sorozatú eszközökre** – ez a kiadás egy új áttelepítési funkciót vezet be, amely lehetővé teszi, hogy a StorSimple 5000-7000 Series készülék felhasználói áttelepítse az adatait egy StorSimple 8000 sorozatú fizikai készülékre vagy virtuális készülékre. Az áttelepítési szolgáltatásnak két fő értékű kiosztása van:                                                                  
  
  * **Üzletmenet-folytonosság**, amely lehetővé teszi, hogy a 5000-7000 sorozatú készülékeken lévő meglévő adatmennyiséget az 8000 sorozatú készülékekre lehessen áttelepíteni.
  * **Az 8000 sorozatú berendezések továbbfejlesztett funkciói**, például több készülék hatékony központosított felügyelete StorSimple Manager szolgáltatáson keresztül, jobb minőségű hardver-és frissített belső vezérlőprogram, virtuális berendezések, adatmobilitás és szolgáltatások a jövőbeli ütemtervekben.
    
    Az [áttelepítési útmutatóból](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) megtudhatja, hogyan telepíthet át egy StorSimple 5000-7000-sorozatot egy 8000 sorozatú eszközre. 
* **Rendelkezésre állás a Azure Government portálon** – a StorSimple mostantól elérhető a Azure Government portálon. Lásd: [StorSimple-eszköz üzembe helyezése a Azure Government portálon](./index.yml).
* **Más felhőalapú szolgáltatók támogatása** – a további támogatott felhőalapú szolgáltatók az Amazon S3, az Amazon S3 és az RR, a HP és a OpenStack (bétaverzió).
* **Frissítés a legújabb Storage API** -khoz – ezzel a kiadással a StorSimple frissítve lett a legújabb Azure Storage Service API-kra. Az 1. frissítést (kiadás, 0,1, 0,2 és 0,3) futtató StorSimple 8000 sorozatú eszközök az Azure Storage szolgáltatás API-jai korábbi verzióját használják, mint az 2009. A [Storage Service-verziók eltávolításával kapcsolatos](/archive/blogs/windowsazurestorage/microsoft-azure-storage-service-version-removal-update-extension-to-2016), 2016. augusztus 1-től a frissített közleményben leírtak szerint ezek az API-k elavultak lesznek. Fontos, hogy a StorSimple 8000 sorozat 1. frissítését a 2016. augusztus 1. előtt alkalmazza. Ha ezt nem teszi meg, a StorSimple-eszközök nem fognak megfelelően működni.
* A **zóna redundáns tárolási (ZRS) támogatása** – a Storage API-k legújabb verziójára való frissítéssel a StorSimple 8000 sorozat a helyileg redundáns tárolás (LRS) és a Geo-redundáns tárolás (GRS) mellett a zóna redundáns tárolását (ZRS) is támogatja. Tekintse meg ezt a [cikket az Azure Storage redundancia-beállításairól](../storage/common/storage-redundancy.md) a ZRS részleteinek megtekintéséhez.
* **Továbbfejlesztett kezdeti üzembe helyezési és frissítési élmény** – ebben a kiadásban a telepítési és frissítési folyamatokat továbbfejlesztettük. Ha a hálózati konfiguráció és a tűzfal beállításai helytelenek, a telepítő varázslón keresztüli telepítés megnövelhető a felhasználó számára. További diagnosztikai parancsmagok is rendelkezésre állnak, amelyek segítenek az eszköz hálózatkezelésének hibaelhárításában. A hibaelhárításhoz használt új diagnosztikai parancsmagokkal kapcsolatos további információkért tekintse meg a [központi telepítés hibaelhárítása című cikket](./storsimple-8000-troubleshoot-deployment.md) .

## <a name="issues-fixed-in-update-12"></a>Az 1,2-es frissítésben rögzített problémák
Az alábbi táblázat a 1,2-es, 1,1-es és 1-es frissítésekben rögzített problémák összegzését tartalmazza.    

| Nem. | Szolgáltatás | Probléma | Javítás folyamatban | A fizikai eszközre vonatkozik | A virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell StorSimple-bővítménye |Amikor a felhasználó a Windows PowerShell StorSimple-bővítménye használatával távolról fér hozzá a StorSimple-eszközhöz, majd elindította a telepítővarázslót, összeomlás történt, amint az adatok 0 IP-címet adtak meg. Ez a hiba már meg van javítva az 1. frissítésben. |1. frissítés |Igen |Yes |
| 2 |Gyári beállítások visszaállítása |Egyes esetekben a gyári beállítások visszaállítását követően a StorSimple-eszköz beragadt, és a következő üzenet jelenik meg: **visszaállítás a gyári állapotba (8. fázis)**. Ez akkor történt, ha a CTRL + C billentyűkombinációt lenyomva, miközben a parancsmag folyamatban volt. Ez a hiba már kijavítva van. |1. frissítés |Igen |Nem |
| 3 |Gyári beállítások visszaállítása |A sikertelen kettős vezérlő gyári beállításainak visszaállítása után engedélyezte az eszköz regisztrációját. Ez nem támogatott rendszerkonfigurációt eredményezett. Az 1. frissítésben hibaüzenet jelenik meg, és a rendszer letiltja a regisztrációt egy olyan eszközön, amelyen a gyári beállítások visszaállítása sikertelen volt. |1. frissítés |Igen |Nem |
| 4 |Gyári beállítások visszaállítása |Bizonyos esetekben a hamis pozitív eltérési riasztások lettek kiemelve. Az 1. frissítést futtató eszközökön nem hozhatók létre helytelen eltérési riasztások. |1. frissítés |Igen |Nem |
| 5 |Gyári beállítások visszaállítása |Ha a befejezés előtt megszakadt a gyári beállítások visszaállítása, az eszköz helyreállítási módba lépett, és nem engedélyezte a Windows PowerShell StorSimple-bővítménye elérését. Ez a hiba már kijavítva van. |1. frissítés |Igen |Nem |
| 6 |Vészhelyreállítás |Egy vész-helyreállítási (DR) hibát rögzítettek, amelyben DR a biztonsági mentések felderítése során meghiúsult a célszámítógépen. |1. frissítés |Igen |Yes |
| 7 |Figyelési LED-EK |Bizonyos esetekben a készülék hátulján lévő figyelő LED-ek nem jelezték a helyes állapotot. A kék LED ki lett kapcsolva. Az adat0 és az 1. adatled-EK akkor is villogtak, ha nincsenek konfigurálva ezek a felületek. A probléma kijavítva lett, és a figyelési LED-ek mostantól a megfelelő állapotot jelzik. |1. frissítés |Igen |Nem |
| 8 |Figyelési LED-EK |Bizonyos példányokban az 1. frissítés alkalmazása után az aktív vezérlő kék fénye kikapcsolva, így nehezen azonosítható az aktív vezérlő. Ezt a problémát javítottuk ebben a javítási kiadásban. |1.2-es frissítés |Igen |Nem |
| 9 |Hálózati adapterek |A korábbi verziókban a nem irányítható átjáróval konfigurált StorSimple-eszközök offline módba kerülhetnek. Ebben a kiadásban a 0 adathoz tartozó útválasztási metrika a legalacsonyabb értékkel lett elvégezve. ezért még ha más hálózati adapterek is engedélyezve vannak a felhőben, az eszközről érkező összes Felhőbeli forgalom a 0. adatokon keresztül lesz átirányítva. |1. frissítés |Igen |Yes |
| 10 |Biztonsági másolatok |Az 1. frissítésben szereplő egyik hiba, amely miatt a biztonsági mentések sikertelenek voltak 24 nap elteltével, a javítás 1,1-es frissítésében megoldódott. |1.1-es frissítés |Igen |Yes |
| 11 |Biztonsági másolatok |A korábbi verziókban felmerülő hibák gyenge teljesítményt eredményeztek a Felhőbeli Pillanatképek alacsony változási arányával. Ez a hibajavítás ebben a javítási kiadásban megoldódott. |1.2-es frissítés |Igen |Yes |
| 12 |Frissítések |Az 1. frissítés egyik hibája, amely egy sikertelen frissítést jelentett, és a vezérlők helyreállítási módba való beváltását okozta, a javítás kiadása megoldódott. |1.2-es frissítés |Igen |Yes |

## <a name="known-issues-in-update-12"></a>Az 1,2-es frissítés ismert problémái
A következő táblázat az ebben a kiadásban található ismert problémák összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma | Megjegyzések/Áthidaló megoldás | A fizikai eszközre vonatkozik | A virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvóruma |Ritka esetekben, ha egy 8600-es eszköz EBOD-borításában lévő lemezek többsége le van választva, ami nem a lemez kvórumát eredményezi, akkor a tárolási készlet offline állapotba kerül. Offline állapotban marad, még akkor is, ha a lemezek újra vannak csatlakoztatva. |Az eszközt újra kell indítani. Ha a probléma továbbra is fennáll, forduljon Microsoft ügyfélszolgálata a következő lépésekhez. |Igen |Nem |
| 2 |Helytelen vezérlő-azonosító |A vezérlő cseréjének végrehajtásakor a vezérlő 0 vezérlőként jelenhet meg. A vezérlő cseréjekor, amikor a rendszerkép betöltődik a társ csomópontból, a vezérlő azonosítója kezdetben megjelenhet a társ-vezérlő AZONOSÍTÓJAként. Ritka esetekben ez a viselkedés a rendszer újraindítása után is megjelenhet. |Nincs szükség felhasználói beavatkozásra. Ez a helyzet a vezérlő cseréjének befejeződése után maga is megoldódik. |Igen |Nem |
| 3 |Tárfiókok |Ha a Storage szolgáltatással törli a Storage-fiókot, a rendszer nem támogatott forgatókönyvet használ. Ez olyan helyzetet eredményez, amelyben a felhasználói adatszolgáltatások nem kérhetők le. |Igen |Yes | |
| 4 |Eszköz feladatátvétele |Egy adott forrásoldali eszközről a különböző cél eszközökre irányuló mennyiségi tárolók több feladatátvétele nem támogatott. Az eszköz feladatátvétele egyetlen elhalt eszközről több eszközre teszi a kötet tárolóját az első sikertelenül átesett eszközön az adatok tulajdonjogának elvesztésével. Ilyen feladatátvétel után ezek a mennyiségi tárolók a klasszikus Azure-portálon megtekintve eltérően jelennek meg vagy viselkednek. | |Igen |Nem |
| 5 |Telepítés |A SharePoint rendszerhez készült StorSimple-adapter során meg kell adnia egy eszköz IP-címét ahhoz, hogy a telepítés sikeresen befejeződik. | |Igen |Nem |
| 6 |Webproxy |Ha a webproxy konfigurációja HTTPS protokollt használ a megadott protokollként, az eszközről a szolgáltatásra irányuló kommunikáció hatással lesz, és az eszköz offline állapotba kerül. A támogatási csomagok a folyamat során is létrejönnek, és jelentős erőforrásokat fogyasztanak az eszközön. |Győződjön meg arról, hogy a webproxy URL-címe HTTP protokollt használ a megadott protokollként. További információ: [Configure web proxy for your device](./storsimple-8000-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
| 7 |Webproxy |Ha a webproxyt egy regisztrált eszközön konfigurálja és engedélyezi, akkor újra kell indítania az aktív vezérlőt az eszközön. | |Igen |Nem |
| 8 |Magas Felhőbeli késés és magas I/O-munkaterhelés |Ha a StorSimple-eszköz nagyon magas Felhőbeli késések (másodpercek sorrendje) és magas I/O-munkaterhelések kombinációját tapasztalja, az eszközök mennyisége csökkentett teljesítményű állapotba kerül, és az I/O-művelet meghiúsulhat az "eszköz nem üzemkész" hiba miatt. |Ennek a helyzetnek a helyreállításához manuálisan kell újraindítani az eszközöket, vagy feladatátvételt kell végrehajtani az eszközön. |Igen |Nem |
| 9 |Azure PowerShell |Ha a **Get-&#124; AzureStorSimpleStorageAccountCredential StorSimple parancsmagot használja Select-Object – első 1 – várja** meg az első objektum kiválasztását, hogy új **volumecontainer tárhoz való** objektumot hozzon létre, a parancsmag az összes objektumot visszaadja. |Zárja be a parancsmagot zárójelben a következő módon: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object – első 1 – várakozás** |Igen |Yes |
| 10 |Migrálás |Ha több mennyiségi tárolót továbbítanak az áttelepítéshez, a legújabb biztonsági mentéshez használt ETA csak az első kötet-tároló esetében pontos. Emellett a párhuzamos áttelepítés az első kötet-tároló első 4 biztonsági mentése után is elindul. |Javasoljuk, hogy egyszerre egy mennyiségi tárolót telepítsen át. |Igen |Nem |
| 11 |Migrálás |A visszaállítást követően a kötetek nincsenek hozzáadva a biztonsági mentési házirendhez vagy a virtuális lemez csoportjához. |A biztonsági másolatok létrehozásához hozzá kell adnia ezeket a köteteket egy biztonsági mentési szabályzathoz. |Igen |Yes |
| 12 |Migrálás |Az áttelepítés befejezése után az 5000/7000 sorozatú eszköz nem fér hozzá az áttelepített adattárolóhoz. |Azt javasoljuk, hogy az áttelepítés befejezése és véglegesítése után törölje az áttelepített adattárolókat. |Igen |Nem |
| 13 |Klón és DR |Az 1. frissítést futtató StorSimple-eszközök nem tudják az 1. frissítés előtti szoftvert futtató eszközön a klónozást vagy a vész-helyreállítást végrehajtani. |A művelet végrehajtásához frissítenie kell a megcélzott eszközt az 1. frissítéshez |Igen |Yes |
| 14 |Migrálás |Az áttelepítésre vonatkozó konfigurációs biztonsági mentés meghiúsulhat egy 5000-7000 sorozatú eszközön, ha nincsenek társított kötetek nélküli kötetek. |Törölje az összes olyan üres kötetet, amely nem rendelkezik társított kötetekkel, majd próbálja megismételni a konfiguráció biztonsági mentését. |Igen |Nem |

## <a name="physical-device-updates-in-update-12"></a>Fizikai eszközök frissítései a 1,2-es frissítésben
Ha a 1,2-es patch-es frissítés egy fizikai eszközre van alkalmazva (az 1. frissítés előtti verziókban fut), akkor a szoftver verziója a 6.3.9600.17584 értékre változik.

## <a name="controller-and-firmware-updates-in-update-12"></a>Vezérlő és belső vezérlőprogram frissítései a 1,2-es frissítésben
Ez a kiadás frissíti az illesztőprogramot és a lemez belső vezérlőprogramot az eszközön.

* A SAS-vezérlő frissítésével kapcsolatos további információkért lásd: [1. frissítés LSI sas-vezérlőkhöz a Microsoft Azure StorSimple készülékben](https://support.microsoft.com/kb/3043005). 
* További információ a lemez belső vezérlőprogram-frissítéséről: [Microsoft Azure StorSimple berendezés lemezes belső vezérlőprogram-frissítése 1](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Virtuális eszközök frissítései a 1,2-es frissítésben
Ez a frissítés nem alkalmazható a virtuális eszközre. Új virtuális eszközöket kell létrehozni. 

## <a name="next-steps"></a>Következő lépések
* [Telepítse az 1,2-es frissítést az eszközön](./storsimple-8000-install-update-5.md).