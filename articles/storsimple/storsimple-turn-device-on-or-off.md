---
title: A StorSimple 8000 Series eszköz be-és kikapcsolása
description: Elmagyarázza, hogyan kapcsolhatja be az új StorSimple eszközt, bekapcsolhatja a leállított vagy megszakadt eszközt, és kikapcsolhatja a futó eszközöket.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8f4269235c494ff9dd8d1bf8e0ef940562f8927
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85515272"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>A StorSimple 8000 Series eszköz bekapcsolása vagy kikapcsolása

## <a name="overview"></a>Áttekintés
Microsoft Azure StorSimple eszköz leállítása nem szükséges a normál rendszerművelet részeként. Előfordulhat azonban, hogy be kell kapcsolni egy új eszközt vagy egy leállítani kívánt eszközt. Általában leállítás szükséges azokban az esetekben, amelyekben le kell cserélni a meghibásodott hardvert, fizikailag át kell helyeznie egy egységet, vagy ki kell vennie egy eszközt a szolgáltatásból. Ez az oktatóanyag ismerteti a StorSimple-eszköz bekapcsolásának és leállításának szükséges eljárását különböző forgatókönyvekben.

## <a name="turn-on-a-new-device"></a>Új eszköz bekapcsolása
A StorSimple-eszközök első alkalommal történő bekapcsolásának lépései eltérnek attól függően, hogy az eszköz 8100 vagy 8600 modell. Az 8100 egyetlen elsődleges bekerítéssel rendelkezik, míg a 8600 egy kettős bekerítésű eszköz, amely egy elsődleges bekerítéssel és egy EBOD ház. Mindkét modell részletes lépései a következő részekben találhatók.

