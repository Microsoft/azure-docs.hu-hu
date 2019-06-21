---
title: Távoli figyelés hozzáférés-vezérlés – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan konfigurálhatja a távoli figyelési megoldásgyorsító szerepköralapú hozzáférés-vezérlést (RBAC)
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: b0c9699bccbb539c9617fac2f3296483139e7188
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203157"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>A távoli figyelési megoldásgyorsító a szerepköralapú hozzáférés-vezérlés konfigurálása

Ez a cikk ismerteti a szerepköralapú hozzáférés-vezérlés konfigurálása a távoli figyelési megoldásgyorsító. Szerepköralapú hozzáférés-vezérlés lehetővé teszik a megoldásban a funkciók korlátozza a hozzáférést az egyéni felhasználók számára.

## <a name="default-settings"></a>Alapértelmezett beállítások

Ha először telepíti a távoli figyelési megoldás, olyan két szerepkör is létezik: **Rendszergazdai** és **csak olvasható**.

A felhasználói a **rendszergazdai** szerepkör a megoldáshoz, többek között a következő engedélyeket az alábbi teljes hozzáféréssel rendelkezik. A felhasználó a **csak olvasható** szerepkör csak lesz hozzáférése a megoldás megtekintéséhez.

| Engedély            | rendszergazda | Csak olvasási engedély |
|----------------       |-------|-----------|
| Megoldás megtekintése         | Igen   | Igen       |
| Riasztás frissítése         | Igen   | Nem        |
| Riasztás törlése         | Igen   | Nem        |
| Eszközök létrehozása        | Igen   | Nem        |
| Eszközök frissítése        | Igen   | Nem        |
| Eszközök törlése        | Igen   | Nem        |
| Eszközcsoportok létrehozása  | Igen   | Nem        |
| Az eszközcsoportok frissítése  | Igen   | Nem        |
| Az eszközcsoportok törlése  | Igen   | Nem        |
| Szabályok létrehozása          | Igen   | Nem        |
| Frissítési szabályok          | Igen   | Nem        |
| Szabályok törlése          | Igen   | Nem        |
| Feladatok létrehozása           | Igen   | Nem        |
| A frissítéskezelés SIM | Igen   | Nem        |

Alapértelmezés szerint a rendszer automatikusan hozzárendel a felhasználó, aki üzembe helyezte a megoldást a **rendszergazdai** szerepkör és a egy Azure Active Directory-alkalmazás tulajdonosa. Alkalmazás tulajdonosként, szerepköröket rendelhetnek más felhasználók az Azure Portalon keresztül. Ha azt szeretné, ha a megoldás a szerepköröket egy másik felhasználó, akkor is meg kell application tulajdonosként az Azure Portalon.

> [!NOTE]
> A felhasználó, aki üzembe helyezte a megoldást, a **csak személy** ki láthatja után azonnal annak lett létrehozva. Megadását mások eléréséhez egy csak olvasható, rendszergazda vagy egy egyéni szerepkört, tekintse meg a következő útmutatások alatt a felhasználók hozzáadásához és eltávolításához, az alkalmazás megtekintéséhez.

## <a name="add-or-remove-users"></a>Felhasználók hozzáadása vagy eltávolítása

