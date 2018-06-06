---
title: Associazione di raccolte Objective-C
description: Questo documento fornisce una panoramica generale di creazione in c# le associazioni al codice Objective-C, che descrive come associare gli eventi, metodi, i controlli personalizzati e altro ancora.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: f7c4be4254ce3e3301c0c1e98d37134f5524c23b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782320"
---
# <a name="binding-objective-c-libraries"></a>Associazione di raccolte Objective-C

Quando si lavora con xamarin. IOS o Xamarin.Mac, potrebbero verificarsi casi in cui si desidera utilizzare una libreria di terze parti Objective-C. In questi casi, è possibile utilizzare progetti di associazione di Xamarin per creare un'associazione alle librerie native Objective-C in c#. Il progetto utilizza gli stessi strumenti utilizzati per portare il iOS e Mac API c#.

Questo documento viene descritto come associare le API di Objective-C, se si desidera associare solo le API di C, utilizzare il meccanismo standard di .NET a tale scopo, [il framework di P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/).
Dettagli su come collegare in modo statico una libreria di C sono disponibili sul [collegamento librerie Native](~/ios/platform/native-interop.md) pagina.

Consultare il manuale [Guida di riferimento per i tipi di associazione](~/cross-platform/macios/binding/binding-types-reference.md).
Inoltre, se si desidera ulteriori informazioni su ciò che accade dietro le quinte, controllare il nostro [Panoramica del Binding](~/cross-platform/macios/binding/overview.md) pagina.

Associazioni possono essere compilate per iOS e librerie Mac.
Questa pagina viene descritto come utilizzare in un'associazione, tuttavia le associazioni Mac sono molto simili di iOS.

**Codice di esempio per iOS**

È possibile utilizzare il [iOS Binding Sample](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) progetto per sperimentare le associazioni.

<a name="Getting_Started" />

## <a name="getting-started"></a>Per iniziare

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Il modo più semplice per creare un'associazione consiste nel creare un progetto di associazione di xamarin. IOS.
È possibile farlo da Visual Studio per Mac, selezionare il tipo di progetto, **iOS > Libreria > raccolta di associazioni**:

