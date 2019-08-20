---
title: Rappresentazioni in Xamarin Workbooks
description: Questo documento descrive la pipeline di rappresentazione Xamarin Workbooks, che consente il rendering di risultati avanzati per qualsiasi codice che restituisce un valore.
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: b61452fc21d81f427249825decee4f119c50abf0
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511506"
---
# <a name="representations-in-xamarin-workbooks"></a>Rappresentazioni in Xamarin Workbooks

## <a name="representations"></a>Rappresentazioni

All'interno di una cartella di lavoro o di una sessione di controllo, il codice eseguito e produce un risultato, ad esempio un metodo che restituisce un valore o il risultato di un'espressione, viene elaborato tramite la pipeline di rappresentazione nell'agente. Tutti gli oggetti, ad eccezione delle primitive, ad esempio i numeri interi, verranno riflessi per produrre grafici di membri interattivi ed eseguiranno un processo per fornire rappresentazioni alternative che il client può eseguire il rendering in modo più completo. Gli oggetti di qualsiasi dimensione e profondità sono supportati in modo sicuro (inclusi i cicli e gli enumerabili infiniti) a causa della reflection interattiva e della funzionalità di comunicazione remota.

Xamarin Workbooks fornisce alcuni tipi comuni a tutti gli agenti e i client che consentono il rendering completo dei risultati. `Color`è un esempio di tale tipo, in cui ad esempio in iOS, l'agente è responsabile della conversione `CGColor` di `UIColor` oggetti o in `Xamarin.Interactive.Representations.Color` un oggetto.

Oltre alle rappresentazioni comuni, Integration SDK fornisce API per la serializzazione di rappresentazioni personalizzate nell'agente e le rappresentazioni di rendering nel client.

## <a name="external-representations"></a>Rappresentazioni esterne

`Xamarin.Interactive.IAgent.RepresentationManager`fornisce la possibilità di registrare un `RepresentationProvider`oggetto, che deve essere implementato da un'integrazione per la conversione da un oggetto arbitrario a un form agnostico per il rendering. Questi moduli agnostici devono implementare `ISerializableObject` l'interfaccia.

L'implementazione `ISerializableObject` dell'interfaccia aggiunge un metodo Serialize che controlla con precisione la modalità di serializzazione degli oggetti. Il `Serialize` metodo prevede che uno sviluppatore specifichi esattamente quali proprietà devono essere serializzate e quale sarà il nome finale. Esaminando l' `Person` oggetto nel [`KitchenSink` esempio] [sample], è possibile vedere come funziona:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

Se volevamo fornire un superset o un subset di proprietà dall'oggetto originale, possiamo farlo con `Serialize`. Ad esempio, è possibile eseguire un'operazione simile alla seguente per fornire una `Age` proprietà pre-calcolata in: `Person`

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; set; }
  public DateTime DateOfBirth { get; set; }

  // <snip>

  void ISerializableObject.Serialize (ObjectSerializer serializer)
  {
    serializer.Property (nameof (Name), Name);
    serializer.Property (nameof (DateOfBirth), DateOfBirth);

    // Let's pre-compute an Age property that's the person's age in years,
    // so we don't have to compute it in the renderer.
    var age = (DateTime.MinValue + (DateTime.Now - DateOfBirth)).Year - 1;
    serializer.Property ("Age", age)
  }
}
```

> [!NOTE]
> Le API che `ISerializableObject` producono oggetti direttamente non devono essere gestite da un oggetto `RepresentationProvider`. Se l'oggetto che si desidera visualizzare **non** è un `ISerializableObject`, sarà necessario gestirlo in `RepresentationProvider`.

### <a name="rendering-a-representation"></a>Rendering di una rappresentazione

I renderer sono implementati in JavaScript e avranno accesso a una versione JavaScript dell'oggetto rappresentato tramite `ISerializableObject`. La copia JavaScript disporrà anche di `$type` una proprietà stringa che indica il nome del tipo .NET.

Si consiglia di usare TypeScript per il codice di integrazione client, che naturalmente si compila a Vanilla JavaScript. In entrambi i casi, l'SDK fornisce [tipi][typings] di digitazioni a cui è possibile fare riferimento direttamente da typescript o semplicemente a cui viene fatto riferimento manualmente se si preferisce scrivere la vaniglia JavaScript.

Il punto di integrazione principale per il `xamarin.interactive.RendererRegistry`rendering è:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

In questo `PersonRenderer` caso, `Renderer` implementa l'interfaccia. Per ulteriori informazioni, vedere le [digitazioni][typings].

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
