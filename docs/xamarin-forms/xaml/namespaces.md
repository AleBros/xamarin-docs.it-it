---
title: Spazi dei nomi XAML inXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7f35342134767ccdadfab086bfa14f6b610b325d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130377"
---
# <a name="xaml-namespaces-in-xamarinforms"></a>Spazi dei nomi XAML inXamarin.Forms

_XAML usa l'attributo XML xmlns per le dichiarazioni dello spazio dei nomi. In questo articolo viene introdotta la sintassi dello spazio dei nomi XAML e viene illustrato come dichiarare uno spazio dei nomi XAML per accedere a un tipo._

## <a name="overview"></a>Panoramica

Sono presenti due dichiarazioni dello spazio dei nomi XAML che sono sempre all'interno dell'elemento radice di un file XAML. Il primo definisce lo spazio dei nomi predefinito, come illustrato nell'esempio di codice XAML seguente:

```xaml
xmlns="http://xamarin.com/schemas/2014/forms"
```

Lo spazio dei nomi predefinito specifica che gli elementi definiti all'interno del file XAML senza prefisso fanno riferimento alle Xamarin.Forms classi, ad esempio [`ContentPage`](xref:Xamarin.Forms.ContentPage) .

La seconda dichiarazione dello spazio dei nomi usa il `x` prefisso, come illustrato nell'esempio di codice XAML seguente:

```xaml
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML usa i prefissi per dichiarare gli spazi dei nomi non predefiniti, con il prefisso usato per fare riferimento ai tipi all'interno dello spazio dei nomi. La `x` dichiarazione dello spazio dei nomi specifica che gli elementi definiti all'interno del codice XAML con prefisso `x` vengono usati per gli elementi e gli attributi intrinseci a XAML (in particolare la specifica XAML 2009).

Nella tabella seguente vengono descritti gli `x` attributi dello spazio dei nomi supportati da Xamarin.Forms :

|Costrutto|Descrizione|
|--- |--- |
|`x:Arguments`|Specifica gli argomenti del costruttore per un costruttore non predefinito o per una dichiarazione dell'oggetto Metodo Factory.|
|`x:Class`|Specifica lo spazio dei nomi e il nome della classe per una classe definita in XAML. Il nome della classe deve corrispondere al nome della classe del file code-behind. Si noti che questo costrutto può essere visualizzato solo nell'elemento radice di un file XAML.|
|`x:DataType`|Specifica il tipo dell'oggetto a cui verrà associato l'elemento XAML e gli elementi figlio.|
|`x:FactoryMethod`|Specifica un metodo factory che può essere utilizzato per inizializzare un oggetto.|
|`x:FieldModifier`|Specifica il livello di accesso per i campi generati per gli elementi XAML denominati.|
|`x:Key`|Specifica una chiave univoca definita dall'utente per ogni risorsa in un oggetto `ResourceDictionary` . Il valore della chiave viene usato per recuperare la risorsa XAML e viene in genere usato come argomento per l' `StaticResource` estensione di markup.|
|`x:Name`|Specifica il nome di un oggetto di runtime per l'elemento XAML. `x:Name`L'impostazione è simile alla dichiarazione di una variabile nel codice.|
|`x:TypeArguments`|Specifica gli argomenti di tipo generico per il costruttore di un tipo generico.|

Per ulteriori informazioni sull' `x:DataType` attributo, vedere [Binding compilati](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md). Per ulteriori informazioni sull' `x:FieldModifier` attributo, vedere [modificatori di campo](~/xamarin-forms/xaml/field-modifiers.md). Per ulteriori informazioni sugli `x:Arguments` attributi e `x:FactoryMethod` , vedere [passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md). Per ulteriori informazioni sull' `x:TypeArguments` attributo, vedere [generics in XAML con Xamarin.Forms ](generics.md).

> [!NOTE]
> Oltre agli attributi dello spazio dei nomi elencati sopra, Xamarin.Forms include anche le estensioni di markup che possono essere utilizzate tramite il `x` prefisso dello spazio dei nomi. Per ulteriori informazioni, vedere [utilizzo delle estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

In XAML, le dichiarazioni dello spazio dei nomi ereditano dall'elemento padre all'elemento figlio. Pertanto, quando si definisce uno spazio dei nomi nell'elemento radice di un file XAML, tutti gli elementi all'interno del file ereditano la dichiarazione dello spazio dei nomi.

## <a name="declaring-namespaces-for-types"></a>Dichiarazione di spazi dei nomi per i tipi

È possibile fare riferimento ai tipi in XAML dichiarando uno spazio dei nomi XAML con un prefisso, con la dichiarazione dello spazio dei nomi che specifica il nome dello spazio dei nomi CLR (Common Language Runtime) e, facoltativamente, un nome di assembly. Questa operazione viene eseguita definendo i valori per le parole chiave seguenti all'interno della dichiarazione dello spazio dei nomi:

- **clr-namespace:** o **using:** -lo spazio dei nomi CLR dichiarato all'interno dell'assembly che contiene i tipi da esporre come elementi XAML. Questa parola chiave è obbligatoria.
- **assembly =** : assembly contenente lo spazio dei nomi CLR a cui si fa riferimento. Questo valore è il nome dell'assembly, senza l'estensione di file. Il percorso dell'assembly deve essere definito come un riferimento nel file di progetto che contiene il file XAML che fa riferimento all'assembly. Questa parola chiave può essere omessa se il valore **clr-namespace** si trova nello stesso assembly del codice dell'applicazione che fa riferimento ai tipi.

Si noti che il carattere che separa `clr-namespace` il `using` token o dal relativo valore è costituito da due punti, mentre il carattere che separa il `assembly` token dal relativo valore è un segno di uguale. Il carattere da utilizzare tra i due token è un punto e virgola.

Nell'esempio di codice seguente viene illustrata una dichiarazione dello spazio dei nomi XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:HelloWorld" ...>
  ...
</ContentPage>
```

In alternativa, può essere scritto come segue:

```xaml
<ContentPage ... xmlns:local="using:HelloWorld" ...>
  ...
</ContentPage>
```

Il `local` prefisso è una convenzione utilizzata per indicare che i tipi nello spazio dei nomi sono locali per l'applicazione. In alternativa, se i tipi si trovano in un assembly diverso, il nome dell'assembly deve essere definito anche nella dichiarazione dello spazio dei nomi, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

Il prefisso dello spazio dei nomi viene quindi specificato quando si dichiara un'istanza di un tipo da uno spazio dei nomi importato, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

Per informazioni sulla definizione di uno schema dello spazio dei nomi personalizzato, vedere [schemi dello spazio dei nomi XAML personalizzato](custom-namespace-schemas.md).

## <a name="summary"></a>Riepilogo

In questo articolo è stata introdotta la sintassi dello spazio dei nomi XAML e viene illustrato come dichiarare uno spazio dei nomi XAML per accedere a un tipo. XAML usa l' `xmlns` attributo XML per le dichiarazioni dello spazio dei nomi e i tipi a cui è possibile fare riferimento in XAML dichiarando uno spazio dei nomi XAML con un prefisso.

## <a name="related-links"></a>Collegamenti correlati

- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
- [Generics in XAML conXamarin.Forms](generics.md)
