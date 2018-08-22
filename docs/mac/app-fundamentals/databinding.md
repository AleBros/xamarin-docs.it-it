---
title: Data binding e codifica di chiave-valore in xamarin. Mac
description: Questo articolo viene illustrato l'utilizzo chiave-valore di codifica e chiave-valore osservando la possibilità di associazione dati a elementi dell'interfaccia utente in Interface Builder di Xcode.
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 0adb8cda71ca8803c535679da2aecf00f3fa46a5
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251262"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>Data binding e codifica di chiave-valore in xamarin. Mac

_Questo articolo viene illustrato l'utilizzo chiave-valore di codifica e chiave-valore osservando la possibilità di associazione dati a elementi dell'interfaccia utente in Interface Builder di Xcode._

## <a name="overview"></a>Panoramica

Lavora in c# e .NET in un'applicazione xamarin. Mac, è possibile utilizzare la stessa chiave-valore di codifica e le tecniche di associazione di dati che gli sviluppatori che lavorano *Objective-C* e *Xcode* viene. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Xcode _Interface Builder_ per associare i dati con elementi dell'interfaccia utente invece di scrivere codice.

Usando chiave-valore di codifica e di data binding tecniche nell'applicazione xamarin. Mac, è possibile ridurre la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È anche il vantaggio di un'ulteriore separazione dei dati di backup (_modello di dati_) dal front end interfaccia utente (_Model-View-Controller_), sfociando in più facilmente gestibile, più flessibile dell'applicazione progettazione.

