---
title: Gyors útmutató az Azure-alkalmazások konfigurációjának Python-alkalmazásokkal való használatához | Microsoft Docs
description: Ebben a rövid útmutatóban egy Python-alkalmazást hozunk létre az Azure-alkalmazás konfigurációjával, hogy központilag kezelje az alkalmazás-beállítások tárolási és kezelési beállításait a kódból elkülönítve.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 1be74ceeb1f4808a8df8f56b71b66aa870cdd91d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800873"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>Gyors útmutató: Python-alkalmazás létrehozása az Azure app Configuration szolgáltatással

Ebben a rövid útmutatóban az Azure app Configuration használatával központosíthatja az Alkalmazásbeállítások tárolását és kezelését az [Azure app Configuration ügyféloldali kódtár használatával a Pythonhoz](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- Python 2,7, vagy 3,6 vagy újabb – a Python Windowson való beállításával kapcsolatos információkért tekintse meg a [Python a Windows-dokumentációban]( https://docs.microsoft.com/windows/python/) című témakört.

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7.   >    >  A következő kulcs-érték párok hozzáadásához válassza a Configuration Explorer Create **Key-Value** elemet:

    | Kulcs | Érték |
    |---|---|
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

8. Kattintson az **Alkalmaz** gombra.

## <a name="setting-up-the-python-app"></a>A Python-alkalmazás beállítása

1. Ebben az oktatóanyagban létre fog hozni egy új könyvtárat az *app-Configuration-Gyorsindítás* nevű projekthez.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Váltson az újonnan létrehozott *app-Configuration-Gyorsindítás* könyvtárba.

    ```console
    cd app-configuration-quickstart
    ```

1. Telepítse az Azure-alkalmazás konfigurációs ügyféloldali könyvtárát az `pip install` paranccsal.

    ```console
    pip install azure-appconfiguration
    ```

1. Hozzon létre egy *app-Configuration-Quickstart.py* nevű új fájlt az *app-Configuration-Gyorsindítás* könyvtárban, és adja hozzá a következő kódot:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> Az ebben a rövid útmutatóban szereplő kódrészletek segítséget nyújtanak az alkalmazás konfigurálásához a Pythonhoz. Az alkalmazás esetében érdemes megfontolnia a kivételek kezelését is az igényeinek megfelelően. A kivételek kezelésére vonatkozó további tudnivalókért tekintse meg a [PYTHON SDK dokumentációját](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="configure-your-app-configuration-connection-string"></a>Alkalmazás-konfigurációs kapcsolatok karakterláncának konfigurálása

1. Állítson be egy **AZURE_APP_CONFIG_CONNECTION_STRING** nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. A parancssorban futtassa a következő parancsot:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. A módosítás érvénybe léptetéséhez indítsa újra a parancssort. Nyomtassa ki a környezeti változó értékét annak ellenőrzéséhez, hogy megfelelően van-e beállítva.

## <a name="code-samples"></a>Kódminták

Az ebben a szakaszban szereplő kódrészletek bemutatják, hogyan végezheti el a gyakori műveleteket a Pythonhoz készült alkalmazás-konfigurációs ügyféloldali kódtár használatával. Adja hozzá ezeket a kódrészleteket a `try` korábban létrehozott *app-Configuration-Quickstart.py* -fájlban található blokkhoz.

> [!NOTE]
> Az alkalmazás konfigurációs ügyféloldali könyvtára a kulcs-érték objektumra hivatkozik `ConfigurationSetting` . Ezért ebben a cikkben az alkalmazás konfigurációs tárolójának **kulcs-értékeit** **konfigurációs beállításoknak** nevezzük.

* [Kapcsolódás alkalmazás-konfigurációs tárolóhoz](#connect-to-an-app-configuration-store)
* [Konfigurációs beállítás beolvasása](#get-a-configuration-setting)
* [Konfigurációs beállítás hozzáadása](#add-a-configuration-setting)
* [Konfigurációs beállítások listájának beolvasása](#get-a-list-of-configuration-settings)
* [Konfigurációs beállítás zárolása](#lock-a-configuration-setting)
* [Konfigurációs beállítás feloldása](#unlock-a-configuration-setting)
* [Konfigurációs beállítás frissítése](#update-a-configuration-setting)
* [Konfigurációs beállítás törlése](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

A következő kódrészlet létrehozza a **AzureAppConfigurationClient** egy példányát a környezeti változókban tárolt kapcsolatok karakterlánc használatával.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>Konfigurációs beállítás beolvasása

A következő kódrészlet egy konfigurációs beállítást kérdez le név szerint `key` .

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>Konfigurációs beállítás hozzáadása

A következő kódrészlet létrehoz egy `ConfigurationSetting` objektumot a `key` és `value` a mezőkkel, és meghívja a `add_configuration_setting` metódust. Ez a metódus kivételt jelez, ha olyan konfigurációs beállítást próbál meg hozzáadni, amely már létezik a tárolóban. Ha el szeretné kerülni ezt a kivételt, akkor a [set_configuration_setting](#update-a-configuration-setting) metódus használható.

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>Konfigurációs beállítások listájának beolvasása

A következő kódrészlet lekéri a konfigurációs beállítások listáját. A `key_filter` és a `label_filter` argumentumok a kulcs-értékeknek a és a alapján történő szűréséhez adhatók meg `key` `label` . A szűréssel kapcsolatos további információkért lásd: a [konfigurációs beállítások lekérdezése](./concept-key-value.md#query-key-values).

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>Konfigurációs beállítás zárolása

A kulcs-érték zárolási állapotát az alkalmazás konfigurációjában az `read_only` objektum attribútuma jelöli `ConfigurationSetting` . Ha a `read_only` értéke `True` , a beállítás zárolva van. A `set_read_only` metódus meghívható `read_only=True` argumentummal a konfigurációs beállítás zárolásához.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>Konfigurációs beállítás feloldása

Ha a `read_only` attribútuma `ConfigurationSetting` `False` , a beállítás fel van oldva. A `set_read_only` metódus meghívható `read_only=False` argumentummal a konfigurációs beállítás feloldásához.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>Konfigurációs beállítás frissítése

A `set_configuration_setting` metódus használható meglévő beállítások frissítésére vagy új beállítás létrehozására. A következő kódrészlet megváltoztatja egy meglévő konfigurációs beállítás értékét.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>Konfigurációs beállítás törlése

A következő kódrészlet törli a konfigurációs beállításokat név szerint `key` .

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ebben a rövid útmutatóban létrehozott egy olyan Python-alkalmazást, amely az Azure app Configuration ügyféloldali függvénytárát használja a Azure Portalon létrehozott konfigurációs beállítások lekéréséhez, egy új beállítás hozzáadásához, a meglévő beállítások listájának beolvasásához, a beállítások zárolásához és feloldásához, a beállítások frissítéséhez és végül a beállítás törléséhez.

Ezen a ponton a *app-Configuration-Quickstart.py* -fájlnak a következő kóddal kell rendelkeznie:

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

A konzol ablakban navigáljon a *app-Configuration-Quickstart.py* fájlt tartalmazó könyvtárhoz, és hajtsa végre a következő Python-parancsot az alkalmazás futtatásához:

```console
python app-configuration-quickstart.py
```

A következő kimenetnek kell megjelennie:

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és megtanulta, hogyan érheti el a kulcs-értékeket egy Python-alkalmazásból.

További kódokért keresse fel a következőt:

> [!div class="nextstepaction"]
> [Az Azure app Configuration ügyféloldali függvénytár-mintái](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
