---
title: Data binding e la generazione di codice chiave-valore
description: In questo articolo viene illustrato l'utilizzo chiave-valore di codifica e l'analisi per consentire il data binding a elementi dell'interfaccia utente in interfaccia generatore del Xcode chiave-valore.
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 48ee5d4e4a0a53de49fbba46d79424e03af6fe5c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="data-binding-and-key-value-coding"></a>Data binding e la generazione di codice chiave-valore

_In questo articolo viene illustrato l'utilizzo chiave-valore di codifica e l'analisi per consentire il data binding a elementi dell'interfaccia utente in interfaccia generatore del Xcode chiave-valore._

## <a name="overview"></a>Panoramica

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere alla stessa chiave-valore di codifica e le tecniche di associazione di dati che uno sviluppatore che lavora *Objective-C* e *Xcode* does. Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare del Xcode _generatore interfaccia_ per associare i dati con elementi dell'interfaccia utente anziché scrivere codice.

Tramite chiave-valore di codifica di data binding e le tecniche Xamarin.Mac nell'applicazione in uso, è possibile ridurre notevolmente la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È inoltre il vantaggio di separazione ulteriormente i dati di backup (_modello di dati_) la parte anteriore terminare l'interfaccia utente (_Model-View-Controller_), i punti iniziali per più semplici da gestire, più flessibile dell'applicazione progettazione.