[![](objective-c-libraries-images/00-sml.png "Eseguire questa operazione da Visual Studio per Mac, selezionare il tipo di progetto libreria di associazioni di iOS")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Il modo più semplice per creare un'associazione consiste nel creare un progetto di associazione di xamarin. IOS.
È possibile farlo da Visual Studio in Windows, selezionare il tipo di progetto, **Visual c# > iOS > raccolta di associazioni (iOS)**:

[![](objective-c-libraries-images/00vs-sml.png "Raccolta di associazioni iOS iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Nota: Associazione di progetti per **Xamarin.Mac** sono supportate solo in Visual Studio per Mac.

-----

Il progetto generato contiene un modello di piccole dimensioni che è possibile modificare, contiene due file: `ApiDefinition.cs` e `StructsAndEnums.cs`.

Il `ApiDefinition.cs` verrà definisce il contratto API, si tratta del file che descrive il modo in cui viene proiettato API Objective-C sottostante in c#. La sintassi e il contenuto di questo file è l'argomento principale di discussione di questo documento e il contenuto è limitato alle interfacce c# e dichiarazioni di delegato c#. Il `StructsAndEnums.cs` file è il file in cui immettere eventuali definizioni necessarie per le interfacce e delegati. Sono inclusi i valori di enumerazione e strutture che potrebbe utilizzare il codice.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Un'API di associazione

Per eseguire un'associazione completa, è possibile comprendere la definizione dell'API Objective-C e acquisire familiarità con le linee guida di progettazione di .NET Framework.

Per associare la raccolta in genere si inizierà con un file di definizione API. Un file di definizione API è semplicemente un c# file di origine che contiene le interfacce in c# che sono state annotate con un numero limitato di attributi che consentono di controllare l'associazione.  Questo file è quello definito che cos'è il contratto tra i linguaggi c# e Objective-C.

Ad esempio, questo è un file di api semplice per una libreria:

```csharp
using Foundation;

namespace Cocos2D {
  [BaseType (typeof (NSObject))]
  interface Camera {
    [Static, Export ("getZEye")]
    nfloat ZEye { get; }

    [Export ("restore")]
    void Restore ();

    [Export ("locate")]
    void Locate ();

    [Export ("setEyeX:eyeY:eyeZ:")]
    void SetEyeXYZ (nfloat x, nfloat y, nfloat z);

    [Export ("setMode:")]
    void SetMode (CameraMode mode);
  }
}
```

L'esempio precedente definisce una classe denominata `Cocos2D.Camera` che deriva dal `NSObject` tipo di base (questo tipo proviene da `Foundation.NSObject`) e che definisce una proprietà statica (`ZEye`), due metodi che accettano alcun argomento e un metodo che accetta tre argomenti.

In cui verrà illustrata un'analisi approfondita del formato del file dell'API e gli attributi che è possibile utilizzare il [file di definizione dell'API](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) sezione riportata di seguito.

Per produrre un binding completo, in genere gestire con quattro componenti:

-  Il file di definizione API (`ApiDefinition.cs` nel modello).
-  Facoltativo: le enumerazioni, tipi, struct necessari dal file di definizione API (`StructsAndEnums.cs` nel modello).
-  Facoltative: origini aggiuntive che possono espandere l'associazione generata o fornire una più descrittiva API c# (c# file aggiunti al progetto).
-  La libreria nativa che si desidera associare.

Questo grafico mostra la relazione tra i file:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "Questo grafico mostra la relazione tra i file")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

Il file di definizione dell'API conterrà solo le definizioni di interfaccia e gli spazi dei nomi (con tutti i membri che può contenere un'interfaccia) e non deve contenere classi, enumerazioni, delegati o strutture. Il file di definizione API è semplicemente il contratto che verrà utilizzato per generare l'API.

Qualsiasi codice aggiuntivo che è necessario come enumerazioni o le classi di supporto devono essere ospitata in un file separato, come nell'esempio precedente "CameraMode" è un valore di enumerazione che non esiste nel file CS e deve essere ospitato in un file separato, ad esempio `StructsAndEnums.cs` :

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

Il `APIDefinition.cs` viene combinato con il `StructsAndEnum` classe e vengono utilizzati per generare l'associazione di base della libreria. È possibile utilizzare la raccolta risulta come-è, ma in genere, è possibile ottimizzare la libreria risulta per aggiungere alcune funzionalità c# beneficio degli utenti. Alcuni esempi includono l'implementazione di un `ToString()` metodo, fornire gli indicizzatori di c#, aggiungere le conversioni implicite in e da alcuni tipi nativi o fornire versioni fortemente tipizzata di alcuni metodi. Questi miglioramenti sono archiviati in file aggiuntivi in c#. Aggiungere semplicemente i file c# al progetto e saranno inclusi in questo processo di compilazione.

Viene illustrato come implementare il codice del `Extra.cs` file. Si noti che verrà utilizzato classi parziali come questi aumentare le classi parziali che vengono generate dalla combinazione del `ApiDefinition.cs` e `StructsAndEnums.cs` associazione di base:

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

La generazione della libreria produrrà l'associazione nativa.

Per completare questa associazione, è necessario aggiungere la libreria nativa al progetto.  Questo scopo, è possibile aggiungere la libreria nativa al progetto, trascinando e rilasciando la libreria nativa dal Finder nel progetto in Esplora soluzioni o facendo clic al progetto e scegliendo **Aggiungi**  >  **Aggiungi file** per selezionare la libreria nativa.
Librerie native per convenzione iniziano con la parola "lib" e terminano con l'estensione "con estensione". Quando si esegue questa operazione, Visual Studio per Mac aggiungerà due file: il file con estensione a e un file c# automaticamente popolato contenente informazioni sul contenuto della libreria nativa:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Librerie native per convenzione, iniziano con la libreria di word e terminano con il con estensione estensione")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

Il contenuto del `libMagicChord.linkwith.cs` dispone di informazioni sull'utilizzo di questa raccolta di file e indica l'IDE per questo file binario del pacchetto nel file DLL risulta:

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Completa i dettagli su come usare il [ `[LinkWith]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) attributo sono documentati nel [Guida di riferimento per i tipi di associazione](~/cross-platform/macios/binding/binding-types-reference.md).

Ora quando si compila il progetto si finirà con un `MagicChords.dll` file che contiene l'associazione e la libreria nativa. È possibile distribuire il progetto o utilizzare il file DLL risultante agli altri sviluppatori per le proprie.

In alcuni casi si potrebbe rilevare che è necessario alcuni valori di enumerazione, definizioni di delegati o altri tipi. Non inserire quelli nel file di definizione API, poiché si tratta semplicemente di un contratto

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>Il file di definizione API

Il file di definizione API è costituito da un numero di interfacce. Le interfacce nella definizione dell'API si trasformerà in una dichiarazione di classe e deve essere decorate con il [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) attributo per specificare la classe basa per la classe.

È lecito chiedersi perché è stata utilizzata classi anziché le interfacce per la definizione di contratto. Abbiamo scelto interfacce perché consentita per il contratto per un metodo di scrittura senza dover fornire un corpo del metodo nel file di definizione API o la necessità di fornire un corpo di un'eccezione o restituisce un valore significativo.

Tuttavia, poiché si sta utilizzando l'interfaccia come scheletro per generare una classe, era necessario ricorrere al decorazione di varie parti del contratto con attributi per guidare l'associazione.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Associazione di metodi

L'associazione più semplice che procedere consiste nell'associare un metodo. È sufficiente dichiarare un metodo nell'interfaccia con le convenzioni di denominazione in c# e decorare il metodo con il [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributo. Il [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributo è quali collegamenti il nome di c# con il nome di Objective-C in fase di esecuzione di xamarin. IOS. Il parametro del [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributo è il nome del selettore di Objective-C. Ecco alcuni esempi:

```csharp
// A method, that takes no arguments
[Export ("refresh")]
void Refresh ();

// A method that takes two arguments and return the result
[Export ("add:and:")]
nint Add (nint a, nint b);

// A method that takes a string
[Export ("draw:atColumn:andRow:")]
void Draw (string text, nint column, nint row);
```

Negli esempi sopra viene illustrato come associare i metodi di istanza. Per associare i metodi statici, è necessario usare il [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) attributo, simile al seguente:

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Ciò è necessario perché il contratto è parte di un'interfaccia e interfacce non hanno alcun concetto di dichiarazioni di istanza statica di Visual Studio, pertanto è nuovamente occorre fare ricorso agli attributi. Se si desidera nascondere un metodo particolare per l'associazione, è possibile decorare il metodo con il [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attributo.

Il `btouch-native` comando introdurrà controlli per i parametri di riferimento che non sia null. Se si desidera consentire valori null per un determinato parametro, utilizzare il [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) attributo al parametro, simile al seguente:

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Durante l'esportazione di un tipo riferimento, con la [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) parola chiave è anche possibile specificare la semantica di allocazione. Ciò è necessario per garantire che nessun dato viene perso.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Proprietà di binding

Proprio come i metodi, proprietà Objective-C vengono associate usando il [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributi e mapping direttamente alle proprietà di c#. Proprio come i metodi, proprietà possono essere decorate con il [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) e il [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attributi.

Quando si usa il [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributo su una proprietà in realtà btouch-nativo associa effettivamente due metodi: il metodo Get e set. Il nome che si fornisce per esportare il **basename** e viene calcolato il setter anteponendo la parola "set", attivare la prima lettera del **basename** in lettere maiuscole e rendere il selettore di richiedere un argomento. Ciò significa che `[Export ("label")]` applicata a una proprietà effettivamente associa "label" e "setLabel:" metodi Objective-C.

A volte la proprietà Objective-C non seguono il modello descritto in precedenza e il nome viene sovrascritto manualmente. In questi casi è possibile controllare il modo che l'associazione viene generato usando il [ `[Bind]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) dell'attributo IsDefault sul getter o setter, ad esempio:

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

Viene quindi associa "isMenuVisible" e "setMenuVisible:". Facoltativamente, una proprietà può essere associata utilizzando la sintassi seguente:

```csharp
[Category, BaseType(typeof(UIView))]
interface UIView_MyIn
{
  [Export ("name")]
  string Name();

  [Export("setName:")]
  void SetName(string name);
}
```

In cui get e set sono definite esplicitamente come nel `name` e `setName` associazioni precedente.

Oltre a supporto per le proprietà statiche utilizzando [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute), è possibile decorare proprietà thread statiche con [ `[IsThreadStatic]` ](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute), ad esempio:

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Proprio come i metodi consentono di alcuni parametri per essere contrassegnate con [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute), è possibile applicare [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) a una proprietà per indicare che null è un valore valido per la proprietà, ad esempio:

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

Il [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) può anche specificare il parametro direttamente nel metodo di impostazione:

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Avvertenze di associazione di controlli personalizzati

Quando si configura l'associazione per un controllo personalizzato, è necessario considerare i seguenti avvertimenti:

1. **Proprietà di associazione devono essere statiche** - quando si definisce l'associazione delle proprietà, il [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) attributo deve essere usato.
 2. **I nomi di proprietà devono corrispondere esattamente** -il nome utilizzato per associare la proprietà deve corrispondere al nome della proprietà nel controllo personalizzato esattamente.
3. **Tipi di proprietà devono corrispondere esattamente** -il tipo di variabile utilizzato per associare la proprietà deve corrispondere al tipo della proprietà nel controllo personalizzato esattamente.
4. **I punti di interruzione e il getter/setter** - posizionati i punti di interruzione nel getter o metodi per l'impostazione della proprietà non verranno mai attivati.
5. **Osservare i callback** -è necessario utilizzare i callback di osservazione per ricevere notifica delle modifiche nei valori delle proprietà di controlli personalizzati.

L'associazione automatica esito negativo in fase di esecuzione può comportare il mancato rispetto delle avvertenze elencate sopra.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Le proprietà e pattern modificabile objective-C

Framework Objective-C, usano un linguaggio in cui alcune classi non sono modificabili con una sottoclasse modificabile. Ad esempio `NSString` è la versione non modificabile, mentre `NSMutableString` è della sottoclasse che consente di mutazione.

In queste classi è frequente vedere la classe di base non modificabile contiene le proprietà con un metodo Get, ma non ci sono setter. E per la versione modificabile di introdurre il setter. Poiché non è effettivamente possibile con c#, è stato necessario eseguire il mapping di questo linguaggio in un linguaggio che funzionerà con c#.

Il modo che questa proprietà è associata a c# prevede l'aggiunta di entrambi i metodi get e set sulla classe base, ma quando si contrassegna il metodo di impostazione con un [ `[NotImplemented]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute) attributo.

Quindi, all'interno della sottoclasse modificabile, usare il [ `[Override]` ](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) attributo sulla proprietà per assicurarsi che la proprietà è effettivamente override del comportamento dell'elemento padre.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
interface MyTree {
    string Name { get; [NotImplemented] set; }
}

[BaseType (typeof (MyTree))]
interface MyMutableTree {
    [Override]
    string Name { get; set; }
}
```

<a name="Binding_Constructors" />

### <a name="binding-constructors"></a>Costruttori di associazione

Il `btouch-native` lo strumento genererà automaticamente i costruttori di quattro nella classe, per una determinata classe `Foo`, viene generato:

-  `Foo ()`: il costruttore predefinito (esegue il mapping al costruttore "init" Objective-C)
-  `Foo (NSCoder)`: il costruttore usato durante la deserializzazione del file NIB (esegue il mapping a Objective-C "initWithCoder:" costruttore).
-  `Foo (IntPtr handle)`: il costruttore per la creazione di basate su handle, viene richiamato dal runtime quando il runtime deve esporre un oggetto gestito da un oggetto non gestito.
-  `Foo (NSEmptyFlag)`: viene utilizzato dalle classi derivate per impedire inizializzato due volte.

Per i costruttori definiti, devono essere dichiarati mediante la firma seguente all'interno della definizione di interfaccia: devono restituire un `IntPtr` valore e il nome del metodo deve essere il costruttore. Ad esempio per associare il `initWithFrame:` costruttore, è necessario utilizzare:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Protocolli di associazione

Come descritto nel documento di progettazione API, nella sezione [discutere dei modelli e i protocolli](~/ios/internals/api-design/index.md#Models), xamarin. IOS viene eseguito il mapping dei protocolli Objective-C nelle classi che sono state contrassegnate con il [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) attributo. Viene in genere utilizzato quando si implementa le classi delegate Objective-C.

La grande differenza tra una classe normale associata e una classe delegata è che la classe delegata potrebbe avere uno o più metodi facoltativi.

Si consideri ad esempio il `UIKit` classe `UIAccelerometerDelegate`, questa è la modalità con cui è associato in xamarin. ios:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Poiché si tratta di un metodo facoltativo nella definizione di per `UIAccelerometerDelegate` non c'è niente altro. Ma se si è verificato un metodo di richiesta sul protocollo, è necessario aggiungere il [ `[Abstract]` ](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute) attributo al metodo. Questa operazione forzerà l'utente dell'implementazione per garantire un corpo del metodo.

In generale, i protocolli vengono utilizzati nelle classi che rispondono ai messaggi. Ciò in genere avviene in Objective-C tramite l'assegnazione alla proprietà "delegato" un'istanza di un oggetto che risponde ai metodi nel protocollo.

La convenzione di xamarin. IOS è supportare entrambi Objective-C regime stile in qualsiasi istanza di un `NSObject` può essere assegnato al delegato e anche esporre a una versione fortemente tipizzata. Per questo motivo, è in genere forniscono entrambi una `Delegate` proprietà che deve essere fortemente tipizzato e una `WeakDelegate` non fortemente tipizzata. In genere si associa la versione non fortemente tipizzato con [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute), e viene usata la [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) attributo per fornire la versione fortemente tipizzata.

Viene illustrato come è associata la `UIAccelerometer` classe:

```csharp
[BaseType (typeof (NSObject))]
interface UIAccelerometer {
        [Static] [Export ("sharedAccelerometer")]
        UIAccelerometer SharedAccelerometer { get; }

        [Export ("updateInterval")]
        double UpdateInterval { get; set; }

        [Wrap ("WeakDelegate")]
        UIAccelerometerDelegate Delegate { get; set; }

        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }
}
```

<a name="iOS7ProtocolSupport" />

**Novità di MonoTouch 7.0**

A partire da MonoTouch 7.0 un protocollo di nuovo e migliorato funzionalità di associazione è stata incorporata.  Questo nuovo supporto rende più semplice da utilizzare idiomi Objective-C per l'adozione di uno o più protocolli in una determinata classe.

Per ogni definizione di protocollo `MyProtocol` in Objective-C, è ora disponibile un `IMyProtocol` interfaccia che elenca tutti i metodi richiesti dal protocollo, come una classe di estensione che fornisce tutti i metodi facoltativi.  Quanto sopra, combinato con il nuovo supporto in Xamarin Studio editor consente agli sviluppatori di implementare i metodi di protocollo senza dover usare separate sottoclassi delle classi astratte modello precedente.

Qualsiasi definizione che contiene il [ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) attributo effettivamente genererà tre classi di supporto che consentono di migliorare notevolmente la modalità che usare protocolli:

```csharp
    // Full method implementation, contains all methods
    class MyProtocol : IMyProtocol {
        public void Say (string msg);
        public void Listen (string msg);
    }

    // Interface that contains only the required methods
    interface IMyProtocol: INativeObject, IDisposable {
        [Export ("say:")]
        void Say (string msg);
    }

    // Extension methods
    static class IMyProtocol_Extensions {
        public static void Optional (this IMyProtocol this, string msg);
        }
    }
