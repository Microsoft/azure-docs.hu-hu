---
title: A StorSimple 8000 sorozat üzembe helyezési problémáinak elhárítása | Microsoft Docs
description: Leírja, hogyan diagnosztizálhatja és javíthatja a StorSimple első telepítésekor előforduló hibákat.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: f1bce5c0ecdab4a9c96c3f2cc0e9734247bdfd66
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566345"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>StorSimple-eszközök üzembe helyezésével kapcsolatos problémák elhárítása
## <a name="overview"></a>Áttekintés
Ez a cikk hasznos hibaelhárítási útmutatót nyújt a Microsoft Azure StorSimple telepítéséhez. Ismerteti a gyakori problémákat, a lehetséges okokat és a javasolt lépéseket, amelyek segítenek megoldani a StorSimple konfigurálásakor felmerülő problémákat. 

Ezek az információk az StorSimple 8000 sorozatú fizikai eszközre és a StorSimple Cloud Appliance egyaránt érvényesek.

> [!NOTE]
> Az eszköz konfigurálásával kapcsolatos problémák, amelyek akkor fordulhatnak elő, ha az eszközt első alkalommal telepíti, vagy később is előfordulhat, ha az eszköz működőképes. Ez a cikk az első alkalommal történő üzembe helyezéssel kapcsolatos hibák elhárítására koncentrál. Egy operatív eszköz hibakereséséhez lépjen [a diagnosztika eszköz használatára az operatív eszköz hibakereséséhez](storsimple-8000-diagnostics.md).

Ez a cikk az StorSimple-telepítésekkel kapcsolatos hibaelhárítási eszközöket is ismerteti, és részletes hibaelhárítási példát is tartalmaz.

## <a name="first-time-deployment-issues"></a>Üzembe helyezési problémák az első alkalommal
Ha az eszköz első üzembe helyezése során problémát tapasztal, vegye figyelembe a következő útmutatást:

