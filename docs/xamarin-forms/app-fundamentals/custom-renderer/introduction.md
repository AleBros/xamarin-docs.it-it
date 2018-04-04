---
title: Introduzione a un renderer personalizzato
description: Renderer personalizzati forniscono un approccio potente per la personalizzazione dell'aspetto e comportamento dei controlli di xamarin. Forms. Possono essere utilizzati per lo stile piccole modifiche o sofisticati layout specifici della piattaforma e personalizzazione del comportamento. In questo articolo viene fornita un'introduzione al renderer personalizzati e viene descritto il processo per la creazione di un renderer personalizzato.
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: e555c038d66033d925da42e4c70b89d5caac8ad6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-custom-renderers"></a>Introduzione a un renderer personalizzato

_Renderer personalizzati forniscono un approccio potente per la personalizzazione dell'aspetto e comportamento dei controlli di xamarin. Forms. Possono essere utilizzati per lo stile piccole modifiche o sofisticati layout specifici della piattaforma e personalizzazione del comportamento. In questo articolo viene fornita un'introduzione al renderer personalizzati e viene descritto il processo per la creazione di un renderer personalizzato._

Xamarin. Forms [controlli, layout e pagine](~/xamarin-forms/user-interface/controls/index.md) presentare un'API comune per descrivere le interfacce utente per dispositivi mobili multipiattaforma. Ogni pagina, layout e il controllo viene eseguito il rendering in modo diverso in ogni piattaforma, utilizzando un `Renderer` classe che a sua volta crea un controllo nativo (corrispondente nella rappresentazione di xamarin. Forms), li dispone sullo schermo e aggiunge il comportamento specificato nella codice condiviso.

Gli sviluppatori possono implementare le proprie classi `Renderer` per personalizzare l'aspetto e/o il comportamento di un controllo. Un renderer personalizzato per un determinato tipo può essere aggiunto al progetto di un'applicazione per personalizzare il controllo in un'unica posizione, consentendo il comportamento predefinito su altre piattaforme; o renderer personalizzato diversi possono essere aggiunti a ogni progetto di applicazione per creare un aspetto diverso su iOS, Android e Windows Phone. Tuttavia, implementare una classe renderer personalizzato per eseguire una controllo semplice di personalizzazione è spesso una risposta pesante. Effetti semplificano questo processo e vengono generalmente utilizzati per lo stile piccole modifiche. Per altre informazioni, vedere [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Effetti).

## <a name="examining-why-custom-renderers-are-necessary"></a>Esame perché renderer personalizzati sono necessari

Modifica dell'aspetto di un controllo di xamarin. Forms, senza utilizzare un renderer personalizzato, è un processo in due fasi che prevede la creazione di un controllo personalizzato tramite creazione di una sottoclasse e che quindi il controllo personalizzato al posto del controllo originale. Esempio di codice seguente viene illustrato un esempio di creazione di una sottoclasse di `Entry` controllo:

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

Il `MyEntry` controllo è un `Entry` consentono di controllare il `BackgroundColor` è impostato su grigio e possono fare riferimento in Xaml dichiarare uno spazio dei nomi per la posizione e utilizzando il prefisso dello spazio dei nomi dell'elemento di controllo. Nell'esempio di codice riportato di seguito viene illustrato come la `MyEntry` controllo personalizzato può essere utilizzato da un `ContentPage`:

```xaml
<ContentPage
    ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

Il `local` prefisso dello spazio dei nomi può essere qualsiasi carattere. Tuttavia, il `namespace` e `assembly` i valori devono corrispondere i dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene utilizzato per fare riferimento al controllo personalizzato.

> [!NOTE]
> La definizione di `xmlns` è molto più semplice in PCLs rispetto ai progetti condivisi. Una libreria di classi Portabile viene compilato in un assembly in modo semplice determinare il tipo è il `assembly=CustomRenderer` valore deve essere. Quando si usano progetti condivisi, tutte le risorse condivise (incluso il codice XAML) vengono compilate in ognuno di questi riferimenti, il che significa che se il iOS, Android e Windows Phone progetti hanno i propri *i nomi degli assembly* non è possibile per scrivere il `xmlns` dichiarazione perché il valore deve essere diverso per ogni applicazione. Controlli personalizzati in XAML per i progetti condivisi richiederà ogni progetto di applicazione deve essere configurato con lo stesso nome di assembly.

Il `MyEntry` controllo personalizzato viene quindi eseguito il rendering in ogni piattaforma, con uno sfondo grigio, come illustrato nelle schermate seguenti:

![](introduction-images/screenshots.png "Controllo personalizzato MyEntry in ciascuna piattaforma")

Modifica il colore di sfondo del controllo in ogni piattaforma ha eseguita esclusivamente tramite una sottoclasse del controllo. Tuttavia, questa tecnica è limitata in ciò che è possibile ottenere perché non è possibile usufruire dei miglioramenti specifici della piattaforma e delle personalizzazioni. Quando sono necessari, è necessario implementare un renderer personalizzato.

## <a name="creating-a-custom-renderer-class"></a>Creazione di una classe Renderer personalizzato

Il processo per la creazione di una classe renderer personalizzato è come segue:

1. Creare una sottoclasse della classe renderer che esegue il rendering del controllo nativo.
1. Override del metodo che esegue il rendering del controllo nativo e scrivere una logica per personalizzare il controllo. Spesso, il `OnElementChanged` metodo viene utilizzato per il rendering del controllo nativo, che viene chiamato quando viene creato il controllo di xamarin. Forms corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per il rendering del controllo di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> Per la maggior parte degli elementi di xamarin. Forms, è facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, verrà utilizzato il renderer predefinito per la classe di base del controllo. Un renderer personalizzato è tuttavia necessarie in ogni progetto della piattaforma per il rendering di un [vista](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) o [ViewCell](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) elemento.

Negli argomenti di questa serie fornirà dimostrazioni e una spiegazione di questo processo per diversi elementi di xamarin. Forms.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se un controllo personalizzato è contenuto in un progetto libreria di classi Portabile che è stato aggiunto alla soluzione (vale a dire non PCL creata da Visual Studio per il modello di progetto App di xamarin. Forms Mac e Visual Studio), un'eccezione può verificarsi in iOS, quando si tenta di accedere al controllo personalizzato. Se si verifica questo problema può essere risolto tramite la creazione di un riferimento al controllo personalizzato dalla `AppDelegate` classe:

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

In questo modo il compilatore di riconoscere il `ClassInPCL` tipo da risolvere. In alternativa, il `Preserve` attributo può essere aggiunti alla `AppDelegate` classe per ottenere lo stesso risultato:

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

Crea un riferimento di `ClassInPCL` tipo, che indica che è necessario in fase di esecuzione. Per ulteriori informazioni, vedere [codice mantenimento](~/ios/deploy-test/linker.md).

## <a name="summary"></a>Riepilogo

In questo articolo ha fornito un'introduzione ai renderer personalizzati ed è stato descritto il processo per la creazione di un renderer personalizzato. Renderer personalizzati forniscono un approccio potente per la personalizzazione dell'aspetto e comportamento dei controlli di xamarin. Forms. Possono essere utilizzati per lo stile piccole modifiche o sofisticati layout specifici della piattaforma e personalizzazione del comportamento.


## <a name="related-links"></a>Collegamenti correlati

- [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