```

Il **implementazione della classe** fornisce una classe astratta completezza che è possibile eseguire l'override di singoli metodi di e ottenere l'indipendenza completa.  Ma a causa di c# non supporta l'ereditarietà multipla, esistono deve avere una classe di base diversi scenari in cui è possibile, ma si desidera implementare un'interfaccia, dove il

Generato **definizione dell'interfaccia** è disponibile in.  È un'interfaccia con tutti i metodi richiesti dal protocollo.  In questo modo gli sviluppatori che desiderano implementare il protocollo per implementare semplicemente l'interfaccia.  Il runtime registrerà automaticamente il tipo come adottare il protocollo.

Si noti che sono elencati i metodi richiesti solo l'interfaccia e di esporre i metodi facoltativi.  Ciò significa che le classi che adottano il protocollo otterranno pieno controllo dei tipi per i metodi richiesti, ma saranno necessario ricorrere al tipizzazione debole (manualmente utilizzando [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) gli attributi e corrispondere alla firma) per il parametro facoltativo metodi di protocollo.

Per rendere più semplice utilizzare un'API che utilizza i protocolli, lo strumento di associazione anche produrrà una classe di metodo di estensioni che espone tutti i metodi facoltativi.  Ciò significa che fino a quando si utilizza un'API, sarà in grado di gestire i protocolli come con tutti i metodi.

Se si desidera utilizzare le definizioni di protocollo dell'API, è necessario scrivere scheletro interfacce vuote nella definizione di API.  Se si desidera utilizzare il Protocollo1 in un'API, è necessario eseguire questa operazione:

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

Il precedente è necessaria in quanto in fase di associazione di `IMyProtocol` non esiste, vale a dire perché è necessario fornire un'interfaccia vuota.

#### <a name="adopting-protocol-generated-interfaces"></a>Adozione delle interfacce generate protocollo

Ogni volta che si implementa una delle interfacce generate per i protocolli, simile al seguente:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

L'implementazione per i metodi di interfaccia automaticamente viene esportato con il nome corretto, pertanto è equivalente a questa:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Non è importante se l'interfaccia viene implementata in modo implicito o esplicito.

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>Associazione delle estensioni della classe

In Objective-C è possibile estendere le classi con nuovi metodi, bevande simili ai metodi di estensione #. Quando uno di questi metodi è presente, è possibile usare il [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) attributo per contrassegnare il metodo come destinatario del messaggio Objective-C.

Ad esempio, in xamarin sono stati associati i metodi di estensione che sono definiti in `NSString` quando `UIKit` viene importato come metodi nel `NSStringDrawingExtensions`, come segue:

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Associazione di elenchi di argomenti Objective-C

Objective-C supporta argomenti variadic. Ad esempio:

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Per richiamare questo metodo in C# è possibile creare una firma simile al seguente:

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Si dichiara il metodo come interni, nascondere l'API precedente dagli utenti, ma esporlo alla libreria. È quindi possibile scrivere un metodo simile al seguente:

```csharp
public void AppendWorkers(params Worker[] workers)
{
    if (workers == null)
         throw new ArgumentNullException ("workers");

    var pNativeArr = Marshal.AllocHGlobal(workers.Length * IntPtr.Size);
    for (int i = 1; i < workers.Length; ++i)
        Marshal.WriteIntPtr (pNativeArr, (i - 1) * IntPtr.Size, workers[i].Handle);

    // Null termination
    Marshal.WriteIntPtr (pNativeArr, (workers.Length - 1) * IntPtr.Size, IntPtr.Zero);

    // the signature for this method has gone from (IntPtr, IntPtr) to (Worker, IntPtr)
    WorkerManager.AppendWorkers(workers[0], pNativeArr);
    Marshal.FreeHGlobal(pNativeArr);
}
```

<a name="Binding_Fields" />

### <a name="binding-fields"></a>Associazione di campi

Talvolta potrebbe essere accedere ai campi pubblici dichiarati in una libreria.

In genere questi campi contengono valori di stringhe o numeri interi che è necessario fare riferimento. Vengono utilizzati comunemente come stringa che rappresenta una notifica specifica e come chiavi nei dizionari.

Per associare un campo, aggiungere una proprietà al file di definizione dell'interfaccia e decorare la proprietà con il [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) attributo. Questo attributo accetta un parametro: il nome di C del simbolo per la ricerca. Ad esempio:

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Se si desidera eseguire il wrapping di diversi campi in una classe statica che non deriva da `NSObject`, è possibile usare il [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) attributo sulla classe, simile al seguente:

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

Sopra genererà un `LonelyClass` che non deriva da `NSObject` e conterrà un'associazione per il `NSSomeEventNotification` 
 `NSString` esposto come un `NSString`.

Il [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) attributo può essere applicato ai tipi di dati seguenti:

-  `NSString` riferimenti (solo proprietà in sola lettura)
-  `NSArray` riferimenti (solo proprietà in sola lettura)
-  valori integer a 32 bit (`System.Int32`)
-  valori integer a 64 bit (`System.Int64`)
-  float a 32 bit (`System.Single`)
-  virgola mobile a 64 bit (`System.Double`)
-  `System.Drawing.SizeF`
-  `CGSize`

Oltre al nome del campo nativo, è possibile specificare il nome della libreria in cui si trova il campo, passando il nome della libreria:

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

Se ci si collega in modo statico, non vi è Nessuna raccolta da associare, pertanto è necessario utilizzare il `__Internal` nome:

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Enumerazioni di associazione

È possibile aggiungere `enum` direttamente nell'associazione file rende più semplice per utilizzarli all'interno di definizioni di API - senza utilizzare un file di origine diversa (che deve essere compilato in entrambe le associazioni e il progetto finale).

Esempio:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

È anche possibile creare la propria enumerazioni sostituire `NSString` costanti. In questo caso il generatore verrà **automaticamente** creare i metodi per convertire i valori delle enumerazioni e costanti NSString automaticamente.

Esempio:

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}

interface MyType {
    [Export ("performForMode:")]
    void Perform (NSString mode);

    [Wrap ("Perform (mode.GetConstant ())")]
    void Perform (NSRunLoopMode mode);
}
```

