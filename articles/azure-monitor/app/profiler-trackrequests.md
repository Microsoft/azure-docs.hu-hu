---
title: Kód írása a kérelmek nyomon követéséhez az Azure Application Insights használatával | Microsoft Docs
description: Kódot írhat a kérelmek Application Insights való nyomon követéséhez, így a kérésekhez profilokat kérhet le.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.custom: devx-track-csharp
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: aaa1d6df9faa20b1a561bfccdfea682af7645c18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88930247"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Kód írása a kérelmek nyomon követéséhez Application Insights

Ha meg szeretné tekinteni az alkalmazás profilját a teljesítmény lapon, az Azure Application Insightsnak követnie kell az alkalmazásra vonatkozó kérelmeket. A Application Insights automatikusan nyomon követheti a már műszeres keretrendszerekre épülő alkalmazások kérelmeit. Két példa a ASP.NET és a ASP.NET Core. 

Más alkalmazások, például az Azure Cloud Services feldolgozói szerepkörök és a Service Fabric állapot nélküli API-k esetében kódot kell írnia ahhoz, hogy a kérések kezdete és vége legyen Application Insights. A kód megírása után a rendszer elküldi a telemetria-kérelmeket a Application Insightsnak. A telemetria megtekintheti a teljesítmény lapon, és a rendszer ezeket a kérelmeket gyűjti a profilokról. 

A kérelmek manuális nyomon követéséhez tegye a következőket:

  1. Az alkalmazás élettartama elején adja hozzá a következő kódot:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      A globális rendszerállapot-konfigurációval kapcsolatos további információkért lásd: [a Service Fabric használata Application Insights használatával](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Minden olyan kódrészlethez, amelyet szeretne felvenni, vegyen fel egy `StartOperation<RequestTelemetry>` **using** utasítást az alábbi példában látható módon:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        `StartOperation<RequestTelemetry>`Egy másik `StartOperation<RequestTelemetry>` hatókörön belüli hívás nem támogatott. `StartOperation<DependencyTelemetry>`Ehelyett a beágyazott hatókörben használható. Például:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