Egy Azure Active Directory-alkalmazás tulajdonosa, mint az Azure portal használatával adja hozzá, vagy távolítsa el a felhasználó egy szerepkörhöz a távoli figyelési megoldás. Az alábbi lépések az a [Azure Active Directory vállalati alkalmazás](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) , amely a távoli figyelési megoldás üzembe helyezésekor létrejött.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Ellenőrizze a [felhasználó szerepel a könyvtár](../active-directory/fundamentals/add-users-azure-active-directory.md) használ. Döntött, hogy bejelentkezett a könyvtárat a [a Microsoft Azure IoT-Megoldásgyorsítók](https://www.azureiotsolutions.com/Accelerators) hely. A könyvtár neve jelenik meg a jobb felső sarkában a [oldal](https://www.azureiotsolutions.com/Accelerators).

1. Keresse meg a **vállalati alkalmazás** a megoldás az Azure Portalon. Egyszer, a lista szűréséhez beállításával **alkalmazástípus** való **minden alkalmazás**. Keresse meg az alkalmazás által alkalmazás nevére. Az alkalmazás nevét a távoli figyelési megoldás neve. Az alábbi képernyőképen a megoldást és az alkalmazás megjelenített nevek a következők **contoso-rm4**.

    ![A vállalati alkalmazás](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Ellenőrizze, hogy az alkalmazás ezután parancsra kattintva Ön az alkalmazás tulajdonosának **tulajdonosok**. Az alábbi képernyőképen **Contoso rendszergazdai** tulajdonosa a **contoso-rm4** alkalmazás:

    ![Tulajdonosok](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Ha nem egy olyan tulajdonost, kérje meg egy meglévő tulajdonosát hozzáadása a listához szeretné. Csak a tulajdonosok például alkalmazás-szerepkörök is hozzárendelhet **rendszergazdai** vagy **csak olvasható** más felhasználók számára.

1. Az alkalmazás-szerepkörökhöz rendelt felhasználók listájának megtekintéséhez kattintson **felhasználók és csoportok**.

1. A felhasználó hozzáadásához kattintson **+ Hozzáadás felhasználó**, és kattintson a **felhasználókat és csoportokat, nincs kiválasztott** , válasszon ki egy felhasználót a címtárból.

1. A felhasználói szerepkörhöz rendeléséhez kattintson **, nincs kiválasztott szerepkör kiválasztása** , és válassza a **rendszergazdai** vagy **csak olvasható** szerepkört a felhasználóhoz. Kattintson a **kiválasztása**, és kattintson a **hozzárendelése**.

    ![Szerepkör kiválasztása](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. A felhasználó most már elérhető a távoli figyelési megoldás a szerepkör által meghatározott engedélyekkel.

1. Az alkalmazás a felhasználó törölheti az a **felhasználók és csoportok** lapot a portálon.

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

A távoli figyelési megoldás magában foglalja a **rendszergazdai** és **csak olvasható** szerepkörök első telepítésekor. Egyéni szerepkörök az engedélyek eltérő halmazát adhat hozzá. Egyéni szerepkör definiálásához kell tennie:

- Új szerepkör hozzáadása az alkalmazáshoz az Azure Portalon.
- Adja meg az új szerepkör-házirend a hitelesítési és engedélyezési mikroszolgáltatás.
- A megoldás webes felhasználói felület frissítéséhez.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Adjon meg egy egyéni biztonsági szerepkört a az Azure Portalon

A következő lépések bemutatják, hogyan szerepkör hozzáadása az Azure Active Directory-alkalmazás. Ebben a példában hozzon létre egy új szerepkör, amely lehetővé teszi a tagok létrehozása, frissítése és törlése a távoli figyelési megoldásban az eszközök számára.

1. Keresse meg a **alkalmazásregisztráció** a megoldás az Azure Portalon. Az alkalmazás nevét a távoli figyelési megoldás neve. Az alábbi képernyőképen a megoldást és az alkalmazás megjelenített nevek a következők **contoso-rm4**.

    ![Alkalmazásregisztráció](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Válassza ki az alkalmazását, és kattintson a **Manifest**. Láthatja, hogy a két meglévő [alkalmazás-szerepkörök](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) az alkalmazáshoz meghatározott:

    ![Jegyzék megtekintése](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Egy nevű szerepkör hozzáadása a jegyzékfájl szerkesztése **ManageDevices** az alábbi kódrészletben látható módon. Szüksége lesz egy egyedi karakterlánccá, például egy GUID Azonosítót az új szerepkör-azonosítót. Létrehozhat egy új GUID-szolgáltatást használ, mint például a [Online GUID-generátor](https://www.guidgenerator.com/):

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Mentse a módosításokat.

### <a name="define-a-policy-for-the-new-role"></a>Meghatározhat egy olyan szabályzatot, az új szerepkör

Miután a szerepkör hozzáadása az alkalmazáshoz az Azure Portalon, meg kell határoznia egy szabályzatot a [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) a szerepkör, amely hozzárendeli az eszközök kezeléséhez szükséges engedélyekkel.

1. Klónozás a [távoli figyelés Mikroszolgáltatások](https://github.com/Azure/remote-monitoring-services-dotnet) GitHub-adattár helyi számítógépre.

1. Szerkessze a **auth/Services/data/policies/roles.json** fájlját, hogy a szabályzat a **ManageDevices** szerepkör az alábbi kódrészletben látható módon. A **azonosító** és **szerepkör** értékeknek egyezniük kell a szerepkör-definíció az előző szakaszban az alkalmazásjegyzékben. Engedélyezett műveletek listája lehetővé teszi, hogy valaki van a **ManageDevices** szerepkör létrehozása, frissítése és törlése a megoldáshoz csatlakoztatott eszközökre:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. Miután végzett, szerkesztését a **Services/data/policies/roles.json** fájlt, újraépítése és ismételt üzembe helyezése a hitelesítési és engedélyezési mikroszolgáltatást a megoldásgyorsító.

### <a name="how-the-web-ui-enforces-permissions"></a>Hogyan érvénybe lépteti a webes felhasználói felületen az engedélyek

A webes felhasználói Felületet használja a [hitelesítési és engedélyezési mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) együttműködve meghatározza milyen műveleteket egy felhasználó számára engedélyezett időt vesz igénybe, és milyen vezérlők láthatók-e a felhasználói felületen. Például, ha a megoldás neve **contoso-rm4**, a webes felhasználói felületen kéri le az aktuális felhasználó számára engedélyezett műveletek listáját az alábbi kérelem küldésével:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

A felhasználó nevű **Eszközkezelő** a a **ManageDevices** szerepkör, a válasz törzsében is tartalmaz a következő JSON:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

Az alábbi kódrészletet a [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) a a [webes felhasználói Felületét](https://github.com/Azure/pcs-remote-monitoring-webui/) bemutatja, hogyan az engedélyek kényszerített deklaratív:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

További információkért lásd: [védett összetevők](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Megadhatja, hogy a további engedélyeket a [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) fájlt.

### <a name="how-the-microservices-enforce-permissions"></a>Hogyan a mikroszolgáltatások kényszerítése a engedélyek

A mikroszolgáltatások engedélyek jogosulatlan API-kérelmek ellen is ellenőrizheti. Amikor egy mikroszolgáltatás-API-kérést kap, dekódol, és érvényesíti a JWT jogkivonatot beolvasni a felhasználói Azonosítót és a felhasználói szerephez tartozó engedélyeket.

A következő kódrészlet a a [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) fájlt a [IoTHub Manager mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager), bemutatja, hogyan tartatja be az engedélyeket:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan szerepköralapú hozzáférés-vezérlők a távoli figyelési megoldásgyorsító találhatók meg.

Lásd: [hozzáférés-vezérlés konfigurálása a Time Series Insights Explorer](iot-accelerators-remote-monitoring-rbac-tsi.md) a Time Series Insights explorer, a távoli figyelési megoldásgyorsító való hozzáférés kezeléséről további információt.

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [távoli figyelési architektúrával](iot-accelerators-remote-monitoring-sample-walkthrough.md)

A távoli figyelési megoldás testreszabásával kapcsolatos további információkért lásd: [testreszabása és ismételt mikroszolgáltatások üzembe helyezése](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->