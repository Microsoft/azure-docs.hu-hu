---
title: Mit takar a Windows Virtual Desktop? – Azure
description: A Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 09/14/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: a6d98f827c39f973714441e308dcc4f2bd061c9b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835577"
---
# <a name="what-is-windows-virtual-desktop"></a>Mit takar a Windows Virtual Desktop?

Windows Virtual Desktop egy asztali és alkalmazás-virtualizálási szolgáltatás, amely a felhőben fut.

Az azure-beli virtuális Windows Virtual Desktop a következőt lehet tenni:

* Több munkamenetes Windows 10 beállítása, amely teljes körű Windows 10 méretezhetőséget biztosít
* A vállalati Microsoft 365 alkalmazások virtualizálása és optimalizálása többfelhasználós virtuális forgatókönyvekben való futtatáshoz
* Windows 7 rendszerű virtuális asztalok ingyenes kiterjesztett biztonsági frissítésekkel
* Meglévő asztali Távoli asztali szolgáltatások (RDS) és Windows Server-asztalok és -alkalmazások bármely számítógépre való telepítése
* Asztali számítógépek és alkalmazások virtualizálása
* Az Windows 10, a Windows Server és a Windows 7 rendszerű asztali számítógépek és alkalmazások egységes felügyeleti élményben való kezelése

## <a name="introductory-video"></a>Bevezető videó

Ismerje meg Windows Virtual Desktop, miért egyedi, és a videó újdonsága:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

A videókról további Windows Virtual Desktop lejátszási [listánkban láthat.](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)

## <a name="key-capabilities"></a>Főbb képességek

A Windows Virtual Desktop skálázható és rugalmas környezetet állíthat be:

* Hozzon létre egy teljes asztali virtualizálási környezetet az Azure-előfizetésében anélkül, hogy további átjárókiszolgálókat kell futtatnia.
* A különféle számítási feladatokhoz annyi gazdagépkészletet tehet közzé, amennyire szüksége van.
* Saját rendszerképet használhat éles számítási feladatokhoz, vagy tesztelhet az Azure Galleryből.
* Költségek csökkentése készletes, több munkamenetből álló erőforrásokkal. Mivel a Windows Serveren az Windows Virtual Desktop és a Távoli asztal Session Host (RDSH) szerepkörrel rendelkező új több munkamenetes funkcióval jelentősen csökkenthető a virtuális gépek száma és az operációs rendszer (OS) terhelése, miközben továbbra is ugyanazok az erőforrások szolgáltatásokat használhatja a felhasználók számára. Windows 10 Enterprise
* Egyéni tulajdonjogot biztosít a személyes (állandó) asztali számítógépeken keresztül.

A virtuális asztalok üzembe helyezhetők és kezelhetők:

* A powershell Azure Portal Windows Virtual Desktop REST-felületek használatával konfigurálhatja a gazdagépkészleteket, alkalmazáscsoportokat hozhat létre, felhasználókat rendelhet hozzá és erőforrásokat tehet közzé.
* Teljes asztali vagy különálló távoli alkalmazásokat tehet közzé egyetlen gazdagépkészletből, egyéni alkalmazáscsoportokat hozhat létre a különböző felhasználói csoportok számára, vagy akár több alkalmazáscsoporthoz is hozzárendelhet felhasználókat a képek számának csökkentése érdekében.
* A környezet kezelése során a beépített delegált hozzáféréssel szerepköröket rendelhet hozzá, és diagnosztikai adatokat gyűjthet a különböző konfigurációs vagy felhasználói hibákkal kapcsolatos információkért.
* A hibák elhárításához használja az új Diagnosztikai szolgáltatást.
* Csak a rendszerképet és a virtuális gépeket kezelje, az infrastruktúrát ne. Nem kell személyesleg kezelnie a Távoli asztal szerepköröket, mint a Távoli asztali szolgáltatások, csak az Azure-előfizetésében lévő virtuális gépeket.

A felhasználókat a virtuális asztalhoz is hozzárendelheti és csatlakoztathatja:

