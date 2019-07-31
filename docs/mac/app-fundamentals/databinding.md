---
title: Data Binding e codifica chiave-valore in Novell. Mac
description: Questo articolo illustra l'uso della codifica chiave-valore e l'osservazione chiave-valore per consentire data binding agli elementi dell'interfaccia utente nella Interface Builder di Xcode.
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: dfc4c8a5f00fd11d1554dcadf5e35018046e49f4
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642904"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>Data Binding e codifica chiave-valore in Novell. Mac

_Questo articolo illustra l'uso della codifica chiave-valore e l'osservazione chiave-valore per consentire data binding agli elementi dell'interfaccia utente nella Interface Builder di Xcode._

## <a name="overview"></a>Panoramica

Quando si lavora C# con e .NET in un'applicazione Novell. Mac, si ha accesso alla stessa codifica chiave-valore e data binding tecniche che uno sviluppatore lavora in *Objective-C* e *Xcode* . Poiché Novell. Mac si integra direttamente con Xcode, è possibile usare _Interface Builder_ di Xcode per eseguire il binding dei dati con elementi dell'interfaccia utente anziché scrivere codice.

Utilizzando la codifica chiave-valore e le tecniche di data binding nell'applicazione Novell. Mac, è possibile ridurre significativamente la quantità di codice da scrivere e gestire per popolare e utilizzare gli elementi dell'interfaccia utente. Si ha anche il vantaggio di separare ulteriormente i dati di supporto (modello di_dati_) dall'interfaccia utente front-end (_Model-View-Controller_), in modo da semplificare la gestione e la progettazione di applicazioni più flessibili.