[![Un esempio di app in esecuzione](databinding-images/intro01.png "un esempio di app in esecuzione")](databinding-images/intro01-large.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'utilizzo di chiave-valore di codifica e il data binding in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` attributi utilizzato per impostare backup di classi c# per gli oggetti di Objective-C e interfaccia utente di elementi.

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>Che cos'è la codifica di chiave-valore

Chiave-valore di codifica (i KVM) è un meccanismo per l'accesso a proprietà di un oggetto indirettamente, tramite chiavi (stringhe di formattazione speciale) per identificare le proprietà anziché accedervi tramite le variabili di istanza o metodi di accesso (`get/set`). Grazie all'implementazione chiave-valore codifica compatibile con le funzioni di accesso nell'applicazione xamarin. Mac, è possibile accedere ad altre funzionalità di macOS (precedentemente noto come OS X), ad esempio chiave-valore osservazione (KVO), l'associazione dati, Core Data, le associazioni di Cocoa e scriptability.

Usando chiave-valore di codifica e di data binding tecniche nell'applicazione xamarin. Mac, è possibile ridurre la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È anche il vantaggio di un'ulteriore separazione dei dati di backup (_modello di dati_) dal front end interfaccia utente (_Model-View-Controller_), sfociando in più facilmente gestibile, più flessibile dell'applicazione progettazione. 

Ad esempio, verrà ora esaminata la seguente definizione di classe di un oggetto conforme i KVM:

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

Prima di tutto la `[Register("PersonModel")]` attributo registra la classe e la espone a Objective-C. Quindi, la classe deve ereditare `NSObject` (o una sottoclasse che eredita da `NSObject`), questo aggiunge alcuni metodo che consentono alla classe di essere conformi i KVM di base. Successivamente, il `[Export("Name")]` attributo espone il `Name` proprietà e definisce il valore di chiave che verrà successivamente utilizzato per accedere alla proprietà tramite i KVM e KVO tecniche. 

Infine, per poter essere osservato chiave-valore diventa il valore della proprietà, la funzione di accesso necessario eseguire il wrapping delle modifiche al relativo valore in `WillChangeValue` e `DidChangeValue` chiamate al metodo (che specifica la stessa chiave di `Export` attributo).  Ad esempio:

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

Questo passaggio viene _molto_ importanti per il data binding in Xcode Interface Builder di (come vedremo più avanti in questo articolo).

Per altre informazioni, vedere di Apple [chiave-valore di codifica Programming Guide](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html).

### <a name="keys-and-key-paths"></a>Le chiavi e i percorsi delle chiavi

Oggetto _chiave_ è una stringa che identifica una proprietà specifica di un oggetto. In genere, una chiave corrisponde al nome di un metodo della funzione di accesso in scrittura di codice oggetto conforme chiave-valore. Le chiavi, devono usare la codifica ASCII, in genere iniziare con una lettera minuscola e non può contenere spazi vuoti. L'esempio precedente, supponendo `Name` sarebbe un valore della chiave `Name` proprietà del `PersonModel` classe. La chiave e il nome della proprietà che espongono non devono necessariamente essere lo stesso, tuttavia nella maggior parte dei casi sono.

Oggetto _percorso della chiave_ è una stringa di punto separate le chiavi usate per specificare una gerarchia di proprietà dell'oggetto da attraversare. La proprietà della chiave del primo nella sequenza è relativo al ricevitore e ogni chiave successivi viene valutato rispetto al valore della proprietà precedente. Allo stesso modo usare la notazione del punto per attraversare un oggetto e le relative proprietà in una classe c#.

Ad esempio, se sono stati espansi i `PersonModel` classi e aggiungere `Child` proprietà:

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

Il percorso della chiave per il nome del figlio sarebbe `self.Child.Name` o semplicemente `Child.Name` (basato su come veniva utilizzato il valore della chiave).

### <a name="getting-values-using-key-value-coding"></a>Recupero di valori usando la codifica di chiave-valore

Il `ValueForKey` metodo restituisce il valore della chiave specificata (come un `NSString`), relativo all'istanza della classe i KVM quando riceve la richiesta. Ad esempio, se `Person` è un'istanza del `PersonModel` classe definita in precedenza:

```csharp
// Read value 
var name = Person.ValueForKey (new NSString("Name"));
```

Verrà così restituito il valore della `Name` proprietà per l'istanza di `PersonModel`. 

### <a name="setting-values-using-key-value-coding"></a>Impostazione dei valori utilizzando la codifica di chiave-valore

Analogamente, il `SetValueForKey` impostare il valore della chiave specificata (come un `NSString`), relativo all'istanza della classe i KVM quando riceve la richiesta. Ancora una volta, utilizzando un'istanza di `PersonModel` classe, come illustrato di seguito:

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

Modificare il valore dei `Name` proprietà `Jane Doe`.

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>Osservare le modifiche dei valori

Usa chiave-valore osservando (KVO), è possibile associare un osservatore a una chiave specifica di una classe conforme i KVM e da una notifica ogni volta che viene modificato il valore per tale chiave (utilizzando le tecniche i KVM o direttamente l'accesso alla proprietà specificata nel codice c#). Ad esempio:

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

A questo punto, ogni volta che il `Name` proprietà del `Person` istanza il `PersonModel` classe viene modificata, il nuovo valore viene scritto nella console. 

Per altre informazioni, vedere di Apple [Introduzione alla Guida per programmatori osservando chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i).

## <a name="data-binding"></a>Associazione dati

Le sezioni seguenti verranno mostrano come è possibile usare una chiave-valore di codifica e coppie chiave-valore osservando classe conforme per associare dati a elementi dell'interfaccia utente in Interface Builder di Xcode, invece di lettura e scrittura di valori usando codice c#. In questo modo si separano le _modello di dati_ dalle viste che vengono utilizzate per visualizzarli, rendendo l'applicazione xamarin. Mac più flessibile e facile da gestire. Anche ridurre la quantità di codice che è necessario scrivere.

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>Definizione del modello di dati

Prima di poter associare i dati un elemento dell'interfaccia utente in Interface Builder, è necessario disporre di una classe conforme i KVM/KVO definita nell'applicazione xamarin. Mac per agire come le _modello di dati_ per l'associazione. Il modello di dati fornisce tutti i dati che verranno visualizzati nell'interfaccia utente e riceve le modifiche ai dati apportate dall'utente nell'interfaccia utente durante l'esecuzione dell'applicazione.

Ad esempio, se si stesse scrivendo un'applicazione gestita di un gruppo di dipendenti, si potrebbe usare la classe seguente per definire il modello di dati:

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

La maggior parte delle funzionalità di questa classe sono stata trattata nel [What ' s chiave-valore di codifica](#What_is_Key-Value_Coding) sezione precedente. Tuttavia, verranno ora esaminati alcuni elementi specifici stati apportati alcuni miglioramenti che sono state apportate a questa classe per fungere da un modello di dati per consentire **controller di Array** e **albero controller** (che verrà usato in un secondo momento ai dati associare **visualizzazioni dell'albero**, **la visualizzazione della struttura** e **visualizzazioni raccolta**).

In primo luogo, perché un dipendente potrebbe essere un responsabile, abbiamo utilizzato un `NSArray` (in particolare un `NSMutableArray` in modo che i valori possono essere modificati) per consentire i dipendenti che sono gestiti da collegare a essi:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

Due aspetti da notare:

1. È stato usato un `NSMutableArray` anziché un matrice c# standard o una raccolta poiché si tratta, ad esempio un requisito per associare dati a controlli AppKit **viste delle tabelle**, **visualizzazioni della struttura** e **raccolte** .
2. La matrice dei dipendenti è esposta eseguendo il cast a un `NSArray` per nome, in formato di dati ai fini dell'associazione e modificato il linguaggio c# `People`, che prevede l'associazione dati, `personModelArray` nel formato **{class_name} matrice** (nota che il primo carattere è stato apportato lettere minuscole).

Successivamente, è necessario aggiungere alcuni appositamente nome metodi pubblici per supportare **controller di Array** e **albero controller**:

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

Che consentono i controller richiedere e modificare i dati in cui verranno visualizzate. Ad esempio esposti `NSArray` sopra, hanno una convenzione di denominazione molto specifica (che è diverso da tipico c# convenzioni di denominazione):

- `addObject:` -Aggiunge un oggetto nella matrice.
- `insertObject:in{class_name}ArrayAtIndex:` : Indica dove `{class_name}` è il nome della classe. Questo metodo inserisce un oggetto nella matrice in corrispondenza dell'indice specificato.
- `removeObjectFrom{class_name}ArrayAtIndex:` : Indica dove `{class_name}` è il nome della classe. Questo metodo rimuove l'oggetto nella matrice in corrispondenza dell'indice specificato.
- `set{class_name}Array:` : Indica dove `{class_name}` è il nome della classe. Questo metodo consente di sostituire il riporto esistente con uno nuovo.

All'interno di questi metodi, è stato eseguito il wrapping di modifiche alla matrice nel `WillChangeValue` e `DidChangeValue` messaggi per la conformità KVO.

Infine, poiché il `Icon` proprietà si basa sul valore della `isManager` le modifiche alle proprietà, il `isManager` proprietà potrebbe non essere rispecchiata nel `Icon` per gli elementi dell'interfaccia utente associato di dati (durante KVO):

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

Per correggerlo, utilizziamo il codice seguente:

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

Si noti che oltre la propria chiave, il `isManager` funzione di accesso invia anche il `WillChangeValue` e `DidChangeValue` messaggi per il `Icon` in modo da poter visualizzare anche la modifica della chiave.

Verrà usato il `PersonModel` modello di dati nella parte restante di questo articolo.

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>Data binding semplice

Con il nostro modello di dati definiti, esaminiamo un esempio semplice di data binding in Interface Builder di Xcode. Ad esempio, aggiungere un form all'applicazione xamarin. Mac che può essere utilizzato per modificare il `PersonModel` definito sopra. Aggiungiamo alcuni campi di testo e una casella di controllo per visualizzare e modificare le proprietà del modello.

In primo luogo, è possibile aggiungere un nuovo **Controller di visualizzazione** al nostro **Main. Storyboard** in Interface Builder di file e denominare la classe `SimpleViewController`: 

[![Aggiunge un nuovo controller di visualizzazione](databinding-images/simple01.png "aggiunge un nuovo controller di visualizzazione")](databinding-images/simple01-large.png#lightbox)

Successivamente, tornare a Visual Studio per Mac, modificare il **SimpleViewController.cs** file (che è stato aggiunto automaticamente al nostro progetto) ed esporre un'istanza del `PersonModel` che sarà nostro modulo per tale associazione. Aggiungere il codice seguente:

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

Successivamente quando viene caricata la visualizzazione, è possibile creare un'istanza del nostro `PersonModel` e popolarlo con questo codice:

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

A questo punto è necessario creare il modulo, fare doppio clic il **Main. Storyboard** file per aprirlo e modificarlo in Interface Builder. Layout di form per aspetto simile al seguente:

[![Modifica lo storyboard in Xcode](databinding-images/simple02.png "modifica lo storyboard in Xcode")](databinding-images/simple02-large.png#lightbox)

Come associare i dati formato per il `PersonModel` che viene esposto tramite il `Person` chiave, eseguire le operazioni seguenti:

1. Selezionare il **Employee Name** campo di testo e si passa al **Bindings Inspector**.
2. Controllare la **associare** e selezionare **Controller di visualizzazione semplice** dall'elenco a discesa. Immettere quindi `self.Person.Name` per il **percorso della chiave**: 

    [![Immettere il percorso della chiave](databinding-images/simple03.png "immettendo il percorso della chiave")](databinding-images/simple03-large.png#lightbox)
3. Selezionare il **Occupation** campo di testo e verificare il **associare** casella e seleziona **Controller di visualizzazione semplice** dall'elenco a discesa. Immettere quindi `self.Person.Occupation` per il **percorso della chiave**:  

    [![Immettere il percorso della chiave](databinding-images/simple04.png "immettendo il percorso della chiave")](databinding-images/simple04-large.png#lightbox)
4. Selezionare il **dipendente è responsabile** casella di controllo e verificare il **associare a** casella e seleziona **Controller di visualizzazione semplice** dall'elenco a discesa. Immettere quindi `self.Person.isManager` per il **percorso della chiave**:  

    [![Immettere il percorso della chiave](databinding-images/simple05.png "immettendo il percorso della chiave")](databinding-images/simple05-large.png#lightbox)
5. Selezionare il **numero di dipendenti gestito** campo di testo e verificare il **associare** casella e seleziona **Controller di visualizzazione semplice** dall'elenco a discesa. Immettere quindi `self.Person.NumberOfEmployees` per il **percorso della chiave**:  

    [![Immettere il percorso della chiave](databinding-images/simple06.png "immettendo il percorso della chiave")](databinding-images/simple06-large.png#lightbox)
6. Se il dipendente non è un manager, è necessario nascondere l'etichetta del numero di dipendenti gestiti e un campo di testo.
7. Selezionare il **numero di dipendenti gestito** etichetta, espandere il **Hidden** turndown e controllare il **associare a** casella e seleziona **Controller di visualizzazione semplice** dall'elenco a discesa. Immettere quindi `self.Person.isManager` per il **percorso della chiave**:  

    [![Immettere il percorso della chiave](databinding-images/simple07.png "immettendo il percorso della chiave")](databinding-images/simple07-large.png#lightbox)
8. Selezionare `NSNegateBoolean` dal **trasformatore valore** elenco a discesa:  

    ![Selezionando la trasformazione chiave NSNegateBoolean](databinding-images/simple08.png "selezionando la trasformazione chiave NSNegateBoolean")
9. Indica l'associazione dati che l'etichetta verrà nascosto se il valore della `isManager` è di proprietà `false`.
10. Ripetere i passaggi 7 e 8 per il **numero di dipendenti gestito** campo di testo.
11. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue l'applicazione, i valori di `Person` proprietà verrà automaticamente inserito il modulo:

[![Visualizzazione di un modulo compilato automaticamente](databinding-images/simple09.png "che mostra un modulo compilato automaticamente")](databinding-images/simple09-large.png#lightbox)

Tutte le modifiche apportate al modulo gli utenti verranno scritti nuovamente il `Person` proprietà nel Controller di visualizzazione. Ad esempio, se si deseleziona **dipendente è responsabile del** aggiornamenti il `Person` istanza del nostro `PersonModel` e il **numero di dipendenti gestito** etichetta e il campo di testo sono nascosti automaticamente (tramite associazione di dati):

[![Se si nasconde il numero di dipendenti per non responsabili](databinding-images/simple10.png "nascondendo il numero di dipendenti per non responsabili")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>Associazione di dati di visualizzazione tabella

Ora che abbiamo le nozioni di base di un data binding dall'area di lavoro, esaminiamo un'attività di associazione di dati più complessa utilizzando un _Controller dell'Array_ e il data binding a una visualizzazione tabella. Per altre informazioni sull'uso delle visualizzazioni tabella, vedere la [viste delle tabelle](~/mac/user-interface/table-view.md) documentazione.

In primo luogo, è possibile aggiungere un nuovo **Controller di visualizzazione** al nostro **Main. Storyboard** in Interface Builder di file e denominare la classe `TableViewController`:

[![Aggiunge un nuovo controller di visualizzazione](databinding-images/table01.png "aggiunge un nuovo controller di visualizzazione")](databinding-images/table01-large.png#lightbox)

Successivamente, è possibile modificare il **TableViewController.cs** file (che è stato aggiunto automaticamente al nostro progetto) ed esporre una matrice (`NSArray`) di `PersonModel` classi che si sarà nostro modulo per tale associazione. Aggiungere il codice seguente:

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

Come abbiamo fatto nel `PersonModel` classe sopra il [che definisce il modello di dati](#Defining_your_Data_Model) sezione, è stata esposte quattro metodi pubblici appositamente denominati in modo che i Controller dell'Array e lettura e scrittura dati dalla raccolta di `PersonModels`.

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

A questo punto è necessario creare la visualizzazione di tabella, fare doppio clic il **Main. Storyboard** file per aprirlo e modificarlo in Interface Builder. Layout di tabella per la ricerca simile al seguente:

[![Disposizione di una nuova vista tabella](databinding-images/table02.png "disposizione una nuova visualizzazione tabella")](databinding-images/table02-large.png#lightbox)

È necessario aggiungere un' **Controller dell'Array** per fornire i dati associati alla nostra tabella, eseguire le operazioni seguenti:

1. Trascinare un' **Controller della matrice** dalle **Library Inspector** nel **Editor dell'interfaccia**:  

    ![Quando si seleziona un Controller di matrice dalla libreria](databinding-images/table03.png "quando si seleziona un Controller di matrice dalla raccolta")
2. Selezionare **Controller dell'Array** nel **gerarchia delle interfacce** e passare al **attributo Inspector**:  

    [![Selezione Attributes Inspector](databinding-images/table04.png "selezionando Attributes Inspector")](databinding-images/table04-large.png#lightbox)
3. Immettere `PersonModel` per il **Class Name**, fare clic sui **Plus** pulsante e aggiungere tre chiavi. Denominarle `Name`, `Occupation` e `isManager`:  

    ![Aggiungere i percorsi di chiave richiesti](databinding-images/table05.png "aggiungendo i percorsi di chiave richiesti")
4. In questo modo il Controller dell'Array ciò che gestisce una matrice di, e le proprietà che devono essere esposte (tramite le chiavi).
5. Passare al **Bindings Inspector** e in **matrice contenuto** selezionare **associare** e **Controller visualizzazione tabella**. Immettere un **il percorso della chiave del modello** di `self.personModelArray`:  

    ![Immettere un percorso di chiave](databinding-images/table06.png "immettendo un percorso della chiave")
6. Ciò consente di associare il Controller dell'Array per la matrice di `PersonModels` cui viene esposto il Controller di visualizzazione.

A questo punto è necessario associare la visualizzazione di tabella per il Controller dell'Array, eseguire le operazioni seguenti:

1. Selezionare la visualizzazione di tabella e il **Binding Inspector**:  

    [![Selezionare il controllo di associazione](databinding-images/table07.png "selezionando il controllo di associazione")](databinding-images/table07-large.png#lightbox)
2. Sotto il **sommario** turndown, selezionare **associare** e **Controller dell'Array**. Immettere `arrangedObjects` per il **chiave Controller** campo:  

    ![La definizione della chiave controller](databinding-images/table08.png "che definisce la chiave del controller")
3. Selezionare il **cella di visualizzazione tabella** sotto il **dipendente** colonna. Nel **Bindings Inspector** sotto il **valore** turndown, selezionare **associare** e **cella tabella vista**. Immettere `objectValue.Name` per il **percorso della chiave del modello**:  

    [![Impostare il percorso della chiave del modello](databinding-images/table09.png "impostando il percorso della chiave del modello")](databinding-images/table09-large.png#lightbox)
4. `objectValue` è l'oggetto corrente `PersonModel` nella matrice viene gestita dal Controller di matrice.
5. Selezionare il **cella di visualizzazione tabella** sotto il **Occupation** colonna. Nel **Bindings Inspector** sotto il **valore** turndown, selezionare **associare** e **cella tabella vista**. Immettere `objectValue.Occupation` per il **percorso della chiave del modello**:  

    [![Impostare il percorso della chiave del modello](databinding-images/table10.png "impostando il percorso della chiave del modello")](databinding-images/table10-large.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue l'applicazione, la tabella verrà popolata con la matrice di `PersonModels`:

[![Esecuzione dell'applicazione](databinding-images/table11.png "esecuzione dell'applicazione")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>Associazione di dati di visualizzazione di struttura

associazione dati in una visualizzazione della struttura è molto simile all'associazione a fronte di una visualizzazione tabella. La differenza principale è che verrà usato un **Controller di struttura ad albero** invece di un **Controller dell'Array** per fornire i dati associati alla visualizzazione della struttura. Per altre informazioni sull'uso delle visualizzazioni struttura, vedere la [visualizzazioni della struttura](~/mac/user-interface/outline-view.md) documentazione.

In primo luogo, è possibile aggiungere un nuovo **Controller di visualizzazione** al nostro **Main. Storyboard** in Interface Builder di file e denominare la classe `OutlineViewController`: 

[![Aggiunge un nuovo controller di visualizzazione](databinding-images/outline01.png "aggiunge un nuovo controller di visualizzazione")](databinding-images/outline01-large.png#lightbox)

Successivamente, è possibile modificare il **OutlineViewController.cs** file (che è stato aggiunto automaticamente al nostro progetto) ed esporre una matrice (`NSArray`) di `PersonModel` classi che si sarà nostro modulo per tale associazione. Aggiungere il codice seguente:

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

Come abbiamo fatto nel `PersonModel` classe sopra il [che definisce il modello di dati](#Defining_your_Data_Model) sezione, è stata esposte quattro metodi pubblici appositamente denominati in modo che i Controller di struttura ad albero e lettura e scrittura dati dalla raccolta di `PersonModels`.

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

A questo punto è necessario creare la visualizzazione di struttura, fare doppio clic il **Main. Storyboard** file per aprirlo e modificarlo in Interface Builder. Layout di tabella per la ricerca simile al seguente:

[![Creazione di visualizzazione della struttura](databinding-images/outline02.png "Creazione visualizzazione della struttura")](databinding-images/outline02-large.png#lightbox)

È necessario aggiungere un' **albero Controller** per fornire i dati associati al nostro profilo, eseguire le operazioni seguenti:

1. Trascinare un' **albero Controller** dal **Library Inspector** nel **Editor dell'interfaccia**:  

    ![Quando si seleziona un Controller di struttura ad albero dalla libreria](databinding-images/outline03.png "quando si seleziona un Controller di struttura ad albero dalla libreria")
2. Selezionare **Controller di struttura ad albero** nel **gerarchia delle interfacce** e passare al **attributo Inspector**:  

    [![Selezione Attribute Inspector](databinding-images/outline04.png "selezionando Attribute Inspector")](databinding-images/outline04-large.png#lightbox)
3. Immettere `PersonModel` per il **Class Name**, fare clic sui **Plus** pulsante e aggiungere tre chiavi. Denominarle `Name`, `Occupation` e `isManager`:  

    ![Aggiungere i percorsi di chiave richiesti](databinding-images/outline05.png "aggiungendo i percorsi di chiave richiesti")
4. In questo modo il Controller di struttura ad albero ciò che gestisce una matrice di, e le proprietà che devono essere esposte (tramite le chiavi).
5. Sotto il **Controller di struttura ad albero** sezione, immettere `personModelArray` per **figli**, immettere `NumberOfEmployees` sotto il **conteggio** e immettere `isEmployee` in  **Foglia**:  

    ![Impostare i percorsi principali di Controller di struttura ad albero](databinding-images/outline05.png "impostando i percorsi principali di Controller di struttura ad albero")
6. In questo modo il Controller di struttura ad albero dove trovare elementi figlio, i nodi, il numero di nodi figlio presenti è e se il nodo corrente presenta nodi figlio.
7. Passare al **Bindings Inspector** e in **matrice contenuto** selezionare **associare** e **il proprietario del File**. Immettere un **il percorso della chiave del modello** di `self.personModelArray`:  

    ![Modifica il percorso della chiave](databinding-images/outline06.png "modifica il percorso della chiave")
8. Ciò consente di associare il Controller di struttura ad albero nella matrice di `PersonModels` cui viene esposto il Controller di visualizzazione.

A questo punto è necessario associare la visualizzazione struttura al Controller di struttura ad albero, le operazioni seguenti:

1. Selezionare visualizzazione della struttura e il **Inspector associazione** selezionare:  

    [![Selezionare il controllo di associazione](databinding-images/outline07.png "selezionando il controllo di associazione")](databinding-images/outline07-large.png#lightbox)
2. Sotto il **struttura Visualizza contenuto** turndown, selezionare **associare** e **albero Controller**. Immettere `arrangedObjects` per il **chiave Controller** campo:  

    ![Impostazione della chiave controller](databinding-images/outline08.png "impostazione della chiave controller")
3. Selezionare il **cella di visualizzazione tabella** sotto il **dipendente** colonna. Nel **Bindings Inspector** sotto il **valore** turndown, selezionare **associare** e **cella tabella vista**. Immettere `objectValue.Name` per il **percorso della chiave del modello**:  

    [![Immettere il percorso della chiave del modello](databinding-images/outline09.png "immettendo il percorso della chiave del modello")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` è l'oggetto corrente `PersonModel` nella matrice viene gestita dal Controller di struttura ad albero.
5. Selezionare il **cella di visualizzazione tabella** sotto il **Occupation** colonna. Nel **Bindings Inspector** sotto il **valore** turndown, selezionare **associare** e **cella tabella vista**. Immettere `objectValue.Occupation` per il **percorso della chiave del modello**:  

    [![Immettere il percorso della chiave del modello](databinding-images/outline10.png "immettendo il percorso della chiave del modello")](databinding-images/outline10-large.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue l'applicazione, la struttura verrà popolata con la matrice di `PersonModels`:

[![Esecuzione dell'applicazione](databinding-images/outline11.png "esecuzione dell'applicazione")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>Associazione di dati di visualizzazione raccolta

Data binding con una visualizzazione di raccolta è molto simile a binding con una visualizzazione tabella, un Controller di matrice viene usata per fornire dati per la raccolta. Poiché la visualizzazione di raccolta non ha un formato di visualizzazione di set di impostazioni, è necessario fornire commenti e suggerimenti l'interazione dell'utente e di tenere traccia di selezione dell'utente più lavoro.

> [!IMPORTANT]
> A causa di un problema in Xcode 7 e Mac OS 10.11 (e versioni successive), le visualizzazioni di raccolta non riesce a essere usato all'interno di un file Storyboard (con estensione storyboard). Di conseguenza, dovrai continuare a usare file con estensione xib per definire le visualizzazioni di raccolta per le app xamarin. Mac. Vedere la [visualizzazioni raccolta](~/mac/user-interface/collection-view.md) per altre informazioni.

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

## <a name="debugging-native-crashes"></a>Debug di un arresto anomalo nativo

Commettere un errore delle associazioni di dati può comportare un _arresto anomalo nativo_ nel codice non gestito e che l'applicazione xamarin. Mac esito completamente negativo con un `SIGABRT` errore:

[![Esempio di una finestra di dialogo di arresto anomalo nativo](databinding-images/debug01.png "esempio di una finestra di dialogo di arresto anomalo nativo")](databinding-images/debug01-large.png#lightbox)

In genere esistono quattro cause principali di un arresto anomalo nativo durante l'associazione dati:

1. Il modello di dati non eredita da `NSObject` o a una sottoclasse `NSObject`.
2. Non espone le proprietà da Objective-C tramite il `[Export("key-name")]` attributo.
3. È non è stata eseguire il wrapping delle modifiche al valore della funzione di accesso nella `WillChangeValue` e `DidChangeValue` chiamate al metodo (che specifica la stessa chiave di `Export` attributo).
4. Si dispone di una chiave digitata incorrettamente o errata **Inspector associazione** in Interface Builder.

### <a name="decoding-a-crash"></a>Decodifica un arresto anomalo del sistema

È possibile causare un arresto anomalo nativo l'associazione di dati in modo che possiamo illustra come individuare e risolvere il problema. In Interface Builder, modifichiamo l'associazione della prima etichetta nell'esempio di visualizzazione dell'insieme `Name` a `Title`:

[![Modifica la chiave di associazione](databinding-images/debug02.png "modifica la chiave di associazione")](databinding-images/debug02-large.png#lightbox)

È possibile salvare le modifiche, tornare a Visual Studio per Mac Sincronizza con Xcode ed eseguire l'applicazione. Quando viene visualizzata la visualizzazione di raccolta, l'applicazione si arresterà momentaneamente con un `SIGABRT` errore (come illustrato nella **Output dell'applicazione** in Visual Studio per Mac) poiché il `PersonModel` non espone una proprietà con la chiave `Title`:

[![Esempio di un errore di associazione](databinding-images/debug03.png "esempio di un errore di associazione")](databinding-images/debug03-large.png#lightbox)

Se si scorre a dell'errore nella parte superiore di **Output applicazione** noteremo la chiave per risolvere il problema:

[![Individuare il problema nel log degli errori](databinding-images/debug04.png "individuare il problema nel log degli errori")](databinding-images/debug04-large.png#lightbox)

Questa riga indica che la chiave `Title` non esiste nell'oggetto cui si sta effettuando l'associazione. Se si modifica l'associazione nuovamente `Name` in Interface Builder, save, sincronizzazione, ricompilare ed eseguire, l'applicazione verrà eseguita come previsto senza problemi.

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di utilizzo di data binding e codifica di chiave-valore in un'applicazione xamarin. Mac. In primo luogo, esaminata l'esposizione di una classe c# Objective-c usando chiave-valore di codifica (i KVM) e chiave-valore osservando (KVO). Successivamente, è stato illustrato come usare una classe conforme KVO e dati associarla agli elementi dell'interfaccia utente in Interface Builder di Xcode. Infine, è stato illustrato tramite associazione dati complessi **controller di Array** e **albero controller**.


## <a name="related-links"></a>Collegamenti correlati

- [MacDatabinding Storyboard (esempio)](https://developer.xamarin.com/samples/mac/MacDatabinding-Storyboard/)
- [File Xib MacDatabinding (esempio)](https://developer.xamarin.com/samples/mac/MacDatabinding-XIBs/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Controlli standard](~/mac/user-interface/standard-controls.md)
- [Visualizzazioni di tabelle](~/mac/user-interface/table-view.md)
- [Visualizzazione della struttura](~/mac/user-interface/outline-view.md)
- [Visualizzazioni di raccolta](~/mac/user-interface/collection-view.md)
- [Guida per programmatori di codifica di tipo chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introduzione alla Guida per programmatori osservando chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Introduzione alle associazioni Cocoa argomenti di programmazione](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introduzione a Cocoa associazioni riferimento](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [macOS Human Interface Guidelines](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