* A hozzárendelés után a felhasználók bármilyen Windows Virtual Desktop elindíthat, hogy a felhasználókat a közzétett Windows-asztalaikhoz és -alkalmazásaikhoz csatlakoztassa. Bármely eszközről csatlakozhat az eszközön található natív alkalmazáson vagy a Windows Virtual Desktop HTML5 webes ügyfélen keresztül.
* Biztonságosan létrehozhat felhasználókat a szolgáltatáshoz fordított kapcsolaton keresztül, így soha nem kell nyitva hagynia a bejövő portokat.

## <a name="requirements"></a>Követelmények

Néhány dolgot be kell állítania, és Windows Virtual Desktop sikeresen csatlakoztatni a felhasználókat a Windows rendszerű asztalaikhoz és alkalmazásaikhoz.

A következő operációs rendszereket támogatjuk, ezért [](https://azure.microsoft.com/pricing/details/virtual-desktop/) győződjön meg arról, hogy rendelkezik a megfelelő licencekkel a felhasználók számára a telepíteni tervbe ve veni asztal és alkalmazások alapján:

|Operációs rendszer|Szükséges licenc|
|---|---|
|Windows 10 Enterprise munkamenet vagy munkamenet Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|RDS ügyfél-hozzáférési licenc (CAL) Frissítési Garancia|

Az infrastruktúrának a következő dolgokra van szüksége a Windows Virtual Desktop:

* Egy [Azure Active Directory](../active-directory/index.yml).
* Egy Windows Server Active Directory van szinkronizálva a Azure Active Directory. Ezt konfigurálhatja a Azure AD Connect (hibrid szervezetek számára) vagy Azure AD Domain Services (hibrid vagy felhőalapú szervezetek számára).
  * Egy Windows Server AD van szinkronizálva a Azure Active Directory. A felhasználó forrása a Windows Server AD, és Windows Virtual Desktop virtuális gép csatlakozik egy Windows Server AD tartományhoz.
  * Egy Windows Server AD van szinkronizálva a Azure Active Directory. A felhasználó forrása a Windows Server AD, és Windows Virtual Desktop virtuális gép csatlakozik egy Azure AD Domain Services tartományhoz.
  * Egy Azure AD Domain Services tartomány. A felhasználó forrása a Azure Active Directory, és Windows Virtual Desktop virtuális gép csatlakozik egy Azure AD Domain Services tartományhoz.
* Egy Azure-előfizetés, amely ugyanannak az Azure AD-bérlőnek a szülője, és egy olyan virtuális hálózatot tartalmaz, amely tartalmazza vagy csatlakoztatva van a Windows Server Active Directory vagy Azure AD DS példányhoz.

Felhasználói követelmények a következő Windows Virtual Desktop:

* A felhasználónak ugyanattól a fióktól kell Active Directory, amely az Azure AD-hez csatlakozik. Windows Virtual Desktop nem támogatja a B2B- vagy MSA-fiókokat.
* Az előfizetéshez használt UPN-nek Windows Virtual Desktop abban a Active Directory tartományban kell lennie, amelybe a virtuális gép csatlakozik.

A virtuális gépekhez létrehozott Azure-beli Windows Virtual Desktop a következőnek kell lennie:

* [Standard tartományhoz vagy](../active-directory-domain-services/compare-identity-solutions.md) [Hibrid AD-hez csatlakozott.](../active-directory/devices/hybrid-azuread-join-plan.md) A virtuális gépek nem csatlakoznak az Azure AD-hez.
* Az alábbi támogatott [operációsrendszer-lemezképek valamelyikének futtatása.](#supported-virtual-machine-os-images)

>[!NOTE]
>Ha Azure-előfizetésre van szüksége, regisztrálhat egy egy hónapos ingyenes [próbaverzióra.](https://azure.microsoft.com/free/) Ha az Azure ingyenes próbaverzióját használja, a Azure AD Domain Services használatával szinkronban Windows Server Active Directory a Azure Active Directory.

Azon URL-címek listájáért, amelyeket érdemes feloldani a Windows Virtual Desktop üzembe helyezéséhez, tekintse meg a Szükséges [URL-címek listáját.](safe-url-list.md)

Windows Virtual Desktop windowsos asztali számítógépekből és alkalmazásokból áll, amelyeket a felhasználóknak kézbesít, valamint a microsoft által az Azure szolgáltatásként üzemeltetett felügyeleti megoldását. Az asztalok és az alkalmazások bármely Azure-régióban lévő virtuális gépeken üzembe helyezhetők, és a virtuális gépek felügyeleti megoldása és adatai a Egyesült Államok. Ez adatátvitelt eredményezhet a Egyesült Államok.

Az optimális teljesítmény érdekében győződjön meg arról, hogy a hálózat megfelel a következő követelményeknek:

* Az ügyfél hálózata és a gazdagépkészleteket üzembe helyező Azure-régió közötti rtT-késésnek 150 ms-nél kisebbnek kell lennie. A Experience [Estimator használatával](https://azure.microsoft.com/services/virtual-desktop/assessment) megtekintheti a kapcsolat állapotát és az ajánlott Azure-régiót.
* A hálózati forgalom az ország/régió határain kívülre áramlik, amikor az asztali számítógépeket és alkalmazásokat kezelő virtuális gépek csatlakoznak a felügyeleti szolgáltatáshoz.
* A hálózati teljesítmény optimalizálása érdekében javasoljuk, hogy a munkamenetgazda virtuális gépei ugyanabban az Azure-régióban találhatóak, mint a felügyeleti szolgáltatás.

Az architektúra jellemző architekturális beállításait Windows Virtual Desktop [](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)architektúraútumunkban láthatja.

## <a name="supported-remote-desktop-clients"></a>Támogatott Távoli asztal ügyfelek

A következő Távoli asztal ügyfelek támogatják a Windows Virtual Desktop:

* [Windows asztali verzió](connect-windows-7-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android](connect-android.md)
* Microsoft Store-ügyfél

> [!IMPORTANT]
> Windows Virtual Desktop nem támogatja a RemoteApp- és asztali kapcsolatok (RADC)-ügyfelet vagy a Távoli asztali kapcsolat (MSTSC) ügyfelet.

Az ügyfelek használatának letiltásának feloldásával kapcsolatos további információkért tekintse meg a [Biztonságos URL-címek listáját.](safe-url-list.md)

## <a name="supported-virtual-machine-os-images"></a>Támogatott virtuálisgép-operációsrendszer-lemezképek

Windows Virtual Desktop az alábbi x64 operációsrendszer-lemezképeket támogatja:

* Windows 10 Enterprise 1809-es vagy újabb verzió
* Windows 10 Enterprise 1809-es vagy újabb verzió (csak Féléves csatorna)
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop nem támogatja az x86 (32 bites), Windows 10 Enterprise N, Windows 10 Pro vagy Windows 10 Enterprise KN operációsrendszer-lemezképeket. A Windows 7 a szektorméret korlátozása miatt nem támogatja a felügyelt Azure Storage-on üzemeltetett VHD- vagy VHDX-alapú profilmegoldásokat.

Az elérhető automatizálási és üzembe helyezési lehetőségek a választott operációs rendszertől és verziótól függnek, az alábbi táblázatban látható módon:

|Operációs rendszer|Azure Image Gallery|Virtuális gépek manuális üzembe helyezése|Azure Resource Manager sablonintegráció|Gazdagépkészletek üzembe Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 Enterprise (több munkamenet), 2004-es verzió|Igen|Igen|Igen|Yes|
|Windows 10 Enterprise (több munkamenet), 1909-es verzió|Igen|Igen|Igen|Yes|
|Windows 10 Enterprise (több munkamenet), 1903-as verzió|Igen|Igen|Nem|Nem|
|Windows 10 Enterprise (több munkamenet), 1809-es verzió|Igen|Igen|Nem|Nem|
|Windows 7 Enterprise|Igen|Igen|Nem|Nem|
|Windows Server 2019|Igen|Igen|Nem|Nem|
|Windows Server 2016|Igen|Igen|Igen|Yes|
|Windows Server 2012 R2|Igen|Igen|Nem|Nem|

## <a name="next-steps"></a>Következő lépések

Ha klasszikus (klasszikus) Windows Virtual Desktop használ, első lépésekért lásd: Bérlő létrehozása [a Windows Virtual Desktop.](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)

Ha a gazdagépet Windows Virtual Desktop integrációval Azure Resource Manager, ehelyett létre kell hoznia egy gazdagépkészletet. Az első lépésekhez a következő oktatóanyagot kell követnie.

> [!div class="nextstepaction"]
> [Gazdagépcsoport létrehozása az Azure Portalon](create-host-pools-azure-marketplace.md)