Nell'esempio precedente è possibile decidere di decorare `void Perform (NSString mode);` con un [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attributo. In questo modo **nascondere** dell'API basate su costante da consentire agli utenti di associazione.

Tuttavia modo da limitare il tipo di creazione di sottoclassi adoperati dalla alternativo API coloro una [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) attributo. Tali metodi generati non sono `virtual`, vale a dire è non essere in grado di eseguire l'override dei - che è possibile, o non, si consiglia di utilizzare.

In alternativa è possibile contrassegnare originale, `NSString`-in base, definizione come `[Protected]`. In questo modo la creazione di sottoclassi di lavoro, quando necessario, e la versione wrap'ed sarà comunque funzionare e chiamare il metodo viene sottoposto a override.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Binding `NSValue`, `NSNumber`, e `NSString` a un tipo migliore

Il [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) attributo consente l'associazione `NSNumber`, `NSValue` e `NSString`(enum) in tipi c# più accurati. L'attributo può essere utilizzato per creare una migliore, più accurato, l'API di .NET tramite l'API nativa.

È possibile decorare i metodi (per il valore restituito), parametri e le proprietà con [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute). L'unica restrizione è che il membro **non devono** trovarsi all'interno di un [ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) oppure [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) interfaccia.

Ad esempio:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Genererebbe:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Internamente questa operazione verrà eseguita la `bool?`  <->  `NSNumber` e `CGRect`  <->  `NSValue` conversioni.

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) supporta inoltre le matrici di `NSNumber` `NSValue` e `NSString`(enum).

