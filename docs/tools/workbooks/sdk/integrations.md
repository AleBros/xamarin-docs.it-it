---
title: Argomenti avanzati sulle integrazioni
description: Questo documento illustra argomenti avanzati relativi a integrations Xamarin Workbooks. Illustra il pacchetto Xamarin.Workbook.Integrations NuGet e l'esposizione di API all'interno di una cartella di lavoro di Xamarin.
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 56ee709b78b8587c2717dc9d25a6357041812d23
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104206"
---
# <a name="advanced-integration-topics"></a>Argomenti avanzati sulle integrazioni

Gli assembly di integrazione devono fare riferimento il [ `Xamarin.Workbooks.Integrations` NuGet][nuget]. Consultare il [documentazione introduttiva](~/tools/workbooks/sdk/index.md) per altre informazioni sulle operazioni iniziali con il pacchetto NuGet.

Integrazioni di client sono inoltre supportate e vengono avviate, inserire i file CSS o JavaScript con lo stesso nome dell'assembly di integrazione dell'agente nella stessa directory. Ad esempio, se l'assembly di integrazione dell'agente (che fa riferimento a NuGet) è denominato `SampleExternalIntegration.dll`, quindi `SampleExternalIntegration.js` e `SampleExternalIntegration.css` verranno integrate nel client anche se esistono. Integrazioni di client sono facoltative.

L'integrazione esterno stesso può essere compresso come pacchetto NuGet, fornita e fare riferimento direttamente all'interno dell'applicazione che ospita l'agente o semplicemente posizionati l'accanto un `.workbook` file quale per usare il servizio.

Integrazioni esterne (agente e client) in pacchetti NuGet vengono caricate automaticamente quando il pacchetto viene fatto riferimento, in base alla documentazione di avvio rapido, mentre gli assembly di integrazione forniti insieme alla cartella di lavoro saranno necessario farvi riferimento come così:

```csharp
#r "SampleExternalIntegration.dll"
```

Quando si fa riferimento un'integrazione in questo modo, non venga caricato dal client sin da subito&mdash;, devi chiamare codice dall'integrazione di averlo di carico. Si sarà addressing questo bug in futuro.

Il `Xamarin.Interactive` PCL offre l'integrazione di importanti alcune API. Ogni integrazione necessario fornire almeno un punto di ingresso di integrazione:

```csharp
using Xamarin.Interactive;

[assembly: AgentIntegration (typeof (AgentIntegration))]

class AgentIntegration : IAgentIntegration
{
    const string TAG = nameof (AgentIntegration);

    public void IntegrateWith (IAgent agent)
    {
        // hook into IAgent APIs
    }
}
```

A questo punto, quando viene fatto riferimento all'assembly di integrazione, il client in modo implicito caricherà i file di integrazione di JavaScript e CSS.

## <a name="apis"></a>API

Come con qualsiasi assembly cui viene fatto riferimento da una cartella di lavoro o in tempo reale analizza sessione, una delle relative API pubbliche sono accessibile per la sessione. Pertanto è importante disporre di una superficie API ragionevole e sicura per gli utenti a esplorare.

L'assembly dell'integrazione è in effetti un ponte tra un'applicazione o SDK di interesse e la sessione. Fornisce nuove API che hanno un significato in particolar modo nel contesto di una cartella di lavoro o in tempo reale esaminare sessione, oppure non fornire alcuna API pubbliche e semplicemente eseguire attività "dietro le quinte", ad esempio sospendere l'oggetto [rappresentazioni](~/tools/workbooks/sdk/representations.md).

> [!NOTE]
> Le API che devono essere pubblici, ma non devono essere resi visibili tramite IntelliSense possono essere contrassegnate con il consueto `[EditorBrowsable (EditorBrowsableState.Never)]` attributo.

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
