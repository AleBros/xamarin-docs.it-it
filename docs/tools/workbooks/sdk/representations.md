---
title: Rappresentazioni nelle cartelle di lavoro di Xamarin
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: f0fdc4f5145fc9a80f63b251a8a8927486fcd894
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="representations-in-xamarin-workbooks"></a>Rappresentazioni nelle cartelle di lavoro di Xamarin

## <a name="representations"></a>rappresentazioni

All'interno di una sessione di cartella di lavoro o di controllo, il codice che viene eseguito e produce un risultato (ad esempio, un metodo che restituisce un valore oppure il risultato di un'espressione) viene elaborato tramite la pipeline di rappresentazione nell'agente. Tutti gli oggetti, ad eccezione di primitive, ad esempio numeri interi, verranno riflesse per produrre grafici interattivi membro e passa attraverso un processo per fornire rappresentazioni alternative che il client può rendere più complessa. Gli oggetti di qualsiasi dimensione e profondità sono supportati in modo sicuro (inclusi i cicli ed elementi enumerabili infinito) a causa della comunicazione remota e reflection lazy e interattive.

Le cartelle di lavoro di Xamarin offre alcuni tipi comuni a tutti gli agenti e i client che consentono di avanzate per il rendering dei risultati. [`Color`][xir-color] è un esempio di questo tipo, in cui ad esempio in iOS, l'agente è responsabile della conversione `CGColor` o `UIColor` oggetti in un `Xamarin.Interactive.Representations.Color` oggetto.

Oltre alle rappresentazioni comuni, l'integrazione SDK fornisce le API per la serializzazione personalizzate rappresentazioni nell'agente e il rendering di rappresentazioni nel client.

## <a name="external-representations"></a>Rappresentazioni esterne

[`Xamarin.Interactive.IAgent.RepresentationManager`][repman] offre la possibilità di registrare un [ `RepresentationProvider` ] [ repp], quale un'integrazione deve implementare per convertire un oggetto arbitrario in un modulo per eseguire il rendering indipendente. Questi moduli agnostico devono implementare il [ `ISerializableObject` ] [ serobj] interfaccia.

Implementazione di `ISerializableObject` interfaccia aggiunge un metodo di serializzazione che controlla esattamente come gli oggetti vengono serializzati. Il `Serialize` metodo prevede che uno sviluppatore specificherà esattamente quali proprietà devono essere serializzati, e il nome finale sarà. Esaminando il `Person` dell'oggetto nel nostro [`KitchenSink` esempio] [esempio], possiamo vedere il funzionamento:

```csharp
public sealed class Person : ISerializableObject
{
    public string Name { get; }

    // Rest of the code is omitted…

    void ISerializableObject.Serialize (ObjectSerializer serializer)
        => serializer.Property (nameof (Name), Name);
}
```

Se si desidera fornire un superset o un subset delle proprietà dell'oggetto originale, è possibile eseguire questa operazione con `Serialize`. Ad esempio, è possibile procedere come segue per fornire una pre-calcolate `Age` proprietà `Person`:

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
> Le API che producono `ISerializableObject` oggetti direttamente non devono essere gestiti da un `RepresentationProvider`. Se l'oggetto a cui si desidera visualizzare è **non** un `ISerializableObject`, è possibile gestire wrapping nel `RepresentationProvider`.

### <a name="rendering-a-representation"></a>Il rendering di una rappresentazione

Renderer vengono implementati in JavaScript e sarà possibile accedere a una versione JavaScript dell'oggetto rappresentato tramite `ISerializableObject`. La copia di JavaScript avrà inoltre un `$type` proprietà stringa che indica il nome del tipo .NET.

È consigliabile usare TypeScript per il codice client di integrazione, che naturalmente viene compilato in JavaScript vaniglia. In entrambi i casi, il SDK fornisce [esportate] [ typings] che può essere fatto riferimento direttamente dalla macchina o riferimento semplicemente manualmente se la scrittura di vaniglia JavaScript è preferito.

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

In questo caso, `PersonRenderer` implementa il `Renderer` interfaccia. Vedere il [esportate] [ typings] per altri dettagli.

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
[xir-color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[repman]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[repp]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[serobj]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Serialization.ISerializableObject/
