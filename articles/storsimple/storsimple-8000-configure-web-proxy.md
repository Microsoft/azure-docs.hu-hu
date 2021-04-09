---
title: Webproxy beállítása a StorSimple 8000 Series eszközhöz | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a StorSimple-eszköz WebProxy-beállításait a Windows PowerShell StorSimple-bővítménye használatával.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 48671e7558ea1bd613d33372c96fa3c563407e81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017219"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Webproxy konfigurálása a StorSimple-eszközhöz

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag leírja, hogyan konfigurálhatja és tekintheti meg a StorSimple-eszköz WebProxy-beállításait a Windows PowerShell StorSimple-bővítménye használatával. A WebProxy beállításait a StorSimple-eszköz használja a felhővel folytatott kommunikáció során. A webproxy-kiszolgálók egy újabb biztonsági réteg hozzáadására, a tartalom szűrésére, a gyorsítótárazásra, a sávszélesség-követelmények enyhítésére, vagy akár az elemzések megkönnyítésére szolgálnak.

Az oktatóanyagban szereplő útmutató csak az StorSimple 8000 sorozatú fizikai eszközökre vonatkozik. A webproxy konfigurációja nem támogatott a StorSimple Cloud Applianceon (8010 és 8020).

A webproxy egy _opcionális_ konfiguráció a StorSimple-eszközhöz. A webproxyt csak Windows PowerShell StorSimple-bővítménye használatával konfigurálhatja. A konfiguráció egy kétlépéses folyamat, a következőképpen:

1. Először konfigurálja a WebProxy beállításait a telepítővarázsló vagy Windows PowerShell StorSimple-bővítménye parancsmagok segítségével.
2. Ezután engedélyezheti a konfigurált webproxy-beállításokat Windows PowerShell StorSimple-bővítménye parancsmagokon keresztül.

A webproxy-konfiguráció befejezése után megtekintheti a konfigurált webproxy-beállításokat a Microsoft Azure StorSimple Eszközkezelő szolgáltatásban és a Windows PowerShell StorSimple-bővítményeban is.

Az oktatóanyag elolvasása után a következőket teheti:

* Konfigurálja a webproxyt a telepítővarázsló és a parancsmagok használatával.
* Engedélyezze a webproxyt a parancsmagok használatával.
* Webproxy beállításainak megtekintése a Azure Portalban.
* A webproxy konfigurálása során fellépő hibák elhárítása.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Webproxy konfigurálása Windows PowerShell StorSimple-bővítménye használatával

A webproxy beállításainak konfigurálásához a következők egyikét kell használnia:

* A telepítővarázsló végigvezeti Önt a konfigurációs lépéseken.
* Windows PowerShell StorSimple-bővítménye parancsmagok.

Ezeket a módszereket a következő szakaszokban tárgyaljuk.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>A webproxy konfigurálása a telepítővarázsló használatával

A telepítővarázsló segítségével végigvezeti a webproxy konfigurálásának lépésein. Az alábbi lépések végrehajtásával konfigurálja a webproxyt az eszközön.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>A webproxy konfigurálása a telepítővarázsló segítségével

1. A soros konzol menüjében válassza az 1. lehetőséget, **Jelentkezzen be a teljes hozzáférés** lehetőséggel, és adja meg az **eszköz rendszergazdai jelszavát**. A telepítővarázsló-munkamenet elindításához írja be a következő parancsot:
   
    `Invoke-HcsSetupWizard`
