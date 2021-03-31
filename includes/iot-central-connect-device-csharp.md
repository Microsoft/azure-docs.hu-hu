---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/25/2020
ms.openlocfilehash: f4536beae18a50d3e1d42fc1593cf826c94418f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033852"
---
## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* Egy Azure IoT Central-alkalmazás, amely az **egyéni alkalmazás** sablonnal lett létrehozva. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](../articles/iot-central/core/quick-deploy-iot-central.md). Az alkalmazást a 2020. július 14-én vagy azt követően kell létrehozni.
* Egy fejlesztői gép a [Visual Studióval (Közösség, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/).
* A [Microsoft Azure IoT minta a C# (.net) GitHub-](https://github.com/Azure-Samples/azure-iot-samples-csharp) tárház helyi másolata, amely tartalmazza a mintakód. Ezzel a hivatkozással töltheti le az adattár másolatát: [zip letöltése](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Ezután bontsa ki a fájlt egy megfelelő helyre a helyi gépen.

## <a name="review-the-code"></a>A kód áttekintése

A korábban letöltött C#-tárház Microsoft Azure IoT-mintáinak másolatában nyissa meg a *Azure-IOT-Samples-csharp-master\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* projektfájlt a Visual Studióban. A **termosztát** projektben nyissa meg a *program. cs* és a *ThermostatSample. cs* fájlt a minta kódjának megtekintéséhez.

Amikor futtatja a mintát IoT Centralhoz való csatlakozáshoz, az eszköz kiépítési szolgáltatását (DPS) használja az eszköz regisztrálásához és egy kapcsolati karakterlánc létrehozásához. A minta lekérdezi a DPS által az adott környezet által igényelt kapcsolatok információit.

A *program. cs programban* a metódus a következőt `main` hívja `SetupDeviceClientAsync` :

* Akkor használja a modell AZONOSÍTÓját `dtmi:com:example:Thermostat;1` , ha a DPS-vel kiépíti az eszközt. IoT Central a modell AZONOSÍTÓját használja az eszközhöz tartozó sablon azonosításához vagy létrehozásához. További információ: [eszköz hozzárendelése egy eszköz sablonnal](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Hozzon létre egy **DeviceClient** -példányt a IoT Centralhoz való kapcsolódáshoz.

```csharp
private static async Task<DeviceClient> SetupDeviceClientAsync(Parameters parameters, CancellationToken cancellationToken)
{
  DeviceClient deviceClient;
  switch (parameters.DeviceSecurityType.ToLowerInvariant())
  {
    case "dps":
      s_logger.LogDebug($"Initializing via DPS");
      DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, cancellationToken);
      var authMethod = new DeviceAuthenticationWithRegistrySymmetricKey(dpsRegistrationResult.DeviceId, parameters.DeviceSymmetricKey);
      deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
      break;

    case "connectionstring":
        // ...
        break;

    default:
        // ...
  }

  return deviceClient;
}
```

A Main metódus Ezután létrehoz egy **ThermostatSample** -példányt, és meghívja a `PerformOperationsAsync` metódust, hogy az interakciókat a IoT Central segítségével kezelje.

A *ThermostatSample. cs* `PerformOperationsAsync` metódusban a metódus:

* Beállítja a kezelőt a célként megadott hőmérséklet kívánt tulajdonságainak fogadására.
* A **getMaxMinReport** parancs kezelőjét állítja be.
* A hőmérséklet telemetria rendszeres időközönként küldi el.
* A maximális hőmérsékletet küldi el az utolsó újraindítás óta, amikor új maximális hőmérsékletet ér el.

```csharp
public async Task PerformOperationsAsync(CancellationToken cancellationToken)
{
  await _deviceClient.SetDesiredPropertyUpdateCallbackAsync(TargetTemperatureUpdateCallbackAsync, _deviceClient, cancellationToken);

  await _deviceClient.SetMethodHandlerAsync("getMaxMinReport", HandleMaxMinReportCommand, _deviceClient, cancellationToken);

  bool temperatureReset = true;
  while (!cancellationToken.IsCancellationRequested)
  {
    if (temperatureReset)
    {
      // Generate a random value between 5.0°C and 45.0°C for the current temperature reading.
      _temperature = Math.Round(_random.NextDouble() * 40.0 + 5.0, 1);
      temperatureReset = false;
    }

    await SendTemperatureAsync();
    await Task.Delay(5 * 1000);
  }
}
```

A `SendTemperatureAsync` metódus azt mutatja, hogy az eszköz hogyan küldi el a hőmérséklet telemetria IoT Central:

```csharp
private async Task SendTemperatureAsync()
{
  await SendTemperatureTelemetryAsync();

  double maxTemp = _temperatureReadingsDateTimeOffset.Values.Max<double>();
  if (maxTemp > _maxTemp)
  {
    _maxTemp = maxTemp;
    await UpdateMaxTemperatureSinceLastRebootAsync();
  }
}
```

A `UpdateMaxTemperatureSinceLastRebootAsync` metódus egy `maxTempSinceLastReboot` tulajdonság frissítését IoT Centralre küldi:

```csharp
private async Task UpdateMaxTemperatureSinceLastRebootAsync()
{
  const string propertyName = "maxTempSinceLastReboot";

  var reportedProperties = new TwinCollection();
  reportedProperties[propertyName] = _maxTemp;

  await _deviceClient.UpdateReportedPropertiesAsync(reportedProperties);
}
```

A `TargetTemperatureUpdateCallbackAsync` metódus kezeli az írható célként megadott hőmérsékleti tulajdonság frissítését IoT Central:

```csharp
private async Task TargetTemperatureUpdateCallbackAsync(TwinCollection desiredProperties, object userContext)
{
    const string propertyName = "targetTemperature";

    (bool targetTempUpdateReceived, double targetTemperature) = GetPropertyFromTwin<double>(desiredProperties, propertyName);
    if (targetTempUpdateReceived)
    {
      string jsonPropertyPending = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.InProgress}, " +
          $"\"av\": {desiredProperties.Version} }} }}";
      var reportedPropertyPending = new TwinCollection(jsonPropertyPending);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedPropertyPending);

      // Update Temperature in 2 steps
      double step = (targetTemperature - _temperature) / 2d;
      for (int i = 1; i <= 2; i++)
      {
        _temperature = Math.Round(_temperature + step, 1);
        await Task.Delay(6 * 1000);
      }

      string jsonProperty = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.Completed}, " +
        $"\"av\": {desiredProperties.Version}, \"ad\": \"Successfully updated target temperature\" }} }}";
      var reportedProperty = new TwinCollection(jsonProperty);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedProperty);
  }
  else
  {
    // ...
  }
}
```

A `HandleMaxMinReportCommand` metódus kezeli a IoT Central nevű parancsot:

```csharp
private Task<MethodResponse> HandleMaxMinReportCommand(MethodRequest request, object userContext)
{
  try
  {
    DateTime sinceInUtc = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
    var sinceInDateTimeOffset = new DateTimeOffset(sinceInUtc);

    Dictionary<DateTimeOffset, double> filteredReadings = _temperatureReadingsDateTimeOffset
      .Where(i => i.Key > sinceInDateTimeOffset)
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
  catch (JsonReaderException ex)
  {
    // ...
  }
}
```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>A kód futtatása

A minta alkalmazás futtatása:

1. Nyissa meg a *Azure-IOT-Samples-csharp-Master/IOT-hub/Samples/Device/PnpDeviceSamples/termosztát/termosztát. csproj* Project fájlt a Visual Studióban.

1. A Visual Studióban navigáljon a **Project > termosztát tulajdonságai > hibakeresés** elemre. Ezután adja hozzá a következő környezeti változókat a projekthez:

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
[11/25/2020 11:07:58]info: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Press Control+C to quit the sample.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set up the device client.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Initializing via DPS
[11/25/2020 11:08:11]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler to receive "targetTemperature" updates.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Connection status change registered - status=Connected, reason=Connection_Ok.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler for "getMaxMinReport" command.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 36.5°C } is Completed.
[11/25/2020 11:08:18]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:23]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:29]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Láthatja, hogy az eszköz hogyan válaszol a parancsokra és a tulajdonságokra:

```output
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 19/11/2020 06:30:00.
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: MaxMinReport since 19/11/2020 06:30:00: maxTemp=36.5, minTemp=36.5, avgTemp=36.5, startTime=25/11/2020 11:08:13, endTime=25/11/2020 11:09:51

...

[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Received - { "targetTemperature": 56°C }.
[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is InProgress.
[11/25/2020 11:14:40]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 56°C } is Completed.
[11/25/2020 11:14:43]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is Completed.
```