* [Új eszköz csak az elsődleges bekerítéssel](#new-device-with-primary-enclosure-only)
* [Új eszköz EBOD bekerítéssel](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Új eszköz csak az elsődleges bekerítéssel
A StorSimple 8100 modell egyetlen bekerítési eszköz. Az eszköz redundáns energiaellátási és hűtési modulokat (PCMs) tartalmaz. A magas rendelkezésre állás biztosítása érdekében mindkét PCMs telepíteni és csatlakoztatni kell a különböző áramforrásokhoz.

A következő lépések végrehajtásával csatlakoztassa az eszközt a powerhez.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Az eszközök telepítési és kábelezési utasításait az [StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md)című részben találhatja meg. Ügyeljen arra, hogy pontosan kövesse az utasításokat.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Új eszköz EBOD bekerítéssel
A StorSimple 8600 modellnek van egy elsődleges bekerítése és egy EBOD ház is. Ehhez csatlakoztatni kell az egységeket a soros csatlakozású SCSI (SAS) kapcsolathoz és a teljesítményhez.

Ha első alkalommal állítja be az eszközt, hajtsa végre az SAS-kábelezés lépéseit először, majd hajtsa végre a Power kábelezés lépéseit.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Az eszközök telepítési és kábelezési utasításait az [StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md)című részben találhatja meg. Ügyeljen arra, hogy pontosan kövesse az utasításokat.

## <a name="turn-on-a-device-after-shutdown"></a>Eszköz bekapcsolása leállítás után
A StorSimple-eszközök leállítás utáni bekapcsolásának lépései eltérőek attól függően, hogy az eszköz 8100-es vagy 8600-es modell-e. Az 8100 egyetlen elsődleges bekerítéssel rendelkezik, míg a 8600 egy kettős bekerítésű eszköz, amely egy elsődleges bekerítéssel és egy EBOD ház.

* [Csak elsődleges bekerítéssel rendelkező eszköz](#device-with-primary-enclosure-only)
* [EBOD bekerítéssel rendelkező eszköz](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Csak elsődleges bekerítéssel rendelkező eszköz
Leállítás után a következő eljárással bekapcsolhatja a StorSimple-eszközt egy elsődleges bekerítéssel, és nincs EBOD ház.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Az eszköz bekapcsolása csak az elsődleges bekerítéssel
1. Győződjön meg arról, hogy a Power és a hűtő modulok (PCMs-EK) energiaellátási kapcsolói a kikapcsolt állapotban vannak. Ha a kapcsolók nem a kikapcsolt helyzetben vannak, akkor a kikapcsolási pozícióra kell váltania, és várnia kell a fények kikapcsolására.
2. Kapcsolja be az eszközt úgy, hogy a kapcsolókat mindkét PCMs a be pozícióra fordítja. Az eszköznek be kell kapcsolnia.
3. A következő ellenőrzésével ellenőrizheti, hogy az eszköz teljes mértékben be van-e kapcsolva:
   
   1. Az OK LED-ek mindkét PCM-modulon zöldek.
   2. Az állapotjelző LED-ek mindkét vezérlőn tömör zöldek.
   3. A kék LED az egyik vezérlőn villog, ami azt jelzi, hogy a vezérlő aktív.
      
      Ha ezek a feltételek nem teljesülnek, az eszköz nem kifogástalan állapotú. Vegye [fel a kapcsolatot Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>EBOD bekerítéssel rendelkező eszköz
A leállítás után a következő eljárással kapcsolhat be egy StorSimple-eszközt egy elsődleges bekerítéssel és egy EBOD ház használatával. Hajtsa végre az egyes lépéseket a leírtak szerint pontosan. Ennek elmulasztása adatvesztést eredményezhet.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Az eszköz bekapcsolása elsődleges és EBOD bekerítéssel
1. Győződjön meg arról, hogy a EBOD ház csatlakozik az elsődleges bekerítéshez. További információt [a StorSimple 8600-eszköz telepítése](storsimple-8600-hardware-installation.md)című témakörben talál.
2. Győződjön meg arról, hogy a EBOD és az elsődleges házakban a Power és a hűtő modulok (PCMs-EK) ki vannak kapcsolva. Ha a kapcsolók nem a kikapcsolt helyzetben vannak, akkor a kikapcsolási pozícióra kell váltania, és várnia kell a fények kikapcsolására.
3. Először kapcsolja be a EBOD a Power kapcsolók mindkét PCMs való tükrözésével a ON pozícióra. A PCM LED-nek zöld színűnek kell lennie. Az egységhez tartozó zöld EBOD vezérlő azt jelzi, hogy a EBOD ház be van kapcsolva.
4. Kapcsolja be az elsődleges burkolatot a Power kapcsolók mindkét PCMs való tükrözésével a ON pozícióra. A teljes rendszernek ekkor kell lennie.
5. Győződjön meg arról, hogy a SAS LED-ek zöld színűek, ami biztosítja, hogy a EBOD-ház és az elsődleges ház közötti kapcsolat jó legyen.

## <a name="turn-on-a-device-after-a-power-loss"></a>Eszköz bekapcsolása áramkimaradás után
Egy áramszünet vagy megszakítás egy StorSimple-eszközt tud leállítani. A áramkimaradás az egyik tápegységen vagy tápegységen is történhet. A helyreállítási lépések eltérnek attól függően, hogy az eszköz 8100 vagy 8600 modell. Az 8100 egyetlen elsődleges bekerítéssel rendelkezik, míg a 8600 egy kettős bekerítésű eszköz, amely egy elsődleges bekerítéssel és egy EBOD ház. Ez a szakasz az egyes forgatókönyvek helyreállítási eljárását ismerteti.

* [Csak elsődleges bekerítéssel rendelkező eszköz](#8100)
* [EBOD bekerítéssel rendelkező eszköz](#8600)

### <a name="device-with-primary-enclosure-only"></a>Csak elsődleges bekerítéssel rendelkező eszköz <a name="8100"></a>
A rendszer akkor is folytathatja a normál működést, ha áramkimaradás van az egyik áramforrásával. Az eszköz magas rendelkezésre állásának biztosítása érdekében azonban a lehető leghamarabb állítsa vissza az áramellátást a tápegységre.

Ha áramkimaradás vagy áramkimaradás áll fenn mindkét tápegységen, a rendszer sorrendbe állítása és ellenőrzése megtörténik. A visszaállított állapot után a rendszer automatikusan bekapcsol.

### <a name="device-with-ebod-enclosure"></a>EBOD bekerítéssel rendelkező eszköz <a name="8600"></a>
#### <a name="power-loss-on-one-power-supply"></a>Áramkimaradás egyetlen tápegységen
A rendszer akkor is folytathatja a normál működést, ha az elsődleges házban vagy az EBOD-házban áramkimaradás van az egyik áramforrásával. Az eszköz magas rendelkezésre állásának biztosítása érdekében azonban a lehető leghamarabb állítsa vissza az áramellátást a tápegységre.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Az elsődleges és a EBOD-házakban lévő energiaellátási szolgáltatások energiafogyasztása
Ha áramkimaradás vagy áramkimaradás áll fenn mindkét tápegységen, akkor a EBOD-kamra azonnal leáll, és az elsődleges ház szabályos és vezérelt módon leáll. Ha a tápellátás helyreáll, a készülék automatikusan elindul.

Ha a tápellátás manuálisan ki van kapcsolva, hajtsa végre a következő lépéseket a rendszerre való energiaellátás helyreállításához.

1. Kapcsolja be a EBOD bekerítését.
2. Ha a EBOD ház be van kapcsolva, kapcsolja be az elsődleges bekerítést.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Áramkimaradás a EBOD-ház energiaellátási szolgáltatásán
A kábelek beállításakor gondoskodnia kell arról, hogy a EBOD soha ne legyenek csatlakoztatva különálló PDU-hoz. Ha a EBOD és az elsődleges ház egy időben meghiúsul, akkor a rendszer helyreáll.

Ha csak a EBOD ház meghibásodik mindkét tápegységen, a rendszer nem fogja automatikusan helyreállítani. Végezze el a következő lépéseket a rendszer bekapcsolásához, és állítsa vissza Kifogástalan állapotba:

1. Ha az elsődleges bekerítés be van kapcsolva, kapcsolja ki az energiagazdálkodási és a hűtési modult (PCMs).
2. Várjon néhány percet, amíg a rendszer leáll.
3. Kapcsolja be a EBOD bekerítését.
4. Ha a EBOD ház be van kapcsolva, kapcsolja be az elsődleges bekerítést.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Az eszköz bekapcsolása az elsődleges és a EBOD ház kapcsolatának elvesztése után
Ha a kapcsolat megszakad a készenléti vezérlő és a hozzá tartozó EBOD-vezérlő között, az eszköz továbbra is működni fog. Ha a rendszer aktív vezérlője és a hozzá tartozó EBOD-vezérlő közötti kapcsolat megszakad, a feladatátvételt kell megtörténnie, és az eszköznek továbbra is a megszokott módon kell működnie.

Ha a soros csatlakozású SCSI (SAS) kábelek el lettek távolítva, vagy a EBOD-ház és az elsődleges ház közötti kapcsolat megszakad, az eszköz nem fog működni. Ezen a ponton hajtsa végre az alábbi lépéseket.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Az eszköz bekapcsolása a kapcsolat elvesztése után
1. Hozzáférés az eszköz hátoldalához.
2. Ha a EBOD-ház és az elsődleges ház közötti SAS-kábel kapcsolata megszakadt, a EBOD-ház összes SAS-sáv-LED-je ki lesz kapcsolva.
3. Állítsa le mindkét energiaellátási és hűtési modult (PCMs) a EBOD-ház és az elsődleges ház számára.
4. Várjon, amíg a házak hátoldalán lévő összes jelzőfény kikapcsol.
5. Szúrja be újra az SAS-kábeleket, és győződjön meg arról, hogy jó kapcsolat van a EBOD-ház és az elsődleges ház között.
6. Először kapcsolja be a EBOD beosztását úgy, hogy mindkét PCM kapcsolót bekapcsolja a ON pozícióba.
7. Győződjön meg arról, hogy a EBOD ház be van kapcsolva, és ellenőrizze, hogy a zöld LED be van-e kapcsolva.
8. Kapcsolja be az elsődleges bekerítést.
9. Győződjön meg arról, hogy az elsődleges ház be van kapcsolva, és ellenőrizze, hogy a vezérlő zöld LED be van-e kapcsolva.
10. Győződjön meg arról, hogy a EBOD-ház az elsődleges bekerítéssel való kapcsolatban jó, ha ellenőrzi, hogy az SAS Lane-LED-ek (négy EBOD-vezérlő) mindegyike elérhető-e.

> [!IMPORTANT]
> Ha az SAS-kábelek hibásak, vagy ha a EBOD-ház és az elsődleges ház közötti kapcsolat nem jó, akkor a rendszer bekapcsolásakor a helyreállítási módba lép. Ha ez történik, vegye [fel a kapcsolatot Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) .


## <a name="turn-off-a-running-device"></a>Futó eszköz kikapcsolása
Előfordulhat, hogy egy futó StorSimple-eszközt le kell állítani, ha áthelyezik, kivették a szolgáltatást, vagy hibás összetevővel rendelkezik, amelyet cserélni kell. A lépések eltérnek attól függően, hogy a StorSimple-eszköz 8100 vagy 8600 modell. Az 8100 egyetlen elsődleges bekerítéssel rendelkezik, míg a 8600 egy kettős bekerítésű eszköz, amely egy elsődleges bekerítéssel és egy EBOD ház. Ez a szakasz részletesen ismerteti a futó eszközök leállításának lépéseit.

* [Eszköz elsődleges bekerítéssel](#8100a)
* [EBOD bekerítéssel rendelkező eszköz](#8600a)

### <a name="device-with-primary-enclosure"></a>Eszköz elsődleges bekerítéssel <a name="8100a"></a>
Az eszköz leállításához a Azure Portalon vagy a Windows PowerShell StorSimple-bővítménye keresztül teheti meg az eszközt. 

> [!IMPORTANT]
> Ne állítson le egy futó eszközt az eszköz hátoldalán található főkapcsoló gomb használatával.
> 
> Az eszköz leállítása előtt győződjön meg róla, hogy az eszköz összes összetevője kifogástalan állapotú. A Azure Portal navigáljon az **eszközök**  >  **figyelése**  >  **hardver** állapota elemre, és ellenőrizze, hogy az összes összetevő állapota zöld. Ez csak kifogástalan állapotú rendszer esetén igaz. Ha a rendszer leállítása hibás összetevő helyett történik, akkor a **hardver állapotában** a megfelelő összetevő hibás (piros) vagy csökkentett (sárga) állapotát fogja látni.
> 
> 

Miután elvégezte a Windows PowerShell StorSimple-bővítménye vagy a Azure Portal elérését, kövesse a [StorSimple-eszköz leállítása](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device)című témakör lépéseit. 

### <a name="device-with-ebod-enclosure"></a>EBOD bekerítéssel rendelkező eszköz <a name="8600a"></a>
> [!IMPORTANT]
> Az elsődleges bekerítés és a EBOD bekerítésének leállítása előtt győződjön meg arról, hogy az eszköz összes összetevője kifogástalan állapotú. A Azure Portal navigáljon az **eszközök**  >  **figyelése**  >  **hardver állapota** elemre, és ellenőrizze, hogy az összes összetevő kifogástalan állapotú-e.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Futó eszköz leállítása EBOD bekerítéssel
1. Kövesse az [StorSimple-eszköz leállítása](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) az elsődleges ház számára című témakörben felsorolt lépéseket.
2. Az elsődleges bekerítés leállítása után állítsa le a EBOD a Power and hűtő modul (PCM) kapcsolók kikapcsolásával.
3. Annak ellenőrzéséhez, hogy a EBOD le van-e állítva, ellenőrizze, hogy a EBOD-ház hátulján lévő összes jelzőfény ki van-e kapcsolva.

> [!NOTE]
> A EBOD-ház az elsődleges házba való csatlakoztatásához használt SAS-kábelek nem távolíthatók el a rendszer leállítása után.

## <a name="next-steps"></a>Következő lépések
Ha a StorSimple-eszköz bekapcsolása vagy leállítása során problémák merülnek [fel, forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md) .

