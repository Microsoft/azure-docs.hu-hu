---
title: Linux Azure IoT Edge telepítése Windows | Microsoft Docs
description: Azure IoT Edge windowsos eszközök telepítési útmutatója
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 2799e25dbd84ff07b375c6fa1b103789aae82b49
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538424"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>A Linux rendszerhez készült Azure IoT Edge telepítése és üzembe helyezése Windows-eszközön (előzetes verzió)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Az Azure IoT Edge a futásidejű eszköz vált át IoT Edge eszközre. A futtatás a PC-osztálytól az ipari kiszolgálókra telepíthető az eszközökön. Miután konfigurált egy eszközt az IoT Edge-futtatókörnyezettel, üzembe helyezhet rajta üzleti logikát a felhőből. További tudnivalókért lásd [a Azure IoT Edge és architektúráját.](iot-edge-runtime.md)

Azure IoT Edge windowsos Linux rendszeren lehetővé teszi, hogy windowsos Azure IoT Edge linuxos virtuális gépek használatával használja a windowsos eszközöket. Az Azure IoT Edge linuxos verziója és az összes vele üzembe helyezett Linux-modul a virtuális gépen fut. Innentől a Windows-alkalmazások és -kódok, valamint a IoT Edge és modulok szabadon kommunikálhatnak egymással.

Ez a cikk a Windows-eszközök IoT Edge beállításának lépéseit sorolja fel. Ezek a lépések üzembe helyeznek egy Linux rendszerű virtuális gépet, amely tartalmazza a Windows IoT Edge-eszközön futtatott futásidejű virtuális gépet, majd kiépíti az eszközt IoT Hub eszközidentitásával.

