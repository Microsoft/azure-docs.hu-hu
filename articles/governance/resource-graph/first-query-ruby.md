---
title: 'Rövid útmutató: az első Ruby-lekérdezés'
description: Ebben a rövid útmutatóban a következő lépésekkel engedélyezheti a Rubyhoz készült Resource Graph gem-t, és futtathatja az első lekérdezést.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 41769359eaa1f930d34b0a182f35da38a6f5109d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920037"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>Rövid útmutató: az első Resource Graph-lekérdezés futtatása a Ruby használatával

Az Azure Resource Graph használatának első lépéseként győződjön meg arról, hogy a Ruby szükséges gyöngyszemei telepítve vannak. Ez a rövid útmutató végigvezeti a drágakövek Ruby-telepítésben való hozzáadásának folyamatán.

A folyamat végén hozzáadta a drágaköveket a Ruby-telepítéshez, és futtathatja az első Resource Graph-lekérdezést.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
- Egy Azure-szolgáltatásnév, beleértve a _clientId_ és a _clientSecret_.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Az erőforrás-gráf projekt létrehozása

Ahhoz, hogy a Ruby lekérdezze az Azure Resource Graphot, a gem-t hozzá kell adni a következőhöz: `Gemfile` . Ez a gem működik, ahol a Ruby használható, beleértve a [Azure Cloud Shell](https://shell.azure.com), a [bash on Windows 10 vagy a](/windows/wsl/install-win10)helyileg telepített szolgáltatást.

1. Győződjön meg arról, hogy a legújabb Ruby telepítve van (legalább **2.7.1**). Ha még nincs telepítve, töltse le a következő címen: [Ruby-lang.org](https://www.ruby-lang.org/en/downloads/).

1. A választott Ruby-környezetben inicializáljon egy köteget egy új projekt mappájába:

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. Frissítse az `Gemfile` Azure Resource graphhoz szükséges drágaköveket. A frissített fájlnak ehhez a példához hasonlóan kell kinéznie:

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. A Project mappában futtassa a parancsot `bundle install` . Erősítse meg, hogy a drágakövek telepítve lettek `bundle list` .

1. Ugyanebben a Project mappában hozza létre a `argQuery.rb` következő kódot, és mentse a frissített fájlt:

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Ha a Ruby-szkriptet mentette és használatra kész, itt az ideje, hogy kipróbáljon egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást adja vissza az egyes erőforrások **nevével** és **erőforrás-típusával** .

Minden egyes hívásban olyan `argQuery` változót használunk, amelyet a saját értékeivel kell helyettesíteni:

- `{tenantId}` – Cserélje le a-t a bérlői AZONOSÍTÓra
- `{clientId}` – Cserélje le a szolgáltatást a szolgáltatásnév ügyfél-azonosítójával.
- `{clientSecret}` – Cserélje le a szolgáltatást a szolgáltatásnév ügyfél-titkos kódjára
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

1. Módosítsa a címtárakat a projekt mappájába, ahol létrehozta a `Gemfile` és a `argClient.rb` fájlokat.

1. Futtassa az első Azure Resource Graph-lekérdezést a GEMS és a `resources` metódus használatával:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint az `order by`, tehát ha többször is futtatja, valószínűleg minden kéréssel eltérő erőforráslistát fog kapni.

1. Módosítsa a végső paramétert, `argQuery.rb` és módosítsa a lekérdezést `order by` a **Name (név** ) tulajdonságra:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancs először a lekérdezés eredményeit korlátozza, majd megrendeli azokat.

1. Módosítsa a végső paramétert, `argQuery.rb` és módosítsa a lekérdezést a `order by` **Name (név** ) tulajdonságra, majd az első `limit` öt találatra:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Ha a végső lekérdezés többször is fut, feltételezve, hogy a környezetében semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** tulajdonság szerint vannak rendezve, de továbbra is az első öt találatra korlátozódnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a telepített drágaköveket a Ruby-környezetből, ezt a következő paranccsal teheti meg:

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> A gem olyan `azure_mgmt_resourcegraph` függőségekkel rendelkezik `ms_rest` , `ms_rest_azure` amelyek a környezettől függően is telepíthetők. Ha már nincs rá szükség, távolítsa el ezeket a drágaköveket is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzáadta az erőforrás-gráf drágaköveket a Ruby-környezethez, és futtatta az első lekérdezést. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)
