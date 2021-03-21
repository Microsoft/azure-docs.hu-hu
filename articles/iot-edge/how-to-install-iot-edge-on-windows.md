---
title: A Linux rendszerhez készült Azure IoT Edge telepítése Windows rendszeren | Microsoft Docs
description: Azure IoT Edge telepítési utasítások Windows-eszközökön
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 6361a9c337f5d3897cf15d4739b0410d3e820e57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600938"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>A Linux rendszerhez készült Azure IoT Edge telepítése és üzembe helyezése Windows-eszközön (előzetes verzió)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Az Azure IoT Edge futtatókörnyezet az eszköz IoT Edge eszközre való bekapcsolása. A futtatókörnyezet a PC osztályból az ipari kiszolgálókra is telepíthető. Miután konfigurált egy eszközt az IoT Edge-futtatókörnyezettel, üzembe helyezhet rajta üzleti logikát a felhőből. További információ: [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md).

A Windows rendszerhez készült linuxos Azure IoT Edge lehetővé teszi, hogy a Linux rendszerű virtuális gépek használatával Azure IoT Edge használjon Windows-eszközökön. A Azure IoT Edge Linux-verziója és a virtuális gépen futtatott Linux-modulok. Innentől kezdve a Windows-alkalmazások és-kódok, valamint a IoT Edge futtatókörnyezet és modulok szabadon kommunikálhatnak egymással.

Ez a cikk a IoT Edge Windows-eszközökön való beállításának lépéseit sorolja fel. Ezek a lépések olyan linuxos virtuális gépet telepítenek, amely tartalmazza a Windows-eszközön futtatni kívánt IoT Edge futtatókörnyezetet, majd kiépíti az eszközt a IoT Hub eszköz identitásával.