>[!NOTE]
>IoT Edge linuxos windowsos verziója nyilvános [előzetes verzióban érhető el.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>
>Bár IoT Edge windowsos Linuxhoz való használata az ajánlott Azure IoT Edge windowsos környezetben, a Windows-tárolók továbbra is elérhetők. Ha inkább Windows-tárolókat szeretne használni, tekintse meg a Windowshoz Azure IoT Edge telepítésével [és kezelésével kapcsolatos útmutatót.](how-to-install-iot-edge-windows-on-windows.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók érvényes előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) a kezdés előtt. [](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)

* Ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

* Windows-eszköz a következő minimális rendszerkövetelményekkel:

  * Windows 10 1809-es vagy újabb verzió; 17763-as vagy újabb build
  * Professional, Enterprise vagy Server kiadások
  * Minimális szabad memória: 1 GB
  * Minimális szabad lemezterület: 10 GB
  * Ha új üzembe helyezést hoz létre a Windows 10, engedélyezze a Hyper-V-t. További információ: A [Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)telepítése a Windows 10.
  * Ha a Windows Serverrel hoz létre új központi telepítést, győződjön meg arról, hogy telepíti a Hyper-V szerepkört. További információ: A [Hyper-V szerepkör telepítése Windows Serveren.](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  * Ha virtuális gép használatával hoz létre új üzemelő példányokat, győződjön meg arról, hogy megfelelően konfigurálta a beágyazott virtualizálást. További információért tekintse meg a [beágyazott virtualizálási útmutatót.](nested-virtualization.md)

* Hozzáférés Windows Admin Center Azure IoT Edge telepített Windows Admin Center bővítményhez:

   1. Töltse le [a Windows Admin Center telepítőjét.](https://aka.ms/wacdownload)

   1. Futtassa a letöltött telepítőt, és kövesse a telepítővarázsló lépéseit a Windows Admin Center. 

   1. A telepítés után egy támogatott böngészővel nyissa meg a Windows Admin Center. A támogatott böngészők Microsoft Edge (Windows 10, 1709-es vagy újabb verzió), a Google Chrome és az Microsoft Edge Insider.

   1. A tanúsítvány első Windows Admin Center a rendszer arra kéri, hogy válasszon ki egy használni kívánt tanúsítványt. Válassza **Windows Admin Center-ügyfél** tanúsítványként lehetőséget.

   1. Ideje telepíteni a Azure IoT Edge bővítményt. Válassza a jobb felső sarokban található fogaskerék ikont Windows Admin Center irányítópulton.

      ![A beállítások eléréséhez válassza az irányítópult jobb felső részen található fogaskerék ikont.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. A Settings **(Beállítások) menü** Gateway (Átjáró) **menüjében válassza** a **Extensions (Bővítmények) lehetőséget.**

   1. Az Elérhető **bővítmények lapon** keresse meg a Azure IoT Edge **a** bővítmények listájában. Válassza ki, majd a bővítmények listája **felett** válassza a Telepítés parancssort.

   1. A telepítés befejezése után a Telepített bővítmények Azure IoT Edge megjelenik a telepített bővítmények **listájában.**

## <a name="choose-your-provisioning-method"></a>A kiépítési módszer kiválasztása

Azure IoT Edge linuxos windowsos verzió a következő kiépítési módszereket támogatja:

* Manuális üzembe IoT Edge eszköz kapcsolati sztringjére. Ennek a módszernek a használatával regisztrálja az eszközt, és lekéri a kapcsolati sztringet [az Eszköz](how-to-register-device.md)regisztrálása IoT Edge a IoT Hub.
  * Válassza a szimmetrikus kulcsos hitelesítési lehetőséget, mivel az X.509 önaírt tanúsítványokat jelenleg nem támogatja az IoT Edge Linux rendszeren.
* Automatikus kiépítés a Device Provisioning Service (DPS) és szimmetrikus kulcsok használatával. További információ a [dps és szimmetrikus kulcsokkal IoT Edge](how-to-auto-provision-symmetric-keys.md)eszköz létrehozásáról és üzembe IoT Edge létrehozásáról.
* Automatikus kiépítés DPS- és X.509-tanúsítványokkal. További információ a [DS- és X.509-tanúsítványokkal IoT Edge](how-to-auto-provision-x509-certs.md)eszközök létrehozásáról és üzembe IoT Edge létrehozásáról.

A manuális kiépítést egyszerűbb néhány eszközzel kezdeni. A Device Provisioning Service számos eszköz üzembe építéséhez hasznos.

Ha azt tervezi, hogy a DPS-metódusok egyikével kiépíti az eszközt vagy eszközöket, kövesse a fenti hivatkozott megfelelő cikkben leírt lépéseket a DPS-példány létrehozásához, a DPS-példány a IoT Hub-hoz való csatolásához és a DPS-regisztráció létrehozásához. Létrehozhat egyéni *regisztrációt* egyetlen eszközhöz vagy  egy csoportregisztráláshoz egy eszközcsoport számára. A regisztrációs típusokkal kapcsolatos további információkért látogasson el a Azure IoT Hub [Device Provisioning Service alapfogalmait.](../iot-dps/concepts-service.md#enrollment)

## <a name="create-a-new-deployment"></a>Új központi telepítés létrehozása

Hozza létre a linuxos Azure IoT Edge Windows rendszeren a céleszközön.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

A Windows Admin Center lapon, a kapcsolatok listájában megjelenik egy helyi gazdakapcsolat, amely azt a számítógépet képviseli, ahol a Windows Admin Center. A további ön által felügyelt kiszolgálók, számítógépek vagy fürtök is itt fognak ni.

A windowsos Windows Admin Center helyi vagy távoli felügyelt Azure IoT Edge linuxos eszközökre való telepítéséhez és kezeléséhez használhatja az alkalmazásokat. Ebben az útmutatóban a helyi gazdakapcsolat szolgál majd céleszközként a linuxos Azure IoT Edge Windows rendszeren való üzembe helyezéséhez.

Ha a helyi eszköz helyett távoli céleszközre szeretné telepíteni az eszközt, és nem látja a kívánt céleszközt a listában, kövesse az utasításokat az eszköz [hozzáadásához.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Kezdeti Windows Admin Center irányítópult a céleszköz listázva](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Válassza a **Hozzáadás** lehetőséget.

1. Az Erőforrások **hozzáadása vagy létrehozása panelen** keresse meg a Azure IoT Edge **csempét.** Válassza **az Új létrehozása** lehetőséget a linuxos Azure IoT Edge új példányának windowsos eszközön való telepítéséhez.

   Ha már rendelkezik linuxos IoT Edge eszközön futó Windows rendszeren,  a Hozzáadás lehetőséget választva csatlakozhat a meglévő IoT Edge-eszközhöz, és kezelheti a Windows Admin Center.

   ![Kattintson az Új létrehozása Azure IoT Edge csempére a Windows Admin Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Megnyílik **a Create an Azure IoT Edge for Linux on Windows deployment (Linux-alkalmazás létrehozása Windows rendszeren üzemelő példányon)** panel. Az **1.-ben. Első lépések lapon** ellenőrizze, hogy a céleszköz megfelel-e a minimális követelményeknek, majd válassza a **Tovább lehetőséget.**

1. Tekintse át a licencszerződéseket, jelölje be az **Elfogadom jelölőnégyzetet,** majd válassza a **Tovább lehetőséget.**

1. Igény szerint **be-** és kikapcsolhatja a Választható diagnosztikai adatok beállítást.

1. Válassza **a Tovább: Üzembe helyezés lehetőséget.**

   ![Válassza a Tovább: Üzembe helyezés gombot, miután kiválasztotta a választható diagnosztikai adatokat](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. A **2. A** Deploy (Üzembe helyezés) lap Select a target device (Céleszköz **kiválasztása)** lapján kattintson a listában szereplő eszközre annak ellenőrzéséhez, hogy az megfelel-e a minimális követelményeknek. Ha az állapota támogatott, válassza a Tovább **lehetőséget.**

   ![Válassza ki az eszközt annak ellenőrzéséhez, hogy támogatott-e](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. A **2.2 Beállítások lapon** tekintse át a telepítés konfigurációs beállításait. Ha elégedett a beállításokkal, válassza a Tovább **lehetőséget.**

   ![Az üzemelő példány konfigurációs beállításainak áttekintése](./media/how-to-install-iot-edge-on-windows/default-deployment-configuration-settings.png)

   >[!NOTE]
   >Ha Windows rendszerű virtuális gépet használ, javasoljuk, hogy külső kapcsoló helyett egy alapértelmezett kapcsolót használjon annak biztosításához, hogy a telepítésben létrehozott Linux rendszerű virtuális gép ip-címet szerezzen be.
   >
   >Az alapértelmezett kapcsolóval egy belső IP-címet rendel a Linux rendszerű virtuális géphez. Ez a belső IP-cím nem érhető el a Windows rendszerű virtuális gépen kívülről, de a Windows rendszerű virtuális gépre bejelentkezve helyileg is csatlakoztatható.
   >
   >Ha Windows Servert használ, vegye figyelembe, hogy Azure IoT Edge Windows rendszeren a linuxos virtuális gépek nem támogatják automatikusan az alapértelmezett kapcsolót. Helyi Windows Server rendszerű virtuális gépek esetén győződjön meg arról, hogy a Linux rendszerű virtuális gép a külső kapcsolón keresztül tud IP-címet beszerezni. Az Azure-beli Windows Server rendszerű virtuális gépeken állítson be egy belső kapcsolót, mielőtt üzembe helyez IoT Edge Linux rendszeren való üzembe helyezéséhez.

1. A **2.3-as központi telepítés lapon** látható az üzembe helyezés előrehaladása. A teljes folyamat magában foglalja a linuxos Azure IoT Edge windowsos csomag letöltését, a csomag telepítését, a gazdaeszköz konfigurálását és a Linux rendszerű virtuális gép beállítását. A folyamat több percig is eltarthat. A sikeres üzembe helyezésről az alábbiakban olvashat.

   ![A sikeres üzembe helyezés minden lépést egy zöld pipával és egy "Complete" (Kész) címkével jelöl](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Az üzembe helyezés befejezése után készen áll az eszköz üzembe helyezésére. Válassza **a Tovább: Csatlakozás lehetőséget** a **3. lépéshez. Csatlakozás** lap, amely az Azure IoT Edge kezeléséhez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha még IoT Edge a céleszközre, telepítse a windowsos Linuxhoz való IoT Edge alkalmazást.

> [!NOTE]
> A következő PowerShell-folyamat bemutatja, hogyan hozhat létre helyi gazdagép-Azure IoT Edge Linux rendszeren való telepítéséhez. Ha a PowerShell használatával központi telepítést hoz létre egy távoli céleszközön, a Távoli [PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote) használatával kapcsolatot létesíthet egy távoli eszközzel, és távolról futtathatja ezeket a parancsokat az eszközön.

1. Rendszergazda jogú PowerShell-munkamenetben futtassa az alábbi parancsokat a linuxos IoT Edge Windows rendszeren való letöltéséhez.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Telepítse IoT Edge linuxos windowsos alkalmazást az eszközére.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > Egyéni linuxos IoT Edge windowsos telepítéshez és VHDX-könyvtárakhoz az INSTALLDIR="<FULLY_QUALIFIED_PATH>" és a VHDXDIR="<FULLY_QUALIFIED_PATH>" paramétereknek a fenti telepítési parancshoz való hozzáadásával adhat meg.

1. Ahhoz, hogy az üzembe helyezés sikeresen fusson, be kell állítania a végrehajtási szabályzatot a céleszközön, ha még `AllSigned` nem tette meg. Az aktuális végrehajtási szabályzatot rendszergazda jogú PowerShell-parancssorban ellenőrizheti a következővel:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Ha a végrehajtási `local machine` szabályzata nem `AllSigned` , a következő használatával állíthatja be a végrehajtási szabályzatot:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Hozza létre a IoT Edge Linux rendszeren üzemelő példányhoz.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > Ezt a parancsot futtathatja paraméterek nélkül, vagy testreszabhatja az üzembe helyezést paraméterekkel. A linuxos linuxos IoT Edge szkriptreferenciában [Windows PowerShell](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) a paraméterek jelentését és az alapértelmezett értékeket.

1. A licencszerződés elfogadásához írja be az "Y" billentyűt.

1. Az "O" vagy az "R" be- vagy kikapcsolhatja a Választható diagnosztikai adatok be- és kikapcsolása beállítást.  Alább látható egy sikeres üzembe helyezési folyamat.

   ![A sikeres üzembe helyezés "Sikeres üzembe helyezés" üzenetet fog tartalmazni az üzenetek végén](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Az üzembe helyezés befejezése után készen áll az eszköz üzembe helyezésére.

---

Az eszköz kiépítése érdekében kövesse az alábbi hivatkozásokat, hogy a kiválasztott kiépítési módszer szakaszra ugorhat:

* [1. lehetőség: Manuális kiépítés IoT Edge eszköz kapcsolati sztringjével](#option-1-provisioning-manually-using-the-connection-string)
* [2. lehetőség: Automatikus kiépítés a Device Provisioning Service (DPS) és szimmetrikus kulcsok használatával](#option-2-provisioning-via-dps-using-symmetric-keys)
* [3. lehetőség: Automatikus kiépítés DPS- és X.509-tanúsítványokkal](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Az eszköz kiépítése

Válasszon ki egy módszert az eszköz üzembe építéséhez, és kövesse a megfelelő szakaszban található utasításokat. Az eszközök üzembe Windows Admin Center vagy egy emelt szintű PowerShell-munkamenetet is használhat.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>1. lehetőség: Manuális kiépítés a kapcsolati sztring használatával

Ez a szakasz az eszköz manuális üzembe Azure IoT Edge az eszköz kapcsolati sztringjéhez.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Az eszköz **Azure IoT Edge panelen** válassza a Kapcsolati sztring **(manuális)** lehetőséget a kiépítési módszer legördülő menüből.

1. A [Azure Portal](https://ms.portal.azure.com/)lapon lépjen a IoT Edge **lapra** IoT Hub.

1. Kattintson az eszköz eszközazonosítójára. Másolja ki az **Elsődleges kapcsolati sztring** mezőt.

1. Illessze be az eszköz kapcsolati sztring mezőjébe a Windows Admin Center. Ezután válassza a **Kiépítés a kiválasztott módszerrel lehetőséget.**

   ![A kiválasztott metódussal való kiépítés kiválasztása az eszköz kapcsolati sztringének beillesztése után](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. A kiépítés befejezése után válassza a Befejezés **lehetőséget.** A fő irányítópultra visszatér. Most egy új eszközt kell látnia a listában, amelynek típusa `IoT Edge Devices` . Kiválaszthatja a IoT Edge, hogy csatlakozni szeretne hozzá. Az Áttekintés **oldalon megtekintheti** az eszköz IoT Edge **és** IoT Edge **állapotát.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. A [Azure Portal](https://ms.portal.azure.com/)lapon lépjen a IoT Edge **lapra** IoT Hub.

1. Kattintson az eszköz eszközazonosítójára. Másolja ki az **Elsődleges kapcsolati sztring** mezőt.

1. Illessze be a helyőrző szöveget a következő parancsba, és futtassa emelt szintű PowerShell-munkamenetben a céleszközön.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>2. lehetőség: Kiépítés DPS-en keresztül szimmetrikus kulcsokkal

Ez a szakasz az eszköz DPS-sel és szimmetrikus kulcsokkal való automatikus üzembeépítését tartalmazza.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Az **Azure IoT Edge kiépítése panelen** válassza a Szimmetrikus kulcs **(DPS)** lehetőséget a kiépítési módszer legördülő menüből.

1. A [Azure Portal](https://ms.portal.azure.com/)keresse meg a DPS-példányt.

1. Az Áttekintés **lapon** másolja ki az **Azonosító hatóköre értéket.** Illessze be a hatókör-azonosító mezőbe a Windows Admin Center.

1. A **regisztrációk kezelése lap** Azure Portal válassza ki a létrehozott regisztrációt. Másolja az **Elsődleges kulcs értéket** a regisztráció részleteibe. Illessze be a szimmetrikus kulcs mezőbe a Windows Admin Center.

1. Adja meg az eszköz regisztrációs azonosítóját a regisztrációs azonosító mezőben a Windows Admin Center.

1. Válassza **a Kiépítés lehetőséget a kiválasztott módszerrel.**

   ![A szimmetrikus kulcsos kiépítéshez szükséges mezők kitöltése után válassza a kiválasztott módszerrel való kiépítést](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. A kiépítés befejezése után válassza a Befejezés **lehetőséget.** A fő irányítópultra visszatér. Most egy új eszközt kell látnia a listában, amelynek típusa `IoT Edge Devices` . Kiválaszthatja a IoT Edge, hogy csatlakozni szeretne hozzá. Az Áttekintés **oldalon megtekintheti** az eszköz IoT Edge **és** IoT Edge **állapotát.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Másolja az alábbi parancsot egy szövegszerkesztőbe. Cserélje le a helyőrző szöveget a részletes információkra.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. A [Azure Portal](https://ms.portal.azure.com/)keresse meg a DPS-példányt.

1. Az Áttekintés **lapon** másolja ki az **Azonosító hatóköre értéket.** Illessze be a megfelelő helyőrző szövegre a parancsban.

1. A **regisztrációk kezelése lap** Azure Portal válassza ki a létrehozott regisztrációt. Másolja az **Elsődleges kulcs értékét** a regisztráció részleteibe. Illessze be a megfelelő helyőrző szövegre a parancsban.

1. Adja meg az eszköz regisztrációs azonosítóját a parancs megfelelő helyőrző szövegének cseréjéhez.

1. Futtassa a parancsot emelt szintű PowerShell-munkamenetben a céleszközön.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>3. lehetőség: Kiépítés DPS-en keresztül X.509-tanúsítványokkal

Ez a szakasz az eszköz DPS- és X.509-tanúsítványok használatával való automatikus üzembevét foglalja magában.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. A Azure IoT Edge **kiépítése** panelen válassza az **X.509-tanúsítvány (DPS)** lehetőséget a kiépítési módszer legördülő menüből.

1. A [Azure Portal](https://ms.portal.azure.com/)keresse meg a DPS-példányt.

1. Az Áttekintés **lapon** másolja ki az **Azonosító hatóköre** értéket. Illessze be a hatókör-azonosító mezőbe a Windows Admin Center.

1. Adja meg az eszköz regisztrációs azonosítóját a regisztrációs azonosító mezőben a Windows Admin Center.

1. Töltse fel a tanúsítvány- és titkoskulcs-fájlokat.

1. Válassza **a Kiépítés lehetőséget a kiválasztott módszerrel.**

   ![Válassza a kiépítést a kiválasztott módszerrel, miután kitöltötte az X.509-tanúsítvány építéshez szükséges mezőket](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. A kiépítés befejezése után válassza a Befejezés **lehetőséget.** A fő irányítópultra visszatér. Most egy új eszközt kell látnia a listában, amelynek típusa `IoT Edge Devices` . Kiválaszthatja a IoT Edge, hogy csatlakozni szeretne hozzá. Az Áttekintés **oldalon** megtekintheti az eszköz IoT Edge **és IoT Edge** **állapotát.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Másolja az alábbi parancsot egy szövegszerkesztőbe. Cserélje le a helyőrző szövegét a részletes információkra.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. A [Azure Portal](https://ms.portal.azure.com/)keresse meg a DPS-példányt.

1. Az Áttekintés **lapon** másolja ki az **Azonosító hatóköre** értéket. Illessze be a megfelelő helyőrző szövegre a parancsban.

1. Adja meg az eszköz regisztrációs azonosítóját a parancs megfelelő helyőrző szövegének cseréjéhez.

1. Cserélje le a megfelelő helyőrző szöveget a tanúsítványfájl abszolút forrásútvonalával.

1. Cserélje le a megfelelő helyőrző szöveget a titkos kulcsfájl abszolút forrásútvonalával.

1. Futtassa a parancsot emelt szintű PowerShell-munkamenetben a céleszközön.

---

## <a name="verify-successful-configuration"></a>A sikeres konfigurálás ellenőrzése

Győződjön meg IoT Edge, hogy a Windows rendszeren telepítette és konfigurálta a Linuxhoz való IoT Edge eszközön.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Válassza ki IoT Edge eszközét a csatlakoztatott eszközök listájából a Windows Admin Center a csatlakozáshoz.

1. Az eszközáttekintő lapon megjelenik néhány információ az eszközről:

    1. A **IoT Edge modullista szakaszban** az eszközön futó modulok láthatóak. Amikor a IoT Edge szolgáltatás először indul el, csak az **edgeAgent modulnak kell futnia.** Az edgeAgent modul alapértelmezés szerint fut, és segít telepíteni és elindítani az eszközön üzembe helyezett további modulokat.
    1. A **IoT Edge állapot szakasz** a szolgáltatás állapotát jeleníti meg, és aktív (futó) jelentést kell **jelentenie.**

1. Ha az IoT Edge szolgáltatás hibaelhárítására van szüksége, használja az eszközoldalon található Parancshéj eszközt az ssh (Secure **Shell)** a virtuális gépre való ssh-jának futtatásához, és futtassa a Linux-parancsokat.

    1. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

       ```bash
       journalctl -u iotedge
       ```

    2. Az eszközzel ellenőrizheti az eszköz konfigurációját és `check` kapcsolati állapotát.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Jelentkezzen be a linuxos IoT Edge windowsos virtuális gépen a PowerShell-munkamenetben a következő paranccsal:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

   >[!NOTE]
   >A virtuális géphez SSH-kapcsolathoz csak az azt létrehozó felhasználónak engedélyezett fiók.

1. Miután bejelentkezett, a következő Linux-paranccsal ellenőrizheti a IoT Edge modulok futtatásának listáját:

   ```bash
   iotedge list
   ```

1. Ha el kell hárítani a IoT Edge, használja a következő Linux-parancsokat.

    1. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

       ```bash
       journalctl -u iotedge
       ```

    2. Az eszközzel `check` ellenőrizheti az eszköz konfigurációját és kapcsolati állapotát.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Következő lépések

* A [modulokat IoT Edge modulokkal folytatva](how-to-deploy-modules-portal.md) megtudhatja, hogyan helyezhet üzembe modulokat az eszközén.
* Megtudhatja, hogyan [kezelheti a](how-to-manage-device-certificates.md) windowsos virtuális gépeken IoT Edge linuxos virtuális gépek tanúsítványait, és hogyan továbbítja a fájlokat a gazda operációs rendszerről a Linux rendszerű virtuális gépre.
* Ismerje meg, [hogyan konfigurálhatja a IoT Edge, hogy proxykiszolgálón keresztül kommunikáljanak.](how-to-configure-proxy-support.md)
