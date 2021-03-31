---
title: Ruby-alkalmazások konfigurálása – Azure App Service
description: Ismerje meg, hogyan konfigurálhat egy előre összeállított Ruby-tárolót az alkalmazásához. A cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.topic: quickstart
ms.date: 06/18/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 038d62573b491325adc60647debf17fa87e06cfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92743684"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Linux Ruby-alkalmazás konfigurálása az Azure App Service-hez

Ez a cikk azt ismerteti, hogy az [Azure App Service](overview.md) hogyan futtat Ruby-alkalmazásokat egy Linux-tárolóban, és Ön szükség esetén hogyan szabhatja testre az App Service viselkedését. A Ruby-alkalmazásokat a szükséges [gemekkel](https://rubygems.org/gems) együtt kell üzembe helyezni.

Ez az útmutató olyan Ruby-fejlesztőknek biztosít főbb fogalmakat és útmutatást, akik beépített Linux-tárolót használnak az App Service-ben. Ha még soha nem használta az Azure App Service-t, először ismerkedjen meg a [Ruby rövid útmutatójával](quickstart-ruby.md) és a [Ruby PostgreSQL-lel történő használatának oktatóanyagával](tutorial-ruby-postgres-app.md).

## <a name="show-ruby-version"></a>Ruby-verzió megjelenítése

Az aktuális Ruby-verzió megjelenítéséhez futtassa az alábbi parancsot a [Cloud Shellben](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott Ruby-verzió megjelenítéséhez futtassa az alábbi parancsot a [Cloud Shellben](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

A Ruby nem támogatott verzióját úgy futtathatja, hogy kiépíti a saját tárolórendszerképét. További információ: [egyéni Docker-rendszerkép használata](tutorial-custom-container.md?pivots=container-linux).

## <a name="set-ruby-version"></a>Ruby-verzió beállítása

Ha 2.3-asra szeretné beállítani a Ruby verzióját, futtassa az alábbi parancsot a [Cloud Shellben](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Előfordulhat, hogy a következőhöz hasonló hibákba ütközik az üzembe helyezés során:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> vagy
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Ez azt jelenti, hogy a projektben konfigurált Ruby-verzió eltér a futtatott tárolóban telepített verziótól (ami a fenti példában `2.3.3`). A fenti példában ellenőrizze a *Gemfile* és a *.ruby-version* paramétereket, és győződjön meg róla, hogy nincs beállítva Ruby-verzió, vagy a futtatott tárolóban telepített verzióra van állítva (ami a fenti példában `2.3.3`).

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-szel az alkalmazás kódján kívül is [megadhatja az alkalmazások beállításait](configure-common.md#configure-app-settings). A beállításokhoz a szabványos [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html) mintával férhet hozzá. Például egy `WEBSITE_SITE_NAME` nevű alkalmazásbeállítás hozzáféréséhez használja a következő kódot:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Üzembe helyezés testreszabása

Ha [Git-adattárat](deploy-local-git.md) vagy egy [Zip-csomagot](deploy-zip.md) helyez üzembe bekapcsolt összeállítási folyamatokkal, az üzembehelyezési motor (Kudu) alapértelmezés szerint automatikusan futtatja az üzembe helyezés utáni következő lépéseket:

1. Ellenőrzi, hogy létezik-e a *Gemfile*.
1. Futtatja a `bundle clean` parancsot. 
1. Futtatja a `bundle install --path "vendor/bundle"` parancsot.
1. A `bundle package` futtatásával a gemeket becsomagolja a szállítói/gyorsítótármappába.

### <a name="use---without-flag"></a>A --without jelző használata

Ha a `bundle install` parancsot a [--without](https://bundler.io/man/bundle-install.1.html) jelzővel szeretné futtatni, a `BUNDLE_WITHOUT` [alkalmazásbeállításnál](configure-common.md#configure-app-settings) csoportok vesszővel tagolt listáját adja meg. A következő paranccsal például ezt állíthatja be: `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Ha megadja ezt a beállítást, akkor az üzembehelyezési motor a `bundle install` parancsot a `--without $BUNDLE_WITHOUT` jelzővel futtatja.

### <a name="precompile-assets"></a>Adategységek előfordítása

Az üzembe helyezés utáni lépések alapértelmezett esetben nem fordítják le előre az adategységeket. Az adategységek előfordításához az `ASSETS_PRECOMPILE` [alkalmazásbeállítás](configure-common.md#configure-app-settings) értéke legyen `true`. Ekkor az üzembe helyezés utáni lépések végén lefut a `bundle exec rake --trace assets:precompile` parancs. Például:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

További információk: [Statikus adategységek szolgáltatása](#serve-static-assets).

## <a name="customize-start-up"></a>Indítás testreszabása

Alapértelmezés szerint a Ruby-tároló a következő sorrendben indítja el a Rails-kiszolgálót (további információért lásd az [indítási szkriptet](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Ha még nem létezik, létrehoz egy [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) értéket. Ez egy kötelezően megadandó érték az alkalmazás éles üzemmódban történő futtatásához.
1. A `RAILS_ENV` környezeti változót a `production` értékre állítja.
1. Törli a korábban futó Rails-kiszolgáló által hátrahagyott *.pid* fájlokat a *tmp/pids* könyvtárból.
1. Ellenőrzi, hogy az összes függőség telepítve van-e. Ha nincs, megpróbál gemeket telepíteni a helyi *szállítói/gyorsítótár* könyvtárból.
1. Futtassa az `rails server -e $RAILS_ENV` parancsot.

A következő módokon szabhatja testre az indítási folyamatot:

- [Statikus adategységek szolgáltatása](#serve-static-assets)
- [Futtatás nem éles környezetben](#run-in-non-production-mode)
- [A secret_key_base manuális beállítása](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Statikus adategységek szolgáltatása

A Ruby-tárolóban lévő Rails-kiszolgáló alapértelmezés szerint éles módban fut, és [feltételezi, hogy az adategységek előre le vannak fordítva, és a webkiszolgáló szolgáltatja őket](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Ha a Rails-kiszolgálóról szeretne statikus adategységeket szolgáltatni, két dolgot kell tennie:

- **Fordítsa le előre az adategységeket** - [A statikus adategységeket helyileg fordítsa le](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation), majd telepítse őket manuálisan, vagy hagyja, hogy az üzembehelyezési motor gondoskodjon erről (lásd: [Adategységek előfordítása](#precompile-assets).
- **Statikus fájlok szolgáltatásának engedélyezése** – Ahhoz, hogy a Ruby-tároló szolgáltasson statikus objektumokat, a [ `RAILS_SERVE_STATIC_FILES`alkalmazásbeállítást](configure-common.md#configure-app-settings) állítsa `true` értékre. Például:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Futtatás nem éles környezetben

Alapértelmezés szerint a Rails-kiszolgáló éles módban fut. Ha például fejlesztői módban szeretné futtatni, állítsa a `RAILS_ENV` [alkalmazásbeállítást](configure-common.md#configure-app-settings) `development` értékre.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Ez a beállítás azonban önmagában azt eredményezi, hogy a Rails-kiszolgáló fejlesztési módban indul el, amely csak a localhost kérelmeket fogadja el, és a tárolón kívül nem érhető el. A távoli ügyfélkérelmek fogadásához az `APP_COMMAND_LINE` [alkalmazásbeállítást](configure-common.md#configure-app-settings) állítsa `rails server -b 0.0.0.0` értékre. Ezzel az alkalmazásbeállítással egyéni parancsokat futtathat a Ruby-tárolóban. Például:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a> A secret_key_base manuális beállítása

Ha az App Service által létrehozott helyett saját `secret_key_base` értéket szeretne használni, a `SECRET_KEY_BASE` [alkalmazásbeállításnál](configure-common.md#configure-app-settings) állítsa be a kívánt értéket. Például:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Rails-alkalmazás PostgreSQL-lel](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service a Linuxon – gyakori kérdések](faq-app-service-linux.md)