>[!NOTE]
>A Windows rendszerhez készült Linux IoT Edge [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.
>
>Habár a Windows Linux rendszerhez készült IoT Edge a Windows-környezetben való Azure IoT Edge ajánlott használata, a Windows-tárolók továbbra is elérhetők. Ha a Windows-tárolókat szeretné használni, tekintse meg a Windows rendszerű [Azure IoT Edge telepítésének és felügyeletének](how-to-install-iot-edge-windows-on-windows.md)útmutatója című témakört.

## <a name="prerequisites"></a>Előfeltételek

* Egy érvényes előfizetéssel rendelkező Azure-fiók. Ha nem rendelkezik Azure- [előfizetéssel](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

* Ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

* Egy Windows-eszköz a következő minimális rendszerkövetelményekkel:

  * Windows 10 1809-es vagy újabb verzió; Build 17763 vagy újabb
  * Professional, Enterprise vagy Server kiadás
  * Minimális RAM: 4 GB (ajánlott 8 GB)
  * Minimális tárterület: 10 GB
  * Ha Windows 10 rendszerű új központi telepítést hoz létre, győződjön meg arról, hogy engedélyezi a Hyper-V használatát. További információ: a [Hyper-V telepítése Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v.md)rendszeren.
  * Ha a Windows Serverrel új központi telepítést hoz létre, akkor győződjön meg arról, hogy telepíti a Hyper-V szerepkört. További információ: [a Hyper-V szerepkör telepítése a Windows Serveren](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server.md).
  * Ha virtuális géppel hoz létre új központi telepítést, győződjön meg arról, hogy megfelelően konfigurálta a beágyazott virtualizálás szolgáltatást. További információ: [beágyazott virtualizációs](nested-virtualization.md) útmutató.

* Hozzáférés a Windows felügyeleti központhoz a Windows felügyeleti központhoz telepített Azure IoT Edge bővítménnyel:

   1. Töltse le a [Windows felügyeleti központ telepítőjét](https://aka.ms/wacdownload).

   1. Futtassa a letöltött telepítőt, és kövesse a telepítés varázsló utasításait a Windows felügyeleti központ telepítéséhez. A telepítés után nyissa meg a Windows felügyeleti központot.

   1. A Windows felügyeleti központ első használatakor a rendszer kérni fogja a használni kívánt tanúsítvány kiválasztását. Tanúsítványként válassza a **Windows felügyeleti központ ügyfele** lehetőséget.

   1. Itt az ideje, hogy telepíti a Azure IoT Edge bővítményt. Válassza a fogaskerék ikont a Windows felügyeleti központ irányítópultjának jobb felső részén.

      ![A beállítások eléréséhez kattintson az irányítópult jobb felső sarkában található fogaskerék ikonra.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. A **Beállítások** menü **átjáró** területén válassza a **bővítmények** lehetőséget.

   1. Válassza a **csatornák** fület, és válassza a **Hozzáadás** lehetőséget.

   1. Adja meg https://aka.ms/wac-insiders-feed a szövegmezőt, és válassza a **Hozzáadás** lehetőséget.

   1. A hírcsatorna hozzáadása után navigáljon az **elérhető bővítmények** lapra. A bővítmények listájának frissítése eltarthat egy kis ideig.

   1. Az **elérhető bővítmények** lapon keresse meg **Azure IoT Edge** a bővítmények listájában. Válassza ki, majd a bővítmények listáján válassza a **telepítési** kérést.

   1. A telepítés befejezése után Azure IoT Edge jelenik meg a telepített bővítmények listájában a **telepített bővítmények** lapon.

## <a name="choose-your-provisioning-method"></a>Kiépítési módszer kiválasztása

A Windows rendszerhez készült Linux-Azure IoT Edge a következő üzembe helyezési módszereket támogatja:

* Manuális kiépítés a IoT Edge-eszközhöz tartozó kapcsolatok karakterláncának használatával. Ennek a módszernek a használatához regisztrálja az eszközt, és kérje le a kapcsolódási karakterláncot a [IoT Edge eszköz regisztrálása a IoT hub-ban](how-to-register-device.md)című témakörben leírtak alapján.
  * Válassza a szimmetrikus kulcsos hitelesítés lehetőséget, mivel az X. 509 önaláírt tanúsítványokat jelenleg nem támogatja az IoT Edge for Linux Windows rendszeren.
* Automatikus kiépítés a Device kiépítési szolgáltatás (DPS) és a szimmetrikus kulcsok használatával. További információ [az IoT Edge-eszközök létrehozásáról és üzembe helyezéséről DPS és szimmetrikus kulcsokkal](how-to-auto-provision-symmetric-keys.md).
* Automatikus kiépítés a DPS és az X. 509 tanúsítvánnyal. További információ [az IoT Edge-eszközök létrehozásáról és üzembe helyezéséről a DPS és az X. 509 tanúsítvánnyal](how-to-auto-provision-x509-certs.md).

A manuális kiépítés könnyebben megkezdhető néhány eszköz megkezdéséhez. A Device kiépítési szolgáltatás számos eszköz kiépítés esetén hasznos.

Ha azt tervezi, hogy az eszköz vagy az eszközök kiépítéséhez az egyik DPS-metódust használja, kövesse a fenti csatolt cikkben található lépéseket a DPS-példány létrehozásához, a DPS-példány összekapcsolásához a IoT Hubhoz, és hozzon létre egy DPS-regisztrációt. *Egyéni regisztrációt* is létrehozhat egyetlen eszközhöz vagy *csoportos regisztrációhoz* az eszközök egy csoportjára. A regisztrációs típusokkal kapcsolatos további információkért tekintse meg az [Azure IoT hub Device Provisioning Service fogalmakat](../iot-dps/concepts-service.md#enrollment).

## <a name="create-a-new-deployment"></a>Új központi telepítés létrehozása

Hozza létre Azure IoT Edge Linux rendszerhez készült központi telepítését a célszámítógépen.

# <a name="windows-admin-center"></a>[Windows felügyeleti központ](#tab/windowsadmincenter)

A Windows felügyeleti központ kezdőlapján, a kapcsolatok listájában megjelenik egy helyi gazdagép-kapcsolat, amely a Windows felügyeleti központot futtató számítógépet jelképezi. Az Ön által kezelt további kiszolgálók, számítógépek vagy fürtök itt is megjelennek.

A Windows felügyeleti központ használatával Windows rendszeren telepítheti és kezelheti a Linux rendszerhez készült Azure IoT Edge a helyi eszközön vagy a távoli felügyelt eszközökön. Ebben az útmutatóban a helyi gazdagép-kapcsolatok célként szolgáló eszközként szolgál a Linux rendszerhez készült Azure IoT Edge telepítéséhez Windows rendszeren.

Ha a helyi eszköz helyett egy távoli céleszköz kíván üzembe helyezni, és nem látja a kívánt céleszköz a listában, kövesse az [eszköz hozzáadásához szükséges utasításokat](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Kezdeti Windows felügyeleti központ irányítópultja a felsorolt eszközzel](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Válassza a **Hozzáadás** lehetőséget.

1. Az **erőforrások hozzáadása vagy létrehozása** panelen keresse meg a **Azure IoT Edge** csempét. Válassza az **új létrehozása** lehetőséget a Linux rendszerhez készült Azure IoT Edge új példányának telepítéséhez az eszközön.

   Ha már rendelkezik IoT Edge Linux rendszeren az eszközön futó Windows rendszeren, a **Hozzáadás** gombra kattintva csatlakozhat ehhez a meglévő IoT Edge eszközhöz, és felügyelheti azt a Windows felügyeleti központtal.

   ![Válassza az új létrehozása Azure IoT Edge csempén a Windows felügyeleti központban lehetőséget.](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Megnyílik az **Azure IoT Edge létrehozása Linux rendszerhez a központi Windows-telepítési** panelen. Az **1. Első lépések** lapon ellenőrizze, hogy a célként megadott eszköz megfelel-e a minimális követelményeknek, majd kattintson a **Tovább gombra**.

1. Tekintse át a licencfeltételeket, jelölje be az **Elfogadom** lehetőséget, majd kattintson a **Tovább gombra**.

1. A **választható diagnosztikai adatait** be-és kikapcsolhatja a beállítástól függően.

1. Válassza a **Next (tovább): telepítés** lehetőséget.

   ![Jelölje be a Next: Deploy (telepítés) gombot, miután a választható diagnosztikai adatait a kívánt beállításokra alkalmazza](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. A **2. A központi telepítés** lap **cél eszköz kiválasztása** területén kattintson a felsorolt eszközre, és ellenőrizze, hogy az megfelel-e a minimális követelményeknek. Ha az állapota támogatottként van megerősítve, válassza a **tovább** lehetőséget.

   ![Válassza ki az eszközt, hogy az ellenőrizze, hogy támogatott-e](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Fogadja el az alapértelmezett beállításokat a **2,2 beállítások** lapon.

1. Az 2,3-es **telepítés** lapon megtekintheti az üzemelő példány állapotát. A teljes folyamat magában foglalja a Linux Azure IoT Edge letöltését a Windows-csomagban, a csomag telepítését, a gazdagép eszközének konfigurálását és a linuxos virtuális gép beállítását. A folyamat végrehajtása több percet is igénybe vehet. A sikeres üzembe helyezés az alábbi képen látható.

   ![A sikeres üzembe helyezés minden lépést zöld pipa jellel és "Complete" címkével fog látni](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

A telepítés befejezése után készen áll az eszköz kiépítésére. Válassza a Next (tovább) lehetőséget **: kapcsolódjon** a **3. A csatlakoztatás** lap, amely kezeli Azure IoT Edge eszköz kiépítési folyamatát.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha még nem tette meg, telepítse a Windows rendszerhez készült Linux-IoT Edget a kívánt eszközre.

> [!NOTE]
> A következő PowerShell-folyamat azt ismerteti, hogyan hozható létre a Windows rendszerű Linux rendszerhez készült Azure IoT Edge helyi központi telepítése. Ha a PowerShell használatával szeretne üzembe helyezést létrehozni egy távoli céleszköz-eszközön, a [távoli PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote) használatával kapcsolatot létesíthet egy távoli eszközzel, és távolról is futtathatja ezeket a parancsokat az adott eszközön.

1. Egy emelt szintű PowerShell-munkamenetben futtassa a következő parancsok mindegyikét a Linux rendszerhez készült IoT Edge letöltéséhez Windows rendszeren.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Telepítse a Linux rendszerhez készült IoT Edget az eszközön.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > A Linux rendszerhez készült egyéni IoT Edge a Windows-telepítési és VHDX-címtárakban a INSTALLDIR = "<FULLY_QUALIFIED_PATH>" és a VHDXDIR = "<FULLY_QUALIFIED_PATH>" paramétereknek a fenti telepítési parancshoz való hozzáadásával adhatók meg.

1. Ahhoz, hogy a központi telepítés sikeresen fusson, a cél eszközön be kell állítania a végrehajtási szabályzatot, `AllSigned` Ha az még nem létezik. Az aktuális végrehajtási szabályzatot egy emelt szintű PowerShell-parancssorban tekintheti meg a következőket használva:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Ha a végrehajtási házirendje `local machine` nem `AllSigned` , a végrehajtási házirendet a alábbiak szerint állíthatja be:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Hozza létre a Linux rendszerhez készült IoT Edge a Windows központi telepítésben.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > Ezt a parancsot paraméterek nélkül is futtathatja, vagy opcionálisan testreszabhatja az üzembe helyezést paraméterekkel. A jelentések megjelenítéséhez tekintse meg [a Linux rendszerhez készült IoT Edge a Windows PowerShell parancsfájl-hivatkozását](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) .

1. A licencfeltételek elfogadásához írja be az "Y" kifejezést.

1. Adja meg az "O" vagy az "R" értéket a **választható diagnosztikai** elemek be-és kikapcsolásához, a beállítástól függően. A sikeres üzembe helyezés az alábbi képen látható.

   ![A sikeres üzembe helyezés az üzenetek végén az "üzembe helyezés sikeres" üzenetet fogja mondani](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

A telepítés befejezése után készen áll az eszköz kiépítésére.

---

Az eszköz kiépítéséhez kövesse az alábbi hivatkozásokat a kiválasztott létesítési módszerhez tartozó szakaszra való ugráshoz:

* [1. lehetőség: manuális kiépítés a IoT Edge eszköz csatlakoztatási sztringje használatával](#option-1-provisioning-manually-using-the-connection-string)
* [2. lehetőség: automatikus kiépítés a Device kiépítési szolgáltatás (DPS) és a szimmetrikus kulcsok használatával](#option-2-provisioning-via-dps-using-symmetric-keys)
* [3. lehetőség: automatikus kiépítés a DPS és az X. 509 tanúsítvánnyal](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Az eszköz kiépítése

Válassza ki az eszköz kiépítési módszerét, és kövesse a megfelelő szakasz utasításait. Az eszközök kiépítéséhez a Windows felügyeleti központot vagy egy emelt szintű PowerShell-munkamenetet is használhat.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>1. lehetőség: manuális kiépítés a kapcsolatok karakterláncának használatával

Ez a szakasz az eszköz a Azure IoT Edge eszköz kapcsolati karakterláncával történő manuális kiépítési folyamatát ismerteti.

# <a name="windows-admin-center"></a>[Windows felügyeleti központ](#tab/windowsadmincenter)

1. Az **Azure IoT Edge eszköz kiépítés** ablaktábláján válassza a létesítési módszer legördülő menüből a **kapcsolatok karakterlánca (manuális)** lehetőséget.

1. A [Azure Portal](https://ms.portal.azure.com/)navigáljon a IoT hub **IoT Edge** lapjára.

1. Kattintson az eszköz AZONOSÍTÓJÁHOZ. Másolja az **elsődleges kapcsolatok karakterlánca** mezőt.

1. Illessze be a Windows felügyeleti központ eszköz-kapcsolatok karakterlánc mezőjébe. Ezután válassza **a kiépítés lehetőséget a kiválasztott módszerrel**.

   ![Válassza ki a kiépítés lehetőséget a kiválasztott módszerrel az eszköz csatlakoztatási karakterláncának beillesztése után](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. A kiépítés befejezése után válassza a **Befejezés** lehetőséget. Vissza fog térni a fő irányítópultra. Most meg kell jelennie egy új eszköz listájának, amelynek típusa a következő: `IoT Edge Devices` . A IoT Edge eszközhöz való kapcsolódáshoz válassza ki az eszközt. Az **Áttekintés** lapon megtekintheti az eszköz IoT Edge- **modulok listáját** és **IoT Edge állapotát** .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. A [Azure Portal](https://ms.portal.azure.com/)navigáljon a IoT hub **IoT Edge** lapjára.

1. Kattintson az eszköz AZONOSÍTÓJÁHOZ. Másolja az **elsődleges kapcsolatok karakterlánca** mezőt.

1. Illessze be a helyőrző szövegét a következő parancsba, és futtassa egy emelt szintű PowerShell-munkamenetben a megcélzott eszközön.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>2. lehetőség: kiépítés a DPS-n keresztül szimmetrikus kulcsok használatával

Ez a szakasz az eszköznek a DPS és a szimmetrikus kulcsok használatával történő automatikus kiépítési folyamatát ismerteti.

# <a name="windows-admin-center"></a>[Windows felügyeleti központ](#tab/windowsadmincenter)

1. Az **Azure IoT Edge eszköz kiépítés** ablaktábláján válassza a **szimmetrikus kulcs (DPS)** lehetőséget a létesítési módszer legördülő listából.

1. A [Azure Portal](https://ms.portal.azure.com/)navigáljon a DPS-példányhoz.

1. Az **Áttekintés** lapon másolja az **azonosító hatókörének** értékét. Illessze be a Windows felügyeleti központ hatókör-azonosító mezőjébe.

1. A Azure Portal **regisztrációk kezelése** lapján válassza ki a létrehozott beléptetést. Másolja az **elsődleges kulcs** értékét a beléptetés részleteibe. Illessze be a szimmetrikus kulcs mezőbe a Windows felügyeleti központban.

1. Adja meg az eszköz regisztrációs AZONOSÍTÓját a Windows felügyeleti központ regisztrációs azonosító mezőjében.

1. Válassza **a kiépítés lehetőséget a kiválasztott módszerrel**.

   ![Válassza ki a kiépítés lehetőséget a kiválasztott módszerrel a szimmetrikus kulcs kiépítés kötelező mezőinek kitöltése után](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. A kiépítés befejezése után válassza a **Befejezés** lehetőséget. Vissza fog térni a fő irányítópultra. Most meg kell jelennie egy új eszköz listájának, amelynek típusa a következő: `IoT Edge Devices` . A IoT Edge eszközhöz való kapcsolódáshoz válassza ki az eszközt. Az **Áttekintés** lapon megtekintheti az eszköz IoT Edge- **modulok listáját** és **IoT Edge állapotát** .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Másolja az alábbi parancsot egy szövegszerkesztőbe. Cserélje le a helyőrző szövegét a részletes adatokra.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. A [Azure Portal](https://ms.portal.azure.com/)navigáljon a DPS-példányhoz.

1. Az **Áttekintés** lapon másolja az **azonosító hatókörének** értékét. Illessze be a megfelelő helyőrző szöveg fölé a parancsban.

1. A Azure Portal **regisztrációk kezelése** lapján válassza ki a létrehozott beléptetést. Másolja az **elsődleges kulcs** értékét a beléptetés részleteibe. Illessze be a megfelelő helyőrző szöveg fölé a parancsban.

1. Adja meg az eszköz regisztrációs AZONOSÍTÓját, hogy lecserélje a megfelelő helyőrző szöveget a parancsban.

1. Futtassa a parancsot egy emelt szintű PowerShell-munkamenetben a célként megadott eszközön.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>3. lehetőség: kiépítés a DPS használatával X. 509 tanúsítvánnyal

Ez a szakasz az eszköznek a DPS és az X. 509 tanúsítvánnyal történő automatikus kiépítési folyamatát ismerteti.

# <a name="windows-admin-center"></a>[Windows felügyeleti központ](#tab/windowsadmincenter)

1. Az **Azure IoT Edge eszköz kiépítés** ablaktábláján válassza az **X. 509 tanúsítvány (DPS)** lehetőséget a létesítési módszer legördülő listából.

1. A [Azure Portal](https://ms.portal.azure.com/)navigáljon a DPS-példányhoz.

1. Az **Áttekintés** lapon másolja az **azonosító hatókörének** értékét. Illessze be a Windows felügyeleti központ hatókör-azonosító mezőjébe.

1. Adja meg az eszköz regisztrációs AZONOSÍTÓját a Windows felügyeleti központ regisztrációs azonosító mezőjében.

1. Töltse fel a tanúsítvány és a titkos kulcs fájljait.

1. Válassza **a kiépítés lehetőséget a kiválasztott módszerrel**.

   ![Az X. 509 tanúsítvány kiépítés kötelező mezőinek kitöltése után válassza a kiépítés a kiválasztott módszerrel lehetőséget.](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. A kiépítés befejezése után válassza a **Befejezés** lehetőséget. Vissza fog térni a fő irányítópultra. Most meg kell jelennie egy új eszköz listájának, amelynek típusa a következő: `IoT Edge Devices` . A IoT Edge eszközhöz való kapcsolódáshoz válassza ki az eszközt. Az **Áttekintés** lapon megtekintheti az eszköz IoT Edge- **modulok listáját** és **IoT Edge állapotát** .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Másolja az alábbi parancsot egy szövegszerkesztőbe. Cserélje le a helyőrző szövegét a részletes adatokra.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. A [Azure Portal](https://ms.portal.azure.com/)navigáljon a DPS-példányhoz.

1. Az **Áttekintés** lapon másolja az **azonosító hatókörének** értékét. Illessze be a megfelelő helyőrző szöveg fölé a parancsban.

1. Adja meg az eszköz regisztrációs AZONOSÍTÓját, hogy lecserélje a megfelelő helyőrző szöveget a parancsban.

1. Cserélje le a megfelelő helyőrző szöveget a tanúsítványfájl abszolút forrásának elérési útjára.

1. Cserélje le a megfelelő helyőrző szöveget a titkos kulcsfájl abszolút forrásának elérési útjára.

1. Futtassa a parancsot egy emelt szintű PowerShell-munkamenetben a célként megadott eszközön.

---

## <a name="verify-successful-configuration"></a>Sikeres konfiguráció ellenőrzése

Ellenőrizze, hogy a Windows Linux rendszerhez készült IoT Edge telepítése és konfigurálása sikeres volt-e a IoT Edge eszközön.

# <a name="windows-admin-center"></a>[Windows felügyeleti központ](#tab/windowsadmincenter)

1. A csatlakozáshoz válassza ki a IoT Edge eszközt a csatlakoztatott eszközök listájáról a Windows felügyeleti központban.

1. Az eszköz áttekintése oldalon az eszközre vonatkozó információk láthatók:

    1. A **IoT Edge modul listája** szakasz a futó modulokat mutatja az eszközön. Ha a IoT Edge szolgáltatás első alkalommal indul el, csak a **edgeAgent** modult kell futtatnia. A edgeAgent modul alapértelmezés szerint fut, és az eszközre telepített további modulok telepítését és elindítását is lehetővé teszi.
    1. A **IoT Edge állapota** szakasz a szolgáltatás állapotát jeleníti meg, és a jelentésnek aktívnak kell lennie **(fut)**.

1. Ha a IoT Edge-szolgáltatást kell elhárítani, használja az eszköz lapján az SSH (Secure Shell) **parancsot** a virtuális gépre, és futtassa a Linux-parancsokat.

    1. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

       ```bash
       journalctl -u iotedge
       ```

    2. Az eszköz használatával `check` ellenőrizze az eszköz konfigurációját és kapcsolódási állapotát.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Jelentkezzen be a IoT Edge for Linux rendszerű virtuális gépen a következő parancs használatával a PowerShell-munkamenetben:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

1. Miután bejelentkezett, a következő Linux-paranccsal tekintheti meg a futó IoT Edge modulok listáját:

   ```bash
   iotedge list
   ```

1. Ha a IoT Edge-szolgáltatást kell elhárítani, használja a következő Linux-parancsokat.

    1. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

       ```bash
       journalctl -u iotedge
       ```

    2. Az eszköz használatával `check` ellenőrizze az eszköz konfigurációját és kapcsolódási állapotát.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Következő lépések

Folytassa a [IoT Edge-modulok üzembe](how-to-deploy-modules-portal.md) helyezésével, hogy megtudja, hogyan helyezhet üzembe modulokat az eszközön.
