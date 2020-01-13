---
title: Binding di librerie Objective-C
description: In questo documento viene fornita una panoramica di alto livello su come C# creare binding per il codice Objective-C, descrivendo come associare eventi, metodi, controlli personalizzati e altro ancora.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 6841e94ad13357c51e6ccf59e35c659dfb9954aa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016297"
---
# <a name="binding-objective-c-libraries"></a>Binding di librerie Objective-C

Quando si usa Xamarin.iOS o Xamarin.Mac, è possibile che si verifichino casi in cui si vuole usare una libreria Objective-C di terze parti. In questi casi, è possibile usare i progetti di binding Novell per C# creare un'associazione alle librerie Objective-C native. Il progetto usa gli stessi strumenti usati per portare le API iOS e Mac C#.

In questo documento viene descritto come associare le API Objective-C, se si stanno associando solo le API C, è necessario utilizzare il meccanismo .NET standard per questo [Framework P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/).
Informazioni dettagliate su come collegare in modo statico una libreria C sono disponibili nella pagina [linking native Libraries](~/ios/platform/native-interop.md) .

Vedere la [Guida di riferimento ai tipi di binding](~/cross-platform/macios/binding/binding-types-reference.md)complementari.
Inoltre, per altre informazioni sulle operazioni che si verificano dietro le quinte, vedere la pagina [Panoramica del binding](~/cross-platform/macios/binding/overview.md) .

È possibile compilare binding per le librerie iOS e Mac.
Questa pagina descrive come usare un'associazione iOS, tuttavia le associazioni Mac sono molto simili.

**Codice di esempio per iOS**

È possibile usare il progetto di [esempio di binding iOS](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) per sperimentare le associazioni.

<a name="Getting_Started" />

## <a name="getting-started"></a>Per iniziare

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Il modo più semplice per creare un'associazione consiste nel creare un progetto di binding Xamarin.iOS.
Questa operazione può essere eseguita da Visual Studio per Mac selezionando il tipo di progetto, la libreria **> iOS > libreria bindings**:

