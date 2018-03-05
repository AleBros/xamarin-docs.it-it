---
title: Spazi dei nomi XAML
description: XAML Usa l'attributo XML xmlns per le dichiarazioni dello spazio dei nomi. In questo articolo presenta la sintassi dello spazio dei nomi XAML e viene illustrato come dichiarare uno spazio dei nomi XAML per un tipo di accesso.
ms.topic: article
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 07/10/2017
ms.openlocfilehash: b0afba90dab5cba4bad385f8d6447d8b83c1de3d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-namespaces"></a>Spazi dei nomi XAML

_XAML Usa l'attributo XML xmlns per le dichiarazioni dello spazio dei nomi. In questo articolo presenta la sintassi dello spazio dei nomi XAML e viene illustrato come dichiarare uno spazio dei nomi XAML per un tipo di accesso._

## <a name="overview"></a>Panoramica

Esistono due dichiarazioni dello spazio dei nomi XAML che sono sempre all'interno dell'elemento radice di un file XAML. Il primo definisce lo spazio dei nomi predefinito, come illustrato nell'esempio di codice XAML seguente:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
```

Spazio dei nomi predefinito specifica che gli elementi definiti all'interno del file XAML senza il prefisso fanno riferimento alle classi di xamarin. Forms, ad esempio [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/).

La seconda dichiarazione dello spazio dei nomi viene utilizzato il `x` prefisso, come illustrato nell'esempio di codice XAML seguente:

```csharp
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML Usa i prefissi per dichiarare spazi dei nomi non predefinito, con il prefisso utilizzato per fare riferimento a tipi nello spazio dei nomi. Il `x` dichiarazione dello spazio dei nomi specifica che gli elementi definiti in XAML con il prefisso `x` vengono utilizzati per gli elementi e attributi intrinseci di XAML (in particolare la specifica di XAML 2009).

Nella tabella seguente vengono illustrati il `x` attributi dello spazio dei nomi supportati da xamarin. Forms:

<table>
 <thead>
   <tr>
     <td><strong>Costrutto</strong></td>
     <td><strong>Descrizione</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td><code>x:Arguments</code></td>
     <td>Specifica gli argomenti del costruttore per un costruttore non predefinito, o per una dichiarazione di oggetto del metodo factory.</td>
   </tr>
   <tr>
     <td><code>x:Class</code></td>
     <td>Specifica lo spazio dei nomi e il nome di una classe definita in XAML. Il nome della classe deve corrispondere al nome di classe di file code-behind. Si noti che questo costrutto può apparire solo nell'elemento radice di un file XAML.</td>
   </tr>
   <tr>
     <td><code>x:FactoryMethod</code></td>
     <td>Specifica un metodo factory che può essere utilizzato per inizializzare un oggetto.</td>
   </tr>
   <tr>
     <td><code>x:Key</code></td>
     <td>Specifica una chiave univoca definito dall'utente per ogni risorsa in un <code>ResourceDictionary</code>. Il valore della chiave viene utilizzato per recuperare la risorsa XAML e viene in genere utilizzato come argomento per il <code>StaticResource</code> estensione di markup.</td>
   </tr>
   <tr>
     <td><code>x:Name</code></td>
     <td>Specifica il nome di un oggetto di runtime per l'elemento XAML. Impostazione <code>x:Name</code> è simile alla dichiarazione di una variabile nel codice.</td>
   </tr>
   <tr>
     <td><code>x:TypeArguments</code></td>
     <td>Specifica gli argomenti di tipo generico per il costruttore di un tipo generico.</td>
   </tr>
 </tbody>
</table>

Per ulteriori informazioni sul `x:Arguments`, `x:FactoryMethod`, e `x:TypeArguments` gli attributi, vedere [il passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md).

In XAML, le dichiarazioni dello spazio dei nomi ereditano da elemento padre per l'elemento figlio. Pertanto, quando si definisce uno spazio dei nomi nell'elemento radice di un file XAML, tutti gli elementi all'interno del file ereditano la dichiarazione dello spazio dei nomi.

## <a name="declaring-namespaces-for-types"></a>Dichiarazione di spazi dei nomi per tipi

Fare riferimento a tipi in XAML mediante la dichiarazione di uno spazio dei nomi XAML con un prefisso, con la dichiarazione dello spazio dei nomi specificando il nome dello spazio dei nomi Common Language Runtime (CLR) e, facoltativamente, un nome di assembly. Questo risultato viene ottenuto mediante la definizione di valori per le seguenti parole chiave all'interno della dichiarazione dello spazio dei nomi:

- **CLR-namespace:** o **utilizzo:** : lo spazio dei nomi CLR dichiarato all'interno dell'assembly che contiene i tipi da esporre come elementi XAML. Questa parola chiave è obbligatoria.
- **assembly =** : l'assembly contenente lo spazio dei nomi CLR a cui fa riferimento. Questo valore è il nome dell'assembly, senza l'estensione di file. Il percorso dell'assembly deve essere stabilito come riferimento nel file di progetto che contiene il file XAML che fa riferimento l'assembly. Questa parola chiave può essere omesso se la **clr-namespace** valore è incluso nello stesso assembly del codice dell'applicazione che fa riferimento ai tipi.

Si noti che il carattere che separa il `clr-namespace` o `using` token rispetto al valore sono un carattere due punti, mentre il carattere che separa il `assembly` token dal relativo valore è un segno di uguale. Il carattere da utilizzare tra i due token è un punto e virgola.

Esempio di codice seguente viene illustrata una dichiarazione dello spazio dei nomi XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:HelloWorld" ...>
  ...
</ContentPage>
```

In alternativa, questo può essere scritto come:

```xaml
<ContentPage ... xmlns:local="using:HelloWorld" ...>
  ...
</ContentPage>
```

Il `local` prefisso è una convenzione usata per indicare che i tipi nello spazio dei nomi sono locali dell'applicazione. In alternativa, se i tipi sono in un assembly diverso, il nome dell'assembly deve anche essere definito nella dichiarazione dello spazio dei nomi, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

Il prefisso dello spazio dei nomi viene quindi specificato quando la dichiarazione di un'istanza di un tipo da uno spazio dei nomi importato, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

## <a name="summary"></a>Riepilogo

In questo articolo introdotto la sintassi dello spazio dei nomi XAML e illustrato come dichiarare uno spazio dei nomi XAML per un tipo di accesso. XAML Usa il `xmlns` attributo XML per le dichiarazioni dello spazio dei nomi e i tipi può fare riferimento in XAML dichiara uno spazio dei nomi XAML con un prefisso.


## <a name="related-links"></a>Collegamenti correlati

- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
