---
title: Azure PowerShell minták a Azure Data Factory
description: Azure PowerShell minták – az adatüzemek létrehozásához és kezeléséhez szükséges parancsfájlok.
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 20ee36989acbc36f86478d8205dd61097362eea3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461536"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Azure PowerShell minták a Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A következő táblázat hivatkozásokat tartalmaz a Azure Data Factory Azure PowerShell parancsfájljaira.

| Script | Description  |
|---|---|
|**Adatok másolása**||
|[Blobok másolása egy mappából az Azure-Blob Storage egy másik mappájába](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl blobokat másol egy Azure-beli mappából Blob Storage egy másik mappába, amely ugyanabban a Blob Storage. |
|[Adatok másolása SQL Serverból az Azure-ba Blob Storage](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl egy SQL Server adatbázisból másol át egy Azure Blob Storage-ba. |
|[Tömeges másolás](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl az Azure szinapszis Analytics szolgáltatásban Azure SQL Database egy adatbázisában lévő több táblázat adatait másolja át. |
|[Növekményes másolat](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl csak az új vagy frissített rekordokat tölti be a forrás adattárolóból a fogadó adattárba, miután a forrásról a fogadóba másolta az adatok kezdeti teljes másolatát. |
|**Adatok átalakítása**||
|[Az adatátalakítás Spark-fürt használatával](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript egy Spark-fürtön futó program futtatásával alakítja át az adathalmazokat. |
|**SSIS-csomagok emelése és áthelyezése az Azure-ba**||
|[Azure SSIS integrációs modul létrehozása](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript egy Azure-SSIS integrációs modult foglal le, amely SQL Server Integration Services (SSIS) csomagokat futtat az Azure-ban. |



