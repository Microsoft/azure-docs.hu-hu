---
title: Mi az az Azure IoT Central? | Microsoft Docs
description: Azure IoT Central is an IoT application platform that simplifies the creation of IoT solutions and helps to reduce the burden and cost of IoT management operations, and development. Ez a cikk áttekintést nyújt az Azure IoT Central jellemzőiről.
author: dominicbetts
ms.author: dobett
ms.date: 11/12/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 8cfcbddfc8d50855860af655847f997fb2a01711
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479706"
---
# <a name="what-is-azure-iot-central-preview-features"></a>What is Azure IoT Central (preview features)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> The [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) capabilities in Azure IoT Central are currently in public preview. Don't use an IoT Plug and Play enabled IoT Central [application template](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) for production workloads. For production environments use an IoT central application created from a current, generally available, [application template](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).

IoT Central is an IoT application platform that reduces the burden and cost of developing, managing, and maintaining enterprise-grade IoT solutions. Choosing to build with IoT Central gives you the opportunity to focus time, money, and energy on transforming your business with IoT data, rather than just maintaining and updating a complex and continually evolving IoT infrastructure.

The web UI lets you monitor device conditions, create rules, and manage millions of devices and their data throughout their life cycle. Furthermore, it enables you to act on device insights by extending IoT intelligence into line-of-business applications.

This article outlines, for IoT Central:

- A projekthez társított tipikus szerepek.
- Egy alkalmazás létrehozása.
- Az eszközök az alkalmazáshoz történő csatlakoztatása
- Az alkalmazás felügyelete.
- Azure IoT Edge capabilities in IoT Central.
- How to connect your Azure IoT Edge runtime powered devices to your application.

## <a name="known-issues"></a>Ismert problémák

> [!Note]
> These known issues only apply to the IoT Central preview applications.

- Rules don't support all actions (only email).
- For complex types - rules, analytics, and device groups aren't supported.
- Continuous data export doesn't support the Avro format (incompatibility).
- Simulated devices don't support all complex types.
- GeoJSON isn't currently supported.
- Map tile isn't currently supported.
- Jobs don't support complex types.
- Array schema types aren't supported.
- Application template export and application copy aren't supported.
- Only the C device SDK and the Node.js device and service SDKs are supported.
- It's only available in the United States and Europe locations.
- Device capability models must have all the interfaces defined inline in the same file.

## <a name="personas"></a>Szerepek

The IoT Central documentation refers to four personas who interact with an IoT Central application:

- A _solution builder_ is responsible for defining the types of devices that connect to the application and customizing the application for the operator.
- Az _operátor_ kezeli az alkalmazáshoz csatlakoztatott eszközöket.
- An _administrator_ is responsible for administrative tasks such as managing [user roles and permissions](howto-administer.md) within the application.
- A _device developer_ creates the code that runs on a device or IoT Edge module connected to your application.

## <a name="create-your-iot-central-application"></a>Create your IoT Central application

As a solution builder, you use IoT Central to create a custom, cloud-hosted IoT solution for your organization. Az egyéni IoT-megoldások általában a következőket foglalják magukban:

- Egy felhőalapú alkalmazás, amely telemetriai adatokat fogad az eszközökről és lehetővé teszi az eszközök felügyeletét.
- Több eszköz, amelyek egyéni kódokat futtatnak és a felhőalapú alkalmazáshoz csatlakoznak.

You can quickly deploy a new IoT Central application and then customize it to your specific requirements in your browser. As a solution builder, you use the web-based tools to create a _device template_ for the devices that connect to your application. A device template is the blueprint that defines the characteristics and behavior of a type of device such as the:

- Telemetry it sends.
- Az operátor által módosítható üzleti tulajdonságok.
- Azok az eszköztulajdonságok, amelyeket az eszköz állít be, és az alkalmazásban csak olvashatók.
- Properties, that an operator sets, that determine the behavior of the device.

This device template includes:

- A _device capability model_ that describes the capabilities a device should implement such as the telemetry it sends and the properties it reports.
- Cloud properties that aren't stored on the device.
- Customizations, dashboards, and forms that are part of your IoT Central application.

### <a name="create-device-templates"></a>Create device templates

[IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) enables IoT Central to integrate devices without you writing any embedded device code. At the core of IoT Plug and Play, is a device capability model schema that describes device capabilities. In an IoT Central preview application, device templates use these IoT Plug and Play device capability models.

As a solution builder, you have several options for creating device templates:

- Design the device template in IoT Central and then implement its device capability model in your device code.
- Import a device capability model from the [Azure Certified for IoT device catalog](https://aka.ms/iotdevcat) and then add any cloud properties, customizations, and dashboards your IoT Central application needs.
- Create a device capability model using Visual Studio code. Implement your device code from the model, and connect your device to your IoT Central application. IoT Central finds the device capability model from a repository and creates a simple device template for you.
- Create a device capability model using Visual Studio code. Implement your device code from the model. Manually import the device capability model into your IoT Central application and then add any cloud properties, customizations, and dashboards your IoT Central application needs.

As a solution builder, you can use IoT Central to generate code for test devices to validate your device templates.

### <a name="customize-the-ui"></a>A felhasználói felület testreszabása

As a solution builder, you can also customize the IoT Central application UI for the operators who are responsible for the day-to-day use of the application. Customizations that a solution builder can make include:

- Egy eszközsablonban található tulajdonságok és beállítások elrendezésének meghatározása.
- Egyéni irányítópultok konfigurálása, amelyek segítik az operátorokat a megállapítások felismerésében és a problémák gyorsabb megoldásában.
- Egyéni elemzések konfigurálása, amelyek a csatlakoztatott eszközökről származó idősorozat-adatokat vizsgálják.

## <a name="connect-your-devices"></a>Az eszközök csatlakoztatása

Miután a szerkesztő meghatározta, hogy az alkalmazáshoz milyen típusú eszközök csatlakozhatnak, egy eszközfejlesztő létrehozza az eszközökön futtatandó kódokat. Az eszközfejlesztők a Microsoft nyílt forráskódú [Azure IoT SDK-it](https://github.com/Azure/azure-iot-sdks) használhatják az eszközkódok létrehozásához. These SDKs have broad language, platform, and protocol support to meet your needs to connect your devices to your IoT Central application. The SDKs help you implement the following device capabilities:

- Biztonságos kapcsolat létrehozása.
- Telemetria küldése.
- Állapotjelentés.
- Konfigurációfrissítések fogadása.

További információkért olvassa el a következő blogbejegyzést: [Milyen előnyökkel jár, ha az Azure IoT SDK-kat használja, és milyen buktatókkal, ha nem?](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge devices

As well as devices created using the [Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks), you can also connect [Azure IoT Edge devices](../../iot-edge/about-iot-edge.md) to an IoT Central application. Azure IoT Edge lets you run cloud intelligence and custom logic directly on IoT devices managed by IoT Central. The IoT Edge runtime enables you to:

- Install and update workloads on the device.
- Maintain Azure IoT Edge security standards on the device.
- Ensure that IoT Edge modules are always running.
- Report module health to the cloud for remote monitoring.
- Manage communication between downstream leaf devices and an IoT Edge device, between modules on an IoT Edge device, and between an IoT Edge device and the cloud.

For more information, see [Azure IoT Edge devices and IoT Central](./concepts-architecture.md#azure-iot-edge-devices).

## <a name="manage-your-application"></a>Az alkalmazás felügyelete

IoT Central applications are fully hosted by Microsoft, which reduces the administration overhead of managing your applications.

As an operator, you use the IoT Central application to manage the devices in your IoT Central solution. Operators do tasks such as:

- Az alkalmazáshoz csatlakoztatott eszközök monitorozása.
- Az eszközök hibáinak keresése és elhárítása.
- Új eszközök üzembe helyezése.

As a solution builder, you can define custom rules and actions that operate over data streaming from connected devices. Az operátor eszközszinten engedélyezheti vagy letilthatja ezeket a szabályokat, az alkalmazáson belüli feladatok vezérlése és automatizálása céljából.

Administrators manage access to your application with [user roles and permissions](howto-administer.md).

## <a name="quotas"></a>Kvóták

Each Azure subscription has default quotas that could impact the scope of your IoT solution. Currently, IoT Central limits the number of applications you can deploy in a subscription to 10. If you need to increase this limit, contact [Microsoft support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Következő lépések

Now that you have an overview of IoT Central, here are suggested next steps:

- Understand the differences between [IoT Central and Azure IoT solution accelerators](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).
- Ismerkedés az [Azure IoT Central kezelőfelületével](overview-iot-central-tour.md).
- [Egy Azure IoT Central-alkalmazás létrehozásának](quick-deploy-iot-central.md) első lépései.
- Learn more about [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Learn how to [Create Azure IoT Edge Device template](./tutorial-define-edge-device-type.md)
