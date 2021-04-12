---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: 0383949dd9b0aeaa185613474d91d0416169ab1f
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491142"
---
## <a name="prerequisites"></a>Előfeltételek

Az cikkben ismertetett lépések elvégzéséhez az alábbi erőforrásokra lesz szüksége:

* Egy Azure IoT Central-alkalmazás, amely az **egyéni alkalmazás** sablonnal lett létrehozva. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](../articles/iot-central/core/quick-deploy-iot-central.md). Az alkalmazást a 2020. július 14-én vagy azt követően kell létrehozni.
* Egy fejlesztői gép a [Visual Studióval (Közösség, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/).
* A [Microsoft Azure IoT minta a C# (.net) GitHub-](https://github.com/Azure-Samples/azure-iot-samples-csharp) tárház helyi másolata, amely tartalmazza a mintakód. Ezzel a hivatkozással töltheti le az adattár másolatát: [zip letöltése](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Ezután bontsa ki a fájlt egy megfelelő helyre a helyi gépen.

## <a name="review-the-code"></a>A kód áttekintése

A korábban letöltött C#-tárház Microsoft Azure IoT-mintáinak másolatában nyissa meg a *"* megoldás fájlját a Visual Studióban. A **megoldáskezelő** bontsa ki a *PnpDeviceSamples > TemperatureController* mappát, és nyissa meg a *program. cs* és a *TemperatureControllerSample. cs* fájlokat a minta kódjának megtekintéséhez.

Amikor futtatja a mintát IoT Centralhoz való csatlakozáshoz, az eszköz kiépítési szolgáltatását (DPS) használja az eszköz regisztrálásához és egy kapcsolati karakterlánc létrehozásához. A minta lekérdezi a DPS által az adott környezet által igényelt kapcsolatok információit.

A *program. cs programban* a metódus a következőt `Main` hívja `SetupDeviceClientAsync` :

* Akkor használja a modell AZONOSÍTÓját `dtmi:com:example:TemperatureController;2` , ha a DPS-vel kiépíti az eszközt. IoT Central a modell AZONOSÍTÓját használja az eszközhöz tartozó sablon azonosításához vagy létrehozásához. További információ: [eszköz hozzárendelése egy eszköz sablonnal](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Hozzon létre egy **DeviceClient** -példányt a IoT Centralhoz való kapcsolódáshoz.

```csharp
private static async Task<DeviceClient> SetupDeviceClientAsync(Parameters parameters, CancellationToken cancellationToken)
{
  DeviceClient deviceClient;
  switch (parameters.DeviceSecurityType.ToLowerInvariant())
  {
    case "dps":
      DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, cancellationToken);
      var authMethod = new DeviceAuthenticationWithRegistrySymmetricKey(dpsRegistrationResult.DeviceId, parameters.DeviceSymmetricKey);
      deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
      break;

    case "connectionstring":
      // ...

    default:
      // ...
  }
  return deviceClient;
}
```

A Main metódus Ezután létrehoz egy **TemperatureControllerSample** -példányt, és meghívja a `PerformOperationsAsync` metódust, hogy az interakciókat a IoT Central segítségével kezelje.

A *TemperatureControllerSample. cs* `PerformOperationsAsync` metódusban a metódus:

* Az **Újraindítási** parancs kezelőjét állítja be az alapértelmezett összetevőn.
* A két termosztát-összetevő **getMaxMinReport** parancsainak kezelőit állítja be.
* Beállítja a kezelőket a cél hőmérsékleti tulajdonságok frissítéseinek fogadására a két termosztát-összetevőn.
* A rendszer elküldi a kezdeti eszköz információ tulajdonságának frissítéseit.
* A rendszer rendszeres időközönként küldi el a hőmérsékleti telemetria a két termosztát-összetevőtől.
* A munkakészlet telemetria rendszeres időközönkénti küldése az alapértelmezett összetevőből.
* A maximális hőmérsékletet küldi el az utolsó újraindítás óta, amikor a két termosztát-összetevőben új maximális hőmérséklet érhető el.

```csharp
public async Task PerformOperationsAsync(CancellationToken cancellationToken)
{
  await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);

  // For a component-level command, the command name is in the format "<component-name>*<command-name>".
  await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommand, Thermostat1, cancellationToken);
  await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommand, Thermostat2, cancellationToken);

  await _deviceClient.SetDesiredPropertyUpdateCallbackAsync(SetDesiredPropertyUpdateCallback, null, cancellationToken);
  _desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
  _desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

  await UpdateDeviceInformationAsync(cancellationToken);
  await SendDeviceSerialNumberAsync(cancellationToken);

  bool temperatureReset = true;
  _maxTemp[Thermostat1] = 0d;
  _maxTemp[Thermostat2] = 0d;

  while (!cancellationToken.IsCancellationRequested)
  {
    if (temperatureReset)
    {
      // Generate a random value between 5.0°C and 45.0°C for the current temperature reading for each "Thermostat" component.
      _temperature[Thermostat1] = Math.Round(s_random.NextDouble() * 40.0 + 5.0, 1);
      _temperature[Thermostat2] = Math.Round(s_random.NextDouble() * 40.0 + 5.0, 1);
    }

    await SendTemperatureAsync(Thermostat1, cancellationToken);
    await SendTemperatureAsync(Thermostat2, cancellationToken);
    await SendDeviceMemoryAsync(cancellationToken);

    temperatureReset = _temperature[Thermostat1] == 0 && _temperature[Thermostat2] == 0;
    await Task.Delay(5 * 1000);
  }
}
```

A `SendTemperatureAsync` metódus azt mutatja, hogy az eszköz hogyan küldi el a hőmérsékleti telemetria egy összetevőtől a IoT Centralig. A `SendTemperatureTelemetryAsync` metódus a osztály használatával hozza `PnpConvention` létre az üzenetet:

```csharp
private async Task SendTemperatureAsync(string componentName, CancellationToken cancellationToken)
{
  await SendTemperatureTelemetryAsync(componentName, cancellationToken);

  double maxTemp = _temperatureReadingsDateTimeOffset[componentName].Values.Max<double>();
  if (maxTemp > _maxTemp[componentName])
  {
    _maxTemp[componentName] = maxTemp;
    await UpdateMaxTemperatureSinceLastRebootAsync(componentName, cancellationToken);
  }
}

private async Task SendTemperatureTelemetryAsync(string componentName, CancellationToken cancellationToken)
{
  const string telemetryName = "temperature";
  double currentTemperature = _temperature[componentName];
  using Message msg = PnpConvention.CreateMessage(telemetryName, currentTemperature, componentName);

  await _deviceClient.SendEventAsync(msg, cancellationToken);

  if (_temperatureReadingsDateTimeOffset.ContainsKey(componentName))
  {
    _temperatureReadingsDateTimeOffset[componentName].TryAdd(DateTimeOffset.UtcNow, currentTemperature);
  }
  else
  {
    _temperatureReadingsDateTimeOffset.TryAdd(
      componentName,
      new Dictionary<DateTimeOffset, double>
      {
        { DateTimeOffset.UtcNow, currentTemperature },
      });
  }
}
```

A `UpdateMaxTemperatureSinceLastRebootAsync` metódus egy `maxTempSinceLastReboot` tulajdonság frissítését IoT Centralre küldi. Ez a metódus a (z) `PnpConvention` osztály használatával hozza létre a javítást:

```csharp
private async Task UpdateMaxTemperatureSinceLastRebootAsync(string componentName, CancellationToken cancellationToken)
{
  const string propertyName = "maxTempSinceLastReboot";
  double maxTemp = _maxTemp[componentName];
  TwinCollection reportedProperties = PnpConvention.CreateComponentPropertyPatch(componentName, propertyName, maxTemp);

  await _deviceClient.UpdateReportedPropertiesAsync(reportedProperties, cancellationToken);
}
```

A `TargetTemperatureUpdateCallbackAsync` metódus kezeli az írható célként megadott hőmérsékleti tulajdonság frissítését IoT Centralról. Ez a metódus az `PnpConvention` osztály használatával olvassa be a tulajdonság frissítési üzenetét, és létrehozza a választ:

```csharp
private async Task TargetTemperatureUpdateCallbackAsync(TwinCollection desiredProperties, object userContext)
{
  const string propertyName = "targetTemperature";
  string componentName = (string)userContext;

  bool targetTempUpdateReceived = PnpConvention.TryGetPropertyFromTwin(
    desiredProperties,
    propertyName,
    out double targetTemperature,
    componentName);
  if (!targetTempUpdateReceived)
  {
      return;
  }

  TwinCollection pendingReportedProperty = PnpConvention.CreateComponentWritablePropertyResponse(
      componentName,
      propertyName,
      targetTemperature,
      (int)StatusCode.InProgress,
      desiredProperties.Version);

  await _deviceClient.UpdateReportedPropertiesAsync(pendingReportedProperty);

  // Update Temperature in 2 steps
  double step = (targetTemperature - _temperature[componentName]) / 2d;
  for (int i = 1; i <= 2; i++)
  {
      _temperature[componentName] = Math.Round(_temperature[componentName] + step, 1);
      await Task.Delay(6 * 1000);
  }

  TwinCollection completedReportedProperty = PnpConvention.CreateComponentWritablePropertyResponse(
      componentName,
      propertyName,
      _temperature[componentName],
      (int)StatusCode.Completed,
      desiredProperties.Version,
      "Successfully updated target temperature");

  await _deviceClient.UpdateReportedPropertiesAsync(completedReportedProperty);
}

```

A `HandleMaxMinReportCommand` metódus kezeli a IoT Central nevű összetevők parancsait:

```csharp
private Task<MethodResponse> HandleMaxMinReportCommand(MethodRequest request, object userContext)
{
    try
    {
        string componentName = (string)userContext;
        DateTime sinceInUtc = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
        var sinceInDateTimeOffset = new DateTimeOffset(sinceInUtc);

        if (_temperatureReadingsDateTimeOffset.ContainsKey(componentName))
        {

            Dictionary<DateTimeOffset, double> allReadings = _temperatureReadingsDateTimeOffset[componentName];
            Dictionary<DateTimeOffset, double> filteredReadings = allReadings.Where(i => i.Key > sinceInDateTimeOffset)
                .ToDictionary(i => i.Key, i => i.Value);

            if (filteredReadings != null && filteredReadings.Any())
            {
                var report = new
                {
                    maxTemp = filteredReadings.Values.Max<double>(),
                    minTemp = filteredReadings.Values.Min<double>(),
                    avgTemp = filteredReadings.Values.Average(),
                    startTime = filteredReadings.Keys.Min(),
                    endTime = filteredReadings.Keys.Max(),
                };

                byte[] responsePayload = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(report));
                return Task.FromResult(new MethodResponse(responsePayload, (int)StatusCode.Completed));
            }

            return Task.FromResult(new MethodResponse((int)StatusCode.NotFound));
        }

        return Task.FromResult(new MethodResponse((int)StatusCode.NotFound));
    }
    catch (JsonReaderException ex)
    {
        // ...
    }
}
```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>A kód futtatása

A minta alkalmazás futtatása a Visual Studióban:

1. **Megoldáskezelő** válassza ki a **PnpDeviceSamples > TemperatureController** projektfájlt.

1. Navigáljon a **Project > TemperatureController tulajdonságok > hibakereséshez**. Ezután adja hozzá a következő környezeti változókat a projekthez:

    | Name | Érték |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Az azonosító hatókörének értéke korábban jegyzett tett. |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | minta-Device-01 |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | A generált eszköz kulcsának értéke korábban jegyzett tett. |

Most már futtathatja és hibakeresést végezhet a Visual Studióban.

Az alábbi kimenetben az eszköz regisztrálása és a IoT Centralhoz való csatlakozás látható. A minta elindítja a telemetria küldését:

```output
[03/31/2021 14:43:17]info: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Press Control+C to quit the sample.
[03/31/2021 14:43:17]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set up the device client.
[03/31/2021 14:43:18]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Initializing via DPS
[03/31/2021 14:43:27]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set handler for 'reboot' command.
[03/31/2021 14:43:27]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Connection status change registered - status=Connected, reason=Connection_Ok.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set handler for "getMaxMinReport" command.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set handler to receive 'targetTemperature' updates.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component = 'deviceInformation', properties update is complete.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - { "serialNumber": "SR-123456" } is complete.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - component="thermostat1", { "temperature": 34.2 } in °C.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat1", { "maxTempSinceLastReboot": 34.2 } in °C is complete.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - component="thermostat2", { "temperature": 25.1 } in °C.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat2", { "maxTempSinceLastReboot": 25.1 } in °C is complete.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - {"workingSet":31412} in KB.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Láthatja, hogy az eszköz hogyan válaszol a parancsokra és a tulajdonságokra:

```output
[03/31/2021 14:47:00]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Command: Received - component="thermostat2", generating max, min and avg temperature report since 31/03/2021 06:00:00.
[03/31/2021 14:47:00]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Command: component="thermostat2", MaxMinReport since 31/03/2021 06:00:00: maxTemp=36.4, minTemp=36.4, avgTemp=36.4, startTime=31/03/2021 14:46:33, endTime=31/03/2021 14:46:55

...

[03/31/2021 14:46:36]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Received - component="thermostat1", { "targetTemperature": 67°C }.
[03/31/2021 14:46:36]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat1", {"targetTemperature": 67 } in °C is InProgress.
[03/31/2021 14:46:49]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat1", {"targetTemperature": 67 } in °C is Completed
[03/31/2021 14:46:49]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - component="thermostat1", { "temperature": 67 } in °C.
```