* Ha fizikai eszközt használ, győződjön meg arról, hogy a hardver telepítve és konfigurálva van a [StorSimple 8100-eszköz telepítése](storsimple-8100-hardware-installation.md) vagy [a StorSimple 8600-eszköz telepítése](storsimple-8600-hardware-installation.md)című részben leírtak szerint.
* Az üzembe helyezés előfeltételeinek ellenőrzése. Győződjön meg arról, hogy az összes információ megtalálható a [központi telepítési konfiguráció ellenőrzőlistáján](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Tekintse át a StorSimple kibocsátási megjegyzéseit, és ellenőrizze, hogy a probléma le van-e írva. A kibocsátási megjegyzések az ismert telepítési problémákra vonatkozó megkerülő megoldásokat tartalmaznak. 

Az eszköz telepítése során a felhasználók által felmerülő leggyakoribb problémák a telepítővarázsló futtatásakor és az eszköz Windows PowerShell StorSimple-bővítménye-on keresztüli regisztrálásakor fordulnak elő. (Windows PowerShell StorSimple-bővítménye használatával regisztrálja és konfigurálja a StorSimple-eszközt. Az eszközök regisztrálásával kapcsolatos további információkért tekintse meg a [3. lépés: az eszköz konfigurálása és regisztrálása a Windows PowerShell StorSimple-bővítménye használatával](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)című témakört.

A következő részekben a StorSimple-eszköz első konfigurálásakor felmerülő problémák megoldásában talál segítséget.

## <a name="first-time-setup-wizard-process"></a>A telepítővarázsló első lépéseinek folyamata
Az alábbi lépések összefoglalják a telepítővarázsló folyamatát. Részletes telepítési információk: helyszíni [StorSimple-eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).

1. Futtassa a [Meghívási-hcssetupwizard parancsmagot](/previous-versions/windows/powershell-scripting/dn688135(v=wps.630)) parancsmagot a telepítővarázsló elindításához, amely végigvezeti Önt a hátralévő lépéseken. 
2. A hálózat konfigurálása: a telepítővarázsló lehetővé teszi a hálózati beállítások konfigurálását a StorSimple-eszközön lévő 0 hálózati adapterhez. A következő beállítások tartoznak ide:
   * Virtuális IP-cím (VIP), alhálózati maszk és átjáró – a [set-HcsNetInterface](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)) parancsmag a háttérben fut. Konfigurálja az IP-címet, az alhálózati maszkot és az átjárót a StorSimple-eszközön található adat0 hálózati adapterhez.
   * Elsődleges DNS-kiszolgáló – a [set-HcsDnsClientServerAddress](/previous-versions/windows/powershell-scripting/dn688172(v=wps.630)) parancsmag a háttérben fut. Konfigurálja a StorSimple-megoldás DNS-beállításait.
   * NTP-kiszolgáló – a [set-HcsNtpClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) parancsmag a háttérben fut. Konfigurálja az NTP-kiszolgáló beállításait a StorSimple-megoldáshoz.
   * Opcionális webes proxy – a [set-HcsWebProxy](/previous-versions/windows/powershell-scripting/dn688154(v=wps.630)) parancsmag a háttérben fut. Beállítja és engedélyezi a StorSimple-megoldás webproxy-konfigurációját.
3. A jelszó beállítása: a következő lépés az eszköz rendszergazdai jelszavának beállítása.
   Az eszköz rendszergazdai jelszava az eszközre való bejelentkezéshez használható. Az eszköz alapértelmezett jelszava: **Password1**.
        
     > [!IMPORTANT]
     > A rendszer a regisztráció előtt összegyűjti a jelszavakat, de csak az eszköz sikeres regisztrálását követően alkalmazza. Ha hiba lép fel a jelszó alkalmazása során, a rendszer kérni fogja a jelszó újbóli megadását, amíg begyűjti a szükséges jelszavakat (amelyek megfelelnek az összetettségi követelményeknek).
     
4. Az eszköz regisztrálása: az utolsó lépés az eszköz regisztrálása a Microsoft Azure-ben futó StorSimple Eszközkezelő szolgáltatással. A regisztrációhoz le kell [kérnie a szolgáltatás regisztrációs kulcsát](storsimple-8000-manage-service.md#get-the-service-registration-key) a Azure Portal, és meg kell adnia a telepítő varázslóban. **Az eszköz sikeres regisztrálása után a szolgáltatás adattitkosítási kulcsát kapja meg. Ügyeljen arra, hogy a titkosítási kulcsot biztonságos helyen tárolja, mert az összes jövőbeli eszközt regisztrálni kell a szolgáltatással.**

## <a name="common-errors-during-device-deployment"></a>Az eszköz üzembe helyezése során felmerülő gyakori hibák
Az alábbi táblázatok felsorolják az esetlegesen előforduló gyakori hibákat:

* Konfigurálja a szükséges hálózati beállításokat.
* Adja meg a nem kötelező webproxy-beállításokat.
* Állítsa be az eszköz rendszergazdai jelszavát.
* Regisztrálja az eszközt.

## <a name="errors-during-the-required-network-settings"></a>Hibák a szükséges hálózati beállításokban
| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |Meghívás – Hcssetupwizard parancsmagot: Ez a parancs csak az aktív vezérlőn futtatható. |A konfiguráció a passzív vezérlőn lett elvégezve. |Futtassa ezt a parancsot az aktív vezérlőről. További információ: [aktív vezérlő azonosítása az eszközön](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Meghívás – Hcssetupwizard parancsmagot: az eszköz nem áll készen. |Problémák léptek fel a 0. adatkapcsolat hálózati kapcsolataival kapcsolatban. |Keresse meg a fizikai hálózati kapcsolatot a 0. adatkapcsolaton. |
| 3 |Meghívás – Hcssetupwizard parancsmagot: az IP-címek ütköznek a hálózat egy másik rendszerével (kivétel a HRESULT: 0x80070263). |A 0. adatcsomaghoz megadott IP-címet már egy másik rendszer használta. |Olyan új IP-címet adjon meg, amely nincs használatban. |
| 4 |Meghívás – Hcssetupwizard parancsmagot: A fürterőforrás nem sikerült. (Kivétel a HRESULT: 0x800713AE). |Ismétlődő VIP-cím. A megadott IP-cím már használatban van. |Olyan új IP-címet adjon meg, amely nincs használatban. |
| 5 |Meghívó-Hcssetupwizard parancsmagot: érvénytelen IPv4-címe. |Az IP-cím formátuma helytelen. |Keresse meg a formátumot, és adja meg újra az IP-címét. További információ: IPv4- [címzés][1]. |
| 6 |Meghívás – Hcssetupwizard parancsmagot: érvénytelen IPv6-cím. |Az IP-cím formátuma helytelen. |Keresse meg a formátumot, és adja meg újra az IP-címét. További információ: [IPv6-címzés][2]. |
| 7 |Meghívás – Hcssetupwizard parancsmagot: a Endpoint Mapper nem érhető el több végpont. (Kivétel a HRESULT: 0x800706D9) |A fürt funkciója nem működik. |A következő lépésekhez [forduljon Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) . |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Hibák a nem kötelező Webproxy-beállítások alatt
| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |Meghívó-Hcssetupwizard parancsmagot: Érvénytelen paraméter (kivétel a HRESULT: 0x80070057) |A proxybeállítások számára megadott paraméterek egyike érvénytelen. |Az URI-azonosító nem a megfelelő formátumban van megadva. Használja a következő formátumot: http:// *\<IP address or FQDN of the web proxy server>* :*\<TCP port number>* |
| 2 |Meghívás – Hcssetupwizard parancsmagot: az RPC-kiszolgáló nem érhető el (kivétel a HRESULT: 0x800706ba) |A kiváltó ok a következők egyike:<ol><li>A fürt nem működik.</li><li>A passzív vezérlő nem tud kommunikálni az aktív vezérlővel, és a parancs a passzív vezérlőről fut.</li></ol> |Az alapvető okoktól függően:<ol><li>[Lépjen kapcsolatba Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) , és ellenőrizze, hogy a fürt működik-e.</li><li>Futtassa a parancsot az aktív vezérlőből. Ha a parancsot a passzív vezérlőből szeretné futtatni, gondoskodnia kell arról, hogy a passzív vezérlő kommunikáljon az aktív vezérlővel. Ha a kapcsolat megszakad, [kapcsolatba kell lépnie Microsoft ügyfélszolgálataval](storsimple-8000-contact-microsoft-support.md) .</li></ol> |
| 3 |Meghívó-Hcssetupwizard parancsmagot: az RPC-hívás sikertelen volt (kivétel a HRESULT: 0x800706be) |A fürt nem üzemel. |[Lépjen kapcsolatba Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) , és ellenőrizze, hogy a fürt működik-e. |
| 4 |Meghívás – Hcssetupwizard parancsmagot: a fürterőforrás nem található (kivétel a HRESULT: 0x8007138f) |A fürterőforrás nem található. Ez akkor fordulhat elő, ha a telepítés nem megfelelő. |Előfordulhat, hogy alaphelyzetbe kell állítania az eszközt az alapértelmezett gyári beállításokra. Fürterőforrás létrehozásához [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md) . |
| 5 |Meghívás – Hcssetupwizard parancsmagot: a fürterőforrás nem online állapotú (kivétel a HRESULT: 0x8007138c) |A fürterőforrás nem online állapotú. |A következő lépésekhez [forduljon Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) . |

## <a name="errors-related-to-device-administrator-password"></a>Az eszköz rendszergazdai jelszavával kapcsolatos hibák
Az eszköz alapértelmezett rendszergazdai jelszava **jelszó1**. Ez a jelszó az első bejelentkezés után lejár. Ezért a telepítővarázsló segítségével módosítania kell a telepítési varázslót. Ha első alkalommal regisztrálja az eszközt, meg kell adnia egy új rendszergazdai jelszót. 

Győződjön meg arról, hogy a jelszavuk megfelel a következő követelményeknek:

* Az eszköz rendszergazdai jelszavának 8 és 15 karakter közöttinek kell lennie.
* A jelszavaknak a következő karakterek közül hármat kell tartalmazniuk: kisbetűs, nagybetűs, numerikus és speciális. 
* A jelszó nem egyezhet meg az utolsó 24 jelszóval.

Ne feledje továbbá, hogy a jelszavak minden évben lejárnak, és csak az eszköz sikeres regisztrálása után módosíthatók. Ha a regisztráció bármilyen okból meghiúsul, a rendszer nem módosítja a jelszavakat.

Az eszköz rendszergazdai jelszavával kapcsolatos további információkért látogasson el [a StorSimple Eszközkezelő szolgáltatás használatára a StorSimple jelszavának módosításához](storsimple-8000-change-passwords.md).

Az eszköz rendszergazdája és a StorSimple Snapshot Manager jelszavának beállításakor előfordulhat, hogy a következő hibák közül egyet vagy többet tapasztal.

| Nem. | Hibaüzenet | Javasolt művelet |
| --- | --- | --- |
| 1 |A jelszó hossza meghaladja a maximális hosszt. |Az eszköz rendszergazdai jelszavának 8 – 15 karakter hosszúnak kell lennie. |
| 2 |A jelszó nem felel meg a szükséges hossznak. |Az eszköz rendszergazdai jelszavának 8 – 15 karakter hosszúnak kell lennie.|
| 3 |A jelszónak kisbetűket kell tartalmaznia. |A jelszónak a következő 4 karakterből kell állnia: kisbetűs, nagybetűs, numerikus és speciális. Ellenőrizze, hogy a Jelszó megfelel-e a követelményeknek. |
| 4 |A jelszónak numerikus karaktereket kell tartalmaznia. |A jelszónak a következő 4 karakterből kell állnia: kisbetűs, nagybetűs, numerikus és speciális. Ellenőrizze, hogy a Jelszó megfelel-e a követelményeknek. |
| 5 |A jelszónak speciális karaktereket kell tartalmaznia. |A jelszónak a következő 4 karakterből kell állnia: kisbetűs, nagybetűs, numerikus és speciális. Ellenőrizze, hogy a Jelszó megfelel-e a követelményeknek. |
| 6 |A jelszónak a következő 4 karakterből kell állnia: nagybetűs, kisbetűs, numerikus és speciális. |A jelszó nem tartalmazza a szükséges típusú karaktereket. Ellenőrizze, hogy a Jelszó megfelel-e a követelményeknek. |
| 7 |A paraméter nem felel meg a megerősítésnek. |Ellenőrizze, hogy a Jelszó megfelel-e az összes követelménynek, és hogy helyesen adta-e meg. |
| 8 |A jelszó nem egyezhet meg az alapértelmezett értékkel. |Az alapértelmezett jelszó a *jelszó1*. Az első bejelentkezés után módosítania kell ezt a jelszót. |
| 9 |A beírt jelszó nem egyezik meg az eszköz jelszavával. Írja be újra a jelszót. |Győződjön meg a jelszó megadásáról, majd írja be újra. |

A rendszer a jelszavakat az eszköz regisztrálása előtt gyűjti, de csak a sikeres regisztráció után alkalmazza. A jelszó-helyreállítási munkafolyamat megköveteli az eszköz regisztrálását.

> [!IMPORTANT]
> Általánosságban elmondható, hogy ha egy jelszó alkalmazására tett kísérlet meghiúsul, a szoftver ismételten megkísérli a jelszó gyűjtését, amíg a művelet nem sikerült. Ritka esetekben a jelszó nem alkalmazható. Ebben az esetben regisztrálhatja az eszközt, és folytathatja a jelszót, azonban a jelszavak nem lesznek módosítva. Az eszköz rendszergazdai jelszavát a Azure Portal regisztrációját követően is módosíthatja.


A Azure Portal jelszavát a StorSimple Eszközkezelő szolgáltatáson keresztül állíthatja alaphelyzetbe. További információért lépjen [az eszköz rendszergazdai jelszavának módosítása](storsimple-8000-change-passwords.md#change-the-device-administrator-password)elemre.

## <a name="errors-during-device-registration"></a>Hibák az eszközregisztráció során
Az eszköz regisztrálásához használja a Microsoft Azure futó StorSimple Eszközkezelő szolgáltatást. Az eszköz regisztrációja során a következő problémák valamelyike merülhet fel.

| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |350027-es hiba: nem sikerült regisztrálni az eszközt a StorSimple Eszközkezelő. | |Várjon néhány percet, majd próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md). |
| 2 |350013-es hiba: hiba történt az eszköz regisztrálásakor. Ennek oka az, hogy a szolgáltatás regisztrációs kulcsa helytelen. | |Regisztrálja újra az eszközt a megfelelő szolgáltatás regisztrációs kulcsával. További információ: [a szolgáltatás regisztrációs kulcsának beszerzése.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |350063-es hiba: a StorSimple Eszközkezelő szolgáltatás hitelesítése sikeres, de a regisztráció nem sikerült. Némi idő elteltével próbálja megismételni a műveletet. |Ez a hiba azt jelzi, hogy az ACS-vel való hitelesítés át lett adva, de a szolgáltatáshoz való regisztrálási hívás sikertelen volt. Ez lehet egy szórványos hálózati hiba eredménye. |Ha a probléma továbbra is fennáll, [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md). |
| 4 |350049-es hiba: a szolgáltatás nem érhető el a regisztráció során. |Ha a szolgáltatás hívása történik, a rendszer egy webes kivételt fogad. Bizonyos esetekben előfordulhat, hogy a művelet később újra próbálkozik. |Ellenőrizze az IP-címet és a DNS-nevet, majd próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, [forduljon a Microsoft ügyfélszolgálatahoz.](storsimple-8000-contact-microsoft-support.md) |
| 5 |350031-es hiba: az eszköz már regisztrálva van. | |Nincs szükség beavatkozásra. |
| 6 |350016-es hiba: az eszköz regisztrálása nem sikerült. | |Győződjön meg arról, hogy a regisztrációs kulcs helyes. |
| 7 |Meghívás – Hcssetupwizard parancsmagot: hiba történt az eszköz regisztrálása közben; Ennek oka az lehet, hogy helytelen az IP-cím vagy a DNS-név. Ellenőrizze a hálózati beállításokat, és próbálkozzon újra. Ha a probléma továbbra is fennáll, [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md). (350050-es hiba) |Győződjön meg arról, hogy az eszköz képes pingelni a külső hálózatot. Ha nem rendelkezik kapcsolattal a külső hálózattal, akkor a regisztráció meghiúsulhat ezzel a hibával. Ez a hiba a következők közül egy vagy több kombinációja lehet:<ul><li>Helytelen IP-cím</li><li>Helytelen alhálózat</li><li>Helytelen átjáró</li><li>Helytelen DNS-beállítások</li></ul> |Tekintse meg a [lépésenkénti hibaelhárítási példa](#step-by-step-storsimple-troubleshooting-example)lépéseit. |
| 8 |Meghívás – Hcssetupwizard parancsmagot: az aktuális művelet végrehajtása egy belső szolgáltatási hiba miatt meghiúsult [0x1FBE2]. Némi idő elteltével próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |Ez egy általános hiba, amely a szolgáltatás vagy az ügynök összes felhasználójának láthatatlan hibáját felváltja. A leggyakoribb ok az lehet, hogy az ACS-hitelesítés sikertelen volt. A hiba lehetséges oka az, hogy az NTP-kiszolgáló konfigurációjának problémái vannak, és az eszközön beállított idő nincs megfelelően beállítva. |Javítsa ki az időt (ha vannak problémák), majd próbálja megismételni a regisztrációs műveletet. Ha a Set-HcsSystem-timezone paranccsal állítja be az időzónát, az időzónában lévő összes szót kihasználja (például "csendes-óceáni téli idő").  Ha a probléma továbbra is fennáll, [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md) a következő lépésekhez. |
| 9 |Figyelmeztetés: nem aktiválható az eszköz. Az eszköz rendszergazdája és a StorSimple Snapshot Manager jelszava nem módosult. |Ha a regisztráció meghiúsul, az eszköz rendszergazdája és StorSimple Snapshot Manager jelszavak nem változnak. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>A StorSimple-környezetek hibaelhárítási eszközei
A StorSimple több eszközt is tartalmaz, amelyek segítségével elháríthatja a StorSimple-megoldást. Ezek az eszközök a következőket foglalják magukban:

* A csomagok és az eszközök naplófájljainak támogatása.
* Hibaelhárításhoz tervezett parancsmagok.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>A hibaelhárításhoz elérhető támogatási csomagok és eszközök naplói
A támogatási csomag tartalmazza az összes olyan naplót, amely segítséget nyújt a Microsoft ügyfélszolgálata csapatnak az eszköz hibáinak elhárításához. A Windows PowerShell StorSimple-bővítménye használatával létrehozhat egy titkosított támogatási csomagot, amelyet aztán megoszthat a támogatási személyzettel.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>A támogatási csomag naplófájljainak vagy tartalmának megtekintése
1. A Windows PowerShell StorSimple-bővítménye használatával hozzon létre egy támogatási csomagot a [támogatási csomag létrehozása és kezelése](storsimple-8000-create-manage-support-package.md)című témakörben leírtak szerint.
2. Töltse le a [visszafejtési parancsfájlt](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) helyileg az ügyfélszámítógépen.
3. Ezzel a [lépésenkénti eljárással](storsimple-8000-create-manage-support-package.md#edit-a-support-package) megnyithatja és visszafejtheti a támogatási csomagot.
4. A visszafejtett támogatási csomag naplói ETW/etvx formátumban vannak. A következő lépések végrehajtásával tekintheti meg ezeket a fájlokat a Windows Eseménynaplóban:
   
   1. A Eseménynapló elindításához futtassa a **eventvwr** parancsot a Windows-ügyfélen.
   2. A **műveletek** ablaktáblán kattintson a **mentett napló megnyitása** lehetőségre, és mutasson a naplófájlok etvx/ETW formátumban (a támogatási csomag) elemre. Most már megtekintheti a fájlt. A fájl megnyitása után kattintson a jobb gombbal, és mentse a fájlt szövegként.
      
      > [!IMPORTANT]
      > A **Get-WinEvent** parancsmag használatával is megnyithatja ezeket a fájlokat a Windows PowerShellben. További információ: [Get-WinEvent](/powershell/module/microsoft.powershell.diagnostics/get-winevent) a Windows PowerShell-parancsmagok dokumentációjában.
     
5. Ha a naplók Eseménynaplóban nyílnak meg, keresse meg a következő naplókat, amelyek az eszköz konfigurációjával kapcsolatos problémákat tartalmaznak:
   
   * hcs_pfconfig/Operational-napló
   * hcs_pfconfig/config
6. A naplófájlokban keressen a telepítővarázsló által meghívott parancsmagokkal kapcsolatos karakterláncokra. A parancsmagok listájáért tekintse meg a telepítővarázsló [első alkalommal történő feldolgozását](#first-time-setup-wizard-process) ismertető témakört.
7. Ha nem tudja kideríteni a probléma okát, [lépjen kapcsolatba Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) a következő lépésekhez. Ha segítségre Microsoft ügyfélszolgálata van szüksége, használja a [támogatási kérelem létrehozása](storsimple-8000-contact-microsoft-support.md#create-a-support-request) című témakör lépéseit.

## <a name="cmdlets-available-for-troubleshooting"></a>Hibaelhárításhoz elérhető parancsmagok
A kapcsolódási hibák észleléséhez használja az alábbi Windows PowerShell-parancsmagokat.

* `Get-NetAdapter`: Ezzel a parancsmaggal észlelhető a hálózati adapterek állapota.
* `Test-Connection`: Ezzel a parancsmaggal ellenőrizheti a hálózaton belüli és kívüli hálózati kapcsolatot.
* `Test-HcsmConnection`: Ezzel a parancsmaggal ellenőrizheti egy sikeresen regisztrált eszköz kapcsolatát.
* `Sync-HcsTime`: Ezzel a parancsmaggal megjelenítheti az eszköz időpontját, és kényszerítheti az időszinkronizálást az NTP-kiszolgálóval.
* `Enable-HcsPing` és `Disable-HcsPing` : ezekkel a parancsmagokkal engedélyezheti a gazdagépek számára a StorSimple-eszközön lévő hálózati adapterek pingelését. Alapértelmezés szerint a StorSimple hálózati adapterek nem válaszolnak a pingelési kérelmekre.
* `Trace-HcsRoute`: Használja ezt a parancsmagot útvonal-nyomkövetési eszközként. A csomagokat az egyes útválasztóknak egy adott időszakon belül egy végső célhelyre küldi, majd az egyes ugrásokból visszaadott csomagok alapján kiszámítja az eredményeket. Mivel `Trace-HcsRoute` a csomagok elvesztésének mértékét az adott útválasztón vagy hivatkozáson jeleníti meg, meghatározhatja, hogy mely útválasztók vagy hivatkozások okozhatnak hálózati problémákat.
* `Get-HcsRoutingTable`: Ezzel a parancsmaggal jelenítheti meg a helyi IP-útválasztási táblázatot.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Az Get-NetAdapter parancsmaggal való hibakeresés
Amikor a hálózati adaptereket konfigurálja egy első alkalommal üzemelő eszköz telepítésére, a hardver állapota nem érhető el a StorSimple Eszközkezelő szolgáltatás felhasználói felületén, mert az eszköz még nincs regisztrálva a szolgáltatásban. Emellett előfordulhat, hogy a **hardver állapota** panel nem mindig megfelelően tükrözi az eszköz állapotát, különösen akkor, ha olyan problémák merülnek fel, amelyek befolyásolják a szolgáltatás szinkronizálását. Ilyen helyzetekben a `Get-NetAdapter` parancsmaggal határozható meg a hálózati adapterek állapota és állapota.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Az eszközön található összes hálózati adapter listájának megjelenítése
1. Indítsa el Windows PowerShell StorSimple-bővítménye, majd írja be a következőt: `Get-NetAdapter` . 
2. Használja a parancsmag kimenetét `Get-NetAdapter` és a következő irányelveket a hálózati adapter állapotának megismeréséhez.
   
   * Ha a csatoló állapota Kifogástalan, és engedélyezve van, a **előtérillesztő ifindex** **állapot jelenik meg.**
   * Ha az illesztőfelület kifogástalan állapotú, de nincs fizikailag csatlakoztatva (hálózati kábel), a **előtérillesztő ifindex** **letiltottként** jelenik meg.
   * Ha az illesztőfelület kifogástalan, de nincs engedélyezve, a **előtérillesztő ifindex** állapota **NotPresent** lesz látható.
   * Ha az illesztőfelület nem létezik, akkor nem jelenik meg a listában. A StorSimple Eszközkezelő szolgáltatás felhasználói felülete továbbra is hibás állapotban jeleníti meg ezt az illesztőfelületet.

A parancsmag használatáról további információt a [Get-NetAdapter](/powershell/module/netadapter/get-netadapter) című témakörben olvashat a Windows PowerShell-parancsmagok dokumentációjában.

A következő részekben a parancsmag kimenetének mintái láthatók `Get-NetAdapter` .

 Ezekben a mintákban a Controller 0 a passzív vezérlő volt, és a következőképpen lett konfigurálva:

* Az eszközön a 0, az 1., a 2. és a 3. adatkapcsolatú hálózati adapter található.
* A 4. és az 5. adatkártyák hálózati adapterei nem voltak jelen; ezért nem szerepelnek a kimenetben.
* AZ adat0 engedélyezve lett.

Az 1. vezérlő volt az aktív vezérlő, és a következőképpen lett konfigurálva:

* Az eszközön a 0, az 1., a 2., a 3., a 4. és az 5. adathálózati adapter található.
* AZ adat0 engedélyezve lett.

**Minta kimenete – 0. vezérlő**

A következő mintaadatok a vezérlő 0 (a passzív vezérlő) kimenetét jelentik. Az 1., a 2. és a 3. adatkapcsolat nincs csatlakoztatva. A 4. és az 5. adatértékek nem szerepelnek a felsorolásban, mert nem szerepelnek az eszközön.

```output
Controller0>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
Ethernet 2           HCS VNIC                                    13       Up
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
```


**Minta kimenet – 1. vezérlő**

A következő mintaadatok az 1. vezérlő (az aktív vezérlő) kimenete. Az eszközön csak az adat0 hálózati adapter van konfigurálva és működik.

```output
Controller1>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
Ethernet 2           HCS VNIC                                    13       Up
DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent
```


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Az Test-Connection parancsmaggal való hibakeresés
A `Test-Connection` parancsmag segítségével meghatározhatja, hogy a StorSimple-eszköz csatlakozhat-e a külső hálózathoz. Ha az összes hálózati paraméter, beleértve a DNS-t is, helyesen van konfigurálva a telepítő varázslóban, a `Test-Connection` parancsmag használatával pingelheti a hálózaton kívüli ismert címeket, például a Outlook.com.

Ha a ping parancsmag le van tiltva, engedélyezze a ping használatát a kapcsolódási problémák elhárítása érdekében.

Tekintse meg az alábbi, kimenetből származó mintákat a `Test-Connection` parancsmagból.

> [!NOTE]
> Az első mintában az eszköz helytelen DNS-sel van konfigurálva. A második mintában a DNS helyes.

**Minta kimenete – helytelen DNS**

Az alábbi minta nem tartalmazza az IPV4-és IPV6-címek kimenetét, ami azt jelzi, hogy a DNS nem oldható fel. Nincs kapcsolat a külső hálózattal, és megfelelő DNS-t kell megadni.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
```

**Minta kimenete – helyes DNS**

A következő példában a DNS az IPV4-címeket adja vissza, ami azt jelzi, hogy a DNS megfelelően van konfigurálva. A kimenet ellenőrzi, hogy van-e kapcsolat a külső hálózattal.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
```

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Az Test-HcsmConnection parancsmaggal való hibakeresés
Használja a `Test-HcsmConnection` parancsmagot olyan eszközhöz, amely már csatlakozott a StorSimple Eszközkezelő szolgáltatáshoz, és regisztrálja őket. Ez a parancsmag segítséget nyújt a regisztrált eszközök és a hozzájuk tartozó StorSimple Eszközkezelő szolgáltatás közötti kapcsolat ellenőrzéséhez. Ezt a parancsot Windows PowerShell StorSimple-bővítménye futtathatja.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Az Test-HcsmConnection-parancsmag futtatása
1. Győződjön meg arról, hogy az eszköz regisztrálva van.
2. Keresse meg az eszköz állapotát. Ha az eszköz inaktiválva van, karbantartási módban vagy offline üzemmódban, a következő hibák valamelyike jelenhet meg:
   
   * ErrorCode. CiSDeviceDecommissioned: azt jelzi, hogy az eszköz inaktiválva van.
   * ErrorCode. DeviceNotReady: azt jelzi, hogy az eszköz karbantartási módban van.
   * ErrorCode. DeviceNotReady: azt jelzi, hogy az eszköz nem online állapotú.
3. Ellenőrizze, hogy fut-e a StorSimple Eszközkezelő szolgáltatás (használja a [Get-ClusterResource](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461004(v=technet.10)) parancsmagot). Ha a szolgáltatás nem fut, a következő hibák jelenhetnek meg:
   
   * ErrorCode. CiSApplianceAgentNotOnline
   * ErrorCode. CisPowershellScriptHcsError: azt jelzi, hogy kivétel történt a Get-ClusterResource futtatásakor.
4. Keresse meg az Access Control Service (ACS) tokent. Ha webes kivételt dob, akkor lehetséges, hogy egy átjáróval kapcsolatos probléma, hiányzó proxy-hitelesítés, helytelen DNS vagy hitelesítési hiba okozta. A következő hibák jelenhetnek meg:
   
   * ErrorCode. CiSApplianceGateway: egy HttpStatusCode. BadGateway kivételt jelez: a név-feloldó szolgáltatás nem tudja feloldani az állomásnév nevét.
   * ErrorCode. CiSApplianceProxy: egy HttpStatusCode. ProxyAuthenticationRequired kivételt jelez (407-es HTTP-állapotkód): az ügyfél nem tudott hitelesíteni a proxykiszolgálót.
   * ErrorCode. CiSApplianceDNSError: egy WebExceptionStatus. NameResolutionFailure kivételt jelez: a név-feloldó szolgáltatás nem tudja feloldani az állomásnév nevét.
   * ErrorCode. CiSApplianceACSError: azt jelzi, hogy a szolgáltatás hitelesítési hibát adott vissza, de van kapcsolat.
     
     Ha nem mutat webes kivételt, ellenőrizze a ErrorCode. CiSApplianceFailure, amely azt jelzi, hogy a berendezés sikertelen volt.
5. A Cloud Service-kapcsolat ellenőrzése. Ha a szolgáltatás webes kivételt dob, a következő hibák jelenhetnek meg:
   
   * ErrorCode. CiSApplianceGateway: egy HttpStatusCode. BadGateway kivételt jelez: egy közbenső proxykiszolgáló rossz kérést kapott egy másik proxytól vagy az eredeti kiszolgálótól.
   * ErrorCode. CiSApplianceProxy: egy HttpStatusCode. ProxyAuthenticationRequired kivételt jelez (407-es HTTP-állapotkód): az ügyfél nem tudott hitelesíteni a proxykiszolgálót.
   * ErrorCode. CiSApplianceDNSError: egy WebExceptionStatus. NameResolutionFailure kivételt jelez: a név-feloldó szolgáltatás nem tudja feloldani az állomásnév nevét.
   * ErrorCode. CiSApplianceACSError: azt jelzi, hogy a szolgáltatás hitelesítési hibát adott vissza, de van kapcsolat.
     
     Ha nem mutat webes kivételt, ellenőrizze a ErrorCode. CiSApplianceSaasServiceError, amely a StorSimple Eszközkezelő szolgáltatással kapcsolatos problémát jelez.
6. Azure Service Bus kapcsolat ellenőrzése. A ErrorCode. CiSApplianceServiceBusError azt jelzi, hogy az eszköz nem tud csatlakozni a Service Bushoz.

A CiSCommandletLog0Curr. errlog és a CiSAgentsvc0Curr. errlog naplófájlok további információkat fognak tartalmazni, például a kivétel részleteit.

További információ a parancsmag használatáról: [test-HcsmConnection](/previous-versions/windows/powershell-scripting/dn715782(v=wps.630)) a Windows PowerShell-dokumentációban.

> [!IMPORTANT]
> Ezt a parancsmagot az aktív és a passzív vezérlőhöz is futtathatja.

Tekintse meg az alábbi, kimenetből származó mintákat a `Test-HcsmConnection` parancsmagból.

**Minta kimenet – a StorSimple 3. frissítését futtató eszköz regisztrálása sikeresen megtörtént**

```output
Controller1>Test-HcsmConnection

Checking device registration state  ... Success
Device registered successfully

Checking primary NTP server [time.windows.com] ... Success

Checking web proxy  ... NOT SET

Checking primary IPv4 DNS server [10.222.118.154] ... Success
Checking primary IPv6 DNS server  ... NOT SET
Checking secondary IPv4 DNS server [10.222.120.24] ... Success
Checking secondary IPv6 DNS server  ... NOT SET

Checking device online  ... Success

Checking device authentication  ... This will take a few minutes.
Checking device authentication  ... Success

Checking connectivity from device to service  ... This will take a few minutes.

Checking connectivity from device to service  ... Success

Checking connectivity from service to device  ... Success

Checking connectivity to Microsoft Update servers  ... Success
Controller1>
```

**Minta kimenet – offline eszköz** 

Ez a minta olyan eszközről származik, amely **Offline** állapotú állapotban van a Azure Portal.

```output
Checking device registrationstate: Success
Device is registered successfully
Checking connectivity from device to SaaS.. Failure
```

Az eszköz nem tudott kapcsolatot létesíteni az aktuális webproxy-konfiguráció használatával. Probléma lehet a webproxy-konfigurációval vagy a hálózati kapcsolattal kapcsolatos problémával. Ebben az esetben győződjön meg arról, hogy a webproxy beállításai helyesek, és a webproxy-kiszolgálók online állapotban vannak és elérhetők.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Az Sync-HcsTime parancsmaggal való hibakeresés
Ezzel a parancsmaggal megjelenítheti az eszköz időpontját. Ha az eszköz órája eltolással rendelkezik az NTP-kiszolgálóval, akkor ezzel a parancsmaggal kényszerítheti az időt az NTP-kiszolgálóval való szinkronizálásra.
- Ha az eszköz és az NTP-kiszolgáló közötti eltolás 5 percnél hosszabb, figyelmeztetés jelenik meg. 
- Ha az eltolás meghaladja a 15 percet, az eszköz offline állapotba kerül. Ezt a parancsmagot továbbra is használhatja az időszinkronizálás kényszerítéséhez. 
- Ha azonban az eltolás meghaladja a 15 órát, akkor nem fogja tudni kikényszeríteni – szinkronizálni az időt, és hibaüzenet jelenik meg.

**Minta kimenet – kényszerített idő szinkronizálása a Sync-HcsTime használatával**

```output
Controller0>Sync-HcsTime
The current device time is 4/24/2015 4:05:40 PM UTC.

Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
[Y] Yes [N] No (Default is "Y"): Y
Controller0>
```

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>A Enable-HcsPing és Disable-HcsPing parancsmagokkal való problémamegoldás
Ezekkel a parancsmagokkal biztosíthatja, hogy az eszközön lévő hálózati adapterek válaszoljanak az ICMP ping-kérelmekre. Alapértelmezés szerint a StorSimple hálózati adapterek nem válaszolnak a pingelési kérelmekre. Ezzel a parancsmaggal megtudhatja, hogy az eszköz online állapotban van-e, és elérhető-e.

**Minta kimenet – Enable-HcsPing és letiltás – HcsPing**

```output
Controller0>
Controller0>Enable-HcsPing
Successfully enabled ping.
Controller0>
Controller0>
Controller0>Disable-HcsPing
Successfully disabled ping.
Controller0>
```

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Az Trace-HcsRoute parancsmaggal való hibakeresés
Használja ezt a parancsmagot útvonal-nyomkövetési eszközként. A csomagokat az egyes útválasztóknak egy adott időszakon belül egy végső célhelyre küldi, majd az egyes ugrásokból visszaadott csomagok alapján kiszámítja az eredményeket. Mivel a parancsmag a csomagok elvesztésének mértékét mutatja az adott útválasztón vagy hivatkozáson, megadhatja, hogy mely útválasztók vagy hivatkozások okozhatnak hálózati problémákat.

**Minta kimenet, amely bemutatja, hogyan lehet nyomon követni egy csomag útvonalát a trace-HcsRoute**

```output
Controller0>Trace-HcsRoute -Target 10.126.174.25

Tracing route to contoso.com [10.126.174.25]
over a maximum of 30 hops:
   0  HCSNode0 [10.126.173.90]
   1  contoso.com [10.126.174.25]

Computing statistics for 25 seconds...
            Source to Here   This Node/Link
Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
   0                                           HCSNode0 [10.126.173.90]
                                 0/ 100 =  0%   |
   1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
[10.126.174.25]

Trace complete.
```

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Az Get-HcsRoutingTable parancsmaggal való hibakeresés
Ezzel a parancsmaggal megtekintheti a StorSimple-eszköz útválasztási táblázatát. Az útválasztási táblázat olyan szabályok halmaza, amelyek segítségével meghatározható, hogy az Internet Protocol (IP) hálózaton áthaladó adatcsomagok hová legyenek irányítva.

Az útválasztási táblázat megjeleníti a csatolókat és az átjárót, amely a megadott hálózatokra irányítja az adatközpontot. Emellett megadja az útválasztási metrikát is, amely egy adott célhely eléréséhez szükséges útvonal döntéshozója. Minél alacsonyabb az útválasztási metrika, annál magasabb a preferencia.

Tegyük fel például, hogy két hálózati adaptere van, a 2. adatkapcsolat és a 3. adatkapcsolat az internethez csatlakozik. Ha a 2. és a 3. adat útválasztási mérőszámai 15 és 261, akkor a 2. adat, az alacsonyabb útválasztási metrika pedig az internet elérésére használt előnyben részesített felület.

Ha a StorSimple-eszközön az 1. frissítést futtatja, az adatok 0 hálózati adaptere a legmagasabb prioritású a Felhőbeli forgalom számára. Ezzel a konfigurációval, még akkor is, ha vannak más felhőalapú felületek is, a Felhőbeli forgalom nagy része a 0. ADATOKon keresztül lesz átirányítva.<!--This implies that even if there are other cloud-enabled interfaces, the cloud traffic would be routed through DATA 0. - Preceding sentence replaces this one. Use of "This implies" is a bit murky. DATA 0 will be the preferred network interface? Is my translation OK?-->

Ha a `Get-HcsRoutingTable` (következő példában látható) paraméterek megadása nélkül futtatja a parancsmagot, a parancsmag az IPv4-és IPv6-útválasztási táblákat is kiírja. Másik lehetőségként megadhatja `Get-HcsRoutingTable -IPv4` vagy `Get-HcsRoutingTable -IPv6`  beolvashatja a megfelelő útválasztási táblázatot.

```output
Controller0>
Controller0>Get-HcsRoutingTable
===========================================================================
Interface List
   14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
   12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
   13...28 18 78 bc 4b 85 ......HCS VNIC
   1...........................Software Loopback Interface 1
   21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
   22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
         127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
         127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
   127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
      169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
   169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
      192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
   192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
   192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
         224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
         224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
         224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
   255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
   255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
   255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
===========================================================================
Persistent Routes:
   Network Address          Netmask  Gateway Address  Metric
            0.0.0.0          0.0.0.0  192.168.111.100       5
===========================================================================

IPv6 Route Table
===========================================================================
Active Routes:
   If Metric Network Destination      Gateway
   1    306 ::1/128                  On-link
   13    276 fd99:4c5b:5525:d80b::/64 On-link
   13    276 fd99:4c5b:5525:d80b::1/128
                                    On-link
   13    276 fd99:4c5b:5525:d80b::3/128
                                    On-link
   13    276 fe80::/64                On-link
   12    261 fe80::/64                On-link
   13    276 fe80::17a:4eba:7c80:727f/128
                                    On-link
   12    261 fe80::fc97:1a53:e81a:3454/128
                                    On-link
   1    306 ff00::/8                 On-link
   13    276 ff00::/8                 On-link
   12    261 ff00::/8                 On-link
   14    266 ff00::/8                 On-link
===========================================================================
Persistent Routes:
   None

Controller0>
```

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Lépésről lépésre StorSimple – hibaelhárítási példa
A következő példa egy StorSimple-telepítés lépésenkénti hibaelhárítását mutatja be. A példában az eszköz regisztrálása egy hibaüzenettel meghiúsul, amely azt jelzi, hogy a hálózati beállítások vagy a DNS-név helytelen.

A visszaadott hibaüzenet a következő:

```output
Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
+CategoryInfo: Not specified
+FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand
```

A hibát a következő problémák okozhatják:

* Helytelen a hardver telepítése
* Hibás hálózati adapter (ek)
* Helytelen IP-cím, alhálózati maszk, átjáró, elsődleges DNS-kiszolgáló vagy webproxy
* Helytelen regisztrációs kulcs
* Helytelen tűzfalbeállítások

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Az eszköz regisztrációs problémájának megkeresése és javítása
1. Keresse meg az eszköz konfigurációját: az aktív vezérlőn futtassa a parancsot `Invoke-HcsSetupWizard` .
   
   > [!NOTE]
   > A telepítővarázsló az aktív vezérlőn kell futnia. Annak ellenőrzéséhez, hogy csatlakozik-e az aktív vezérlőhöz, tekintse meg a soros konzolon megjelenő szalagcímet. A szalagcím jelzi, hogy csatlakozik-e a Controller 0 vagy az 1. vezérlőhöz, és hogy a vezérlő aktív vagy passzív. További információért lépjen az [aktív vezérlő azonosítása az eszközön](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Győződjön meg arról, hogy az eszköz megfelelően van-e kitöltve: Ellenőrizze a hálózati kábeleket az eszköz hátsó síkja. A kábelezés az eszköz modelljére jellemző. További információ: [telepítse a StorSimple 8100 eszközt](storsimple-8100-hardware-installation.md) , vagy [telepítse a StorSimple 8600 eszközt](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Ha 10 GbE hálózati portot használ, akkor a megadott QSFP-SFP adaptereket és SFP-kábeleket kell használnia. További információkért tekintse meg a [10 GbE-porthoz ajánlott kábelek, kapcsolók és adóvevők listáját](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Ellenőrizze a hálózati adapter állapotát:
   
   * A Get-NetAdapter parancsmaggal azonosíthatja a 0 adatmennyiség hálózati adapterének állapotát. 
   * Ha a hivatkozás nem működik, az `ifindex` állapot azt jelzi, hogy az illesztőfelület le van állítva. Ezután ellenőriznie kell a port hálózati csatlakozását a készülékhez és a kapcsolóhoz. Emellett a hibás kábelek kizárására is szükség lesz. 
   * Ha azt gyanítja, hogy az aktív vezérlőn nem sikerült az adat0-port, akkor ellenőrizze, hogy az 1. vezérlő 1-es portjához kapcsolódik-e. Válassza le a hálózati kábelt az eszköz hátoldaláról a 0 vezérlőről, csatlakoztassa a kábelt az 1. vezérlőhöz, majd futtassa újból a Get-NetAdapter parancsmagot.
   
     Ha a vezérlőn található adat 0 port meghibásodik, a következő lépésekhez [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md) . Előfordulhat, hogy cserélnie kell a vezérlőt a rendszeren.
4. Ellenőrizze, hogy van-e kapcsolat a kapcsolóval:
   
   * Győződjön meg arról, hogy az elsődleges ház 0. és 1. vezérlője hálózati adapterei ugyanazon az alhálózaton találhatók. 
   * Keresse meg a hubot vagy az útválasztót. A vezérlőket általában ugyanahhoz a hubhoz vagy útválasztóhoz kell csatlakoztatni. 
   * Győződjön meg arról, hogy a kapcsolódáshoz használt kapcsolók rendelkeznek a 0 ÉRTÉKkel ugyanazon a vLAN-ban mindkét vezérlőnél.
5. A felhasználói hibák megszüntetése:
   
   * Futtassa újra a telepítővarázslót (futtassa a **meghívást-hcssetupwizard parancsmagot**), és adja meg újra az értékeket, és győződjön meg róla, hogy nincsenek hibák. 
   * Ellenőrizze a használt regisztrációs kulcsot. Ugyanez a regisztrációs kulcs több eszköz StorSimple Eszközkezelő szolgáltatáshoz való csatlakoztatására is használható. A [szolgáltatás regisztrációs kulcsának beszerzése](storsimple-8000-manage-service.md#get-the-service-registration-key) című eljárás használatával ellenőrizze, hogy a megfelelő regisztrációs kulcsot használja-e.
     
     > [!IMPORTANT]
     > Ha több szolgáltatás fut, meg kell győződnie arról, hogy a megfelelő szolgáltatás regisztrációs kulcsa az eszköz regisztrálására szolgál. Ha olyan eszközt regisztrált, amely nem megfelelő StorSimple Eszközkezelő szolgáltatással rendelkezik, akkor a következő lépésekhez [fel kell vennie a kapcsolatot a Microsoft ügyfélszolgálataval](storsimple-8000-contact-microsoft-support.md) . Előfordulhat, hogy el kell végeznie az eszköz gyári visszaállítását (ami adatvesztéshez vezethet), majd a kívánt szolgáltatáshoz kapcsolódhat.
     > 
     > 
6. A Test-Connection parancsmag használatával ellenőrizze, hogy van-e kapcsolat a külső hálózattal. További információ: [a Test-Connection parancsmaggal folytatott hibák megoldása](#troubleshoot-with-the-test-connection-cmdlet).
7. A tűzfal zavarásának keresése. Ha meggyőződött arról, hogy a virtuális IP-cím (VIP), az alhálózat, az átjáró és a DNS-beállítások megfelelőek, és a kapcsolódási problémák továbbra is fennállnak, lehetséges, hogy a tűzfal blokkolja az eszköz és a külső hálózat közötti kommunikációt. Ellenőrizze, hogy a 80-es és a 443-es port elérhető-e a StorSimple-eszközön a kimenő kommunikációhoz. További információ: [hálózati követelmények a StorSimple-eszközhöz](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Tekintse meg a naplókat. [A hibaelhárításhoz keresse fel a támogatási csomagokat és az eszközök naplóit](#support-packages-and-device-logs-available-for-troubleshooting).
9. Ha a fenti lépések nem oldják meg a problémát, [kérje a Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) segítségét.

## <a name="next-steps"></a>Következő lépések
[Megtudhatja, hogyan használhatja a diagnosztikai eszközt a StorSimple-eszközök hibakereséséhez](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379547(v=ws.10)
[2]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd392266(v=ws.10)