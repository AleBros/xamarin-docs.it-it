---
title: Spazi dei nomi XAML in xamarin. Forms
description: XAML Usa l'attributo XML xmlns per le dichiarazioni dello spazio dei nomi. Questo articolo presenta la sintassi dello spazio dei nomi XAML e viene illustrato come dichiarare uno spazio dei nomi XAML per un tipo di accesso.
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 08/21/2018
ms.openlocfilehash: a83e31cd117689e3af2f5357331408de55edaecf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102750"
---
# <a name="xaml-namespaces-in-xamarinforms"></a>Spazi dei nomi XAML in xamarin. Forms

_XAML Usa l'attributo XML xmlns per le dichiarazioni dello spazio dei nomi. Questo articolo presenta la sintassi dello spazio dei nomi XAML e viene illustrato come dichiarare uno spazio dei nomi XAML per un tipo di accesso._

## <a name="overview"></a>Panoramica

Esistono due dichiarazioni dello spazio dei nomi XAML che sono sempre all'interno dell'elemento radice di un file XAML. Il primo definisce lo spazio dei nomi predefinito, come illustrato nell'esempio di codice XAML seguente:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
```

Spazio dei nomi predefinito specifica che gli elementi definiti all'interno del file XAML senza il prefisso fa riferimento alle classi di xamarin. Forms, ad esempio [ `ContentPage` ](xref:Xamarin.Forms.ContentPage).

La seconda dichiarazione dello spazio dei nomi viene utilizzato il `x` prefisso, come illustrato nell'esempio di codice XAML seguente:

```csharp
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML vengono utilizzati prefissi per dichiarare spazi dei nomi non predefinito, con il prefisso viene usato quando si fa riferimento ai tipi nello spazio dei nomi. Il `x` dichiarazione dello spazio dei nomi specifica che gli elementi definiti all'interno di XAML con il prefisso `x` vengono utilizzati per gli elementi e attributi intrinseci XAML (in particolare la specifica di XAML 2009).

La tabella seguente descrive il `x` attributi dello spazio dei nomi supportati da xamarin. Forms:

|Costrutto|Descrizione|
|--- |--- |
|`x:Arguments`|Specifica gli argomenti del costruttore per un costruttore non predefinito o per una dichiarazione dell'oggetto metodo factory.|
|`x:Class`|Specifica il nome dello spazio dei nomi e classe per una classe definita in XAML. Il nome della classe deve corrispondere al nome di classe del file code-behind. Si noti che questo costrutto può essere visualizzato solo nell'elemento radice di un file XAML.|
|`x:DataType`|Specifica il tipo dell'oggetto che verranno associato l'elemento XAML e relativi elementi figlio, a.|
|`x:FactoryMethod`|Specifica un metodo factory che può essere utilizzato per inizializzare un oggetto.|
|`x:FieldModifier`|Specifica il livello di accesso per i campi generati per gli elementi denominati XAML.|
|`x:Key`|Specifica una chiave univoca definito dall'utente per ogni risorsa in un `ResourceDictionary`. Il valore della chiave viene usato per recuperare la risorsa XAML e viene in genere utilizzato come argomento per il `StaticResource` estensione di markup.|
|`x:Name`|Specifica un nome di oggetto di runtime per l'elemento XAML. Impostazione `x:Name` è simile alla dichiarazione di una variabile nel codice.|
|`x:TypeArguments`|Specifica gli argomenti di tipo generico per il costruttore di un tipo generico.|

Per altre informazioni sul `x:DataType` dell'attributo, vedere [associazioni compilate](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md). Per altre informazioni sul `x:FieldModifier` dell'attributo, vedere [modificatori di campo](~/xamarin-forms/xaml/field-modifiers.md). Per altre informazioni sul `x:Arguments`, `x:FactoryMethod`, e `x:TypeArguments` attributi, vedere [passando gli argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md).

In XAML, le dichiarazioni dello spazio dei nomi ereditano da elemento padre all'elemento figlio. Pertanto, quando si definisce uno spazio dei nomi nell'elemento radice di un file XAML, tutti gli elementi all'interno del file ereditano la dichiarazione dello spazio dei nomi.

## <a name="declaring-namespaces-for-types"></a>Dichiarazione di spazi dei nomi per tipi

I tipi sono reperibile in XAML con la dichiarazione di uno spazio dei nomi XAML con un prefisso con la dichiarazione dello spazio dei nomi specificando lo spazio dei nomi Common Language Runtime (CLR) e, facoltativamente, un nome di assembly. Questo risultato viene ottenuto definendo i valori per le parole chiave seguenti all'interno della dichiarazione dello spazio dei nomi:

- **CLR-namespace:** oppure **uso:** : lo spazio dei nomi CLR dichiarato nell'assembly che contiene i tipi da esporre come elementi XAML. Questa parola chiave è obbligatoria.
- **assembly =** : l'assembly contenente lo spazio dei nomi CLR cui viene fatto riferimento. Questo valore è il nome dell'assembly, senza l'estensione di file. Il percorso dell'assembly deve essere stabilito come riferimento nel file di progetto che contiene il file XAML che fa riferimento all'assembly. Questa parola chiave può essere omessa se la **clr-namespace** valore è all'interno dell'assembly stesso come il codice dell'applicazione che fa riferimento ai tipi.

Si noti che il carattere che separa le `clr-namespace` oppure `using` token dal suo valore sono un carattere due punti, mentre il carattere che separa il `assembly` token dal suo valore è un segno di uguale. Il carattere da utilizzare tra i due token è un punto e virgola.

Esempio di codice seguente mostra una dichiarazione dello spazio dei nomi XAML:

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

Il `local` prefisso è una convenzione utilizzata per indicare che i tipi nello spazio dei nomi sono locali dell'applicazione. In alternativa, se i tipi sono in un assembly diverso, il nome dell'assembly deve anche essere definito nella dichiarazione dello spazio dei nomi, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

Il prefisso dello spazio dei nomi viene specificato quando si dichiara un'istanza di un tipo da uno spazio dei nomi importato, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

## <a name="summary"></a>Riepilogo

Questo articolo introdotto la sintassi dello spazio dei nomi XAML e illustrato come dichiarare uno spazio dei nomi XAML per un tipo di accesso. XAML Usa le `xmlns` attributo XML per le dichiarazioni dello spazio dei nomi e tipi è reperibile in XAML con la dichiarazione di uno spazio dei nomi XAML con un prefisso.


## <a name="related-links"></a>Collegamenti correlati

- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