Ad esempio:

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

Genererebbe:

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` è un `NSString` eseguito enum, si recupererà destra `NSString` valore e gestire la conversione del tipo.

Vedere la [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) documentazione per visualizzare i tipi di conversione supportate.

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Notifiche di associazione

Le notifiche sono messaggi che vengono registrati le `NSNotificationCenter.DefaultCenter` e vengono utilizzate come un meccanismo per trasmettere i messaggi da una parte dell'applicazione a un altro. Gli sviluppatori di sottoscrivono le notifiche in genere il [NSNotificationCenter](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/)del [AddObserver](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/M/AddObserver/) metodo. Quando un'applicazione invia un messaggio per il centro notifiche, in genere contiene un payload archiviato nel [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) dizionario. Questo dizionario è scarsamente tipizzato e recupero di informazioni esplicitamente è soggetto a errori, come gli utenti devono in genere nella documentazione le chiavi sono disponibili nel dizionario e i tipi di valori che possono essere archiviati nel dizionario. La presenza di chiavi a volte viene utilizzata come anche un valore booleano.

Il generatore di associazione di xamarin offre supporto per gli sviluppatori associare le notifiche. A tale scopo, impostare il [ `[Notification]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute) attributo su una proprietà che è stato contrassegnato con un [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) proprietà (può essere pubblico o privato).

Questo attributo può essere usato senza argomenti per le notifiche che non trasportano alcun payload, oppure è possibile specificare un `System.Type` che fa riferimento a un'altra interfaccia di definizione dell'API, in genere con il nome che termina con "EventArgs". Il generatore Trasforma l'interfaccia in una classe che rappresenta una sottoclasse `EventArgs` e includerà tutte le proprietà elencate. Il [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributo deve essere utilizzato nella classe EventArgs per elencare il nome della chiave utilizzata per cercare il dizionario di Objective-C per recuperare il valore.

Ad esempio:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

Il codice precedente genera una classe annidata `MyClass.Notifications` con i metodi seguenti:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Gli utenti del codice quindi facilmente richiedere notifiche registrate per il [NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/) utilizzando codice simile al seguente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

Il valore restituito da `ObserveDidStart` può essere utilizzato per arrestare facilmente la ricezione delle notifiche, simile al seguente:

```csharp
token.Dispose ();
```

Oppure è possibile chiamare [NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject/) e passare il token. Se la notifica contiene parametri, è necessario specificare un helper `EventArgs` interfaccia, simile al seguente:

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

Sopra genererà un `MyScreenChangedEventArgs` classe con il `ScreenX` e `ScreenY` le proprietà che recupero i dati dal [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) dizionario utilizzando le chiavi "ScreenXKey" e "ScreenYKey" rispettivamente e applicare le conversioni appropriate. Il `[ProbePresence]` attributo viene utilizzato per il generatore di verificare se la chiave viene impostata nel `UserInfo`, anziché tentare di estrarre il valore. Viene utilizzato per i casi in cui la presenza della chiave è il valore (in genere per i valori booleani).

In questo modo è possibile scrivere codice simile al seguente:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>Categorie di associazione

Le categorie sono un meccanismo di Objective-C utilizzato per estendere il set di metodi e le proprietà disponibili in una classe.   In pratica, vengono utilizzati per estendere la funzionalità di una classe di base (ad esempio `NSObject`) quando è associato un framework specifico (ad esempio `UIKit`), rendendo i metodi disponibili, ma solo se è collegato al nuovo framework.   In alcuni casi, vengono utilizzati per organizzare le funzionalità in una classe dalla funzionalità.   Sono simili per spirito ai metodi di estensione c#. Si tratta di una categoria aspetto in Objective-c:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

L'esempio precedente se trovato in una raccolta si estende anche le istanze di `UIView` con il metodo `makeBackgroundRed`.

Per associare quelli, è possibile usare il [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) attributo in una definizione di interfaccia.  Quando si utilizza il [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) attributo, il significato del [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) attributo cambia vengano utilizzati per specificare la classe di base per estendere, sia il tipo da estendere.