2. Ha első alkalommal használja az eszköz regisztrálásához szükséges varázslót, az összes szükséges hálózati beállítást konfigurálnia kell, amíg el nem éri a webproxy konfigurációját. Ha az eszköz már regisztrálva van, fogadja el az összes konfigurált hálózati beállítást, amíg el nem éri a webproxy konfigurációját. Ha a telepítő varázsló kéri a webproxy beállításainak konfigurálását, írja be az **Igen** értéket.
3. A **webproxy URL-címéhez** adja meg a webproxy-kiszolgáló IP-címét vagy teljes tartománynevét (FQDN), valamint azt a TCP-portszámot, amelyet az eszköznek a felhővel való kommunikációhoz használni kíván. Használja az alábbi formátumot:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Alapértelmezés szerint a 8080-as számú TCP-port van megadva.
4. Válassza a hitelesítési típust **NTLM**, **Basic** vagy **none** értékre. Az alapszintű a proxykiszolgáló konfigurációjának legkevésbé biztonságos hitelesítése. Az NT LAN Manager (NTLM) egy nagyon biztonságos és összetett hitelesítési protokoll, amely egy háromutas üzenetkezelő rendszert használ (esetenként négy, ha további integritásra van szükség) egy felhasználó hitelesítéséhez. Az alapértelmezett hitelesítés az NTLM. További információ: [Alapszintű](https://hc.apache.org/httpclient-3.x/authentication.html) és [NTLM-hitelesítés](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **A StorSimple Eszközkezelő szolgáltatásban az eszköz figyelési diagramjai nem működnek, ha az alapszintű vagy az NTLM-hitelesítés engedélyezve van az eszköz proxykiszolgáló-konfigurációjában. A figyelési diagramok működéséhez gondoskodnia kell arról, hogy a hitelesítés a NONE értékre legyen állítva.**
  
5. Ha engedélyezte a hitelesítést, adjon meg egy **webproxy-felhasználónevet** és egy **webproxy-jelszót**. Emellett meg kell erősítenie a jelszót.
   
    ![Webproxy konfigurálása a StorSimple-Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Ha első alkalommal regisztrálja az eszközt, folytassa a regisztrációval. Ha az eszköz már regisztrálva van, a varázsló kilép. A rendszer menti a konfigurált beállításokat.

A webproxy már engedélyezve van. Kihagyhatja a [webproxy engedélyezése](#enable-web-proxy) lépést, és közvetlenül a [Azure Portal WebProxy-beállításait tekintheti](#view-web-proxy-settings-in-the-azure-portal)meg.

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Webproxy konfigurálása Windows PowerShell StorSimple-bővítménye-parancsmagok használatával

A webproxy-beállítások konfigurálásának másik módja a Windows PowerShell StorSimple-bővítménye-parancsmagok használatával történik. A webproxy konfigurálásához hajtsa végre a következő lépéseket.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>A webproxy konfigurálása a parancsmagok használatával
1. A soros konzol menüjében válassza az 1. lehetőséget, majd **Jelentkezzen be a teljes hozzáférés** lehetőséggel. Ha a rendszer kéri, adja meg az **eszköz rendszergazdai jelszavát**. Az alapértelmezett jelszó: `Password1` .
2. A parancssorba írja be a következőt:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Ha a rendszer kéri, adja meg és erősítse meg a jelszót.
   
    ![Webproxy konfigurálása a StorSimple-Device3](./media/storsimple-configure-web-proxy/IC751831.png)

A webproxyt már konfigurálták, és engedélyezni kell.

## <a name="enable-web-proxy"></a>Webproxy engedélyezése

A webproxy alapértelmezés szerint le van tiltva. Miután konfigurálta a WebProxy beállításait a StorSimple-eszközön, a Windows PowerShell StorSimple-bővítménye használatával engedélyezheti a WebProxy beállításait.

> [!NOTE]
> **Erre a lépésre nincs szükség, ha a telepítővarázsló használatával konfigurálta a webproxyt. A rendszer alapértelmezés szerint automatikusan engedélyezi a webproxyt a telepítővarázsló munkamenete után.**


A webproxy eszközön való engedélyezéséhez hajtsa végre a következő lépéseket a Windows PowerShell StorSimple-bővítményeban:

#### <a name="to-enable-web-proxy"></a>A webproxy engedélyezése
1. A soros konzol menüjében válassza az 1. lehetőséget, majd **Jelentkezzen be a teljes hozzáférés** lehetőséggel. Ha a rendszer kéri, adja meg az **eszköz rendszergazdai jelszavát**. Az alapértelmezett jelszó: `Password1` .
2. A parancssorba írja be a következőt:
   
    `Enable-HcsWebProxy`
   
    Ezzel engedélyezte a webproxy konfigurációját a StorSimple-eszközön.
   
    ![Webproxy konfigurálása a StorSimple-Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Webproxy beállításainak megtekintése a Azure Portal

A webproxy beállításai a Windows PowerShell felületén keresztül konfigurálhatók, és nem módosíthatók a portálon belülről. Megtekintheti azonban ezeket a konfigurált beállításokat a portálon. A webproxy megtekintéséhez hajtsa végre a következő lépéseket.

#### <a name="to-view-web-proxy-settings"></a>A webproxy beállításainak megtekintése
1. Navigáljon a **StorSimple Eszközkezelő service > eszközök** elemre. Válasszon ki egy eszközt, majd kattintson az **eszközbeállítások > hálózat** lehetőségre.

    ![Kattintson a hálózat lehetőségre](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. A **hálózati beállítások** panelen kattintson a **webproxy** csempére.

    ![Kattintson a webproxy elemre.](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. A **webproxy** panelen tekintse át a konfigurált webproxy-beállításokat a StorSimple-eszközön.
   
    ![Webproxy beállításainak megtekintése](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Hibák a webproxy konfigurálásakor

Ha a webproxy beállításai helytelenül vannak konfigurálva, hibaüzenetek jelennek meg Windows PowerShell StorSimple-bővítményeban a felhasználó számára. Az alábbi táblázat a hibaüzenetek némelyikét, azok lehetséges okait és javasolt műveleteit ismerteti.

| Sorozatszám. | HRESULT hibakód | Lehetséges kiváltó ok | Javasolt művelet |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |A parancs a passzív vezérlőről fut, és nem tud kommunikálni az aktív vezérlővel. |Futtassa a parancsot az aktív vezérlőn. Ha a parancsot a passzív vezérlőből szeretné futtatni, ki kell javítania a kapcsolatot a passzívról az aktív vezérlőre. Ha a kapcsolat megszakad, Microsoft ügyfélszolgálata kell bekapcsolódnia. |
| 2. |0x800710dd – a művelet azonosítója érvénytelen. |StorSimple Cloud Appliance nem támogatottak a proxybeállítások. |StorSimple Cloud Appliance nem támogatottak a proxybeállítások. Ezeket csak StorSimple fizikai eszközön lehet konfigurálni. |
| 3. |0x80070057 – Érvénytelen paraméter |A proxybeállítások számára megadott paraméterek egyike érvénytelen. |Az URI-azonosító nem megfelelő formátumban van megadva. Használja a következő formátumot: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba – az RPC-kiszolgáló nem érhető el |A kiváltó ok a következők egyike:</br></br>A fürt nem működik. </br></br>A DataPath szolgáltatás nem fut.</br></br>A parancs a passzív vezérlőről fut, és nem tud kommunikálni az aktív vezérlővel. |Folytassa Microsoft ügyfélszolgálata a fürt és a DataPath szolgáltatás futtatásának biztosításához.</br></br>Futtassa a parancsot az aktív vezérlőből. Ha a parancsot a passzív vezérlőből szeretné futtatni, gondoskodnia kell arról, hogy a passzív vezérlő képes legyen kommunikálni az aktív vezérlővel. Ha a kapcsolat megszakad, Microsoft ügyfélszolgálata kell bekapcsolódnia. |
| 5. |0x800706be – nem sikerült az RPC-hívás |A fürt nem üzemel. |Folytassa Microsoft ügyfélszolgálata a fürt felgyorsításának biztosításához. |
| 6. |0x8007138f – a fürterőforrás nem található |A platform Service-fürt erőforrása nem található. Ez akkor fordulhat elő, ha a telepítés nem volt megfelelő. |Előfordulhat, hogy a gyári beállítások visszaállítását kell végrehajtania az eszközön. Előfordulhat, hogy létre kell hoznia egy platform-erőforrást. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától. |
| 7. |0x8007138c – nem online típusú fürterőforrás |A platform-vagy DataPath-fürt erőforrásai nem online állapotban vannak. |Forduljon a Microsoft ügyfélszolgálatahoz, és győződjön meg arról, hogy a DataPath és a platform Service-erőforrás online állapotban van. |

> [!NOTE]
> * A hibaüzenetek fenti listája nem teljes.
> * A webproxy beállításaival kapcsolatos hibák nem jelennek meg a StorSimple Eszközkezelő szolgáltatásának Azure Portal. Ha a konfiguráció befejezése után probléma van a webproxyval, az eszköz állapota **offline módra** változik a klasszikus portálon. |

## <a name="next-steps"></a>Következő lépések
* Ha problémákat tapasztal az eszköz telepítésekor vagy a webproxy beállításainak konfigurálásakor, tekintse meg a [StorSimple-eszköz központi telepítésének hibaelhárítása](./storsimple-8000-troubleshoot-deployment.md)című témakört.
* Ha szeretné megtudni, hogyan használhatja a StorSimple Eszközkezelő szolgáltatást, a [StorSimple-eszköz felügyeletéhez használja a StorSimple Eszközkezelő szolgáltatását](storsimple-8000-manager-service-administration.md).