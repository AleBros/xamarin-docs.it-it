---
title: Introduzione a C# per sviluppatori Objective-C
description: Questo documento descrive C# per sviluppatori Objective-C. Mette a confronto i due linguaggi esaminando protocolli e interfacce, categorie e metodi di estensione, framework e assembly, selettori e parametri denominati e altro ancora.
ms.prod: xamarin
ms.assetid: 00285CBD-AE5E-4126-8F22-6B231B9467EA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 6b2753b0f1a7389d62866e5c8003b439a283e5b5
ms.sourcegitcommit: 1c87135a47780f34102952d4b140850b4f08b075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536507"
---
# <a name="c-primer-for-objective-c-developers"></a>Introduzione a C# per sviluppatori Objective-C

_Xamarin.iOS consente di condividere tra diverse piattaforme il C# codice indipendente dalla piattaforma scritto in. Tuttavia, le applicazioni iOS esistenti potrebbero voler sfruttare il codice Objective-C che è già stato creato. Questo articolo funge da nozioni di fondo per gli sviluppatori Objective-C che vogliono passare a Novell e C# al linguaggio._

le applicazioni iOS e macOS sviluppate in Objective-C possono trarre vantaggio da Novell C# sfruttando in posizioni in cui il codice specifico della piattaforma non è necessario, consentendo l'uso di tale codice su dispositivi non Apple. Elementi come servizi Web, analisi JSON e XML e algoritmi personalizzati possono quindi essere usati indipendentemente dalla piattaforma.

Per sfruttare i vantaggi di Xamarin mantenendo al tempo stesso gli asset Objective-C esistenti, Xamarin può essere esposto in C# tramite la tecnologia dei binding, che rende disponibile il codice Objective-C nell'ambiente gestito di C#. Inoltre, se necessario, è anche possibile eseguire il porting del codice in C# riga per riga. In ogni caso, indipendentemente dall'approccio adottato (binding o porting), è necessaria una certa conoscenza di Objective-C e C# per sfruttare in modo efficace il codice Objective-C esistente con Xamarin.iOS.

## <a name="objective-c-interop"></a>Interoperabilità di Objective-C

Attualmente non è supportato alcun meccanismo per creare una libreria in C# con Xamarin.iOS che possa essere chiamata da Objective-C. Ciò è dovuto principalmente al fatto che, in aggiunta al binding, è anche necessario il runtime di Mono. Tuttavia, è comunque possibile creare la maggior parte della logica in Objective-C, incluse le interfacce utente. A tale scopo, eseguire il wrapping del codice Objective-C in una libreria e creare un binding a tale libreria. Xamarin.iOS è necessario per eseguire il bootstrap dell'applicazione (ovvero, deve creare il punto di ingresso `Main`). Successivamente, qualsiasi altra logica può essere in Objective-C, esposta in C# mediante il binding (o tramite P/Invoke). In questo modo, è possibile mantenere la logica specifica della piattaforma in Objective-C e sviluppare le parti indipendenti dalla piattaforma in C#.

In questo articolo sono evidenziate le principali analogie e differenze in entrambi i linguaggi, per offrire una panoramica del passaggio a C# con Xamarin.iOS, indipendentemente dal fatto che si esegua il binding a codice Objective-C esistente o il porting in C#.

Per informazioni sulla creazione di binding, vedere gli altri documenti in [Binding di Objective-C](~/ios/platform/binding-objective-c/index.md).

## <a name="language-comparison"></a>Confronto dei linguaggi

Objective-C e C# sono linguaggi molto diversi, sia sintatticamente che dal punto di vista del runtime. Objective-C è un linguaggio dinamico e usa uno schema di passaggio dei messaggi, mentre C# è tipizzato in modo statico. Per quanto riguarda la sintassi, Objective-C è come Smalltalk C# , mentre deriva gran parte della sintassi fondamentale da Java, anche se è maturata in modo da includere molte funzionalità oltre a Java negli ultimi anni.

Detto ciò, esistono diverse funzionalità dei linguaggi Objective-C e C# che hanno una funzione simile. Quando si crea un binding a codice Objective-C da C# o durante il porting di Objective-C in C#, è utile comprendere queste analogie.