[![](objective-c-libraries-images/00-sml.png "Do this from Visual Studio for Mac by selecting the project type, iOS Library Bindings Library")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Il modo più semplice per creare un'associazione consiste nel creare un progetto di binding Xamarin.iOS.
È possibile eseguire questa operazione da Visual Studio in Windows selezionando il tipo di progetto, **visual C# > iOS > bindings Library (iOS)** :

[![](objective-c-libraries-images/00vs-sml.png "iOS Bindings Library iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Nota: i progetti di binding per **Xamarin.Mac** sono supportati solo in Visual Studio per Mac.

-----

Il progetto generato contiene un modello di dimensioni ridotte che è possibile modificare, che contiene due file: `ApiDefinition.cs` e `StructsAndEnums.cs`.

Il `ApiDefinition.cs` è il percorso in cui verrà definito il contratto API. si tratta del file che descrive il modo in C#cui viene proiettata l'API Objective-C sottostante. La sintassi e il contenuto di questo file sono l'argomento principale della discussione di questo documento e il relativo contenuto è limitato alle C# interfacce e C# alle dichiarazioni di delegati. Il file di `StructsAndEnums.cs` è il file in cui verranno immesse le definizioni richieste dalle interfacce e dai delegati. Sono inclusi i valori di enumerazione e le strutture che possono essere usati dal codice.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Associazione di un'API

Per eseguire un'associazione completa, è opportuno comprendere la definizione dell'API Objective-C e acquisire familiarità con le linee guida di progettazione .NET Framework.

Per associare la libreria, in genere si inizia con un file di definizione dell'API. Un file di definizione API è semplicemente C# un file di origine C# che contiene le interfacce che sono state annotate con una manciata di attributi che consentono di gestire l'associazione.  Questo file definisce il tipo di contratto tra C# e Objective-C.

Ad esempio, si tratta di un semplice file API per una libreria:

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

Nell'esempio precedente viene definita una classe denominata `Cocos2D.Camera` che deriva dal tipo di base `NSObject` (questo tipo deriva da `Foundation.NSObject`) e che definisce una proprietà statica (`ZEye`), due metodi che non accettano argomenti e un metodo che accetta tre argomenti.

Una descrizione dettagliata del formato del file API e degli attributi che è possibile usare è illustrata nella sezione del file di [definizione dell'API](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) riportata di seguito.

Per produrre un'associazione completa, in genere si gestiscono quattro componenti:

- Il file di definizione dell'API (`ApiDefinition.cs` nel modello).
- Facoltativo: qualsiasi enumerazione, tipo, struct richiesta dal file di definizione dell'API (`StructsAndEnums.cs` nel modello).
- Facoltativo: origini aggiuntive che potrebbero espandere l'associazione generata o fornire un'API più C# intuitiva (tutti C# i file aggiunti al progetto).
- Libreria nativa che si sta associando.

Questo grafico mostra la relazione tra i file:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "This chart shows the relationship between the files")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

Il file di definizione dell'API conterrà solo gli spazi dei nomi e le definizioni di interfaccia (con tutti i membri che un'interfaccia può contenere) e non deve contenere classi, enumerazioni, delegati o struct. Il file di definizione dell'API è semplicemente il contratto che verrà usato per generare l'API.

Qualsiasi codice aggiuntivo necessario come enumerazioni o classi di supporto deve essere ospitato in un file separato, nell'esempio precedente "CameraMode" è un valore di enumerazione che non esiste nel file CS e deve essere ospitato in un file separato, ad esempio `StructsAndEnums.cs`:

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

Il file di `APIDefinition.cs` viene combinato con la classe `StructsAndEnum` e viene usato per generare l'associazione di base della libreria. È possibile usare la libreria risultante così com'è, ma in genere è opportuno ottimizzare la libreria risultante per aggiungere alcune C# funzionalità per il vantaggio degli utenti. Alcuni esempi includono l'implementazione di un metodo di C# `ToString()`, fornire indicizzatori, aggiungere conversioni implicite a e da alcuni tipi nativi o fornire versioni fortemente tipizzate di alcuni metodi. Questi miglioramenti vengono archiviati in file C# aggiuntivi. Aggiungere semplicemente i C# file al progetto che verranno inclusi in questo processo di compilazione.

Viene illustrato come implementare il codice nel file di `Extra.cs`. Si noti che verranno utilizzate classi parziali perché queste aumentano le classi parziali generate dalla combinazione del `ApiDefinition.cs` e dell'associazione `StructsAndEnums.cs` Core:

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

La compilazione della libreria produrrà il binding nativo.

Per completare questa associazione, è necessario aggiungere la libreria nativa al progetto.  È possibile eseguire questa operazione aggiungendo la libreria nativa al progetto, trascinando la libreria nativa da Finder nel progetto in Esplora soluzioni oppure facendo clic con il pulsante destro del mouse sul progetto e scegliendo **aggiungi** > **Aggiungi file** per selezionare la libreria nativa.
Le librerie native per convenzione iniziano con la parola "lib" e terminano con l'estensione ". a". Quando si esegue questa operazione, Visual Studio per Mac aggiungerà due file: il file. a e un file C# popolato automaticamente che contiene informazioni sul contenuto della libreria nativa:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Native libraries by convention start with the word lib and end with the extension .a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

Il contenuto del file di `libMagicChord.linkwith.cs` contiene informazioni su come questa libreria può essere usata e indica all'IDE di creare un pacchetto di questo file binario nel file DLL risultante:

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Dettagli completi sull'utilizzo del [`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) 
gli attributi sono descritti nella [Guida di riferimento ai tipi di binding](~/cross-platform/macios/binding/binding-types-reference.md).

A questo punto, quando si compila il progetto, verrà creato un file di `MagicChords.dll` contenente sia l'associazione che la libreria nativa. È possibile distribuire questo progetto o la DLL risultante ad altri sviluppatori per uso personale.

Talvolta potrebbe essere necessario disporre di alcuni valori di enumerazione, definizioni di delegati o altri tipi. Non inserire quelli nel file delle definizioni API, poiché si tratta semplicemente di un contratto

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>Il file di definizione dell'API

Il file di definizione dell'API è costituito da un certo numero di interfacce. Le interfacce nella definizione dell'API verranno convertite in una dichiarazione di classe e devono essere decorate con l'attributo [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) per specificare la classe di base per la classe.

Ci si potrebbe chiedere perché non sono state usate classi anziché interfacce per la definizione del contratto. Sono state selezionate le interfacce perché ci ha consentito di scrivere il contratto per un metodo senza dover fornire un corpo del metodo nel file di definizione dell'API o dover fornire un corpo che doveva generare un'eccezione o restituire un valore significativo.

Tuttavia, poiché l'interfaccia viene utilizzata come ossatura per generare una classe, è necessario ricorrere alla decorazione di varie parti del contratto con attributi per guidare l'associazione.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Metodi di associazione

L'associazione più semplice che è possibile eseguire è associare un metodo. È sufficiente dichiarare un metodo nell'interfaccia con le C# convenzioni di denominazione e decorare il metodo con la [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
attributo. L'attributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) viene collegato al C# nome con il nome Objective-C nel runtime Xamarin.iOS. Il parametro della [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attribute è il nome del selettore Objective-C. Ecco alcuni esempi:

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

Gli esempi precedenti illustrano come è possibile associare i metodi di istanza. Per associare metodi statici, è necessario usare l'attributo [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) , come indicato di seguito:

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Questa operazione è necessaria perché il contratto fa parte di un'interfaccia e le interfacce non hanno alcuna nozione di dichiarazioni di istanza di Visual Studio, quindi è necessario ricorrere a attributi. Se si desidera nascondere un particolare metodo dall'associazione, è possibile decorare il metodo con l'attributo [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) .

Il comando `btouch-native` introdurrà i controlli per i parametri di riferimento in modo che non siano null. Se si desidera consentire valori null per un parametro specifico, utilizzare il [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
attributo per il parametro, come indicato di seguito:

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Quando si esporta un tipo di riferimento, con la parola chiave [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) è possibile specificare anche la semantica di allocazione. Questa operazione è necessaria per garantire che non si verifichino perdite di dati.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Proprietà Binding

Analogamente ai metodi, le proprietà Objective-C vengono associate usando il [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
attributo ed eseguire il mapping C# direttamente alle proprietà. Analogamente ai metodi, le proprietà possono essere decorate con la [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)
e la [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
attributi.

Quando si usa l'attributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) su una proprietà sotto le quinte, btouch-native associa due metodi: Getter e setter. Il nome fornito per l'esportazione è il nome **base** e il setter viene calcolato anteponendo la parola "set", trasformando la prima lettera del nome di **base** in lettere maiuscole e facendo in modo che il selettore prenda un argomento. Ciò significa che `[Export ("label")]` applicato a una proprietà associa effettivamente i metodi "label" e "selabel:" Objective-C.

A volte le proprietà Objective-C non seguono il modello descritto in precedenza e il nome viene sovrascritto manualmente. In questi casi è possibile controllare la modalità di generazione dell'associazione usando il [`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) 
attributo sul Getter o Setter, ad esempio:

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

Questa operazione associa quindi "isMenuVisible" e "setMenuVisible:". Facoltativamente, è possibile associare una proprietà usando la sintassi seguente:

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

Dove il getter e il setter sono definiti in modo esplicito come nel `name` e `setName` associazioni precedenti.

Oltre a supportare le proprietà statiche usando [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute), è possibile decorare le proprietà statiche del thread con [`[IsThreadStatic]`](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute), ad esempio:

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Analogamente ai metodi che consentono di contrassegnare alcuni parametri con [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute), è possibile applicare [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
a una proprietà per indicare che null è un valore valido per la proprietà, ad esempio:

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

Il parametro [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) può anche essere specificato direttamente nel setter:

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Avvertenze sull'associazione di controlli personalizzati

Quando si configura l'associazione per un controllo personalizzato, è necessario tenere in considerazione le avvertenze seguenti:

1. Le **proprietà di binding devono essere statiche** : quando si definisce l'associazione delle proprietà, è necessario usare l'attributo [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) .
2. I **nomi delle proprietà devono corrispondere esattamente** . il nome usato per associare la proprietà deve corrispondere esattamente al nome della proprietà nel controllo personalizzato.
3. I **tipi di proprietà devono corrispondere esattamente** . il tipo di variabile usato per associare la proprietà deve corrispondere esattamente al tipo della proprietà nel controllo personalizzato.
4. **I punti di interruzione e i punti di interruzione getter/setter** inseriti nei metodi getter o setter della proprietà non verranno mai raggiunti.
5. **Osservare i callback** : è necessario usare i callback di osservazione per ricevere una notifica delle modifiche apportate ai valori delle proprietà dei controlli personalizzati.

L'impossibilità di osservare le avvertenze elencate in precedenza può comportare un errore di binding in modo invisibile all'utente in fase di esecuzione.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Proprietà e modello modificabile Objective-C

I Framework Objective-C usano un idioma in cui alcune classi non sono modificabili con una sottoclasse modificabile. Ad esempio `NSString` è la versione non modificabile, mentre `NSMutableString` è la sottoclasse che consente la mutazione.

In queste classi è comune vedere che la classe di base non modificabile contiene proprietà con un getter, ma senza Setter. E per la versione modificabile per introdurre il setter. Poiché questo non è realmente possibile con C#, è necessario eseguire il mapping di questo idioma a un idioma C#che funzionerebbe con.

Il modo in cui viene eseguito il C# mapping di questo oggetto consiste nell'aggiungere sia il getter che il setter alla classe di base, ma contrassegnando il Setter con un [`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)
attributo.

Quindi, nella sottoclasse modificabile si usa il [`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 
attributo sulla proprietà per assicurarsi che la proprietà esegua effettivamente l'override del comportamento dell'elemento padre.

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

### <a name="binding-constructors"></a>Costruttori di binding

Lo strumento `btouch-native` genererà automaticamente quattro costruttori nella classe, per una determinata classe `Foo`genera:

- `Foo ()`: il costruttore predefinito (esegue il mapping al costruttore "init" di Objective-C)
- `Foo (NSCoder)`: il costruttore usato durante la deserializzazione dei file del PENNino (esegue il mapping al costruttore "initWithCoder:" di Objective-C).
- `Foo (IntPtr handle)`: il costruttore per la creazione basata su handle, che viene richiamato dal runtime quando il runtime deve esporre un oggetto gestito da un oggetto non gestito.
- `Foo (NSEmptyFlag)`: viene usato dalle classi derivate per impedire l'inizializzazione doppia.

Per i costruttori definiti, devono essere dichiarati usando la firma seguente all'interno della definizione dell'interfaccia: devono restituire un valore `IntPtr` e il nome del metodo deve essere Constructor. Ad esempio, per associare il costruttore `initWithFrame:`, questo è ciò che si utilizzerebbe:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Protocolli di binding

Come descritto nel documento di progettazione dell'API, nella sezione relativa alla [discussione di modelli e protocolli](~/ios/internals/api-design/index.md#models), Xamarin.iOS esegue il mapping dei protocolli Objective-C in classi contrassegnate con il [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)
attributo. Viene in genere utilizzato quando si implementano classi delegate Objective-C.

La differenza sostanziale tra una classe associata regolare e una classe Delegate è che la classe delegata potrebbe avere uno o più metodi facoltativi.

Si consideri ad esempio la classe `UIKit` `UIAccelerometerDelegate`, questo è il modo in cui viene associato in Xamarin.iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Poiché si tratta di un metodo facoltativo per la definizione di `UIAccelerometerDelegate` non è necessario eseguire alcuna operazione. Tuttavia, se era presente un metodo obbligatorio sul protocollo, è necessario aggiungere il [`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)
attributo per il metodo. In questo modo l'utente dell'implementazione fornirà effettivamente un corpo per il metodo.

In generale, i protocolli vengono utilizzati nelle classi che rispondono ai messaggi. Questa operazione viene in genere eseguita in Objective-C assegnando alla proprietà "delegate" un'istanza di un oggetto che risponde ai metodi nel protocollo.

La convenzione in Xamarin.iOS prevede il supporto dello stile a regime di controllo libero di Objective-C, in cui qualsiasi istanza di un `NSObject` può essere assegnata al delegato e anche per esporre una versione fortemente tipizzata. Per questo motivo, in genere viene fornita una proprietà `Delegate` fortemente tipizzata e una `WeakDelegate` con tipizzazione debole. Generalmente si associa la versione debolmente tipizzata con [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)e si usa l'attributo [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) per fornire la versione fortemente tipizzata.

Viene illustrato come è stato associato il `UIAccelerometer` classe:

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

**Novità di MonoTouch 7,0**

A partire da MonoTouch 7,0 è stata incorporata una nuova e migliorata funzionalità di associazione del protocollo.  Questo nuovo supporto semplifica l'uso di idiomi Objective-C per l'adozione di uno o più protocolli in una determinata classe.

Per ogni definizione di protocollo `MyProtocol` in Objective-C, ora è disponibile un'interfaccia `IMyProtocol` che elenca tutti i metodi richiesti dal protocollo, oltre a una classe di estensione che fornisce tutti i metodi facoltativi.  Il codice precedente, combinato con il nuovo supporto nell'editor di Xamarin Studio consente agli sviluppatori di implementare metodi di protocollo senza dover utilizzare le sottoclassi separate delle classi di modelli astratte precedenti.

Tutte le definizioni che contengono l'attributo [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) genereranno tre classi di supporto che migliorano notevolmente il modo in cui si utilizzano i protocolli:

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

L' **implementazione della classe** fornisce una classe astratta completa che è possibile ignorare i singoli metodi di e ottenere la massima indipendenza dai tipi.  Tuttavia, poiché C# non supporta l'ereditarietà multipla, esistono scenari in cui potrebbe essere necessario disporre di una classe di base diversa, ma si vuole comunque implementare un'interfaccia in cui

Viene fornita la **definizione dell'interfaccia** generata.  Si tratta di un'interfaccia che dispone di tutti i metodi richiesti dal protocollo.  Ciò consente agli sviluppatori che vogliono implementare il protocollo di implementare semplicemente l'interfaccia.  Il runtime registrerà automaticamente il tipo come adozione del protocollo.

Si noti che l'interfaccia elenca solo i metodi richiesti ed espone i metodi facoltativi.  Ciò significa che le classi che adottano il protocollo otterranno il controllo del tipo completo per i metodi richiesti, ma dovranno ricorrere alla tipizzazione debole (usando manualmente [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attributi e corrispondenti alla firma) per i metodi di protocollo facoltativi.

Per semplificare l'utilizzo di un'API che utilizza protocolli, lo strumento di associazione produrrà anche una classe del metodo Extensions che espone tutti i metodi facoltativi.  Ciò significa che, se si utilizza un'API, sarà possibile considerare i protocolli come dotati di tutti i metodi.

Se si vogliono usare le definizioni di protocollo nell'API, sarà necessario scrivere interfacce vuote di ossatura nella definizione dell'API.  Se si vuole usare il protocollo protocollo in un'API, è necessario eseguire questa operazione:

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

Il valore precedente è necessario perché in fase di binding il `IMyProtocol` non esiste, per questo motivo è necessario fornire un'interfaccia vuota.

#### <a name="adopting-protocol-generated-interfaces"></a>Adozione di interfacce generate dal protocollo

Ogni volta che si implementa una delle interfacce generate per i protocolli, come indicato di seguito:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

L'implementazione per i metodi di interfaccia viene automaticamente esportata con il nome appropriato, quindi è equivalente a quanto segue:

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

### <a name="binding-class-extensions"></a>Estensioni della classe Binding

In Objective-C è possibile estendere le classi con nuovi metodi, analogamente ai C#metodi di estensione di Spirit. Quando è presente uno di questi metodi, è possibile usare il [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
attributo per contrassegnare il metodo come ricevitore del messaggio Objective-C.

In Xamarin.iOS, ad esempio, sono stati associati i metodi di estensione definiti in `NSString` quando `UIKit` viene importato come metodi nel `NSStringDrawingExtensions`, come indicato di seguito:

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Associazione degli elenchi di argomenti Objective-C

Objective-C supporta gli argomenti Variadic. Ad esempio:

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Per richiamare questo metodo da C# , è necessario creare una firma simile alla seguente:

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Questo metodo dichiara il metodo come interno, nascondendo l'API precedente dagli utenti, ma esponendola alla libreria. Quindi, è possibile scrivere un metodo simile al seguente:

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

### <a name="binding-fields"></a>Campi di binding

A volte si desidera accedere ai campi pubblici dichiarati in una libreria.

In genere questi campi contengono stringhe o valori integer a cui è necessario fare riferimento. Sono comunemente usati come stringhe che rappresentano una notifica specifica e come chiavi nei dizionari.

Per associare un campo, aggiungere una proprietà al file di definizione dell'interfaccia e decorare la proprietà con l'attributo [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) . Questo attributo accetta un parametro, ovvero il nome C del simbolo da cercare. Ad esempio:

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Se si desidera eseguire il wrapping di diversi campi in una classe statica che non deriva da `NSObject`, è possibile utilizzare il [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
attributo della classe, come indicato di seguito:

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

Il valore precedente genererà un `LonelyClass` che non deriva da `NSObject` e conterrà un'associazione al `NSSomeEventNotification`
`NSString` esposto come `NSString`.

È possibile applicare l'attributo [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) ai tipi di dati seguenti:

- riferimenti `NSString` (solo proprietà di sola lettura)
- riferimenti `NSArray` (solo proprietà di sola lettura)
- int a 32 bit (`System.Int32`)
- int a 64 bit (`System.Int64`)
- float a 32 bit (`System.Single`)
- float a 64 bit (`System.Double`)
- `System.Drawing.SizeF`
- `CGSize`

Oltre al nome del campo nativo, è possibile specificare il nome della libreria in cui si trova il campo passando il nome della libreria:

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

Se si esegue il collegamento statico, non esiste alcuna libreria a cui eseguire il binding, quindi è necessario usare il nome del `__Internal`:

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Associazioni enum

È possibile aggiungere `enum` direttamente nei file di binding per facilitarne l'uso all'interno delle definizioni API, senza usare un file di origine diverso (che deve essere compilato sia nei binding che nel progetto finale).

Esempio:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

È anche possibile creare enum personalizzati per sostituire `NSString` costanti. In questo caso il generatore creerà **automaticamente** i metodi per convertire i valori delle enumerazioni e le costanti NSString.

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

Nell'esempio precedente è possibile decidere di decorare `void Perform (NSString mode);` con un attributo [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) . L'API basata su costanti verrà **nascosta** dai consumer di binding.

Tuttavia, questo limiterebbe la sottoclasse del tipo perché l'alternativa più gradevole dell'API usa un attributo [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) . I metodi generati non sono `virtual`, ovvero non è possibile eseguirne l'override, che può essere o meno una scelta ottimale.

In alternativa, è possibile contrassegnare la definizione originale basata su `NSString``[Protected]`. Questo consentirà il funzionamento della sottoclasse, quando necessario, e la versione di wrap'ed continuerà a funzionare e chiamerà il metodo sottoposto a override.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Associazione di `NSValue`, `NSNumber`e `NSString` a un tipo migliore

L'attributo [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) consente l'associazione di `NSNumber`, `NSValue` e `NSString`(enumerazioni) a C# tipi più accurati. L'attributo può essere usato per creare un'API .NET migliore, più accurata sull'API nativa.

È possibile decorare i metodi (in valore restituito), i parametri e le proprietà con [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute). L'unica restrizione è che il membro **non deve** trovarsi all'interno di un [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 
o [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) interfaccia.

Ad esempio:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Output:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Internamente si eseguirà il `bool?` <-> `NSNumber` e `CGRect`le conversioni  <-> `NSValue`.

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) supporta anche matrici di `NSNumber` `NSValue` e `NSString`(enum).

Ad esempio:

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

Output:

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` è un'enumerazione `NSString` supportata, si recupererà il valore di `NSString` corretto e si gestirà la conversione del tipo.

Per visualizzare i tipi di conversione supportati, vedere la documentazione [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) .

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Notifiche di binding

Le notifiche sono messaggi che vengono inviati all'`NSNotificationCenter.DefaultCenter` e vengono usati come meccanismo per trasmettere i messaggi da una parte dell'applicazione a un'altra. Gli sviluppatori sottoscrivono le notifiche in genere tramite il metodo [AddObserver](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification})) di [NSNotificationCenter](xref:Foundation.NSNotificationCenter). Quando un'applicazione invia un messaggio al centro notifiche, in genere contiene un payload archiviato nel dizionario [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) . Questo dizionario è debolmente tipizzato e il recupero di informazioni è soggetto a errori, in quanto gli utenti devono in genere leggere nella documentazione le chiavi disponibili sul dizionario e i tipi di valori che possono essere archiviati nel dizionario. La presenza di chiavi viene talvolta utilizzata anche come valore booleano.

Il generatore di binding Xamarin.iOS fornisce supporto per gli sviluppatori per associare le notifiche. A tale scopo, impostare il [`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)
attributo in una proprietà che è stato anche contrassegnato con un [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)
Property (può essere pubblico o privato).

Questo attributo può essere usato senza argomenti per le notifiche che non contengono payload oppure è possibile specificare un `System.Type` che fa riferimento a un'altra interfaccia nella definizione dell'API, in genere con il nome che termina con "EventArgs". Il generatore trasformerà l'interfaccia in una classe che `EventArgs` sottoclassi e includerà tutte le proprietà elencate. È necessario utilizzare l'attributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) nella classe EventArgs per elencare il nome della chiave utilizzata per cercare il dizionario Objective-C per recuperare il valore.

Ad esempio:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

Il codice precedente genererà una classe annidata `MyClass.Notifications` con i metodi seguenti:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Gli utenti del codice possono quindi sottoscrivere facilmente le notifiche pubblicate in [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) usando un codice simile al seguente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

Il valore restituito da `ObserveDidStart` può essere usato per arrestare facilmente la ricezione delle notifiche, come segue:

```csharp
token.Dispose ();
```

In alternativa, è possibile chiamare [NSNotification. DefaultCenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) e passare il token. Se la notifica contiene parametri, è necessario specificare un helper `EventArgs` interfaccia, come indicato di seguito:

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

Il codice precedente genererà una classe `MyScreenChangedEventArgs` con le proprietà `ScreenX` e `ScreenY` che recupereranno i dati dal dizionario [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) usando rispettivamente le chiavi "ScreenXKey" e "ScreenYKey" e applicano le conversioni corrette. L'attributo `[ProbePresence]` viene usato per il generatore per verificare se la chiave è impostata nel `UserInfo`, anziché tentare di estrarre il valore. Viene usato nei casi in cui la presenza della chiave è il valore, in genere per i valori booleani.

In questo modo è possibile scrivere codice simile al seguente:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>Categorie di binding

Le categorie sono un meccanismo Objective-C usato per estendere il set di metodi e proprietà disponibili in una classe.   In pratica, vengono usati per estendere la funzionalità di una classe di base (ad esempio `NSObject`) quando è collegato un Framework specifico, ad esempio `UIKit`, rendendo disponibili i metodi, ma solo se il nuovo Framework è collegato.   In altri casi vengono usati per organizzare le funzionalità in una classe in base alla funzionalità.   Sono simili ai metodi di C# estensione. Questa è l'aspetto di una categoria in Objective-C:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

Nell'esempio precedente, se presente in una libreria, si estendono le istanze di `UIView` con il metodo `makeBackgroundRed`.

Per associarle, è possibile usare l'attributo [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) in una definizione di interfaccia.  Quando si usa il [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
attributo, il significato della [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
le modifiche degli attributi vengono utilizzate per specificare la classe di base da estendere, per essere il tipo da estendere.

Di seguito viene illustrato il modo in cui le estensioni `UIView` vengono C# associate e convertite in metodi di estensione:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

In precedenza viene creato un `MyUIViewExtension` una classe che contiene il metodo di estensione `MakeBackgroundRed`.  Ciò significa che è ora possibile chiamare "MakeBackgroundRed" in qualsiasi sottoclasse `UIView`, offrendo la stessa funzionalità che si otterrebbe in Objective-C. In altri casi, le categorie vengono usate per non estendere una classe di sistema, ma per organizzare le funzionalità, esclusivamente a scopo decorativo.  analogamente a quanto segue:

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Sebbene sia possibile utilizzare la [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
attributo anche per questo stile di dichiarazione delle dichiarazioni, è possibile aggiungerli tutti alla definizione della classe.  Entrambi gli obiettivi avranno lo stesso risultato:

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

In questi casi è solo più breve eseguire il merge delle categorie:

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

### <a name="binding-blocks"></a>Blocchi di binding

I blocchi sono un nuovo costrutto introdotto da Apple per portare l'equivalente C# funzionale dei metodi anonimi in Objective-C. Ad esempio, la classe `NSSet` espone ora questo metodo:

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

La descrizione precedente dichiara un metodo denominato `enumerateObjectsUsingBlock:` che accetta un argomento denominato `block`. Questo blocco è simile a un C# metodo anonimo perché dispone del supporto per l'acquisizione dell'ambiente corrente (puntatore "This", accesso a variabili e parametri locali). Il metodo precedente in `NSSet` richiama il blocco con due parametri una `NSObject` (la parte `id obj`) e un puntatore a una parte booleana (la `BOOL *stop`).

Per associare questo tipo di API a btouch, è necessario prima dichiarare la firma del tipo di blocco come C# delegato e quindi farvi riferimento da un punto di ingresso dell'API, come indicato di seguito:

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

Ora il codice può chiamare la funzione da C#:

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

Se si preferisce, è anche possibile usare le espressioni lambda:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>Metodi asincroni

Il generatore di associazioni può trasformare una determinata classe di metodi in metodi descrittivi asincroni (metodi che restituiscono un'attività o un'attività&lt;T&gt;).

È possibile utilizzare il [`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 
attributo nei metodi che restituiscono void e il cui ultimo argomento è un callback.  Quando si applica questa operazione a un metodo, il generatore di associazioni genererà una versione di tale metodo con il suffisso `Async`.  Se il callback non accetta parametri, il valore restituito sarà un `Task`, se il callback accetta un parametro, il risultato sarà una `Task<T>`.  Se il callback accetta più parametri, è necessario impostare il `ResultType` o `ResultTypeName` per specificare il nome desiderato del tipo generato che conterrà tutte le proprietà.

Esempio:

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

Il codice precedente genererà sia il metodo LoadFile, sia:

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Emersione di tipi forti per i parametri NSDictionary vulnerabili

In molte posizioni nell'API Objective-C, i parametri vengono passati come API di `NSDictionary` con tipizzazione debole con chiavi e valori specifici, ma questi sono soggetti a errori (è possibile passare chiavi non valide e non ricevere avvisi; è possibile passare valori non validi e non ricevere avvisi) e frustranti da usare perché richiedono più viaggi alla documentazione per cercare i nomi e i valori delle chiavi possibili.

La soluzione consiste nel fornire una versione fortemente tipizzata che fornisca la versione fortemente tipizzata dell'API e che dietro le quinte venga eseguito il mapping dei vari valori e chiavi sottostanti.

Se, ad esempio, l'API Objective-C accetta un `NSDictionary` ed è documentata come prendendo la chiave `XyzVolumeKey`, che accetta un `NSNumber` con un valore di volume compreso tra 0,0 e 1,0 e un `XyzCaptionKey` che accetta una stringa, si vuole che gli utenti dispongano di un'API gradevole simile alla seguente:

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

Il `Volume` proprietà è definito come Nullable float, poiché la convenzione in Objective-C non richiede che questi dizionari abbiano il valore, quindi esistono scenari in cui il valore potrebbe non essere impostato.

A tale scopo, è necessario eseguire alcune operazioni:

- Creare una classe fortemente tipizzata, che sottoclassi [DictionaryContainer](xref:Foundation.DictionaryContainer) e fornisca i vari getter e setter per ogni proprietà.
- Dichiarare gli overload per i metodi che accettano `NSDictionary` acquisire la nuova versione fortemente tipizzata.

È possibile creare la classe fortemente tipizzata manualmente o usare il generatore per eseguire le operazioni.  Prima di tutto viene illustrato come eseguire questa operazione manualmente, in modo da comprendere cosa accade e quindi l'approccio automatico.

È necessario creare un file di supporto per questo, non viene inserito nell'API del contratto.  Questo è ciò che è necessario scrivere per creare la classe XyzOptions:

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

È quindi necessario fornire un metodo wrapper che supporti l'API di alto livello, oltre all'API di basso livello.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Se non è necessario sovrascrivere l'API, è possibile nascondere in modo sicuro l'API basata su NSDictionary usando il [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
attributo.

Come si può notare, si usa il [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)
attributo per la superficie di un nuovo punto di ingresso dell'API, che verrà esposto usando la classe `XyzOptions` fortemente tipizzata.  Il metodo wrapper consente inoltre di passare un valore null.

A questo punto, una cosa che non è stato menzionato è il punto in cui provengono i valori `XyzOptionsKeys`.  In genere, le chiavi di un'API vengono raggruppate in una classe statica come `XyzOptionsKeys`, come indicato di seguito:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Viene ora esaminato il supporto automatico per la creazione di questi dizionari fortemente tipizzati.  In questo modo si evita una grande quantità di standard ed è possibile definire il dizionario direttamente nel contratto API, anziché usare un file esterno.

Per creare un dizionario fortemente tipizzato, introdurre un'interfaccia nell'API e decorarla con l'attributo [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) .  Indica al generatore che deve creare una classe con lo stesso nome dell'interfaccia che deriverà da `DictionaryContainer` e fornirà funzioni di accesso tipizzate complesse.

L'attributo [`[StrongDictionary]`](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) accetta un parametro, ovvero il nome della classe statica che contiene le chiavi del dizionario.  Quindi, ogni proprietà dell'interfaccia diventerà una funzione di accesso fortemente tipizzata.  Per impostazione predefinita, il codice userà il nome della proprietà con il suffisso "Key" nella classe statica per creare la funzione di accesso.

Ciò significa che la creazione di una funzione di accesso fortemente tipizzata non richiede più un file esterno, né la necessità di creare manualmente getter e setter per ogni proprietà, né di eseguire manualmente la ricerca delle chiavi.

Questo è l'aspetto dell'intero binding:

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

Se è necessario fare riferimento ai membri di `XyzOption` un campo diverso (ovvero non il nome della proprietà con il suffisso `Key`), è possibile decorare la proprietà con un [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attributo con il nome che si desidera utilizzare.

<a name="Type_mappings" />

## <a name="type-mappings"></a>Mapping dei tipi

Questa sezione illustra come viene eseguito il mapping tra i tipi C# Objective-C e i tipi.

<a name="Simple_Types" />

### <a name="simple-types"></a>Tipi semplici

La tabella seguente illustra come eseguire il mapping dei tipi dal mondo Objective-C e CocoaTouch al mondo Xamarin.iOS:

|Nome del tipo Objective-C|Tipo di API unificata Xamarin.iOS|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([altre informazioni sull'associazione NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (vedere anche: [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|Tipi CoreFoundation (`CF*`)|`CoreFoundation.CF*`|
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

Il runtime di Xamarin.iOS gestisce automaticamente la conversione C# di matrici in `NSArrays` ed esegue la conversione, quindi, ad esempio, il metodo di Objective-C immaginaria che restituisce un `NSArray` di `UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

È associato come segue:

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

L'idea è quella di usare una matrice fortemente C# tipizzata in quanto ciò consentirà all'IDE di fornire il corretto completamento del codice con il tipo effettivo senza forzare l'utente a indovinare o cercare la documentazione per individuare il tipo effettivo degli oggetti contenuti nella matrice.

Nei casi in cui non è possibile rilevare il tipo effettivo più derivato contenuto nella matrice, è possibile usare `NSObject []` come valore restituito.

<a name="Selectors" />

### <a name="selectors"></a>Selettori

I selettori vengono visualizzati nell'API Objective-C come tipo speciale `SEL`. Quando si associa un selettore, è necessario eseguire il mapping del tipo a `ObjCRuntime.Selector`.  In genere i selettori vengono esposti in un'API con un oggetto, l'oggetto di destinazione e un selettore da richiamare nell'oggetto di destinazione. Fornire entrambi questi elementi corrisponde fondamentalmente al C# delegato: un elemento che incapsula sia il metodo da richiamare che l'oggetto per richiamare il metodo in.

Questo è l'aspetto dell'associazione:

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

Questo è il modo in cui il metodo viene in genere usato in un'applicazione:

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

Per rendere il binding più bello C# per gli sviluppatori, in genere si fornirà un metodo che accetta un parametro `NSAction` C# , che consente di utilizzare delegati e espressioni lambda al posto delle `Target+Selector`. A tale scopo, in genere si nasconde il metodo `SetTarget` contrassegnando il metodo con un [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
e quindi esporre un nuovo metodo di supporto, come segue:

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

Ora il codice utente può essere scritto come segue:

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

Quando si associa un metodo che accetta una `NSString`, è possibile sostituirlo con un C# tipo stringa, sia sui tipi restituiti che sui parametri.

L'unico caso in cui può essere utile usare un `NSString` direttamente è quando la stringa viene usata come token. Per altre informazioni sulle stringhe e `NSString`, vedere il documento [relativo alla progettazione dell'API in NSString](~/ios/internals/api-design/nsstring.md) .

In alcuni casi rari, un'API potrebbe esporre una stringa di tipo C (`char *`) anziché una stringa Objective-C (`NSString *`). In questi casi, è possibile aggiungere annotazioni al parametro con il [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)
attributo.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>parametri out/ref

Alcune API restituiscono valori nei relativi parametri o passano i parametri per riferimento.

In genere, la firma ha un aspetto simile al seguente:

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

Nel primo esempio viene illustrato un idioma Objective-C comune per restituire i codici di errore, viene passato un puntatore a un puntatore `NSError` e, al momento della restituzione, viene impostato il valore.   Il secondo metodo Mostra come un metodo Objective-C potrebbe prendere un oggetto e modificarne il contenuto.   Si tratta di un passaggio per riferimento, anziché di un valore di output puro.

Il binding avrà un aspetto simile al seguente:

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Attributi di gestione della memoria

Quando si usa l'attributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) e si passano dati che verranno conservati dal metodo chiamato, è possibile specificare la semantica degli argomenti passandola come secondo parametro, ad esempio:

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

Il valore precedente contrassegna il valore come avente la semantica "Mantieni". La semantica disponibile è la seguente:

- Assegna
- Copia
- Mantieni

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Linee guida di stile

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>Uso di [Internal]

È possibile utilizzare il [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
attributo per nascondere un metodo dall'API pubblica. Questa operazione può essere utile nei casi in cui l'API esposta è troppo bassa e si desidera fornire un'implementazione di alto livello in un file separato basato su questo metodo.

Questa operazione può essere usata anche quando si verificano limitazioni nel generatore di associazioni. ad esempio, alcuni scenari avanzati potrebbero esporre tipi non associati e si desidera eseguire il binding in modo autonomo e si desidera eseguire il wrapping di tali tipi in modo autonomo.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Gestori di eventi e callback

Le classi Objective-C in genere trasmettono notifiche o richiedono informazioni inviando un messaggio su una classe Delegate (delegato Objective-C).

Questo modello, sebbene sia completamente supportato e esposto da Xamarin.iOS può talvolta risultare complesso. Xamarin.iOS espone il C# modello di eventi e un sistema di callback di metodo nella classe che può essere usata in queste situazioni. Questo consente l'esecuzione di codice simile al seguente:

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

Il generatore di associazione è in grado di ridurre la quantità di tipizzazione necessaria per eseguire il mapping del modello C# Objective-C al modello.

A partire da Xamarin.iOS 1,4 sarà possibile anche indicare al generatore di produrre binding per uno specifico delegati Objective-C ed esporre il delegato come C# eventi e proprietà nel tipo di host.

Questo processo include due classi, ovvero la classe host che è quella che attualmente genera eventi e li invia all'`Delegate` o `WeakDelegate` e alla classe delegata effettiva.

Considerando la seguente configurazione:

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

Per eseguire il wrapping della classe è necessario:

- Nella classe host aggiungere all' [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   dichiarare il tipo che funge da delegato e il C# nome esposto. Nell'esempio precedente sono `typeof (MyClassDelegate)` e `WeakDelegate` rispettivamente.
- Nella classe Delegate, per ogni metodo con più di due parametri, è necessario specificare il tipo che si desidera utilizzare per la classe EventArgs generata automaticamente.

Il generatore di associazioni non è limitato al wrapping di una sola destinazione di evento. è possibile che alcune classi Objective-C creino messaggi a più di un delegato, pertanto sarà necessario fornire matrici per supportare questa configurazione. Per la maggior parte delle configurazioni non sarà necessario, ma il generatore sarà pronto per supportare tali casi.

Il codice risultante sarà:

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

Il `EventArgs` viene utilizzato per specificare il nome della classe `EventArgs` da generare. È consigliabile usare uno per firma (in questo esempio, il `EventArgs` conterrà una proprietà `With` di tipo nint).

Con le definizioni precedenti, il generatore produrrà l'evento seguente nell'oggetto MyClass generato:

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

A questo punto è possibile usare il codice seguente:

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

I callback sono esattamente come le chiamate di eventi, la differenza è che anziché avere più sottoscrittori potenziali (ad esempio, più metodi possono essere collegati a un evento `Clicked` o a un evento `DownloadFinished`) possono avere un solo Sottoscrittore.

Il processo è identico. l'unica differenza consiste nel fatto che anziché esporre il nome della classe `EventArgs` che verrà generata, EventArgs viene effettivamente utilizzato per denominare il nome C# del delegato risultante.

Se il metodo nella classe Delegate restituisce un valore, il generatore di associazioni eseguirà il mapping di questo oggetto in un metodo delegato nella classe padre anziché in un evento. In questi casi è necessario specificare il valore predefinito che deve essere restituito dal metodo se l'utente non esegue l'associazione al delegato. A tale scopo, usare la [`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)
o [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) gli attributi.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) hardcoded un valore restituito, mentre [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
viene usato per specificare l'argomento di input che verrà restituito.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Enumerazioni e tipi di base

È anche possibile fare riferimento a enumerazioni o tipi di base che non sono direttamente supportati dal sistema di definizione dell'interfaccia btouch. A tale scopo, inserire le enumerazioni e i tipi di base in un file separato e includerlo come parte di uno dei file aggiuntivi forniti a btouch.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>Collegamento delle dipendenze

Se si stanno associando API che non fanno parte dell'applicazione, è necessario assicurarsi che il file eseguibile sia collegato a queste librerie.

È necessario informare Xamarin.iOS come collegare le librerie. questa operazione può essere eseguita modificando la configurazione della build per richiamare il comando `mtouch` con alcuni argomenti di compilazione aggiuntivi che specificano come collegare le nuove librerie usando l'opzione "-gcc_flags", seguita da una stringa racchiusa tra virgolette che contiene tutte le librerie aggiuntive necessarie per il programma, in questo modo:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

Nell'esempio precedente vengono collegati `libMyLibrary.a`, `libSystemLibrary.dylib` e la libreria `CFNetwork` Framework nell'eseguibile finale.

In alternativa, è possibile sfruttare i vantaggi del [`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)a livello di assembly, che è possibile incorporare nei file di contratto, ad esempio `AssemblyInfo.cs`.
Quando si usa il [`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), è necessario che la libreria nativa sia disponibile quando si crea l'associazione, in quanto in questo modo verrà incorporata la libreria nativa con l'applicazione. Ad esempio:

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

Ci si potrebbe chiedere, perché è necessario `-force_load` comando e il motivo è che il flag-ObjC, anche se compila il codice in, non mantiene i metadati necessari per supportare le categorie (l'eliminazione del codice indesiderato del linker/compilatore), che è necessario in fase di esecuzione per Xamarin.iOS.

<a name="Assisted_References" />

## <a name="assisted-references"></a>Riferimenti assistiti

Alcuni oggetti temporanei come i fogli di azione e le caselle di avviso sono complessi per tenere traccia di per gli sviluppatori e il generatore di associazioni può essere utile qui.

Ad esempio, se si dispone di una classe che mostra un messaggio e quindi genera un evento `Done`, il modo tradizionale per gestire questa situazione è:

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

Nello scenario precedente è necessario che lo sviluppatore mantenga il riferimento all'oggetto stesso e perda o elimini attivamente il riferimento per box.  Durante l'associazione del codice, il generatore supporta tenendo traccia del riferimento e cancellarlo quando viene richiamato un metodo speciale, il codice precedente diventa quindi:

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Si noti che non è più necessario che la variabile venga mantenuta in un'istanza di, che funzioni con una variabile locale e che non sia necessario cancellare il riferimento quando l'oggetto muore.

Per sfruttare questo aspetto, la classe deve disporre di una proprietà Events impostata nella Dichiarazione [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) e anche della variabile `KeepUntilRef` impostata sul nome del metodo che viene richiamato quando l'oggetto ha completato il proprio lavoro, come indicato di seguito:

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Protocolli di ereditarietà

A partire da Xamarin.iOS v 3.2, è supportata l'ereditarietà da protocolli contrassegnati con la proprietà [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) . Questa operazione è utile in alcuni modelli di API, ad esempio in `MapKit` in cui il protocollo di `MKOverlay`, eredita dal protocollo `MKAnnotation` ed è adottato da un certo numero di classi che ereditano da `NSObject`.

Storicamente è necessario copiare il protocollo in ogni implementazione, ma in questi casi ora è possibile che la classe `MKShape` erediti dal protocollo di `MKOverlay` e generi tutti i metodi richiesti automaticamente.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di binding](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
