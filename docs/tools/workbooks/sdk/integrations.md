---
title: Argomenti avanzati sulle integrazioni
description: Questo documento descrive gli argomenti avanzati correlati a Xamarin Workbooks integrazioni. Viene illustrato il pacchetto NuGet Novell. Workbook. integrations e l'esposizione API all'interno di una cartella di lavoro di Novell.
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: f8105c8285e696f8754799c33c30e31ce5356870
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283887"
---
# <a name="advanced-integration-topics"></a>Argomenti avanzati sulle integrazioni

Gli assembly di [ `Xamarin.Workbooks.Integrations` ][nuget]integrazione devono fare riferimento a NuGet. Per altre informazioni su come iniziare a usare il pacchetto NuGet, vedere la [documentazione di avvio rapido](~/tools/workbooks/sdk/index.md) .

Sono supportate anche le integrazioni client e vengono avviate inserendo i file JavaScript o CSS con lo stesso nome dell'assembly di integrazione dell'agente nella stessa directory. Se, ad esempio, l'assembly di integrazione dell'agente `SampleExternalIntegration.dll` `SampleExternalIntegration.js` , che fa riferimento a NuGet, è `SampleExternalIntegration.css` denominato, e verrà integrato anche nel client, se esistono. Le integrazioni client sono facoltative.

L'integrazione esterna può essere inserita in un pacchetto come NuGet, fornita e a cui viene fatto riferimento direttamente all'interno dell'applicazione che ospita l'agente o semplicemente posizionata insieme a un `.workbook` file che desidera utilizzarla.

Le integrazioni esterne (agente e client) nei pacchetti NuGet verranno caricate automaticamente quando si fa riferimento al pacchetto, in base alla documentazione di avvio rapido, mentre gli assembly di integrazione forniti insieme alla cartella di lavoro dovranno farvi riferimento come indicato di seguito:

```csharp
#r "SampleExternalIntegration.dll"
```

Quando si fa riferimento a un'integrazione in questo modo, quest'operazione non verrà caricata immediatamente dal client&mdash;, quindi è necessario chiamare parte del codice dall'integrazione per caricarla. Il problema verrà risolto in futuro.

La `Xamarin.Interactive` libreria PCL fornisce alcune importanti API di integrazione. Ogni integrazione deve fornire almeno un punto di ingresso di integrazione:

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

A questo punto, una volta fatto riferimento all'assembly di integrazione, il client caricherà in modo implicito i file di integrazione JavaScript e CSS.

## <a name="apis"></a>API

Come per qualsiasi assembly a cui fa riferimento una cartella di lavoro o una sessione di controllo Live, le API pubbliche sono accessibili alla sessione. Pertanto, è importante avere una superficie API sicura e ragionevole per gli utenti da esplorare.

L'assembly di integrazione è effettivamente un bridge tra un'applicazione o un SDK di interesse e la sessione. Può fornire nuove API che hanno un significato specifico nel contesto di una cartella di lavoro o di una sessione di controllo in tempo reale oppure non forniscono API pubbliche ed eseguono semplicemente attività "dietro le quinte", come la creazione di [rappresentazioni](~/tools/workbooks/sdk/representations.md)di oggetti.

> [!NOTE]
> Le API che devono essere pubbliche ma che non devono essere rilevate tramite IntelliSense possono essere contrassegnate con il consueto `[EditorBrowsable (EditorBrowsableState.Never)]` attributo.

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