### <a name="protocols-vs-interfaces"></a>Protocolli e interfacce

Sia Objective-C che C# sono linguaggi a ereditarietà singola. Tuttavia, entrambi i linguaggi supportano l'implementazione di più interfacce in una determinata classe. In Objective-C queste interfacce logiche sono denominate *protocolli*, mentre in C# sono denominate *interfacce*. A livello di implementazione, la principale differenza tra un'interfaccia C# e un protocollo Objective-C è che quest'ultimo può avere metodi facoltativi. Per altre informazioni, vedere l'articolo relativo a [eventi, delegati e protocolli](~/ios/app-fundamentals/delegates-protocols-and-events.md).

### <a name="categories-vs-extension-methods"></a>Categorie e metodi di estensione

Objective-C consente di aggiungere metodi a una classe per cui potrebbe non essere disponibile il codice di implementazione tramite le *categorie*. In C# è disponibile un concetto simile, tramite i *metodi di estensione*.

I metodi di estensione consentono di aggiungere metodi statici a una classe, dove i metodi statici in C# sono analoghi ai metodi di classe in Objective-C. Ad esempio, il codice seguente aggiunge un metodo denominato `ScrollToBottom` alla classe `UITextView`, che a sua volta è una classe gestita associata alla classe `UITextView` di Objective-C da UIKit:

```csharp
public static class UITextViewExtensions
{
    public static void ScrollToBottom (this UITextView textView)
    {
        // code to scroll textView
    }
}
```

Quindi, quando si crea un'istanza di `UITextView` nel codice, il metodo è disponibile nell'elenco di completamento automatico, come illustrato di seguito:

 ![](primer-images/01-extensionmethodintellisense.png "The method available in the autocomplete")

Quando viene chiamato il metodo di estensione, l'istanza viene passata all'argomento, come `textView` in questo esempio.

### <a name="frameworks-vs-assemblies"></a>Framework e assembly

In Objective-C vengono creati pacchetti di classi correlate in speciali directory denominate framework. In C# e .NET, invece, vengono usati gli assembly per fornire bit riutilizzabili di codice precompilato. Negli ambienti all'esterno di iOS, gli assembly contengono codice Intermediate Language (IL) compilato in modalità JIT (Just In Time) in fase di esecuzione. Tuttavia, Apple non consente l'esecuzione di codice compilato JIT nelle applicazioni iOS rilasciate nell'App Store. Pertanto, il codice C# per iOS con Xamarin è compilato in modalità AOT (Ahead Of Time), che produce un singolo file eseguibile Unix insieme ai file di metadati inclusi nel pacchetto dell'applicazione.

### <a name="selectors-vs-named-parameters"></a>Selettori e parametri denominati

I metodi di Objective-C includono intrinsecamente i nomi dei parametri nei selettori per loro natura. Ad esempio, un selettore come `AddCrayon:WithColor:` indica in modo chiaro la funzione di ogni parametro usato nel codice. C# supporta facoltativamente anche gli argomenti denominati.

Ad esempio, un codice analogo in C# con argomenti denominati sarebbe il seguente:

```csharp
AddCrayon (crayon: myCrayon, color: UIColor.Blue);
```

Sebbene sia stato aggiunto questo supporto nella versione 4.0 del linguaggio C#, in pratica non viene usato molto spesso. Se tuttavia si preferisce adottare un approccio esplicito nel codice, il supporto richiesto a tale scopo è disponibile.

### <a name="headers-and-namespaces"></a>Intestazioni e spazi dei nomi

Essendo un superset di C, Objective-C usa intestazioni per le dichiarazioni pubbliche che sono separate dai file di implementazione. In C# non vengono usati file di intestazione. A differenza di Objective-C, il codice C# è contenuto negli spazi dei nomi. Se si vuole includere codice disponibile in uno spazio dei nomi, è possibile aggiungere una direttiva using all'inizio del file di implementazione oppure qualificare il tipo con lo spazio dei nomi completo.