[![Un esempio di app in esecuzione](databinding-images/intro01.png "un esempio di app in esecuzione")](databinding-images/intro01-large.png#lightbox)

In questo articolo verranno descritte le nozioni di base dell'utilizzo chiave-valore di codifica e l'associazione di dati in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile dare un'occhiata il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` gli attributi utilizzato per associare le classi c# Objective-C e agli oggetti dell'interfaccia utente gli elementi.

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>Che cos'è la generazione di codice chiave-valore

Chiave-valore di codifica (i KVM) è un meccanismo per l'accesso a proprietà di un oggetto indirettamente, utilizzando le chiavi (stringhe di formattazione speciale) per identificare le proprietà anziché accedervi tramite le variabili di istanza o metodi di accesso (`get/set`). Implementando chiave-valore codifica compatibile con le funzioni di accesso nell'applicazione Xamarin.Mac, è possibile accedere ad altre funzionalità macOS (precedentemente noto come OS X), ad esempio chiave-valore osservazione (KVO), associazione dati, dati principali, le associazioni Cocoa e scriptability.

Tramite chiave-valore di codifica di data binding e le tecniche Xamarin.Mac nell'applicazione in uso, è possibile ridurre notevolmente la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È inoltre il vantaggio di separazione ulteriormente i dati di backup (_modello di dati_) la parte anteriore terminare l'interfaccia utente (_Model-View-Controller_), i punti iniziali per più semplici da gestire, più flessibile dell'applicazione progettazione. 

Ad esempio, verrà ora esaminata la seguente definizione di classe di un oggetto conformo i KVM:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

Prima di tutto, il `[Register("PersonModel")]` attributo registra la classe e la espone a Objective-C. Quindi, la classe deve ereditare da `NSObject` (o una sottoclasse che eredita da `NSObject`), questo aggiunge diverse metodo che consentono alla classe per essere compatibili con i KVM di base. Successivamente, il `[Export("Name")]` attributo espone il `Name` proprietà e definisce il valore di chiave che verrà successivamente utilizzato per accedere alla proprietà tramite i KVM e KVO tecniche. 

Infine, per poter essere osservata chiave-valore diventa il valore della proprietà, la funzione di accesso deve eseguire il wrapping di modifiche al valore in `WillChangeValue` e `DidChangeValue` chiamate al metodo (che specifica la stessa chiave come la `Export` attributo).  Ad esempio:

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

Questo passaggio è _molto_ importante per il data binding in Xcode del generatore di interfaccia (come verrà illustrato più avanti in questo articolo).

Per ulteriori informazioni, vedere Apple [chiave-valore di codifica Guida per programmatori](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html).

### <a name="keys-and-key-paths"></a>Le chiavi e i percorsi delle chiavi

Oggetto _chiave_ è una stringa che identifica una proprietà specifica di un oggetto. In genere, una chiave corrisponde al nome di un metodo della funzione di accesso in un valore di chiave di codifica di oggetti compatibili. Chiavi devono utilizzare la codifica ASCII, in genere iniziano con una lettera minuscola e non può contenere spazi vuoti. L'esempio precedente, supponendo `Name` può essere un valore di chiave di `Name` proprietà del `PersonModel` classe. La chiave e il nome della proprietà che espongono non deve essere lo stesso, ma nella maggior parte dei casi.

Oggetto _il percorso della chiave_ è una stringa di punto separate le chiavi usate per specificare una gerarchia di proprietà dell'oggetto per attraversare. La proprietà della chiave del primo nella sequenza è relativo ricevitore e ogni chiave successivi viene valutata rispetto al valore della proprietà precedente. Nello stesso modo utilizzare la notazione per attraversare un oggetto e le relative proprietà in una classe c#.

Ad esempio, se è stato espanso il `PersonModel` classe e aggiungere `Child` proprietà:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        private PersonModel _child = new PersonModel();
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        [Export("Child")]
        public PersonModel Child {
            get { return _child; }
            set {
                WillChangeValue ("Child");
                _child = value;
                DidChangeValue ("Child");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

Il percorso della chiave per il nome del bambino sarebbe `self.Child.Name` o semplicemente `Child.Name` (basato su come è stato utilizzato il valore chiave).

### <a name="getting-values-using-key-value-coding"></a>Recupero di valori mediante la generazione di codice chiave-valore

Il `ValueForKey` metodo restituisce il valore per la chiave specificata (come un `NSString`), relativo all'istanza della classe i KVM che riceve la richiesta. Ad esempio, se `Person` è un'istanza di `PersonModel` classe definita in precedenza:

```csharp
// Read value 
var name = Person.ValueForKey (new NSString("Name"));
```

Verrà restituito il valore di `Name` proprietà per l'istanza di `PersonModel`. 

### <a name="setting-values-using-key-value-coding"></a>Impostazione dei valori utilizzando la codifica chiave-valore

Analogamente, il `SetValueForKey` impostare il valore per la chiave specificata (come un `NSString`), relativo all'istanza della classe i KVM che riceve la richiesta. Anche in questo caso utilizzando un'istanza di `PersonModel` classe, come illustrato di seguito:

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

Modificare il valore del `Name` proprietà `Jane Doe`.

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>Osservare le modifiche dei valori

Utilizza chiave-valore osservazione (KVO), è possibile associare un osservatore a una chiave specifica di una classe conforme i KVM e ricevere una notifica ogni volta che viene modificato il valore per la chiave (utilizzando le tecniche i KVM o direttamente l'accesso alla proprietà specificata nel codice c#). Ad esempio:

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

A questo punto, ogni volta che il `Name` proprietà del `Person` istanza la `PersonModel` classe viene modificata, il nuovo valore viene scritto nella console. 

Per ulteriori informazioni, vedere Apple [Introduzione alla Guida per programmatori osservazione chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i).

## <a name="data-binding"></a>Associazione dati

Nelle sezioni seguenti verranno Mostra come è possibile utilizzare una chiave-valore di codifica e osservazione classe conforme chiave-valore per associare dati a elementi dell'interfaccia utente in Generatore del Xcode di interfaccia, anziché durante la lettura e scrittura di valori usando codice c#. In questo modo si separa il _modello di dati_ delle visualizzazioni che vengono utilizzate per visualizzarli, rendendo l'applicazione di Xamarin.Mac più flessibile e più semplici da gestire. Inoltre ridurre la quantità di codice che è necessario scrivere.

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>Definizione del modello di dati

Prima di poter associare i dati un elemento dell'interfaccia utente in Generatore di interfaccia, è necessario disporre di una classe conforme i KVM/KVO definita nell'applicazione Xamarin.Mac come il _modello di dati_ per l'associazione. Il modello di dati fornisce tutti i dati che verranno visualizzati nell'interfaccia utente e riceve le modifiche ai dati apportate dall'utente nell'interfaccia utente durante l'esecuzione dell'applicazione.

Ad esempio, se si scrive un'applicazione gestita a un gruppo di dipendenti, è possibile utilizzare la classe seguente per definire il modello di dati:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

La maggior parte delle funzionalità di questa classe sono stata analizzata nel [qual è la generazione di codice chiave-valore](#What_is_Key-Value_Coding) sezione precedente. Tuttavia, si esaminerà alcuni elementi specifici e stati apportati alcuni miglioramenti che sono state apportate per consentire la agire come un modello di dati per questa classe **controller Array** e **albero controller** (che verrà usato in un secondo momento ai dati associare **visualizzazioni albero**, **visualizzazioni della struttura** e **viste di raccolta**).

In primo luogo, perché un dipendente potrebbe essere un manager, è stata utilizzata una `NSArray` (in particolare un `NSMutableArray` pertanto i valori possono essere modificati) per consentire i dipendenti che sono gestiti da collegare a loro:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

Due osservazioni di seguito:

1. È stato usato un `NSMutableArray` anziché una matrice di c# o una raccolta poiché si tratta di un requisito per associare dati ai controlli AppKit, ad esempio standard **viste delle tabelle**, **visualizzazioni della struttura** e **raccolte** .
2. La matrice dei dipendenti è esposto eseguendo il cast a un `NSArray` per nome, in formato di dati ai fini dell'associazione e modificare il linguaggio c# `People`, che prevede l'associazione dati, `personModelArray` nel formato **{class_name} matrice** (nota che il primo carattere è stato apportato lettere minuscole).

Successivamente, è necessario aggiungere alcuni appositamente nome metodi pubblici per supportare **controller Array** e **controller albero**:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Questi consentono i controller richiedere e modificare i dati in cui verranno visualizzate. Ad esempio l'oggetto esposto `NSArray` sopra, questi hanno una convenzione di denominazione molto specifica (diverso da tipico c# convenzioni di denominazione):

- `addObject:` -Aggiunge un oggetto nella matrice.
- `insertObject:in{class_name}ArrayAtIndex:` -Dove `{class_name}` è il nome della classe. Questo metodo inserisce un oggetto nella matrice in corrispondenza dell'indice specificato.
- `removeObjectFrom{class_name}ArrayAtIndex:` -Dove `{class_name}` è il nome della classe. Questo metodo rimuove l'oggetto nella matrice in corrispondenza dell'indice specificato.
- `set{class_name}Array:` -Dove `{class_name}` è il nome della classe. Questo metodo consente di sostituire il riporto esistente con uno nuovo.

All'interno di questi metodi, è stato eseguito il wrapping delle modifiche nella matrice in `WillChangeValue` e `DidChangeValue` messaggi per la conformità KVO.

Infine, poiché il `Icon` proprietà si basa sul valore della `isManager` le modifiche alle proprietà, il `isManager` proprietà potrebbe non essere rispecchiata nel `Icon` per elementi di interfaccia di associazione dati (durante KVO):

```csharp
[Export("Icon")]
public NSImage Icon {
    get {
        if (isManager) {
            return NSImage.ImageNamed ("group.png");
        } else {
            return NSImage.ImageNamed ("user.png");
        }
    }
}
``` 

Per correggere che, è il codice seguente:

```csharp
[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");
    }
}
```

Si noti che, oltre alla propria chiave, il `isManager` anche l'invio di funzione di accesso di `WillChangeValue` e `DidChangeValue` messaggi per il `Icon` in modo che verrà visualizzate anche la modifica della chiave.

Verrà usato il `PersonModel` modello di dati in tutto il resto di questo articolo.

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>Data binding semplice

Con il modello di dati definito, esaminiamo un esempio semplice di associazione di dati in Generatore del Xcode di interfaccia. Ad esempio, aggiungere un form all'applicazione Xamarin.Mac che può essere utilizzato per modificare il `PersonModel` che è definito in precedenza. Verranno aggiunti alcuni campi di testo e una casella di controllo per visualizzare e modificare le proprietà del modello.

In primo luogo, aggiungere un nuovo **View Controller** per il nostro **Main** interfaccia generatore di file e denominare la classe `SimpleViewController`: 

[![Aggiunge un nuovo controller di visualizzazione](databinding-images/simple01.png "aggiunge un nuovo controller di visualizzazione")](databinding-images/simple01-large.png#lightbox)

Successivamente, tornare a Visual Studio per Mac, modificare il **SimpleViewController.cs** file (che è stato aggiunto automaticamente al progetto) ed esporre un'istanza di `PersonModel` che sarà il form di associazione dati. Aggiungere il codice seguente:

```csharp
private PersonModel _person = new PersonModel();
...

[Export("Person")]
public PersonModel Person {
    get {return _person; }
    set {
        WillChangeValue ("Person");
        _person = value;
        DidChangeValue ("Person");
    }
}
```

Quando viene caricata la visualizzazione, si crea un'istanza del nostro `PersonModel` e popolarlo con questo codice:

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set a default person
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    Person = Craig;

}
```

Ora è necessario creare il form, fare doppio clic su di **Main** file per aprirlo e modificarlo in Generatore di interfaccia. Layout del form per un aspetto simile a quanto segue:

[![Modifica lo storyboard in Xcode](databinding-images/simple02.png "modifica lo storyboard in Xcode")](databinding-images/simple02-large.png#lightbox)

Form per associare ai dati di `PersonModel` esposti tramite il `Person` chiave, eseguire le operazioni seguenti:

1. Selezionare il **nome dipendente** campo di testo e passare al **associazioni controllo**.
2. Controllare il **associare** e selezionare **Controller visualizzazione semplice** nell'elenco a discesa. Quindi immettere `self.Person.Name` per il **il percorso della chiave**: 

    [![Immettere il percorso della chiave](databinding-images/simple03.png "immettendo il percorso della chiave")](databinding-images/simple03-large.png#lightbox)
3. Selezionare il **occupazione** campo di testo e verificare il **associare** e selezionare **Controller visualizzazione semplice** nell'elenco a discesa. Quindi immettere `self.Person.Occupation` per il **il percorso della chiave**:  

    [![Immettere il percorso della chiave](databinding-images/simple04.png "immettendo il percorso della chiave")](databinding-images/simple04-large.png#lightbox)
4. Selezionare il **dipendente è una gestione** casella di controllo e verificare il **associare** e selezionare **Controller visualizzazione semplice** nell'elenco a discesa. Quindi immettere `self.Person.isManager` per il **il percorso della chiave**:  

    [![Immettere il percorso della chiave](databinding-images/simple05.png "immettendo il percorso della chiave")](databinding-images/simple05-large.png#lightbox)
5. Selezionare il **numero di dipendenti gestito** campo di testo e verificare il **associare** e selezionare **Controller visualizzazione semplice** nell'elenco a discesa. Quindi immettere `self.Person.NumberOfEmployees` per il **il percorso della chiave**:  

    [![Immettere il percorso della chiave](databinding-images/simple06.png "immettendo il percorso della chiave")](databinding-images/simple06-large.png#lightbox)
6. Se il dipendente non è una gestione, è necessario nascondere l'etichetta del numero di dipendenti gestiti e un campo di testo.
7. Selezionare il **numero di dipendenti gestito** etichetta, espandere il **Hidden** turndown e controllare il **associare** e selezionare **Controller visualizzazione semplice** nell'elenco a discesa. Quindi immettere `self.Person.isManager` per il **il percorso della chiave**:  

    [![Immettere il percorso della chiave](databinding-images/simple07.png "immettendo il percorso della chiave")](databinding-images/simple07-large.png#lightbox)
8. Selezionare `NSNegateBoolean` dal **valore Transformer** elenco a discesa:  

    ![Selezionando la trasformazione chiave NSNegateBoolean](databinding-images/simple08.png "selezionando la trasformazione chiave NSNegateBoolean")
9. Associazione dati indica che l'etichetta verrà nascosto se il valore della `isManager` proprietà `false`.
10. Ripetere i passaggi 7 e 8 per il **numero di dipendenti gestito** campo di testo.
11. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue l'applicazione, i valori di `Person` proprietà popola automaticamente il form:

[![Visualizzazione di un modulo compilato automaticamente](databinding-images/simple09.png "che mostra un modulo compilato automaticamente")](databinding-images/simple09-large.png#lightbox)

Tutte le modifiche apportate al modulo gli utenti verranno scritte per il `Person` proprietà nel Controller di visualizzazione. Ad esempio, se si deseleziona **dipendente è una gestione** aggiornamenti il `Person` istanza nostri `PersonModel` e il **numero di dipendenti gestito** etichetta e il campo di testo sono nascosti automaticamente (tramite associazione di dati):

[![Nascondere il numero di dipendenti per non responsabili](databinding-images/simple10.png "nascondere il numero di dipendenti per non responsabili")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>Associazione dati di visualizzazione tabella

Ora che è disponibile, i concetti fondamentali dell'associazione di dati, verrà ora esaminata un'attività di associazione di dati più complessa utilizzando un _Controller Array_ e l'associazione dati a una visualizzazione tabella. Per ulteriori informazioni sull'utilizzo di viste di tabella, vedere il nostro [viste delle tabelle](~/mac/user-interface/table-view.md) documentazione.

In primo luogo, aggiungere un nuovo **View Controller** per il nostro **Main** interfaccia generatore di file e denominare la classe `TableViewController`:

[![Aggiunge un nuovo controller di visualizzazione](databinding-images/table01.png "aggiunge un nuovo controller di visualizzazione")](databinding-images/table01-large.png#lightbox)

Successivamente, si modifica il **TableViewController.cs** file (che è stato aggiunto automaticamente al progetto) e esporre una matrice (`NSArray`) di `PersonModel` classi che sarà il form di associazione dati. Aggiungere il codice seguente:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Esattamente come abbiamo fatto nel `PersonModel` classe sopra il [definizione del modello di dati](#Defining_your_Data_Model) sezione, è stato esposto quattro metodi pubblici in modo speciale denominati in modo che i Controller dell'Array e lettura e scrittura dati da questo insieme di `PersonModels`.

Successivamente quando viene caricata la visualizzazione, è necessario popolare la matrice con il seguente codice:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Ora è necessario creare la tabella, vista, fare doppio clic su di **Main** file per aprirlo e modificarlo in Generatore di interfaccia. Layout di tabella per la ricerca simile al seguente:

[![Layout di una nuova visualizzazione tabella](databinding-images/table02.png "layout di una nuova visualizzazione tabella")](databinding-images/table02-large.png#lightbox)

È necessario aggiungere un **Controller Array** per fornire i dati associati a una tabella, eseguire le operazioni seguenti:

1. Trascinare un **matrice Controller** dal **controllo libreria** sul **Editor dell'interfaccia**:  

    ![Selezione di un Controller dell'Array dalla libreria](databinding-images/table03.png "la selezione di un Controller dell'Array dalla libreria")
2. Selezionare **Controller Array** nel **interfaccia gerarchia** e passare al **attributo controllo**:  

    [![Selezionare il controllo degli attributi](databinding-images/table04.png "selezionando il controllo degli attributi")](databinding-images/table04-large.png#lightbox)
3. Immettere `PersonModel` per il **nome classe**, fare clic su di **più** pulsante e aggiungere tre chiavi. Denominarle `Name`, `Occupation` e `isManager`:  

    ![Aggiunta di percorsi chiavi necessari](databinding-images/table05.png "aggiungendo i percorsi di chiave richiesti")
4. In questo modo il Controller dell'Array cosa che gestisce una matrice di, e quali proprietà deve esporre (tramite chiavi).
5. Passare il **controllo associazioni** e in **matrice contenuto** selezionare **associare** e **tabella View Controller**. Immettere un **il percorso della chiave del modello** di `self.personModelArray`:  

    ![Immettere un percorso chiave](databinding-images/table06.png "immettere un percorso di chiave")
6. Questo metodo, il Controller dell'Array per la matrice di `PersonModels` esposto in questo Controller di visualizzazione.

Ora è necessario associare la tabella vista al Controller di Array, eseguire le operazioni seguenti:

1. Selezionare la visualizzazione della tabella e **associazione controllo**:  

    [![Selezionare il controllo con associazione](databinding-images/table07.png "selezionando il controllo con associazione")](databinding-images/table07-large.png#lightbox)
2. Sotto il **sommario** turndown, selezionare **associare** e **Controller Array**. Immettere `arrangedObjects` per il **chiave Controller** campo:  

    ![La definizione della chiave controller](databinding-images/table08.png "che definisce la chiave del controller")
3. Selezionare il **cella di visualizzazione tabella** sotto il **dipendente** colonna. Nel **controllo associazioni** sotto il **valore** turndown, selezionare **associare** e **cella tabella vista**. Immettere `objectValue.Name` per il **il percorso della chiave del modello**:  

    [![Impostare il percorso della chiave del modello](databinding-images/table09.png "impostando il percorso della chiave del modello")](databinding-images/table09-large.png#lightbox)
4. `objectValue` è il numero corrente `PersonModel` nella matrice viene gestita dal Controller di matrice.
5. Selezionare il **cella di visualizzazione tabella** sotto il **occupazione** colonna. Nel **controllo associazioni** sotto il **valore** turndown, selezionare **associare** e **cella tabella vista**. Immettere `objectValue.Occupation` per il **il percorso della chiave del modello**:  

    [![Impostare il percorso della chiave del modello](databinding-images/table10.png "impostando il percorso della chiave del modello")](databinding-images/table10-large.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue l'applicazione, la tabella verrà popolata con la matrice di `PersonModels`:

[![Esecuzione dell'applicazione](databinding-images/table11.png "esecuzione dell'applicazione")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>Associazione dati di visualizzazione struttura

associazione dati in base a una visualizzazione struttura è molto simile per associazione a fronte di una visualizzazione tabella. La differenza principale è che verrà usato un **Controller albero** anziché un **Controller Array** per fornire i dati associati alla visualizzazione struttura. Per ulteriori informazioni sull'utilizzo di visualizzazioni della struttura, consultare il nostro [visualizzazioni della struttura](~/mac/user-interface/outline-view.md) documentazione.

In primo luogo, aggiungere un nuovo **View Controller** per il nostro **Main** interfaccia generatore di file e denominare la classe `OutlineViewController`: 

[![Aggiunge un nuovo controller di visualizzazione](databinding-images/outline01.png "aggiunge un nuovo controller di visualizzazione")](databinding-images/outline01-large.png#lightbox)

Successivamente, si modifica il **OutlineViewController.cs** file (che è stato aggiunto automaticamente al progetto) e esporre una matrice (`NSArray`) di `PersonModel` classi che sarà il form di associazione dati. Aggiungere il codice seguente:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Esattamente come abbiamo fatto nel `PersonModel` classe sopra il [definizione del modello di dati](#Defining_your_Data_Model) sezione, è stato esposto quattro metodi pubblici in modo speciale denominati in modo che i Controller di struttura ad albero e lettura e scrittura dati da questo insieme di `PersonModels`.

Successivamente quando viene caricata la visualizzazione, è necessario popolare la matrice con il seguente codice:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (Craig);

    var Larry = new PersonModel ("Larry O'Brien", "API Documentation Manager");
    Larry.AddPerson (new PersonModel ("Mike Norman", "API Documenter"));
    AddPerson (Larry);

}
```

Ora è necessario creare la visualizzazione struttura, fare doppio clic su di **Main** file per aprirlo e modificarlo in Generatore di interfaccia. Layout di tabella per la ricerca simile al seguente:

[![Creazione della visualizzazione struttura](databinding-images/outline02.png "creazione della visualizzazione struttura")](databinding-images/outline02-large.png#lightbox)

È necessario aggiungere un **Controller albero** per fornire i dati associati a questo profilo, eseguire le operazioni seguenti:

1. Trascinare un **albero Controller** dal **controllo libreria** sul **Editor dell'interfaccia**:  

    ![Selezione di un Controller di struttura ad albero dalla libreria](databinding-images/outline03.png "selezionando un Controller di struttura ad albero dalla libreria")
2. Selezionare **albero Controller** nel **interfaccia gerarchia** e passare al **controllo attributo**:  

    [![Selezionare il controllo di attributo](databinding-images/outline04.png "selezionando il controllo di attributo")](databinding-images/outline04-large.png#lightbox)
3. Immettere `PersonModel` per il **nome classe**, fare clic su di **più** pulsante e aggiungere tre chiavi. Denominarle `Name`, `Occupation` e `isManager`:  

    ![Aggiunta di percorsi chiavi necessari](databinding-images/outline05.png "aggiungendo i percorsi di chiave richiesti")
4. In questo modo il Controller di struttura ad albero cosa che gestisce una matrice di, e quali proprietà deve esporre (tramite chiavi).
5. Nel **Controller albero** immettere `personModelArray` per **figli**, immettere `NumberOfEmployees` sotto il **conteggio** e immettere `isEmployee` in  **Foglia**:  

    ![Impostazione di percorsi chiavi Controller albero](databinding-images/outline05.png "impostando i percorsi principali del Controller di struttura ad albero")
6. In questo modo il Controller di struttura ad albero dove trovare elementi figlio di nodi, è il numero di nodi figlio non esiste e se il nodo corrente dispone di nodi figlio.
7. Passare il **controllo associazioni** e in **matrice contenuto** selezionare **associare** e **proprietario del File**. Immettere un **il percorso della chiave del modello** di `self.personModelArray`:  

    ![Modifica il percorso della chiave](databinding-images/outline06.png "modifica il percorso della chiave")
8. Questo metodo, il Controller di struttura ad albero alla matrice di `PersonModels` esposto in questo Controller di visualizzazione.

Ora è necessario associare la visualizzazione della struttura per il Controller di struttura ad albero, effettuare le operazioni seguenti:

1. Selezionare la visualizzazione struttura e il **controllo associazione** selezionare:  

    [![Selezionare il controllo con associazione](databinding-images/outline07.png "selezionando il controllo con associazione")](databinding-images/outline07-large.png#lightbox)
2. Sotto il **struttura Visualizza contenuto** turndown, selezionare **associare** e **Controller albero**. Immettere `arrangedObjects` per il **chiave Controller** campo:  

    ![Impostazione della chiave controller](databinding-images/outline08.png "l'impostazione della chiave del controller")
3. Selezionare il **cella di visualizzazione tabella** sotto il **dipendente** colonna. Nel **controllo associazioni** sotto il **valore** turndown, selezionare **associare** e **cella tabella vista**. Immettere `objectValue.Name` per il **il percorso della chiave del modello**:  

    [![Immettere il percorso della chiave del modello](databinding-images/outline09.png "immettendo il percorso della chiave del modello")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` è il numero corrente `PersonModel` nella matrice viene gestita dal Controller di struttura ad albero.
5. Selezionare il **cella di visualizzazione tabella** sotto il **occupazione** colonna. Nel **controllo associazioni** sotto il **valore** turndown, selezionare **associare** e **cella tabella vista**. Immettere `objectValue.Occupation` per il **il percorso della chiave del modello**:  

    [![Immettere il percorso della chiave del modello](databinding-images/outline10.png "immettendo il percorso della chiave del modello")](databinding-images/outline10-large.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue l'applicazione, la struttura verrà popolata con la matrice di `PersonModels`:

[![Esecuzione dell'applicazione](databinding-images/outline11.png "esecuzione dell'applicazione")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>Associazione di dati visualizzazione raccolta

Associazione dati con una visualizzazione di raccolta è molto simile a binding con una visualizzazione tabella, come un Controller dell'Array è utilizzato per fornire dati per la raccolta. Poiché la visualizzazione della raccolta non ha un formato di visualizzazione predefinito, è necessario per fornire commenti e suggerimenti di interazione dell'utente e tenere traccia di selezione dell'utente più lavoro.

> [!IMPORTANT]
> A causa di un problema in Xcode 7 e macOS 10.11 (e versioni successive), viste di raccolta sono in grado di essere utilizzato all'interno di un file di Storyboard (.storyboard). Di conseguenza, è necessario continuare a utilizzare i file .xib per definire le visualizzazioni di raccolta per le app Xamarin.Mac. Consultare il nostro [viste di raccolta](~/mac/user-interface/collection-view.md) documentazione per ulteriori informazioni.

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`: 

![](databinding-images/collection01.png)

Next, let's edit the **CollectionViewController.cs** file (that was automatically added to our project) and expose an array (`NSArray`) of `PersonModel` classes that we will be data binding our form to. Add the following code:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Just like we did on the `PersonModel` class above in the [Defining your Data Model](#Defining_your_Data_Model) section, we've exposed four specially named public methods so that the Array Controller and read and write data from our collection of `PersonModels`.

Next when the View is loaded, we need to populate our array with this code:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Now we need to create our Collection View, double-click the **Main.storyboard** file to open it for editing in Interface Builder. Layout the Collection View to look something like the following:

![](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1.  **Collection View Item** -  That manages a single instance of an item in the collection.
2.  **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>Debug nativo arresti anomali del sistema

Commettere delle associazioni di dati può comportare un _Native di arresto anomalo_ in codice non gestito e rallentare l'applicazione Xamarin.Mac completamente generando un `SIGABRT` errore:

[![Esempio di una finestra di dialogo di arresto anomalo del sistema nativo](databinding-images/debug01.png "esempio di una finestra di dialogo di arresto anomalo del sistema nativo")](databinding-images/debug01-large.png#lightbox)

Durante l'associazione dati sono in genere sono quattro cause principali di arresti anomali del sistema nativo:

1. Il modello di dati non eredita da `NSObject` o una sottoclasse di `NSObject`.
2. Si non espongono la proprietà al Objective-C utilizzando la `[Export("key-name")]` attributo.
3. Si non esegue il wrapping le modifiche al valore della funzione di accesso in `WillChangeValue` e `DidChangeValue` chiamate al metodo (specificando la stessa chiave come la `Export` attributo).
4. Sono presenti le chiave errata di **controllo con associazione** in Generatore di interfaccia.

### <a name="decoding-a-crash"></a>Decodifica un arresto anomalo

Si provocare un arresto anomalo del sistema nativo nell'associazione di dati è possibile visualizzare come individuare e risolvere il problema. In Generatore di interfaccia, è necessario modificare l'associazione della prima etichetta nell'esempio di visualizzazione della raccolta da `Name` a `Title`:

[![Modifica la chiave di associazione](databinding-images/debug02.png "modifica la chiave di associazione")](databinding-images/debug02-large.png#lightbox)

Consente di salvare le modifiche, tornare a Visual Studio per Mac per la sincronizzazione con Xcode ed eseguire l'applicazione. Quando viene visualizzata la visualizzazione della raccolta, l'applicazione arresterà temporaneamente con un `SIGABRT` errore (come illustrato nel **Output dell'applicazione** in Visual Studio per Mac) poiché il `PersonModel` non espone una proprietà con la chiave `Title`:

[![Esempio di un errore di associazione](databinding-images/debug03.png "ad esempio un errore di associazione")](databinding-images/debug03-large.png#lightbox)

Se si scorre all'inizio dell'errore nella **Output dell'applicazione** si noterà che la chiave per risolvere il problema:

[![Individuare il problema nel log degli errori](databinding-images/debug04.png "individuare il problema nel log degli errori")](databinding-images/debug04-large.png#lightbox)

Questa riga si rivelano che la chiave `Title` non esiste per l'oggetto che viene eseguita l'associazione. Se si modifica l'associazione nuovamente `Name` in Generatore di interfaccia, salvataggio, la sincronizzazione, ricompilare ed eseguire, l'applicazione verrà eseguita come previsto senza problemi.

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'utilizzo di data binding e la generazione di codice chiave-valore in un'applicazione Xamarin.Mac un'analisi approfondita. Prima di tutto, esaminato l'esposizione di una classe c# per Objective-C utilizzando chiave-valore di codifica (i KVM) e chiave-valore osservando (KVO). Successivamente, è stato spiegato come utilizzare una classe conforme KVO e dati associarlo a elementi dell'interfaccia utente in Generatore del Xcode di interfaccia. Infine, hanno dimostrato di associazione dati complessa utilizzando **controller Array** e **controller albero**.


## <a name="related-links"></a>Collegamenti correlati

- [Storyboard di MacDatabinding (esempio)](https://developer.xamarin.com/samples/mac/MacDatabinding-Storyboard/)
- [MacDatabinding XIBs (esempio)](https://developer.xamarin.com/samples/mac/MacDatabinding-XIBs/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Controlli standard](~/mac/user-interface/standard-controls.md)
- [Viste delle tabelle](~/mac/user-interface/table-view.md)
- [Visualizzazioni della struttura](~/mac/user-interface/outline-view.md)
- [Viste di raccolta](~/mac/user-interface/collection-view.md)
- [Guida per programmatori di codifica chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introduzione alla Guida per programmatori osservazione chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Introduzione alle associazioni Cocoa argomenti di programmazione](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introduzione a Cocoa associazioni riferimento](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
