---
title: Introduzione ai renderer personalizzati
description: Questo articolo offre un'introduzione ai renderer personalizzati e illustra la procedura di creazione di un renderer personalizzato.
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: ad2868a82f662f45066a6111a1dd3bd2aacad671
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70771874"
---
# <a name="introduction-to-custom-renderers"></a>Introduzione ai renderer personalizzati

_I renderer personalizzati offrono un approccio efficace per personalizzare l'aspetto e il comportamento dei controlli Xamarin.Forms. Possono essere usati per le modifiche di stile piccolo o per una personalizzazione sofisticata del layout e del comportamento specifici della piattaforma. Questo articolo fornisce un'introduzione ai renderer personalizzati e descrive il processo di creazione di un renderer personalizzato._

Le [pagine, i layout e i controlli](~/xamarin-forms/user-interface/controls/index.md) Xamarin.Forms presentano un'API comune per descrivere le interfacce utente per dispositivi mobili multipiattaforma. Il rendering di ogni pagina, layout e controllo viene eseguito in modo diverso su ogni piattaforma usando una classe `Renderer`, che a sua volta crea un controllo nativo (corrispondente alla rappresentazione Xamarin.Forms), lo dispone sullo schermo e aggiunge il comportamento specificato nel codice condiviso.

Gli sviluppatori possono implementare le proprie classi `Renderer` per personalizzare l'aspetto e/o il comportamento di un controllo. I renderer personalizzati per un determinato tipo possono essere aggiunti al progetto di un'applicazione per personalizzare il controllo in un'unica posizione, consentendo il comportamento predefinito in altre piattaforme; in alternativa, è possibile aggiungere diversi renderer personalizzati a ogni progetto di applicazione per creare un aspetto diverso in iOS, Android e Universal Windows Platform (UWP). Tuttavia, l'implementazione di una classe di renderer personalizzata per eseguire una personalizzazione di controlli semplici è spesso una risposta complessa. Gli effetti semplificano questo processo e in genere vengono usati per apportare piccole modifiche allo stile. Per altre informazioni, vedere [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Effetti).

## <a name="examining-why-custom-renderers-are-necessary"></a>Perché i renderer personalizzati sono necessari

La modifica dell'aspetto di un controllo Xamarin.Forms, senza usare un renderer personalizzato, è un processo in due passaggi che prevede la creazione di un controllo personalizzato tramite la creazione di sottoclassi e quindi l'uso del controllo personalizzato al posto di quello originale. Il codice seguente illustra un esempio di creazione di sottoclassi del controllo `Entry`:

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

Il controllo `MyEntry` è un controllo `Entry` dove `BackgroundColor` è impostato su grigio e vi si può fare riferimento in Xaml dichiarando uno spazio dei nomi per il percorso e usando il prefisso dello spazio dei nomi nell'elemento di controllo. Nell'esempio di codice riportato di seguito viene illustrato come il controllo personalizzato `MyEntry` può essere usato da un elemento `ContentPage`:

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

Il prefisso dello spazio dei nomi `local` può essere qualsiasi elemento. I valori `namespace` e `assembly` devono tuttavia corrispondere ai dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene usato per fare riferimento al controllo personalizzato.

> [!NOTE]
> La definizione di `xmlns` è molto più semplice nei progetti di librerie .NET Standard che nei progetti condivisi. Una libreria .NET Standard è compilata in un assembly in modo che risulti semplice determinare quale debba essere il valore di `assembly=CustomRenderer`. Quando si usano i progetti condivisi, tutti gli asset condivisi (incluso lo XAML) vengono compilati in ognuno dei progetti di riferimento, il che significa che, se i progetti iOS, Android e UWP hanno i propri *nomi degli assembly* non è possibile scrivere la dichiarazione `xmlns` perché il valore deve essere diverso per ogni applicazione. I controlli personalizzati in XAML per i progetti condivisi richiedono che ogni progetto dell'applicazione venga configurato con lo stesso nome assembly.

Il controllo personalizzato `MyEntry` viene quindi sottoposto a rendering in ogni piattaforma, con uno sfondo grigio, come illustrato negli screenshot seguenti:

![](introduction-images/screenshots.png "MyEntry Custom Control on each Platform")

La modifica del colore di sfondo del controllo in ogni piattaforma è stata eseguita esclusivamente tramite la creazione di una sottoclasse del controllo. Questa tecnica consente tuttavia di ottenere risultati limitati, poiché non è possibile sfruttare i vantaggi di personalizzazioni e miglioramenti specifici della piattaforma. Quando sono obbligatori, è necessario implementare renderer personalizzati.

## <a name="creating-a-custom-renderer-class"></a>Creazione di una classe di renderer personalizzata

Di seguito è illustrato il processo di creazione di una classe di renderer personalizzata:

1. Creare una sottoclasse della classe di renderer che esegue il rendering del controllo nativo.
1. Eseguire l'override del metodo che esegue il rendering del controllo nativo e scrivere la logica per personalizzare il controllo. Il metodo `OnElementChanged` viene spesso usato per eseguire il rendering del controllo nativo, che viene chiamato quando si crea il controllo Xamarin.Forms corrispondente.
1. Aggiungere un attributo `ExportRenderer` alla classe renderer personalizzato per specificare che verrà usata per eseguire il rendering del controllo Xamarin.Forms. L'attributo viene usato per registrare il renderer personalizzato in Xamarin.Forms.

> [!NOTE]
> Per la maggior parte degli elementi di Xamarin.Forms, l'indicazione di un renderer personalizzato in ogni progetto della piattaforma è facoltativa. Se un renderer personalizzato non è registrato, verrà usato il renderer predefinito per la classe di base del controllo. I renderer personalizzati sono tuttavia necessari in ogni progetto della piattaforma quando si esegue il rendering di un elemento [View](xref:Xamarin.Forms.View) o [ ViewCell](xref:Xamarin.Forms.ViewCell).

Negli argomenti di questa sezione vengono presentate dimostrazioni e spiegazioni di questo processo per diversi elementi di Xamarin.Forms.

## <a name="troubleshooting"></a>Troubleshooting

Se un controllo personalizzato è contenuto in un progetto di libreria .NET Standard che è stato aggiunto alla soluzione (vale a dire non la libreria .NET Standard creata dal modello di progetto Visual Studio per Mac/app Xamarin.Forms in Visual Studio), può verificarsi un'eccezione in iOS quando si tenta di accedere al controllo personalizzato. Se si verifica questo problema, può essere risolto tramite la creazione di un riferimento al controllo personalizzato dalla classe `AppDelegate`:

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

In tal modo il compilatore viene forzato al riconoscimento del tipo `ClassInPCL` risolvendolo. In alternativa, per ottenere lo stesso risultato è possibile aggiungere l'attributo `Preserve` alla classe `AppDelegate`:

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

Ciò consente di creare un riferimento al tipo `ClassInPCL`, che indica che è necessario in fase di esecuzione. Per altre informazioni, vedere [Mantenimento del codice](~/ios/deploy-test/linker.md).

## <a name="summary"></a>Riepilogo

Questo articolo ha offerto un'introduzione ai renderer personalizzati e illustrato la procedura di creazione di un renderer personalizzato. I renderer personalizzati offrono un approccio efficace alla personalizzazione dell'aspetto e del comportamento dei controlli di Xamarin.Forms. Possono essere usati per apportare piccole modifiche allo stile o per la personalizzazione di layout e comportamenti sofisticati specifici di una piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
