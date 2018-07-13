---
title: Introduzione al renderer personalizzati
description: Questo articolo fornisce un'introduzione ai renderer personalizzati e illustra la procedura per creare un renderer personalizzato.
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: 180a196e0b95854815c8a74ef1d2df63407dd04f
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998004"
---
# <a name="introduction-to-custom-renderers"></a>Introduzione al renderer personalizzati

_Renderer personalizzati offrono un approccio potente per la personalizzazione dell'aspetto e comportamento dei controlli di xamarin. Forms. Possono essere utilizzati per lo stile piccole modifiche o layout specifici della piattaforma sofisticati e personalizzazione del comportamento. Questo articolo fornisce un'introduzione ai renderer personalizzati e illustra la procedura per creare un renderer personalizzato._

Xamarin. Forms [controlli, layout e pagine](~/xamarin-forms/user-interface/controls/index.md) presentare un'API comune per descrivere le interfacce utente per dispositivi mobili multipiattaforma. Ogni pagina, layout e il controllo viene eseguito il rendering in modo diverso in ogni piattaforma, usando un `Renderer` classe che a sua volta crea un controllo nativo (che corrisponde alla relativa rappresentazione in xamarin. Forms), lo dispone sullo schermo e aggiunge il comportamento specificato di codice condiviso.

Gli sviluppatori possono implementare le proprie classi `Renderer` per personalizzare l'aspetto e/o il comportamento di un controllo. Renderer personalizzati per un determinato tipo può essere aggiunto al progetto di un'applicazione per personalizzare il controllo in un'unica posizione, consentendo il comportamento predefinito in altre piattaforme; o diversi renderer personalizzati possono essere aggiunti a ogni progetto di applicazione per creare un aspetto diverso in iOS, Android e Universal Windows Platform (UWP). Tuttavia, implementazione di una classe di renderer personalizzato per eseguire una personalizzazione di controlli semplici è spesso una risposta approfondita. Effetti semplificano questo processo e vengono generalmente utilizzati per lo stile piccole modifiche. Per altre informazioni, vedere [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Effetti).

## <a name="examining-why-custom-renderers-are-necessary"></a>Analisi perché renderer personalizzati sono necessari

Modifica dell'aspetto di un controllo di xamarin. Forms, senza usare un renderer personalizzato, è un processo in due passaggi che prevede la creazione di un controllo personalizzato tramite sottoclassi di utilizzo di controllo personalizzato al posto del controllo originale. Esempio di codice seguente viene illustrato un esempio di creazione di una sottoclasse di `Entry` controllo:

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

Il `MyEntry` controllo è un `Entry` consentono di controllare il `BackgroundColor` è impostato su in grigio ed è reperibile in Xaml dichiara uno spazio dei nomi per il percorso e usando il prefisso dello spazio dei nomi dell'elemento di controllo. Nell'esempio di codice riportato di seguito viene illustrato come la `MyEntry` controlli personalizzati possono essere usati da un `ContentPage`:

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

Il `local` il prefisso dello spazio dei nomi può essere qualsiasi elemento. Tuttavia, il `namespace` e `assembly` valori devono corrispondere i dettagli del controllo personalizzato. Una volta lo spazio dei nomi è dichiarato, il prefisso viene utilizzato per fare riferimento al controllo personalizzato.

> [!NOTE]
> La definizione di `xmlns` è molto più semplice nei progetti di librerie .NET Standard che progetti condivisi. Una libreria .NET Standard è compilata in un assembly in modo che risulti semplice determinare quali il `assembly=CustomRenderer` il valore deve essere. Quando si usano progetti condivisi, tutte le risorse condivise (incluso il XAML) vengono compilate in ognuno dei progetti di riferimenti, il che significa che, in caso di iOS, Android e UWP i progetti dispongono di propri *i nomi degli assembly* non è possibile scrivere il `xmlns` dichiarazione perché il valore deve essere diverso per ogni applicazione. Controlli personalizzati in XAML per i progetti condivisi richiederà ogni progetto di applicazione deve essere configurata con lo stesso nome di assembly.

Il `MyEntry` controllo personalizzato viene quindi eseguito il rendering in ogni piattaforma, con uno sfondo grigio, come illustrato negli screenshot seguenti:

![](introduction-images/screenshots.png "Controllo personalizzato di MyEntry in ogni piattaforma")

Modifica il colore di sfondo del controllo in ogni piattaforma è stata eseguita esclusivamente tramite una sottoclasse del controllo. Tuttavia, questa tecnica è limitata in ciò che è possibile ottenere perché non è possibile sfruttare i vantaggi di personalizzazioni e miglioramenti specifici della piattaforma. Quando sono necessari, è necessario implementare renderer personalizzati.

## <a name="creating-a-custom-renderer-class"></a>Creazione di una classe di Renderer personalizzato

Il processo per la creazione di una classe di renderer personalizzato è come segue:

1. Creare una sottoclasse della classe renderer che esegue il rendering del controllo nativo.
1. L'override del metodo che esegue il rendering del controllo nativo e scrittura per la logica per personalizzare il controllo. Spesso, il `OnElementChanged` metodo viene utilizzato per eseguire il rendering del controllo nativo, che viene chiamato quando viene creato il controllo di xamarin. Forms corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per il rendering del controllo di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> Per la maggior parte degli elementi di xamarin. Forms, è facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, quindi verrà utilizzato il renderer predefinito per la classe di base del controllo. Renderer personalizzati sono tuttavia necessarie in ogni progetto della piattaforma durante il rendering di un [View](xref:Xamarin.Forms.View) oppure [elemento ViewCell](xref:Xamarin.Forms.ViewCell) elemento.

Negli argomenti di questa serie fornirà troverai dimostrazioni e informazioni sul significato di questo processo per diversi elementi di xamarin. Forms.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se un controllo personalizzato è contenuto in un progetto di libreria .NET Standard che è stato aggiunto alla soluzione (vale a dire non la libreria .NET Standard creata da Visual Studio per il modello di progetto App xamarin. Forms di Mac e Visual Studio), un'eccezione può verificarsi in iOS quando è stato effettuato un tentativo di accedere al controllo personalizzato. Se si verifica questo problema può essere risolto tramite la creazione di un riferimento al controllo personalizzato dal `AppDelegate` classe:

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

In tal modo il compilatore possa riconoscere il `ClassInPCL` tipo da risolvere. In alternativa, il `Preserve` attributo può essere aggiunto al `AppDelegate` classe per ottenere lo stesso risultato:

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

Ciò consente di creare un riferimento al `ClassInPCL` tipo, che indica che è necessario in fase di esecuzione. Per altre informazioni, vedere [mantenimento codice](~/ios/deploy-test/linker.md).

## <a name="summary"></a>Riepilogo

Questo articolo è stata fornita un'introduzione ai renderer personalizzati ed è stato descritto il processo per la creazione di un renderer personalizzato. Renderer personalizzati offrono un approccio potente per la personalizzazione dell'aspetto e comportamento dei controlli di xamarin. Forms. Possono essere utilizzati per lo stile piccole modifiche o layout specifici della piattaforma sofisticati e personalizzazione del comportamento.


## <a name="related-links"></a>Collegamenti correlati

- [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
