---
title: Rappresentazioni in Xamarin Workbooks
description: Questo documento descrive la pipeline di rappresentazione Xamarin Workbooks, che abilita il rendering dei risultati completi per il codice che restituisce un valore.
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: d9aafbe13e06875b6577a4d2308e419932fd1589
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382113"
---
# <a name="representations-in-xamarin-workbooks"></a>Rappresentazioni in Xamarin Workbooks

## <a name="representations"></a>Rappresentazioni

All'interno di una sessione della cartella di lavoro o di controllo, il codice che viene eseguito e viene fornito un risultato (ad esempio, un metodo che restituisce un valore o il risultato di un'espressione) viene elaborato tramite la pipeline di rappresentazione nell'agente. Tutti gli oggetti, ad eccezione di primitive, ad esempio numeri interi, verranno riflesse per creare grafici interattivi membro e passeranno attraverso un processo per fornire le rappresentazioni alternative che il client può rendere più complessa. Gli oggetti di qualsiasi dimensione e una profondità sono supportati in modo sicuro (tra cui cicli ed elementi enumerabili infinito) a causa di .NET remoting e reflection lazy e interattive.

Xamarin Workbooks fornisce alcuni tipi comuni a tutti gli agenti e i client che consentono un rendering complessi dei risultati. [`Color`][xir-color] è un esempio di questo tipo, in cui, ad esempio in iOS, l'agente è responsabile della conversione `CGColor` oppure `UIColor` oggetti in un `Xamarin.Interactive.Representations.Color` oggetto.

Oltre alla comune rappresentazioni, l'integrazione SDK fornisce le API per la serializzazione personalizzate rappresentazioni nell'agente e il rendering di rappresentazioni nel client.

## <a name="external-representations"></a>Rappresentazioni esterne

[`Xamarin.Interactive.IAgent.RepresentationManager`][repman] offre la possibilità di registrare un' [ `RepresentationProvider` ] [ repp], quale un'integrazione deve implementare per convertire un oggetto arbitrario in un form agnostico per eseguire il rendering. Questi moduli agnostico devono implementare il [ `ISerializableObject` ] [ serobj] interfaccia.

Implementazione di `ISerializableObject` interfaccia aggiunge un metodo Serialize che con precisione controlla il modo in cui vengono serializzati gli oggetti. Il `Serialize` metodo prevede che uno sviluppatore esattamente specificherà le proprietà che devono essere serializzate, e qual è il nome finale. Osservando il `Person` dell'oggetto nel nostro [`KitchenSink` esempio] [esempio], possiamo vedere come funziona:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

Se si desidera fornire un superset o un subset di proprietà dall'oggetto originale, è possibile farlo con `Serialize`. Ad esempio, potrebbe essere facciamo qualcosa di simile per fornire una pre-calcolate `Age` proprietà `Person`:

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
> Le API che producono `ISerializableObject` oggetti direttamente non devono essere gestite da un `RepresentationProvider`. Se l'oggetto da visualizzare **non** un' `ISerializableObject`, si dovranno gestire wrapping nel `RepresentationProvider`.

### <a name="rendering-a-representation"></a>Il rendering di una rappresentazione

I renderer vengono implementati in JavaScript e sarà possibile accedere a una versione JavaScript dell'oggetto rappresentato tramite `ISerializableObject`. La copia di JavaScript avrà anche una `$type` proprietà stringa che indica il nome del tipo .NET.

È consigliabile usare TypeScript per il codice client di integrazione, che ovviamente viene compilato per vanilla JavaScript. In entrambi i casi, il SDK fornisce [esportate] [ typings] che possono essere fa direttamente riferimento TypeScript o semplicemente fare riferimento a manualmente se la scrittura di vanilla JavaScript è preferito.

Il punto di integrazione principale per il rendering è `xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

In questo caso, `PersonRenderer` implementa il `Renderer` interfaccia. Vedere le [esportate] [ typings] per altri dettagli.

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
[xir-color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[repman]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[repp]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[serobj]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Serialization.ISerializableObject/