[![Esempio di app in esecuzione](databinding-images/intro01.png "Esempio di app in esecuzione")](databinding-images/intro01-large.png#lightbox)

In questo articolo verranno illustrate le nozioni di base sull'uso della codifica chiave-valore e data binding in un'applicazione Novell. Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [ C# esporre classi/metodi in Objective-C](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) , spiegando `Register` gli attributi e `Export` usati per collegare le C# classi a Oggetti Objective-C ed elementi dell'interfaccia utente.

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>Che cos'è la codifica chiave-valore

Il codice chiave-valore (KVC) è un meccanismo per accedere indirettamente alle proprietà di un oggetto, usando chiavi (stringhe formattate in modo particolare) per identificare le proprietà anziché accedervi tramite variabili di istanza`get/set`o metodi di funzione di accesso (). Implementando le funzioni di accesso conformi al codice chiave-valore nell'applicazione Novell. Mac, si ottiene l'accesso ad altre funzionalità di macOS (precedentemente note come OS X), ad esempio KVO (Key-Value osservating), data binding, core data, associazioni Cocoa e script.

Utilizzando la codifica chiave-valore e le tecniche di data binding nell'applicazione Novell. Mac, è possibile ridurre significativamente la quantità di codice da scrivere e gestire per popolare e utilizzare gli elementi dell'interfaccia utente. Si ha anche il vantaggio di separare ulteriormente i dati di supporto (modello di_dati_) dall'interfaccia utente front-end (_Model-View-Controller_), in modo da semplificare la gestione e la progettazione di applicazioni più flessibili. 

Ad esempio, esaminiamo la definizione di classe seguente di un oggetto conforme a KVC:

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

In primo luogo `[Register("PersonModel")]` , l'attributo registra la classe e la espone a Objective-C. Quindi, la classe deve ereditare da `NSObject` o da una sottoclasse che eredita `NSObject`da. in questo modo viene aggiunto un metodo di base che consente alla classe di essere conforme a KVC. Successivamente, l' `[Export("Name")]` attributo espone la `Name` proprietà e definisce il valore della chiave che verrà usato in un secondo momento per accedere alla proprietà tramite le tecniche KVC e KVO. 

Infine, per poter essere in grado di modificare il valore della proprietà in base al valore della proprietà, è necessario che la funzione di accesso `WillChangeValue` incapsulare le modifiche apportate al relativo `Export` valore nelle chiamate al metodo e `DidChangeValue` (specificando la stessa chiave dell'attributo).  Ad esempio:

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

Questo passaggio è _molto_ importante per data binding nella Interface Builder di Xcode (come si vedrà più avanti in questo articolo).

Per ulteriori informazioni, vedere la guida alla [programmazione per il codice chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)di Apple.

### <a name="keys-and-key-paths"></a>Chiavi e percorsi delle chiavi

Una _chiave_ è una stringa che identifica una proprietà specifica di un oggetto. In genere, una chiave corrisponde al nome di un metodo della funzione di accesso in un oggetto conforme al codice chiave-valore. Le chiavi devono usare la codifica ASCII, in genere iniziano con una lettera minuscola e non possono contenere spazi vuoti. Quindi, in base all'esempio `Name` precedente, sarebbe un valore chiave `Name` della proprietà della `PersonModel` classe. La chiave e il nome della proprietà che espongono non devono necessariamente essere uguali, ma nella maggior parte dei casi sono.

Un _percorso della chiave_ è una stringa di chiavi separate da punti, utilizzate per specificare una gerarchia di proprietà dell'oggetto da attraversare. La proprietà della prima chiave nella sequenza è relativa al ricevitore e ogni chiave successiva viene valutata in relazione al valore della proprietà precedente. Nello stesso modo in cui si usa la notazione del punto per attraversare un oggetto e le relative C# proprietà in una classe.

Ad esempio, se la classe è `PersonModel` stata espansa `Child` e la proprietà è stata aggiunta:

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

Il percorso della chiave per il nome del figlio sarebbe `self.Child.Name` o semplicemente `Child.Name` (in base alla modalità di utilizzo del valore della chiave).

### <a name="getting-values-using-key-value-coding"></a>Recupero di valori tramite codice chiave-valore

Il `ValueForKey` metodo restituisce il valore per la chiave specificata ( `NSString`come), relativo all'istanza della classe KVC che riceve la richiesta. Se `Person` , ad esempio, è un'istanza `PersonModel` della classe definita in precedenza:

```csharp
// Read value 
var name = Person.ValueForKey (new NSString("Name"));
```

Viene restituito il valore della `Name` proprietà per l'istanza di. `PersonModel` 

### <a name="setting-values-using-key-value-coding"></a>Impostazione dei valori con la codifica chiave-valore

Analogamente, `SetValueForKey` imposta il valore per la chiave specificata ( `NSString`come) rispetto all'istanza della classe KVC che riceve la richiesta. Di nuovo, usando un'istanza della `PersonModel` classe, come illustrato di seguito:

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

Modificare il valore della `Name` proprietà in. `Jane Doe`

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>Osservazione delle modifiche ai valori

Utilizzando l'osservazione chiave-valore (KVO), è possibile aggiungere un Observer a una chiave specifica di una classe conforme a KVC e ricevere una notifica ogni volta che il valore della chiave viene modificato (utilizzando tecniche KVC o accedendo direttamente alla proprietà specificata nel C# codice). Ad esempio:

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

A questo punto, ogni `Name` volta che viene `Person` modificata la proprietà `PersonModel` dell'istanza della classe, il nuovo valore viene scritto nella console. 

Per ulteriori informazioni, vedere l' [introduzione di Apple alla guida alla programmazione per l'osservazione di chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i).

## <a name="data-binding"></a>Associazione dati

Le sezioni seguenti illustrano come è possibile usare una codifica chiave-valore e una classe conforme all'osservazione del valore chiave per associare i dati agli elementi dell'interfaccia utente nel Interface Builder di Xcode, anziché leggere e C# scrivere valori usando il codice. In questo modo si separa il _modello di dati_ dalle viste usate per visualizzarle, rendendo l'applicazione Novell. Mac più flessibile e facile da gestire. Si riduce inoltre significativamente la quantità di codice da scrivere.

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>Definizione del modello di dati

Prima di poter associare un elemento dell'interfaccia utente in Interface Builder, è necessario avere una classe conforme a KVC/KVO definita nell'applicazione Novell. Mac per agire come _modello di dati_ per l'associazione. Il modello di dati fornisce tutti i dati che verranno visualizzati nell'interfaccia utente e riceve eventuali modifiche ai dati che l'utente effettua nell'interfaccia utente durante l'esecuzione dell'applicazione.

Se, ad esempio, si scrive un'applicazione che gestisce un gruppo di dipendenti, è possibile utilizzare la classe seguente per definire il modello di dati:

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

La maggior parte delle funzionalità di questa classe è stata descritta nella sezione informazioni sulla [codifica chiave-valore](#What_is_Key-Value_Coding) precedente. Tuttavia, verranno esaminati alcuni elementi specifici e alcune aggiunte apportate per consentire a questa classe di agire come modello di dati per i **controller di array** e i **controller di albero** (che verranno utilizzati in un secondo momento per le **visualizzazioni ad albero**di associazione dati, le **visualizzazioni struttura** e **visualizzazioni della raccolta**).

Innanzitutto, poiché un dipendente potrebbe essere un responsabile, abbiamo usato un `NSArray` (in particolare un oggetto `NSMutableArray` , in modo che i valori possano essere modificati) per consentire ai dipendenti di essere collegati:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

Ecco due punti da considerare:

1. È stato usato `NSMutableArray` un anziché una matrice C# o una raccolta standard, perché questo è un requisito per l'associazione dei dati ai controlli AppKit, ad esempio le **visualizzazioni tabella**, le **visualizzazioni struttura** e le **raccolte**.
2. È stata esposta la matrice di dipendenti eseguendone il cast `NSArray` a un oggetto per data binding e C# il nome formattato è stato modificato, `People`, in uno che data binding prevede, `personModelArray` nel formato **{class_name} array** (si noti che il primo il carattere è stato in lettere minuscole).

Successivamente, è necessario aggiungere alcuni metodi pubblici con nome specifico per supportare i **controller array** e i **controller di albero**:

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

Che consentono ai controller di richiedere e modificare i dati visualizzati. Analogamente a `NSArray` quanto esposto in precedenza, si tratta di una convenzione di denominazione molto specifica (che C# differisce dalle convenzioni di denominazione tipiche):

- `addObject:`: Aggiunge un oggetto alla matrice.
- `insertObject:in{class_name}ArrayAtIndex:`-Dove `{class_name}` è il nome della classe. Questo metodo inserisce un oggetto nella matrice in corrispondenza di un indice specificato.
- `removeObjectFrom{class_name}ArrayAtIndex:`-Dove `{class_name}` è il nome della classe. Questo metodo rimuove l'oggetto nella matrice in corrispondenza di un indice specificato.
- `set{class_name}Array:`-Dove `{class_name}` è il nome della classe. Questo metodo consente di sostituire il valore di Carry esistente con uno nuovo.

All'interno di questi metodi sono state incapsulate le modifiche apportate alla matrice in `WillChangeValue` e `DidChangeValue` i messaggi per la conformità KVO.

Infine, poiché la `Icon` proprietà si basa sul valore `isManager` della `isManager` proprietà, le modifiche apportate alla proprietà potrebbero non essere riflesse nell'oggetto `Icon` per gli elementi dell'interfaccia utente associati ai dati (durante KVO):

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

Per correggere questo, viene usato il codice seguente:

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

Si noti che, oltre alla propria chiave, la `isManager` funzione di accesso invia anche `WillChangeValue` i `DidChangeValue` messaggi e per `Icon` la chiave, in modo che venga visualizzata anche la modifica.

Il `PersonModel` modello di dati verrà usato nel resto di questo articolo.

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>Data binding semplice

Con il modello di dati definito, viene ora esaminato un semplice esempio di data binding nella Interface Builder di Xcode. Ad esempio, aggiungere un modulo all'applicazione Novell. Mac che può essere usata per modificare l'oggetto `PersonModel` definito in precedenza. Verranno aggiunti alcuni campi di testo e una casella di controllo per visualizzare e modificare le proprietà del modello.

Prima di tutto, aggiungere un nuovo **controller di visualizzazione** al file **Main. Storyboard** nel Interface Builder e denominare `SimpleViewController`la relativa classe: 

[![Aggiunta di un nuovo controller di visualizzazione](databinding-images/simple01.png "Aggiunta di un nuovo controller di visualizzazione")](databinding-images/simple01-large.png#lightbox)

Tornare quindi a Visual Studio per Mac, modificare il file **SimpleViewController.cs** , che è stato aggiunto automaticamente al progetto, ed esporre un'istanza del `PersonModel` in cui verrà data binding il modulo. Aggiungere il codice seguente:

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

A questo punto, quando viene caricata la vista, creare un'istanza `PersonModel` del e popolarla con il codice seguente:

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

A questo punto è necessario creare il form, fare doppio clic sul file **Main. Storyboard** per aprirlo per la modifica in Interface Builder. Layout il form sarà simile al seguente:

[![Modifica dello storyboard in Xcode](databinding-images/simple02.png "Modifica dello storyboard in Xcode")](databinding-images/simple02-large.png#lightbox)

Per associare i dati al modulo `PersonModel` esposto tramite la `Person` chiave, eseguire le operazioni seguenti:

1. Selezionare il campo di testo **nome dipendente** e passare al **controllo Binding**.
2. Selezionare la casella **associa a** e selezionare **Simple View Controller** nell'elenco a discesa. Immettere `self.Person.Name` quindi il **percorso della chiave**: 

    [![Immissione del percorso della chiave](databinding-images/simple03.png "Immissione del percorso della chiave")](databinding-images/simple03-large.png#lightbox)
3. Selezionare il campo di testo **occupazione** , selezionare la casella **associa a** e selezionare **Simple View Controller** nell'elenco a discesa. Immettere `self.Person.Occupation` quindi il **percorso della chiave**:  

    [![Immissione del percorso della chiave](databinding-images/simple04.png "Immissione del percorso della chiave")](databinding-images/simple04-large.png#lightbox)
4. Selezionare la casella di controllo **Employee è a Manager** e selezionare la casella **Bind to** e selezionare **Simple View Controller** nell'elenco a discesa. Immettere `self.Person.isManager` quindi il **percorso della chiave**:  

    [![Immissione del percorso della chiave](databinding-images/simple05.png "Immissione del percorso della chiave")](databinding-images/simple05-large.png#lightbox)
5. Selezionare il campo **numero di dipendenti gestiti** del testo e selezionare la casella **associa a** e selezionare **Simple View Controller** nell'elenco a discesa. Immettere `self.Person.NumberOfEmployees` quindi il **percorso della chiave**:  

    [![Immissione del percorso della chiave](databinding-images/simple06.png "Immissione del percorso della chiave")](databinding-images/simple06-large.png#lightbox)
6. Se il dipendente non è un responsabile, si vuole nascondere il numero di dipendenti etichetta gestita e campo di testo.
7. Selezionare l'etichetta **numero di dipendenti gestiti** , espandere la couvertura **nascosta** e selezionare la casella **associa a** e selezionare **Simple View Controller** nell'elenco a discesa. Immettere `self.Person.isManager` quindi il **percorso della chiave**:  

    [![Immissione del percorso della chiave](databinding-images/simple07.png "Immissione del percorso della chiave")](databinding-images/simple07-large.png#lightbox)
8. Selezionare `NSNegateBoolean` dall'elenco a discesa del trasformatore del **valore** :  

    ![Selezione della trasformazione chiave NSNegateBoolean](databinding-images/simple08.png "Selezione della trasformazione chiave NSNegateBoolean")
9. Indica data binding che l'etichetta sarà nascosta se il valore della `isManager` proprietà è. `false`
10. Ripetere i passaggi 7 e 8 per il campo **numero di dipendenti del testo gestito** .
11. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue l'applicazione, i valori `Person` della proprietà compileranno automaticamente il modulo:

[![Visualizzazione di un form popolato automaticamente](databinding-images/simple09.png "Visualizzazione di un form popolato automaticamente")](databinding-images/simple09-large.png#lightbox)

Tutte le modifiche apportate dagli utenti al modulo verranno riscritte `Person` nella proprietà del controller di visualizzazione. Ad esempio, la deselezione di **Employee è un gestore** `PersonModel` che `Person` aggiorna l'istanza di e il campo di testo e l'etichetta **gestita dei dipendenti** vengono nascosti automaticamente (tramite Data Binding):

[![Nascondere il numero di dipendenti per i non responsabili](databinding-images/simple10.png "Nascondere il numero di dipendenti per i non responsabili")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>Visualizzazione tabella data binding

Ora che sono state apportate le nozioni di base di data binding, è possibile esaminare un'attività data binding più complessa usando un _controller array_ e data binding a una visualizzazione tabella. Per ulteriori informazioni sull'utilizzo delle visualizzazioni tabella, vedere la documentazione relativa alle [visualizzazioni di tabella](~/mac/user-interface/table-view.md) .

Prima di tutto, aggiungere un nuovo **controller di visualizzazione** al file **Main. Storyboard** nel Interface Builder e denominare `TableViewController`la relativa classe:

[![Aggiunta di un nuovo controller di visualizzazione](databinding-images/table01.png "Aggiunta di un nuovo controller di visualizzazione")](databinding-images/table01-large.png#lightbox)

Modificare quindi il file **TableViewController.cs** , che è stato aggiunto automaticamente al progetto, ed esporre un Array (`NSArray`) di `PersonModel` classi a cui verrà data binding il modulo. Aggiungere il codice seguente:

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

Come per la `PersonModel` classe precedente nella sezione [definizione del modello di dati](#Defining_your_Data_Model) , abbiamo esposto quattro metodi pubblici appositamente denominati in modo che il controller dell'array e leggano e scrivano i dati dalla `PersonModels`raccolta di.

A questo punto, quando viene caricata la vista, è necessario popolare la matrice con il codice seguente:

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

A questo punto è necessario creare la visualizzazione tabella, fare doppio clic sul file **Main. Storyboard** per aprirlo per la modifica in Interface Builder. Layout della tabella con un aspetto simile al seguente:

[![Layout di una nuova visualizzazione tabella](databinding-images/table02.png "Layout di una nuova visualizzazione tabella")](databinding-images/table02-large.png#lightbox)

È necessario aggiungere un **controller di matrice** per fornire i dati associati alla tabella, procedere come segue:

1. Trascinare un **controller di matrice** da **libreria Inspector** nell' **editor di interfaccia**:  

    ![Selezione di un controller di array dalla libreria](databinding-images/table03.png "Selezione di un controller di array dalla libreria")
2. Selezionare **controller di matrice** nella **gerarchia dell'interfaccia** e passare al controllo **attributi**:  

    [![Selezione del controllo attributi](databinding-images/table04.png "Selezione del controllo attributi")](databinding-images/table04-large.png#lightbox)
3. Immettere `PersonModel` per il **nome della classe**, fare clic sul pulsante con il **segno più** e aggiungere tre chiavi. Assegnare loro `Name` `Occupation` un nome e: `isManager`  

    ![Aggiunta dei percorsi chiave richiesti](databinding-images/table05.png "Aggiunta dei percorsi chiave richiesti")
4. In questo modo si indica al controller dell'array che cosa gestisce una matrice di e quali proprietà deve esporre (tramite chiavi).
5. Passare al controllo **Binding** e in matrice di **contenuto** Selezionare **associa a** e **controller di visualizzazione tabella**. Immettere il **percorso** di una chiave `self.personModelArray`del modello:  

    ![Immissione di un percorso della chiave](databinding-images/table06.png "Immissione di un percorso della chiave")
6. In questo modo il controller di array viene associato `PersonModels` alla matrice di esposta sul controller di visualizzazione.

A questo punto è necessario associare la visualizzazione tabella al controller Array, procedere come segue:

1. Selezionare la visualizzazione tabella e il **controllo Binding**:  

    [![Selezione del controllo Binding](databinding-images/table07.png "Selezione del controllo Binding")](databinding-images/table07-large.png#lightbox)
2. In preparazione **contenuto tabella** selezionare **associa a** e controller di **Array**. Immettere `arrangedObjects` per il campo **chiave del controller** :  

    ![Definizione della chiave del controller](databinding-images/table08.png "Definizione della chiave del controller")
3. Selezionare la **cella di visualizzazione tabella** nella colonna **Employee** . Nel **controllo delle associazioni** sotto il **valore** di couvertura selezionare **associa a** e **visualizzazione celle tabella**. Immettere `objectValue.Name` per il **percorso della chiave del modello**:  

    [![Impostazione del percorso della chiave del modello](databinding-images/table09.png "Impostazione del percorso della chiave del modello")](databinding-images/table09-large.png#lightbox)
4. `objectValue`è l'oggetto `PersonModel` corrente nella matrice gestita dal controller dell'array.
5. Selezionare la **cella di visualizzazione tabella** nella colonna **occupazione** . Nel **controllo delle associazioni** sotto il **valore** di couvertura selezionare **associa a** e **visualizzazione celle tabella**. Immettere `objectValue.Occupation` per il **percorso della chiave del modello**:  

    [![Impostazione del percorso della chiave del modello](databinding-images/table10.png "Impostazione del percorso della chiave del modello")](databinding-images/table10-large.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue l'applicazione, la tabella verrà popolata con la matrice di `PersonModels`:

[![Esecuzione dell'applicazione](databinding-images/table11.png "Esecuzione dell'applicazione")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>visualizzazione Struttura data binding

data binding rispetto a una visualizzazione struttura è molto simile all'associazione rispetto a una visualizzazione tabella. La differenza principale consiste nel fatto che si utilizzerà un **controller di struttura ad albero** anziché un controller di **Array** per fornire i dati associati alla visualizzazione struttura. Per ulteriori informazioni sull'utilizzo delle visualizzazioni struttura, consultare la documentazione relativa alle [visualizzazioni struttura](~/mac/user-interface/outline-view.md) .

Prima di tutto, aggiungere un nuovo **controller di visualizzazione** al file **Main. Storyboard** nel Interface Builder e denominare `OutlineViewController`la relativa classe: 

[![Aggiunta di un nuovo controller di visualizzazione](databinding-images/outline01.png "Aggiunta di un nuovo controller di visualizzazione")](databinding-images/outline01-large.png#lightbox)

Modificare quindi il file **OutlineViewController.cs** , che è stato aggiunto automaticamente al progetto, ed esporre un Array (`NSArray`) di `PersonModel` classi a cui verrà data binding il modulo. Aggiungere il codice seguente:

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

Come per la `PersonModel` classe precedente nella sezione [definizione del modello di dati](#Defining_your_Data_Model) , abbiamo esposto quattro metodi pubblici appositamente denominati in modo che il controller dell'albero e leggano e scrivano i dati dalla `PersonModels`raccolta di.

A questo punto, quando viene caricata la vista, è necessario popolare la matrice con il codice seguente:

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

A questo punto è necessario creare la visualizzazione struttura, fare doppio clic sul file **Main. Storyboard** per aprirlo per la modifica in Interface Builder. Layout della tabella con un aspetto simile al seguente:

[![Creazione della visualizzazione struttura](databinding-images/outline02.png "Creazione della visualizzazione struttura")](databinding-images/outline02-large.png#lightbox)

È necessario aggiungere un **controller di struttura ad albero** per fornire i dati associati alla struttura, seguire questa procedura:

1. Trascinare un **controller di struttura ad albero** da **libreria Inspector** nell' **editor di interfaccia**:  

    ![Selezione di un controller albero dalla libreria](databinding-images/outline03.png "Selezione di un controller albero dalla libreria")
2. Selezionare **controller albero** nella **gerarchia dell'interfaccia** e passare al controllo **attributi**:  

    [![Selezione dell'attributo Inspector](databinding-images/outline04.png "Selezione dell'attributo Inspector")](databinding-images/outline04-large.png#lightbox)
3. Immettere `PersonModel` per il **nome della classe**, fare clic sul pulsante con il **segno più** e aggiungere tre chiavi. Assegnare loro `Name` `Occupation` un nome e: `isManager`  

    ![Aggiunta dei percorsi chiave richiesti](databinding-images/outline05.png "Aggiunta dei percorsi chiave richiesti")
4. Indica al controller dell'albero che cosa gestisce una matrice di e quali proprietà deve esporre (tramite chiavi).
5. Nella sezione **controller albero** `personModelArray` immettere per **elementi figlio**, `NumberOfEmployees` immettere sotto il **conteggio** e immettere `isEmployee` in **foglia**:  

    ![Impostazione dei percorsi delle chiavi del controller dell'albero](databinding-images/outline05.png "Impostazione dei percorsi delle chiavi del controller dell'albero")
6. Indica al controller dell'albero dove trovare i nodi figlio, il numero di nodi figlio presenti e se il nodo corrente ha nodi figlio.
7. Passare al **controllo Binding** e in matrice di **contenuto** Selezionare **associa a** e **proprietario del file**. Immettere il **percorso** di una chiave `self.personModelArray`del modello:  

    ![Modifica del percorso della chiave](databinding-images/outline06.png "Modifica del percorso della chiave")
8. In questo modo il controller dell'albero viene associato `PersonModels` alla matrice di esposta sul controller di visualizzazione.

A questo punto è necessario associare la visualizzazione struttura al controller dell'albero, procedere come segue:

1. Selezionare la visualizzazione struttura e in **Binding Inspector** selezionare:  

    [![Selezione del controllo Binding](databinding-images/outline07.png "Selezione del controllo Binding")](databinding-images/outline07-large.png#lightbox)
2. Sotto la sezione del **contenuto della visualizzazione struttura** , selezionare **associa a** e **controller albero**. Immettere `arrangedObjects` per il campo **chiave del controller** :  

    ![Impostazione della chiave del controller](databinding-images/outline08.png "Impostazione della chiave del controller")
3. Selezionare la **cella di visualizzazione tabella** nella colonna **Employee** . Nel **controllo delle associazioni** sotto il **valore** di couvertura selezionare **associa a** e **visualizzazione celle tabella**. Immettere `objectValue.Name` per il **percorso della chiave del modello**:  

    [![Immissione del percorso della chiave del modello](databinding-images/outline09.png "Immissione del percorso della chiave del modello")](databinding-images/outline09-large.png#lightbox)
4. `objectValue`è l'oggetto `PersonModel` corrente nella matrice gestita dal controller dell'albero.
5. Selezionare la **cella di visualizzazione tabella** nella colonna **occupazione** . Nel **controllo delle associazioni** sotto il **valore** di couvertura selezionare **associa a** e **visualizzazione celle tabella**. Immettere `objectValue.Occupation` per il **percorso della chiave del modello**:  

    [![Immissione del percorso della chiave del modello](databinding-images/outline10.png "Immissione del percorso della chiave del modello")](databinding-images/outline10-large.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue l'applicazione, il contorno verrà popolato con la matrice `PersonModels`di:

[![Esecuzione dell'applicazione](databinding-images/outline11.png "Esecuzione dell'applicazione")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>Visualizzazione raccolta data binding

Il data binding con una visualizzazione di raccolta è molto simile al binding con una visualizzazione tabella, perché viene usato un controller di matrice per fornire i dati per la raccolta. Poiché la visualizzazione raccolta non ha un formato di visualizzazione preimpostato, è necessario più lavoro per fornire commenti e suggerimenti sull'interazione dell'utente e per tenere traccia della selezione dell'utente.

> [!IMPORTANT]
> A causa di un problema in Xcode 7 e macOS 10,11 (e versioni successive), le visualizzazioni di raccolta non possono essere usate all'interno di un file storyboard (con estensione Storyboard). Sarà quindi necessario continuare a usare i file con estensione XIB per definire le visualizzazioni di raccolta per le app Novell. Mac. Per ulteriori informazioni, vedere la documentazione relativa alle [viste della raccolta](~/mac/user-interface/collection-view.md) .

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

## <a name="debugging-native-crashes"></a>Debug di arresti anomali nativi

La creazione di un errore nelle associazioni dati può causare un _arresto anomalo nativo_ del codice non gestito e causare un `SIGABRT` errore dell'applicazione Novell. Mac:

[![Esempio di una finestra di dialogo di arresto anomalo nativo](databinding-images/debug01.png "Esempio di una finestra di dialogo di arresto anomalo nativo")](databinding-images/debug01-large.png#lightbox)

Esistono in genere quattro cause principali per arresti anomali nativi durante data binding:

1. Il modello di dati non eredita da `NSObject` o da una sottoclasse di. `NSObject`
2. La proprietà non è stata esposta a Objective-C utilizzando l' `[Export("key-name")]` attributo.
3. Non è stato eseguito il wrapping delle modifiche al valore della `WillChangeValue` funzione `DidChangeValue` di accesso in e le chiamate al metodo ( `Export` specificando la stessa chiave dell'attributo).
4. Nel **controllo Binding** è presente una chiave errata o digitata in Interface Builder.

### <a name="decoding-a-crash"></a>Decodifica di un arresto anomalo

Si verificherà un arresto anomalo nativo del data binding per illustrare come individuare e risolvere il problema. In Interface Builder modificare il binding della prima etichetta nell'esempio di visualizzazione della raccolta da `Name` a: `Title`

[![Modifica della chiave di associazione](databinding-images/debug02.png "Modifica della chiave di associazione")](databinding-images/debug02-large.png#lightbox)

Salvare la modifica, tornare a Visual Studio per Mac per sincronizzarla con Xcode ed eseguire l'applicazione. Quando viene visualizzata la visualizzazione raccolta, l'applicazione si arresterà temporaneamente in `SIGABRT` modo anomalo con un errore (come illustrato nell' **output dell'applicazione** in `PersonModel` Visual Studio per Mac) perché non espone una proprietà `Title`con la chiave:

[![Esempio di errore di binding](databinding-images/debug03.png "Esempio di errore di binding")](databinding-images/debug03-large.png#lightbox)

Se scorriamo fino alla parte superiore dell'errore nell'output dell' **applicazione** , possiamo vedere la chiave per risolvere il problema:

[![Individuazione del problema nel log degli errori](databinding-images/debug04.png "Individuazione del problema nel log degli errori")](databinding-images/debug04-large.png#lightbox)

Questa riga indica che la chiave `Title` non esiste nell'oggetto a cui si sta associando. Se si modifica il binding `Name` in Interface Builder, Salva, sincronizza, ricompila ed Esegui, l'applicazione viene eseguita come previsto senza problema.

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso di data binding e la codifica chiave-valore in un'applicazione Novell. Mac. In primo luogo, è stata esaminata C# l'esposizione di una classe a Objective-C utilizzando la codifica chiave-valore (KVC) e l'osservazione chiave-valore (KVO). Successivamente, è stato illustrato come usare una classe conforme a KVO e i dati lo associano agli elementi dell'interfaccia utente nel Interface Builder di Xcode. Infine, ha mostrato data binding complesse usando **controller di array** e **controller di albero**.


## <a name="related-links"></a>Collegamenti correlati

- [Storyboard MacDatabinding (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-storyboard)
- [MacDatabinding XIB (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-xibs)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Controlli standard](~/mac/user-interface/standard-controls.md)
- [Viste tabella](~/mac/user-interface/table-view.md)
- [Visualizzazione struttura](~/mac/user-interface/outline-view.md)
- [Viste di raccolta](~/mac/user-interface/collection-view.md)
- [Guida alla programmazione del codice chiave-valore](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introduzione alla guida alla programmazione per l'osservazione del valore chiave](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Introduzione agli argomenti di programmazione delle associazioni Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Informazioni di riferimento sulle associazioni di Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [Linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
