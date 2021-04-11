---
title: Azure Functions-futtatókörnyezet telepítés
description: A Azure Functions-futtatókörnyezet Preview 2 telepítése
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 73b9533fa00c783e1cfb85270198f5c00c66afd5
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106133195"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>A 2. Azure Functions-futtatókörnyezet előzetes verziójának telepítése

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Ha a Azure Functions-futtatókörnyezet Preview 2 verziót szeretné telepíteni, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a gép megfelel a minimális követelményeknek.
1. Töltse le a [Azure functions-futtatókörnyezet előzetes verziójának telepítőjét](https://aka.ms/azafrv2).
1. Távolítsa el az 1. Azure Functions-futtatókörnyezet előzetes verzióját.
1. Telepítse a Azure Functions-futtatókörnyezet Preview 2.
1. Fejezze be a Azure Functions-futtatókörnyezet Preview 2 konfigurációját.
1. Az első függvény létrehozása Azure Functions-futtatókörnyezet előzetes verzióban

## <a name="prerequisites"></a>Előfeltételek

A Azure Functions-futtatókörnyezet előzetes verziójának telepítése előtt a következő erőforrásokat kell elérhetővé tennie:

1. Microsoft Windows Server 2016 vagy Microsoft Windows 10 Creators Update (Professional vagy Enterprise Edition) rendszert futtató számítógép.
1. A hálózaton belül futó SQL Server példány.  A minimálisan szükséges kiadás SQL Server Express.

## <a name="uninstall-previous-version"></a>Előző verzió eltávolítása

Ha korábban már telepítette a Azure Functions-futtatókörnyezet előzetes verzióját, el kell távolítania a legújabb kiadás telepítése előtt.  Távolítsa el a Azure Functions-futtatókörnyezet előzetes verzióját, ha eltávolítja a programot a Windows rendszer programok telepítése és törlése alkalmazásával.

## <a name="install-the-azure-functions-runtime-preview"></a>A Azure Functions-futtatókörnyezet előzetes verziójának telepítése

A Azure Functions-futtatókörnyezet előzetes verziójának telepítője végigvezeti a Azure Functions-futtatókörnyezet előzetes verziójú felügyeleti és feldolgozói szerepkörök telepítésén.  A felügyeleti és a feldolgozói szerepkört ugyanarra a gépre lehet telepíteni.  Azonban a további Function-alkalmazások hozzáadásakor több feldolgozói szerepkört kell üzembe helyeznie a további gépeken, hogy a függvényeket több feldolgozóra lehessen méretezni.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>A felügyeleti és feldolgozói szerepkör telepítése ugyanarra a gépre

1. Futtassa a Azure Functions-futtatókörnyezet Preview telepítőjét.

    ![Azure Functions-futtatókörnyezet előzetes verziójának telepítője][1]

1. Kattintson a **Tovább** gombra.
1. Ha elolvasta a **végfelhasználói licencszerződés** feltételeit, **jelölje be a jelölőnégyzetet** a feltételek elfogadásához, majd kattintson a **tovább** gombra.
1. Válassza ki azokat a szerepköröket, amelyeket telepíteni kíván a Machine **functions felügyeleti szerepkör** és/vagy **functions feldolgozói szerepkörre** , majd kattintson a **tovább** gombra.

    ![Azure Functions-futtatókörnyezet előzetes verziójának telepítője – szerepkör kiválasztása][3]

    > [!NOTE]
    > A **functions feldolgozói szerepkört** számos más gépen is telepítheti. Ehhez kövesse az alábbi utasításokat, és csak a **functions feldolgozói szerepkört** válassza ki a telepítőben.

1. Kattintson a **tovább** gombra, hogy a **Azure functions-futtatókörnyezet** telepítővarázsló megkezdje a telepítési folyamatot a gépen.
1. Ha elkészült, a telepítővarázsló elindítja a **Azure functions-futtatókörnyezet** konfigurációs eszközt.

    ![Azure Functions-futtatókörnyezet előnézet telepítője kész][6]

    > [!NOTE]
    > Ha a-t a **Windows 10 rendszerre** telepíti, és a **tároló** funkció korábban nem volt engedélyezve, a **Azure functions-futtatókörnyezet telepítője** felszólítja a gép újraindítására a telepítés befejezéséhez.

## <a name="configure-the-azure-functions-runtime"></a>A Azure Functions-futtatókörnyezet konfigurálása

A Azure Functions-futtatókörnyezet telepítésének befejezéséhez végre kell hajtania a konfigurációt.

1. A **Azure functions-futtatókörnyezet** konfigurációs eszköz megjeleníti, hogy mely szerepkörök vannak telepítve a gépen.

    ![Azure Functions-futtatókörnyezet előnézet konfigurációs eszköz][7]

1. Kattintson az **adatbázis** lapra, adja meg SQL Server példányának kapcsolati adatait, beleértve az [adatbázis főkulcsának](/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)megadását, majd kattintson az **alkalmaz** gombra.  SQL Server-példányhoz való kapcsolódásra van szükség ahhoz, hogy a Azure Functions-futtatókörnyezet adatbázist hozzon létre a futtatókörnyezet támogatásához.

    ![Azure Functions-futtatókörnyezet előnézeti adatbázis konfigurálása][8]

1. Kattintson a **hitelesítő adatok** fülre.  Itt két új hitelesítő adatot kell létrehoznia egy fájlmegosztás használatával az összes Function-alkalmazás üzemeltetéséhez.  Adja meg a **fájlmegosztás tulajdonosának** és a **fájlmegosztás felhasználójának** **felhasználónevét** és **jelszavát** , majd kattintson az **alkalmaz** gombra.

    ![Azure Functions-futtatókörnyezet előzetes verziójú hitelesítő adatok][9]

1. Kattintson a **fájlmegosztás** fülre.  Itt meg kell adnia a fájlmegosztás helyének részleteit.  A fájlmegosztás létrehozható, vagy használhat egy meglévő fájlmegosztást, és kattintson az **alkalmaz** gombra.  Ha új fájlmegosztási helyet választ, meg kell adnia egy könyvtárat, amelyet a Azure Functions-futtatókörnyezet használhat.

    ![Azure Functions-futtatókörnyezet előnézet-fájlmegosztás][10]

1. Kattintson az **IIS** fülre.  Ezen a lapon megtekintheti az IIS-ben a Azure Functions-futtatókörnyezet konfigurációs eszköz által létrehozott webhelyek részleteit.  Itt megadhat egyéni DNS-nevet a Azure Functions-futtatókörnyezet betekintő portálhoz.  A befejezéshez kattintson az **alkalmaz** gombra.

    ![Azure Functions-futtatókörnyezet előnézet IIS][11]

1. Kattintson a **szolgáltatások** fülre.  Ezen a lapon láthatók a Azure Functions-futtatókörnyezet konfigurációs eszköz szolgáltatásainak állapota.  Ha a  **Azure functions gazdagép aktiválási szolgáltatása** nem fut a kezdeti konfigurálás után, kattintson a **szolgáltatás indítása** lehetőségre.

    ![Azure Functions-futtatókörnyezet előzetes verzió konfigurációjának befejezése][12]

1. Tallózással keresse meg a **Azure functions-futtatókörnyezet portált** `https://<machinename>.<domain>/` .

    ![Azure Functions-futtatókörnyezet betekintő portál][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Az első függvény létrehozása Azure Functions-futtatókörnyezet előzetes verzióban

Az első függvény létrehozása Azure Functions-futtatókörnyezet előzetes verzióban

1. Keresse meg például a **Azure functions-futtatókörnyezet portált** `https://<machinename>.<domain>` `https://mycomputer.mydomain.com` .

1. A rendszer arra kéri, hogy **Jelentkezzen be**, ha egy tartományban van telepítve, használja a tartományi fiókja felhasználónevét és jelszavát, máskülönben a helyi fiók felhasználónevét és jelszavát használja a portálra való bejelentkezéshez.

    ![Azure Functions-futtatókörnyezet betekintő portál bejelentkezés][14]

1. A Function apps létrehozásához létre kell hoznia egy előfizetést.  A portál bal felső sarkában kattintson az **+** előfizetések melletti lehetőségre.

    ![Azure Functions-futtatókörnyezet betekintő portál előfizetései][15]

1. Válassza a **DefaultPlan** lehetőséget, adja meg az előfizetés nevét, majd kattintson a **Létrehozás** gombra.

    ![Azure Functions-futtatókörnyezet betekintő portál előfizetési terve és neve][16]

1. Az összes Function-alkalmazás a portál bal oldali ablaktábláján jelenik meg.  Új függvényalkalmazás létrehozásához válassza ki a fejléc **függvény alkalmazásait** , és kattintson a **+** lehetőségre.

1. Adja meg a Function alkalmazás nevét, válassza ki a megfelelő előfizetést, válassza ki, hogy a Azure Functions futtatókörnyezet melyik verzióját szeretné programozni, és kattintson a **Létrehozás** gombra.

    ![Azure Functions-futtatókörnyezet betekintő portál új Function alkalmazás][17]

1. Az új Function-alkalmazás a portál bal oldali ablaktábláján jelenik meg.  Válassza a függvények lehetőséget, majd kattintson a portál középső paneljének tetején található **new Function (új függvény** ) elemre.

    ![Azure Functions-futtatókörnyezet előnézeti sablonok][18]

1. Válassza ki az időzítő eseményindító függvényt, a jobb oldali menüben adja meg a függvényt, és módosítsa az ütemtervet `*/5 * * * * *` (ez a cron-kifejezés azt eredményezi, hogy az időzítő függvény öt másodpercenként végrehajtódik), majd kattintson a **Létrehozás** gombra.

    ![Azure Functions-futtatókörnyezet előzetes verzió új időzítő funkciójának konfigurálása][19]

1. A függvény már létre lett hozva.  A Function alkalmazás végrehajtási naplóját a portál alján található **napló** ablaktábla kibontásával tekintheti meg.

    ![Azure Functions-futtatókörnyezet előnézeti függvény végrehajtása][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