Nella seguente come `UIView` le estensioni sono associate e convertite in metodi di estensione c#:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Il precedente creerà una `MyUIViewExtension` una classe che contiene il `MakeBackgroundRed` metodo di estensione.  Ciò significa che è ora possibile chiamare "MakeBackgroundRed" in qualsiasi `UIView` sottoclasse, offrendo la stessa funzionalità, si otterrebbe in Objective-C. In alcuni casi, le categorie vengono utilizzate non estendere una classe di sistema, ma per organizzare funzionalità, esclusivamente per scopi di effetto.  analogamente a quanto segue:

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Sebbene sia possibile usare il [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) attributo anche per questo stile decorazione di dichiarazioni, si potrebbe anche sufficiente aggiungerli tutti alla definizione della classe.  Entrambe queste sarebbe possibile ottenere lo stesso:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Twitter {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Facebook {
    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

È semplicemente più breve in questi casi le categorie di tipo merge:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);

    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

<a name="Binding_Blocks" />

### <a name="binding-blocks"></a>Blocchi di associazione

I blocchi sono un nuovo costrutto di introdotti da Apple per portare l'equivalente funzionale di metodi anonimi di c# in Objective-C. Ad esempio, la `NSSet` classe espone ora di questo metodo:

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

La descrizione precedente dichiara un metodo denominato `enumerateObjectsUsingBlock:` che accetta un argomento denominato `block`. Questo blocco è simile a un metodo anonimo c#, in quanto dispone di supporto per l'acquisizione dell'ambiente corrente (il puntatore "this", l'accesso ai parametri e variabili locali). Il metodo sopra indicato in `NSSet` richiama il blocco con due parametri di un `NSObject` (il `id obj` parte) e un puntatore a un valore booleano (il `BOOL *stop`) parte.

Per associare questo tipo di API con btouch, è necessario dichiarare in primo luogo la firma del tipo di blocco come c# delegare e farvi riferimento da un punto di ingresso di API, simile al seguente:

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

E ora è possibile chiamare la funzione da c#:

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

Se si preferisce, è inoltre possibile utilizzare le espressioni lambda:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>Metodi asincroni

Il generatore di associazione è possibile attivare una determinata classe di metodi in metodi async semplice (metodi che restituiscono un'attività o&lt;T&gt;).

È possibile usare il [ `[Async]` ](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) attributo sui metodi che restituiscono void e il cui ultimo argomento è un callback.  Quando si applica questo a un metodo, il generatore di associazione genererà una versione di tale metodo con il suffisso `Async`.  Se il callback non accetta parametri, il valore restituito sarà una `Task`, se il callback accetta un parametro, il risultato sarà un `Task<T>`.  Se il callback accetta più parametri, è necessario impostare il `ResultType` o `ResultTypeName` per specificare il nome del tipo generato che conterrà tutte le proprietà desiderato.

Esempio:

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

Il codice precedente genera sia il metodo LoadFile, così come:

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Replica di tipi sicuri per i parametri NSDictionary deboli

In numerose posizioni nell'API di Objective-C, i parametri vengono passati come con tipizzazione debole `NSDictionary` API con chiavi specifiche e i valori, ma questi sono errori (è possibile passare le chiavi non valide e non ottenere nessun avviso; è possibile passare valori non validi e non ottenere avvisi) e frustrante Per utilizzare in quanto richiedono più trip la documentazione per la ricerca di possibili nomi delle chiavi e valori.

La soluzione consiste nello specificare una versione fortemente tipizzata che fornisce che la versione fortemente tipizzata dell'API e in background esegue il mapping di varie sottostante chiavi e valori.

In questo caso, ad esempio, se l'API di Objective-C accettata un' `NSDictionary` e è documentata che accetti la chiave `XyzVolumeKey` che accetta un `NSNumber` con un valore volume compreso tra 0.0 e 1.0 e un `XyzCaptionKey` che accetta una stringa, è preferibile che gli utenti abbiano un'API con nice ha aspetto analogo al seguente:

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

Il `Volume` proprietà viene definita come float nullable, come la convenzione di Objective-C non richiede questi dizionari il valore di, pertanto non esistono scenari in cui il valore potrebbe non essere impostato.

A tale scopo, è necessario eseguire alcune operazioni:

* Creare una classe fortemente tipizzata, che rappresenta una sottoclasse [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) e fornisce i metodi get e set diversi per ogni proprietà.
* Dichiarare gli overload per i metodi che accettano `NSDictionary` per richiedere la nuova versione fortemente tipizzata.

È possibile creare la classe fortemente tipizzata manualmente o utilizzare il generatore per l'esecuzione dell'operazione per l'utente.  Vengono innanzitutto analizzate come effettuare questa operazione manualmente in modo da comprendere cosa accade in e quindi l'approccio automatico.

È necessario creare un file di supporto per questo oggetto, non costituiscono il contratto API.  Questo è ciò che è necessario scrivere per creare la classe XyzOptions:

```csharp
public class XyzOptions : DictionaryContainer {
# if !COREBUILD
    public XyzOptions () : base (new NSMutableDictionary ()) {}
    public XyzOptions (NSDictionary dictionary) : base (dictionary){}

    public nfloat? Volume {
       get { return GetFloatValue (XyzOptionsKeys.VolumeKey); }
       set { SetNumberValue (XyzOptionsKeys.VolumeKey, value); }
    }
    public string Caption {
       get { return GetStringValue (XyzOptionsKeys.CaptionKey); }
       set { SetStringValue (XyzOptionsKeys.CaptionKey, value); }
    }
# endif
}
```

È quindi necessario fornire un metodo wrapper che espone l'API di alto livello, sopra l'API di basso livello.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Se l'API non è necessario essere sovrascritti, è possibile nascondere in modo sicuro l'API basata su NSDictionary utilizzando il [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attributo.

Come si può notare, utilizziamo la [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) per la superficie di un nuovo punto di ingresso di API, e di attributo è tramite il nostro fortemente tipizzata della superficie `XyzOptions` classe.  Il metodo wrapper consente anche di null deve essere passato.

Una cosa che non sono menzionati è ora in cui il `XyzOptionsKeys` provengono valori.  È in genere per raggruppare le chiavi che espone un'API in una classe statica, ad esempio `XyzOptionsKeys`, come segue:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Esaminiamo il supporto automatico per la creazione di questi dizionari fortemente tipizzata.  Ciò consente di evitare una notevole quantità di commenti, ed è possibile definire il dizionario direttamente nel contratto di API, anziché utilizzare un file esterno.

Per creare un dizionario fortemente tipizzato, introdurre un'interfaccia in dell'API e decorarla con il [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) attributo.  In questo modo il generatore che deve creare una classe con lo stesso nome di interfaccia che esegue la derivazione da `DictionaryContainer` e fornirà complesse funzioni di accesso tipizzate per tale.

Il [ `[StrongDictionary]` ](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) attributo accetta un parametro, ovvero il nome della classe statica che contiene le chiavi del dizionario.  Quindi, ogni proprietà dell'interfaccia diventerà una funzione di accesso fortemente tipizzata.  Per impostazione predefinita, il codice utilizzerà il nome della proprietà con il suffisso "Chiave" nella classe statica per creare la funzione di accesso.

Ciò significa che crea la funzione di accesso fortemente tipizzate non richiede più un file esterno, né dover creare manualmente getter e setter per tutte le proprietà o dover cercare manualmente le chiavi di se stessi.

Si tratta di aspetto intera associazione:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
[StrongDictionary ("XyzOptionKeys")]
interface XyzOptions {
    nfloat Volume { get; set; }
    string Caption { get; set; }
}

[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Nel caso in cui è necessario fare riferimento nel `XyzOption` membri un campo diverso (vale a dire non il nome della proprietà con il suffisso `Key`), è possibile decorare la proprietà con un [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributo con il nome che si da utilizzare.

<a name="Type_mappings" />

## <a name="type-mappings"></a>Mapping dei tipi

Questa sezione descrive la modalità di mapping dei tipi Objective-C ai tipi c#.

<a name="Simple_Types" />

### <a name="simple-types"></a>Tipi semplici

Nella tabella seguente viene illustrato come è necessario eseguire il mapping tipi dal Objective-C e world CocoaTouch all'ambiente di xamarin:

|Nome del tipo Objective-C|Tipo di Unified API xamarin|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([altre informazioni sul binding NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (vedere anche: [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|Tipi di CoreFoundation (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|Tipi di base (`NS*`)|`Foundation.NS*`|
|`id`|`Foundation`.`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays" />

### <a name="arrays"></a>Matrici

Il runtime xamarin esegue automaticamente la conversione di matrici in c# per `NSArrays` e operazioni di conversione, ad esempio il metodo Objective-C immaginaria che restituisce un `NSArray` di `UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

È associato simile al seguente:

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

L'idea consiste nell'utilizzare una matrice di fortemente tipizzata c# perché in questo modo l'IDE per fornire il completamento del codice corretto con il tipo effettivo evitando all'utente di individuare o cercare la documentazione per scoprire il tipo effettivo degli oggetti contenuti nella matrice.

Nei casi in cui non può rilevare verso il basso il tipo più derivato effettivi contenuto nella matrice, è possibile utilizzare `NSObject []` come valore restituito.

<a name="Selectors" />

### <a name="selectors"></a>Selettori

I selettori visualizzati sull'API Objective-C come il tipo di speciale `SEL`. Quando si associa un selettore, mappare il tipo da `ObjCRuntime.Selector`.  In genere i selettori sono esposti in un'API con un oggetto, l'oggetto di destinazione e un selettore per richiamare nell'oggetto di destinazione. Fornire entrambi questi fondamentalmente corrisponde al delegato c#: un elemento che incapsula il metodo da richiamare sia, nonché l'oggetto per richiamare il metodo in.

Questo è l'associazione l'aspetto seguente:

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

Si tratta di come il metodo viene in genere utilizzato in un'applicazione:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        b.SetTarget (this, new Selector ("print"));
    }

    [Export ("print")]
    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

Per rendere l'associazione coloro per gli sviluppatori c#, è in genere fornirà un metodo che accetta un `NSAction` quale c# delegati e le espressioni lambda per essere utilizzato al posto del parametro di `Target+Selector`. Per eseguire questa operazione è in genere necessario nascondere il `SetTarget` metodo contrassegnandolo con un [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attributo e quindi si esporrà un nuovo metodo di supporto, simile al seguente:

```csharp
// API.cs
interface Button {
   [Export ("setTarget:selector:"), Internal]
   void SetTarget (NSObject target, Selector sel);
}

// Extensions.cs
public partial class Button {
     public void SetTarget (NSAction callback)
     {
         SetTarget (new NSActionDispatcher (callback), NSActionDispatcher.Selector);
     }
}
```

Ora è possibile scrivere il codice utente simile al seguente:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        // First Style
        b.SetTarget (ThePrintMethod);

        // Lambda style
        b.SetTarget (() => {  /* print here */ });
    }

    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

<a name="Strings" />

### <a name="strings"></a>Stringhe

Quando si associa un metodo che accetta un `NSString`, è possibile sostituire che con un tipo di stringa in c#, entrambi gli elementi in tipi restituiti e parametri.

L'unico caso in cui è possibile che si desidera utilizzare un `NSString` è direttamente quando la stringa viene utilizzata come un token. Per ulteriori informazioni sulle stringhe e `NSString`, leggere la [progettazione delle API in NSString](~/ios/internals/api-design/nsstring.md) documento.

In alcuni casi rari, un'API potrebbe esporre una stringa di tipo C (`char *`) anziché una stringa di Objective-C (`NSString *`). In questi casi, è possibile annotare il parametro con il [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring) attributo.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out / parametri ref

Alcune API restituiscono valori i relativi parametri o passano parametri per riferimento.

In genere la firma simile al seguente:

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

Il primo esempio illustra un idioma comune Objective-C per restituire i codici di errore, un puntatore a un `NSError` puntatore viene passato e il valore impostato al momento della restituzione.   Il secondo metodo viene illustrato come un metodo di Objective-C può accettare un oggetto e modificarne il contenuto.   Si tratterà di un passaggio per riferimento, anziché un valore di output pure.

L'associazione è analogo al seguente:

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Attributi di gestione della memoria

Quando si usa il [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributo e si passano dati che verranno mantenuti dal metodo chiamato, è possibile specificare la semantica di argomento da passare come secondo parametro, ad esempio:

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

Il precedente sarebbe il valore come se avessero la semantica "Mantieni" flag. La semantica disponibile è:

-  Assegna
-  Copia
-  Mantieni

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Linee guida di stile

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>Utilizzo [interno]

È possibile usare il [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attributo per nascondere un metodo dell'API pubblica. È possibile eseguire questa operazione in casi in cui l'API esposta è troppo basso livello e si desidera fornire un'implementazione di alto livello in un file separato, in base a questo metodo.

È inoltre possibile utilizzare questo quando si verifichino limitazioni nel generatore di associazione, ad esempio alcuni scenari avanzati potrebbero esporre i tipi che non sono associati e si desidera associare in altro modo per racchiudere tali tipi in altro modo.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Callback e i gestori eventi

Classi di Objective-C in genere broadcast notifiche o richiedono informazioni inviando un messaggio in una classe delegata (delegato Objective-C).

Questo modello, mentre è completamente supportata e viene rilevata dal xamarin può talvolta essere complessa. Xamarin. IOS espone lo schema di eventi in c# e un sistema di metodo callback nella classe che può essere usato in questi casi. Ciò consente al codice simile al seguente per eseguire:

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

Il generatore di associazione è in grado di ridurre la quantità di digitazione necessari per il mapping del modello Objective-C nello schema di c#.

A partire da 1.4 xamarin. IOS, sarà possibile indicare anche il generatore per produrre le associazioni per una specifica di Objective-C delegati ed esporre il delegato come c# eventi e proprietà del tipo di host.

Esistono due classi coinvolte nel processo, la classe dell'host che corrisponde a quella attualmente genera eventi e invia tali file nel `Delegate` o `WeakDelegate` e la classe delegata effettivo.

Considerare le seguenti impostazioni:

```csharp
[BaseType (typeof (NSObject))]
interface MyClass {
    [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
    NSObject WeakDelegate { get; set; }

    [Wrap ("WeakDelegate")][NullAllowed]
    MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
    [Export ("loaded:bytes:")]
    void Loaded (MyClass sender, int bytes);
}
```

Per eseguire il wrapping della classe, è necessario:

-  Nella classe dell'host, aggiungere il [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   dichiarazione il tipo che funge da relativo delegato e il nome del linguaggio c# esposta. Nell'esempio sopra sono `typeof (MyClassDelegate)` e `WeakDelegate` rispettivamente.
-  Nella classe del delegato a ogni metodo che presenta più di due parametri, è necessario specificare il tipo che si desidera utilizzare per la classe EventArgs generata automaticamente.

Il generatore di associazione non è limitato a disposizione solo una destinazione singolo evento, è possibile che alcune classi Objective-C per creare i messaggi a più di un delegano, pertanto è necessario fornire le matrici per supportare il programma di installazione. La maggior parte delle installazioni non saranno necessario, ma il generatore è pronto per supportare i casi.

Il codice risulta sarà:

```csharp
[BaseType (typeof (NSObject),
    Delegates=new string [] {"WeakDelegate"},
    Events=new Type [] { typeof (MyClassDelegate) })]
interface MyClass {
        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }

        [Wrap ("WeakDelegate")][NullAllowed]
        MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
        [Export ("loaded:bytes:"), EventArgs ("MyClassLoaded")]
        void Loaded (MyClass sender, int bytes);
}
```

Il `EventArgs` viene utilizzato per specificare il nome della `EventArgs` classe da generare. È necessario usare uno per ogni firma (in questo esempio, il `EventArgs` conterrà un `With` proprietà di tipo nint).

Con le definizioni precedenti, il generatore produrrà il seguente evento nella MyClass generato:

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

Pertanto, è ora possibile usare codice simile al seguente:

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

I callback sono simili a chiamate agli eventi, la differenza è che, anziché avere più sottoscrittori potenziali (più metodi, ad esempio, possono effettuare l'hook in una `Clicked` evento o un `DownloadFinished` evento) callback possono avere solo un singolo sottoscrittore.

Il processo è identico, l'unica differenza è che, anziché esporre il nome della `EventArgs` classe che verrà generato, EventArgs viene effettivamente usato per denominare il nome del delegato c# risultante.

Se il metodo nella classe delegato restituisce un valore, il generatore di associazione verrà mappa in un metodo delegato nella classe padre anziché un evento. In questi casi è necessario fornire il valore predefinito che deve essere restituito dal metodo se l'utente non collegare al delegato. Eseguire questa operazione usando il [ `[DefaultValue]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) o [ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) attributi.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) verrà impostare come hardcoded un valore restituito, mentre [ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) viene utilizzata per specificare l'argomento di input verrà restituito.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Tipi di base ed enumerazioni

È anche possibile fare riferimento enumerazioni o tipi di base che non sono supportati direttamente dal sistema di definizione di interfaccia btouch. A tale scopo, inserire le enumerazioni e i tipi di base in un file separato e includere come parte di uno dei file aggiuntivi forniti btouch.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>Le dipendenze di collegamento

Se si siano associando le API che non fanno parte dell'applicazione, è necessario assicurarsi che il file eseguibile è collegato in queste librerie.

È necessario informare xamarin. IOS come collegare le librerie, ciò può avvenire mediante la modifica di configurazione di compilazione per richiamare il `mtouch` comando con alcuni extra compilare argomenti che specifichino come collegarsi alle librerie di nuovo usando il "-gcc_flags" opzione, seguito da una stringa tra virgolette contenente tutte le raccolte aggiuntive che sono necessari per il programma, simile al seguente:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

L'esempio precedente verrà creato un collegamento `libMyLibrary.a`, `libSystemLibrary.dylib` e `CFNetwork` libreria framework nell'eseguibile finale.

Oppure è possibile sfruttare il livello di assembly [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), che è possibile includere nei file del contratto (ad esempio `AssemblyInfo.cs`).
Quando si usa il [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), è necessario avere la libreria nativa disponibile al momento l'associazione, come adottare questo incorporeranno la libreria nativa con l'applicazione. Ad esempio:

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

È possibile chiedersi, perché è necessario `-force_load` comando e il motivo è che ObjC - flag anche se compila il codice, non consente di mantenere i metadati richiesti per supportare le categorie (l'eliminazione di messaggi non recapitabili codice compilatore/linker rimuove lo) viene necessario in fase di esecuzione per xamarin. IOS.

<a name="Assisted_References" />

## <a name="assisted-references"></a>Riferimenti assistiti

Alcuni oggetti temporanei come fogli di azione e finestre di avviso sono eccessivamente complesse di tenere traccia per gli sviluppatori e il generatore di associazione per un po' di seguito.

Ad esempio se si dispone di una classe che mostrano un messaggio e quindi generato un `Done` evento, la modalità tradizionale con cui gestire questo sarebbe:

```csharp
class Demo {
    MessageBox box;

    void ShowError (string msg)
    {
        box = new MessageBox (msg);
        box.Done += { box = null; ... };
    }
}
```

Nello scenario precedente, lo sviluppatore deve mantenere il riferimento all'oggetto stesso e una perdita o Cancella attivamente il riferimento per la propria casella.  Mentre il codice di associazione, il generatore supporta tenere traccia del riferimento per l'utente e deselezionare quando viene richiamato un metodo speciale, il codice sopra riportato sarebbe quindi diventa:

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Si noti come non è più necessario per mantenere la variabile in un'istanza, che viene eseguito con una variabile locale e che non è necessario cancellare il riferimento quando non è più attivo.

Per sfruttare i vantaggi di questo, la classe deve avere una proprietà eventi impostata [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) dichiarazione e anche il `KeepUntilRef` variabile impostata sul nome del metodo che viene richiamato quando l'oggetto ha completato il proprio lavoro, ad esempio In questo:

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Protocolli di ereditarietà

A partire da v 3.2 xamarin. IOS, è supportata l'eredità dai protocolli che sono stati contrassegnati con il [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) proprietà. Ciò risulta utile in alcuni modelli di API, ad esempio in `MapKit` in cui il `MKOverlay` del protocollo, eredita il `MKAnnotation` del protocollo e l'adozione di un numero di classi che ereditano da `NSObject`.

In passato è necessario il protocollo di copia per ogni implementazione, ma in questi casi ora è possibile avere il `MKShape` classe ereditare il `MKOverlay` protocollo che verrà generato automaticamente tutti i metodi necessari.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di associazione](https://developer.xamarin.com/samples/BindingSample/)
