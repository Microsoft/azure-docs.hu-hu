---
title: SCSM összekötése IT-szolgáltatásmenedzsmenti csatoló
description: Ez a cikk azt ismerteti, hogyan SCSM a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) Azure Monitor a ITSM-munkaelemek központilag figyeléséhez és kezeléséhez.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 907a78b15cca4718308f79bc6be2e6258bc04d19
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492671"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>System Center Service Manager összekötése IT-szolgáltatásmenedzsmenti csatoló

Ez a cikk azt ismerteti, hogyan konfigurálható a System Center Service Manager-példány és a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) közötti kapcsolat a munkaelemek központilag felügyelhető Log Analyticsban.

A következő szakaszokban részletesen ismertetjük, hogyan csatlakoztathatók a System Center Service Manager-termékek a ITSMC az Azure-ban.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

- A ITSMC telepítve van. További információ: [a it-szolgáltatásmenedzsmenti csatoló megoldás hozzáadása](./itsmc-definition.md).
- A Service Manager webalkalmazás (Web App) telepítése és konfigurálása történik. A webalkalmazással kapcsolatos információk [itt](#create-and-deploy-service-manager-web-app-service)találhatóak.
- A hibrid kapcsolatok létrehozása és konfigurálása megtörtént. További információ: [a hibrid kapcsolatok konfigurálása](#configure-the-hybrid-connection).
- A Service Manager támogatott verziói: 2012 R2 vagy 2016.
- Felhasználói szerepkör:  [speciális kezelő](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- A Azure Monitor által elküldett riasztások jelenleg System Center Service Manager incidensekben hozhatók létre.

> [!NOTE]
> - ITSM-csatoló csak felhőalapú ServiceNow-példányokhoz tud csatlakozni. A helyszíni ServiceNow-példányok jelenleg nem támogatottak.
> - Ha egyéni [sablonokat](./itsmc-definition.md#define-a-template) kíván használni a SCSM sablonban található "ProjectionType" paraméterrel, a "IncidentManagement! System. Munkatétel. incidens. ProjectionType "

## <a name="connection-procedure"></a>Csatlakoztatási eljárás

A System Center Service Manager-példány ITSMC való összekapcsolásához kövesse az alábbi eljárást:

1. A Azure Portalban lépjen a **minden erőforrás** elemre, és keresse meg a **ügyfélszolgálati (YourWorkspaceName)**

2. A **munkaterület-ADATforrások** területen kattintson az **ITSM-kapcsolatok** elemre.

    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás** gombra.

4. Adja meg az adatokat az alábbi táblázatban leírtak szerint, majd kattintson az **OK** gombra a kapcsolódás létrehozásához.

> [!NOTE]
> Ezeket a paramétereket kötelező megadni.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be annak a System Center Service Manager-példánynak a nevét, amelyhez csatlakozni szeretne a ITSMC.  Ezt a nevet később, a munkaelemek ebben a példányban való konfigurálásakor, illetve a részletes log Analytics megtekintésekor kell használni. |
| **Partner típusa**   | Válassza a **System Center Service Manager** lehetőséget. |
| **Kiszolgáló URL-címe**   | Írja be a Service Manager webalkalmazás URL-címét. Service Manager webalkalmazással kapcsolatos további információkért [tekintse meg a](#create-and-deploy-service-manager-web-app-service)következőt:.
| **Ügyfél-azonosító**   | Írja be a generált ügyfél-azonosítót (az automatikus parancsfájl használatával) a webalkalmazás hitelesítéséhez. Az automatizált parancsfájlról további információt [itt talál.](./itsmc-service-manager-script.md)|
| **Ügyfél titka**   | Adja meg az AZONOSÍTÓhoz generált ügyfél-titkot.   |
| **Adatszinkronizálás**   | Válassza ki azokat a Service Manager munkaelemeket, amelyeket szinkronizálni szeretne a ITSMC-on keresztül.  Ezeket a munkaelemeket a rendszer a Log Analyticsba importálja. **Beállítások:**  Incidensek, módosítási kérelmek.|
| **Adatszinkronizálási hatókör** | Adja meg az elmúlt napok számát, amelyből az adatok származnak. **Maximális korlát**: 120 nap. |
| **Új konfigurációs elemek létrehozása a ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha a ITSM termékben szeretné létrehozni a konfigurációs elemeket. Ha bejelöli ezt a beállítást, a Log Analytics az érintett CIs-t konfigurációs elemekként (nem létező CIs esetén) hozza létre a támogatott ITSM-rendszeren. **Alapértelmezett**: letiltva. |

![Service Manager-kapcsolatok](media/itsmc-connections/service-manager-connection.png)

**Sikeres csatlakozás és szinkronizálás**:

- A Service Managerból kiválasztott munkaelemek importálása az Azure **log Analyticsba történik.** A munkaelemek összegzését a IT-szolgáltatásmenedzsmenti csatoló csempén tekintheti meg.

- Létrehozhat incidenseket Log Analytics riasztásokból, illetve a naplófájlokból, illetve az Azure-riasztásokból ebben a Service Manager-példányban.

További információ: [ITSM-munkaelemek létrehozása az Azure-riasztásokból](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager Web App Service létrehozása és üzembe helyezése

A helyszíni Service Manager Azure-beli ITSMC való összekapcsolásához a Microsoft létrehozott egy Service Manager webalkalmazást a GitHubon.

A Service Manager ITSM-webalkalmazásának beállításához tegye a következőket:

- **Telepítse a webalkalmazást** – telepítse a webalkalmazást, állítsa be a tulajdonságokat, és végezze el a hitelesítést az Azure ad szolgáltatásban. A webalkalmazást a Microsoft által biztosított [automatikus parancsfájl](./itsmc-service-manager-script.md) használatával helyezheti üzembe.
- **A hibrid kapcsolatok konfigurálása**  -  [Konfigurálja manuálisan a](#configure-the-hybrid-connection)-t.

### <a name="deploy-the-web-app"></a>A webalkalmazás üzembe helyezése
Az automatizált [parancsfájllal](./itsmc-service-manager-script.md) telepítse a webalkalmazást, állítsa be a tulajdonságokat, és végezze el a hitelesítést az Azure ad-vel.

Futtassa a szkriptet a következő szükséges részletek megadásával:

- Azure-előfizetés részletei
- Erőforráscsoport neve
- Hely
- Service Manager kiszolgáló adatai (kiszolgáló neve, tartomány, Felhasználónév és jelszó)
- A webalkalmazás helynév-előtagja
- ServiceBus-névtér.

A parancsfájl a megadott névvel hozza létre a webalkalmazást (néhány további sztringtel együtt, hogy egyedivé tegye azt). Létrehozza a **webalkalmazás URL-címét**, az **ügyfél-azonosítót** és az **ügyfél titkos kulcsát**.

Mentse az értékeket, amikor ITSMC-vel létesített kapcsolatokat hoz létre.

**A webalkalmazás telepítésének megkeresése**

1. Lépjen **Azure Portal**  >  **erőforrásokhoz**.
2. Válassza ki a webalkalmazást, majd kattintson a **Beállítások**  >  **alkalmazás beállításai** elemre.
3. Erősítse meg az alkalmazás parancsfájl használatával történő üzembe helyezésének időpontjában megadott Service Manager-példány adatait.

## <a name="configure-the-hybrid-connection"></a>A hibrid kapcsolatok konfigurálása

A következő eljárással konfigurálhatja a hibrid kapcsolatot, amely összekapcsolja a Service Manager példányt a ITSMC az Azure-ban.

1. Keresse meg az Service Manager webalkalmazást az **Azure-erőforrások** területen.
2. Kattintson a **Beállítások**  >  **hálózatkezelés** elemre.
3. A **hibrid kapcsolatok** alatt kattintson **a hibrid kapcsolatok végpontok konfigurálása** elemre.

    ![Hibrid kapcsolat hálózatkezelése](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. A **hibrid kapcsolatok** panelen kattintson a **hibrid kapcsolatok hozzáadása** lehetőségre.

    ![Hibrid kapcsolatok hozzáadása](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Az **hibrid kapcsolatok hozzáadása** panelen kattintson az **új hibrid kapcsolatok létrehozása** lehetőségre.

    ![Új hibrid kapcsolatok](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Írja be a következő értékeket:

   - **Végpont neve**: adja meg az új hibrid kapcsolatok nevét.
   - **Végpont-gazdagép**: a Service Manager felügyeleti kiszolgáló teljes tartományneve.
   - **Végpont portja**: Type 5724
   - **Servicebus névtér**: használjon meglévő Servicebus-névteret, vagy hozzon létre egy újat.
   - **Hely**: válassza ki a helyet.
   - **Név**: adjon meg egy nevet a servicebus, ha létrehozza azt.

     ![Hibrid kapcsolatok értékei](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Kattintson az **OK** gombra a **hibrid kapcsolatok létrehozása** panel bezárásához és a hibrid kapcsolatok létrehozásának megkezdéséhez.

    A hibrid kapcsolatok létrehozása után az megjelenik a panelen.

7. A hibrid kapcsolatok létrehozása után válassza ki a kapcsolatokat, és kattintson a **kiválasztott hibrid kapcsolatok hozzáadása** lehetőségre.

    ![Új hibrid kapcsolatok](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>A figyelő beállításának konfigurálása

A következő eljárással konfigurálhatja a figyelő telepítőjét a hibrid kapcsolatok esetében.

1. A **hibrid kapcsolatok** panelen kattintson a **Csatlakozáskezelő letöltése** elemre, és telepítse azt a gépre, amelyen System Center Service Manager példány fut.

    Miután a telepítés befejeződött, **hibridkapcsolat-kezelő felhasználói felület** lehetőség elérhető a **Start** menüben.

2. Kattintson **hibridkapcsolat-kezelő felhasználói felületre** , a rendszer kérni fogja az Azure-beli hitelesítő adatait.

3. Jelentkezzen be az Azure-beli hitelesítő adataival, és válassza ki az előfizetését, ahol a hibrid kapcsolatok létrejöttek.

4. Kattintson a **Mentés** gombra.

A hibrid kapcsolat sikeresen csatlakoztatva van.

![sikeres hibrid kapcsolatok](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> A hibrid kapcsolat létrehozása után ellenőrizze és tesztelje a kapcsolatát az üzembe helyezett Service Manager webalkalmazás meglátogatásával. Győződjön meg arról, hogy a kapcsolat sikeres, mielőtt megpróbál csatlakozni a ITSMC az Azure-ban.

A következő minta képe a sikeres kapcsolat részleteit mutatja be:

![Hibrid kapcsolatok tesztelése](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>Következő lépések

* [Az ITSM-összekötő áttekintése](itsmc-overview.md)
* [ITSM-munkaelemek létrehozása az Azure-riasztásokból](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Hibaelhárítás az ITSM-összekötőben](./itsmc-resync-servicenow.md)