Ad esempio, il codice seguente include lo spazio dei nomi `UIKit`, rendendo disponibili tutte le classi nello spazio dei nomi per l'implementazione:

```csharp
using UIKit
namespace MyAppNamespace
{
    // implementation of classes
}
```

Inoltre, la parola chiave namespace nel codice precedente imposta lo spazio dei nomi usato per il file di implementazione. Se più file di implementazione condividono lo stesso spazio dei nomi, non è necessario includere lo spazio dei nomi in una direttiva using, perché è implicita.

### <a name="properties"></a>Proprietà

Sia Objective-C che C# prevedono il concetto di proprietà per fornire un'astrazione di alto livello per i metodi delle funzioni di accesso. In Objective-C viene usata la direttiva del compilatore @property per generare in modo efficace i metodi delle funzioni di accesso. Al contrario, C# include il supporto per le proprietà all'interno del linguaggio stesso. Una proprietà C# può essere implementata usando un stile più lungo che accede a un campo sottostante oppure tramite una sintassi automatica più breve per le proprietà, come illustrato negli esempi seguenti:

```csharp
// automatic property syntax
public string Name { get; set; }

// property implemented with a backing field
string address;
public string Address {
    get {
        // could add additional code here
        return address;
    }
    set {
        address = value;
    }
}
```

### <a name="static-keyword"></a>Parola chiave static

La parola chiave *static* ha un significato molto diverso in Objective-C e C#. Le funzioni statiche di Objective-C sono usate per limitare l'ambito di una funzione al file corrente. In C#, tuttavia, l'ambito viene gestito tramite le parole chiave *public*, *private* e *internal*.

Quando la parola chiave static viene applicata a una variabile in Objective-C, la variabile mantiene il relativo valore durante le chiamate di funzione.

Anche C# dispone di una parola chiave static. Quando viene applicata a un metodo, svolge in modo efficace la stessa funzione del modificatore `+` in Objective-C. In particolare, crea un metodo di classe. Analogamente, se applicato ad altri costrutti come campi, proprietà ed eventi, rende tali elementi parte del tipo in cui vengono dichiarati, anziché di qualsiasi istanza del tipo. È inoltre possibile creare una classe statica, in cui anche tutti i metodi definiti nella classe devono essere statici.

### <a name="nsarray-vs-list-initialization"></a>Inizializzazione di NSArray e di elenco

Objective-C include ora la sintassi dei valori letterali per l'uso con `NSArray`, che rende più semplice l'inizializzazione. C# tuttavia dispone di un tipo più completo denominato `List`, che è *generic*, vale a dire che il tipo contenuto nell'elenco può essere fornito dal codice che crea l'elenco, ad esempio i modelli in C++. Inoltre, gli elenchi supportano la sintassi di inizializzazione automatica, come illustrato di seguito:

```csharp
MyClass object1 = new MyClass ();
MyClass object2 = new MyClass ();
List<MyClass> myList = new List<MyClass>{ object1, object2 };
```

### <a name="blocks-vs-lambda-expressions"></a>Blocchi e espressioni lambda

Objective-C usa i *blocchi* per creare le chiusure, in cui è possibile creare una funzione inline che è in grado di usare lo stato in cui è inclusa. In C# è presente un concetto simile tramite l'uso delle espressioni lambda. In C# le espressioni lambda vengono create con l'operatore `=>`, come illustrato di seguito:

```csharp
(args) => {
    //  implementation code
};
```

Per altre informazioni sulle espressioni lambda, vedere la [Guida per programmatori C#](https://msdn.microsoft.com/library/vstudio/bb397687.aspx) di Microsoft.

## <a name="summary"></a>Riepilogo

In questo articolo sono state messe a confronto diverse funzionalità dei linguaggi Objective-C e C#. In alcuni casi, sono state descritte le caratteristiche simili in entrambi i linguaggi, come i blocchi e le espressioni lambda o le categorie e i metodi di estensione. Sono state inoltre illustrate le differenze tra i linguaggi, ad esempio per quanto riguarda gli spazi dei nomi in C# e il significato della parola chiave static.
