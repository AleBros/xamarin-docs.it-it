---
title: Associazione di librerie Objective-C
description: Questo documento fornisce una panoramica generale di come creare C# binding a codice Objective-C, che descrive come associare gli eventi, metodi, i controlli personalizzati e altro ancora.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: e97b211fe6a92ee0df3beed2301cf00c3d42c8f8
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671806"
---
# <a name="binding-objective-c-libraries"></a>Associazione di librerie Objective-C

Quando si usa xamarin. IOS o xamarin. Mac, potrebbero verificarsi casi in cui si vuole usare una libreria Objective-C di terze parti. In questi casi, è possibile usare progetti di Binding Xamarin per creare un C# binding alle librerie native di Objective-C. Il progetto usa gli stessi strumenti che utilizziamo per portare iOS e Mac API per C#.

Questo documento descrive come associare le API Objective-C, se si esegue il binding solo le API C, è consigliabile usare il meccanismo di .NET standard a tale scopo, [framework di P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/).
Informazioni dettagliate su come collegare in modo statico una libreria di C sono disponibili nel [collegamento di librerie Native](~/ios/platform/native-interop.md) pagina.

Vedere i nostri complementare [Guida di riferimento per i tipi di associazione](~/cross-platform/macios/binding/binding-types-reference.md).
Inoltre, se si desidera ottenere ulteriori informazioni su cosa accade dietro le quinte, controllare nostri [Panoramica sul Binding](~/cross-platform/macios/binding/overview.md) pagina.

Le associazioni possono essere create per iOS e raccolte Mac.
Questa pagina descrive come usare in un'associazione, tuttavia le associazioni Mac sono molto simili di iOS.

**Codice di esempio per iOS**

È possibile usare la [iOS di esempio di associazione](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) progetto sperimentare con le associazioni.

<a name="Getting_Started" />

## <a name="getting-started"></a>Per iniziare

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Il modo più semplice per creare un'associazione consiste nel creare un progetto di associazione di xamarin. IOS.
È possibile farlo da Visual Studio per Mac selezionando il tipo di progetto **iOS > Libreria > libreria di binding**:

[![](objective-c-libraries-images/00-sml.png "Eseguire questa operazione da Visual Studio per Mac selezionando il tipo di progetto libreria di binding libreria iOS")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Il modo più semplice per creare un'associazione consiste nel creare un progetto di associazione di xamarin. IOS.
È possibile farlo da Visual Studio in Windows, selezionare il tipo di progetto **Visual C# > iOS > libreria di binding (iOS)**:

[![](objective-c-libraries-images/00vs-sml.png "Libreria di binding iOS iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Nota: Per progetti di binding **xamarin. Mac** sono supportati solo in Visual Studio per Mac.

-----

Il progetto generato contiene un modello di piccole dimensioni che è possibile modificare, contiene due file: `ApiDefinition.cs` e `StructsAndEnums.cs`.

Il `ApiDefinition.cs` è dove si definirà il contratto API, si tratta del file che descrive come le API Objective-C sottostante viene proiettato in C#. La sintassi e il contenuto del file l'argomento principale di discussione di questo documento e il contenuto di essa è limitato a C# le interfacce e C# dichiarazioni delegate. Il `StructsAndEnums.cs` è il file in cui vengono immesse le definizioni necessarie per le interfacce e delegati. Sono inclusi valori di enumerazione e strutture che potrebbe essere usato dal codice.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Associazione di un'API

Per eseguire un'associazione completa, è opportuno comprendere la definizione dell'API di Objective-C e acquisire familiarità con le linee guida per la progettazione di .NET Framework.

Per associare la raccolta in genere iniziare con un file di definizione API. Un file di definizione API è semplicemente un C# file di origine che contiene C# le interfacce che sono state annotate con un numero limitato di attributi che consentono di guidare l'associazione.  Questo file si trova ciò che definisce il contratto tra C# e Objective-C è.

Ad esempio, questo è un file di api semplici per una libreria:

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

Nell'esempio precedente definisce una classe denominata `Cocos2D.Camera` che deriva dal `NSObject` tipo di base (questo tipo viene fornito dal `Foundation.NSObject`) e che definisce una proprietà statica (`ZEye`), due metodi che accettano alcun argomento e un metodo che accetta tre argomenti.

Viene descritta un'analisi approfondita del formato del file di API e gli attributi che è possibile usare nel [file di definizione dell'API](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) sezione riportata di seguito.

Per produrre un'associazione completa, è in genere gestirà quattro componenti:

-  Il file di definizione API (`ApiDefinition.cs` nel modello).
-  Facoltativo: qualsiasi enumerazioni, tipi, gli struct necessari dal file di definizione API (`StructsAndEnums.cs` nel modello).
-  Facoltativo: le origini aggiuntive che potrebbero espandere l'associazione generata o fornire un più C# API descrittivo (qualsiasi C# i file aggiunti al progetto).
-  La libreria nativa che si esegue l'associazione.

Questo grafico mostra la relazione tra i file:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "Questo grafico mostra la relazione tra i file")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

Il file di definizione dell'API conterrà solo le definizioni di interfaccia e gli spazi dei nomi (con tutti i membri che può contenere un'interfaccia) e non deve contenere le classi, enumerazioni, delegati o gli struct. Il file di definizione API è semplicemente il contratto che verrà usato per generare le API.

Aggiungere codice che è necessario come enumerazioni o le classi di supporto deve essere ospitato in un file separato, nell'esempio precedente "CameraMode" è un valore di enumerazione che non esiste nel file CS e deve essere ospitato in un file separato, ad esempio `StructsAndEnums.cs` :

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

Il `APIDefinition.cs` file viene combinato con il `StructsAndEnum` classe e vengono usati per generare l'associazione di core della libreria. È possibile usare la libreria risulta come-è, ma in genere, è consigliabile ottimizzare la raccolta risulta per aggiungere alcuni C# funzionalità a vantaggio di utenti. Alcuni esempi includono l'implementazione di un `ToString()` metodo, fornire C# indicizzatori, aggiungere le conversioni implicite in e da alcuni tipi nativi o fornire versioni fortemente tipizzata di alcuni metodi. Questi miglioramenti vengono archiviati in eccesso C# file. Aggiungere semplicemente il C# al progetto e verranno inclusi in questo processo di compilazione.

Viene illustrato come implementare il codice di `Extra.cs` file. Si noti che si useranno le classi parziali come questi aumentare le classi parziali generate dalla combinazione dei `ApiDefinition.cs` e il `StructsAndEnums.cs` associazione di base:

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

Compilazione della libreria produrrà il binding nativo.

Per completare questa associazione, è necessario aggiungere la libreria nativa per il progetto.  È possibile farlo aggiungendo la libreria nativa per il progetto, trascinando e rilasciando la libreria nativa dal Finder nel progetto in Esplora soluzioni o facendo clic sul progetto e scegliendo **Add**  >  **Add Files** per selezionare la libreria nativa.
Le librerie native per convenzione iniziano con la parola "lib" e terminano con l'estensione "estensione a". Quando si esegue questa operazione, Visual Studio per Mac verrà aggiunto due file: il file di estensione a e popolati automaticamente C# file che contiene informazioni su cosa contiene la libreria nativa:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Le librerie native per convenzione lib word per iniziare e terminano con l'estensione a estensione")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

Il contenuto del `libMagicChord.linkwith.cs` file dispone di informazioni sul modo in cui è possibile usare questa libreria e indica l'IDE per creare un pacchetto di questo file binario nel file DLL risulta:

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Completa i dettagli su come usare il [`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) 
attributo sono documentati nel [Guida di riferimento per i tipi di associazione](~/cross-platform/macios/binding/binding-types-reference.md).

A questo punto quando si compila il progetto finirà con un `MagicChords.dll` file che contiene l'associazione e la libreria nativa. È possibile distribuire il progetto o usare la DLL risultante di altri sviluppatori per i propri.

In alcuni casi si potrebbe rilevare che è necessario alcuni valori di enumerazione, definizioni di delegati o altri tipi. Non inserire quelli nel file di definizione API, poiché si tratta semplicemente di un contratto

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>Il file di definizione API

Il file di definizione API è costituito da un numero di interfacce. Le interfacce nella definizione dell'API si trasformerà in una dichiarazione di classe e deve essere decorate con il [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) attributo per specificare la classe base per la classe.

È lecito chiedersi perché non abbiamo utilizzato le classi anziché le interfacce per la definizione del contratto. Abbiamo scelto di interfacce, perché ci ha permesso di scrivere il contratto per un metodo senza dover fornire un corpo del metodo nel file di definizione API o necessità di fornire un corpo che era necessario generare un'eccezione o restituisce un valore significativo.

Tuttavia, poiché si sta usando l'interfaccia come una struttura per generare una classe, era necessario ricorrere alla decorazione delle varie parti del contratto con gli attributi per guidare l'associazione.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Metodi di associazione

L'associazione più semplice che è possibile eseguire consiste nell'associare un metodo. Dichiarando un metodo nell'interfaccia con il C# convenzioni di denominazione e decorare il metodo con il [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
Attributo. Il [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributo è ciò che collega il C# nome con il nome di Objective-C nel runtime di xamarin. IOS. Il parametro del [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attributo è il nome del selettore di Objective-C. Ecco alcuni esempi:

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

Negli esempi precedenti viene illustrato come è possibile associare i metodi di istanza. Per associare i metodi statici, è necessario usare il [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) attributo, simile al seguente:

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Ciò è necessario perché il contratto è parte di un'interfaccia e le interfacce non hanno alcuna nozione di dichiarazioni di istanza statica di Visual Studio, pertanto è anche in questo caso occorre fare ricorso agli attributi. Se si desidera nascondere un metodo specifico dell'associazione, è possibile decorare il metodo con il [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attributo.

Il `btouch-native` comando introdurrà controlli per i parametri di riferimento non sia null. Se si desidera consentire valori null per un determinato parametro, usare il [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
attributo per il parametro, simile al seguente:

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Quando si esportano un tipo riferimento, con la [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) parola chiave è anche possibile specificare la semantica di allocazione. Ciò è necessario per garantire che nessun dato viene perso.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Proprietà di binding

Proprio come i metodi, proprietà di Objective-C sono associate tramite il [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
attributo ed eseguire il mapping direttamente al C# proprietà. Proprio come i metodi, proprietà possono essere decorate con il [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)
E la [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
Attributi.

Quando si usa la [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributo su una proprietà in background btouch-nativo associa in effetti due metodi: il getter e setter. Il nome specificato per l'esportazione è la **basename** e il setter viene calcolato anteponendo la parola "set", l'attivazione alla prima lettera del **basename** in maiuscolo e rendendo il selettore di richiedere un argomento. Ciò significa che `[Export ("label")]` applicata a una proprietà effettivamente associa "label" e "setLabel:" Metodi di Objective-C.

A volte le proprietà di Objective-C non seguono il modello descritto in precedenza e il nome viene sovrascritto manualmente. In questi casi è possibile controllare il modo in cui l'associazione è generato tramite il [`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) 
attributo nel getter o setter, ad esempio:

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

Viene quindi associa "isMenuVisible" e "setMenuVisible:". Facoltativamente, è possibile associare una proprietà usando la sintassi seguente:

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

In cui il getter e setter sono definite esplicitamente come nel `name` e `setName` binding precedente.

Oltre al supporto per le proprietà statiche usando [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute), è possibile decorare la proprietà statica thread con [ `[IsThreadStatic]` ](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute), ad esempio:

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Proprio come i metodi consentono di alcuni parametri viene contrassegnata con [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute), è possibile applicare [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
per una proprietà per indicare tale valore null è un valore valido per la proprietà, ad esempio:

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

Il [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) possa specificare anche il parametro direttamente nel metodo di impostazione:

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Avvertenze di associazione di controlli personalizzati

Quando si configura l'associazione per un controllo personalizzato, è necessario considerare le avvertenze seguenti:

1. **Le proprietà di associazione devono essere statiche** : quando si definisce l'associazione delle proprietà, il [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) attributo deve essere usato.
 2. **I nomi delle proprietà deve corrispondere esattamente** -il nome utilizzato per associare la proprietà deve corrispondere esattamente al nome della proprietà del controllo personalizzato.
3. **Tipi di proprietà devono corrispondere esattamente** -il tipo di variabile utilizzato per associare la proprietà deve corrispondere esattamente al tipo della proprietà del controllo personalizzato.
4. **I punti di interruzione e il getter/setter** : posizionati i punti di interruzione nel getter o metodi di impostazione della proprietà non verranno mai attivati.
5. **Osservare i callback** -sarà necessario usare i callback di osservazione per ricevere una notifica delle modifiche nei valori delle proprietà di controlli personalizzati.

L'associazione riesce in modo invisibile in fase di esecuzione può causare il mancato rispetto delle avvertenze elencate sopra.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Le proprietà e pattern modificabile objective-C

Framework di Objective-C usano un linguaggio in cui alcune classi non sono modificabili con una sottoclasse modificabile. Ad esempio `NSString` è la versione non modificabile, mentre `NSMutableString` è la sottoclasse che consente la modifica.

In queste classi è frequente vedere la classe di base non modificabile contengono proprietà con un metodo Get, ma nessun setter. E per la versione modificabile di introdurre il setter. Poiché questo non è possibile con C#, è stato necessario eseguire il mapping di questo linguaggio in un linguaggio più il corretto funzionamento con C#.

Il modo in cui è associato a C# viene aggiunta sia il getter e setter sulla classe di base, ma quando si contrassegna il metodo di impostazione con un [`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)
Attributo.

Quindi, nella sottoclasse modificabile, usare il [`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 
attributo sulla proprietà per assicurarsi che la proprietà è effettivamente override del comportamento dell'elemento padre.

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
-  `Foo (NSCoder)`: il costruttore usato durante la deserializzazione del file NIB (esegue il mapping di Objective-c "initWithCoder:" costruttore).
-  `Foo (IntPtr handle)`: il costruttore per la creazione basata su handle, questo viene richiamato dal runtime quando il runtime deve esporre un oggetto gestito da un oggetto non gestito.
-  `Foo (NSEmptyFlag)`: viene utilizzato dalle classi derivate per impedire l'inizializzazione valore double.

Per i costruttori definiti, devono essere dichiarati usando la firma seguente all'interno della definizione di interfaccia: devono restituire un `IntPtr` valore e il nome del metodo deve essere il costruttore. Ad esempio per associare il `initWithFrame:` costruttore, questo è ciò che si utilizzerebbe:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Protocolli di associazione

Come descritto nel documento di progettazione API, nella sezione [discutere i modelli e i protocolli](~/ios/internals/api-design/index.md#Models), xamarin. IOS viene eseguito il mapping dei protocolli di Objective-C in classi che sono state contrassegnate con il [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)
Attributo. In genere viene utilizzato durante l'implementazione di classi di delegati di Objective-C.

La grande differenza tra una normale classe associata e una classe delegata è che la classe delegate può avere uno o più metodi facoltativi.

Si consideri ad esempio la `UIKit` classe `UIAccelerometerDelegate`, si tratta di come è associato in xamarin. ios:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Poiché si tratta di un metodo facoltativo alla definizione `UIAccelerometerDelegate` non c'è altro a eseguire operazioni. Ma se si è verificato un metodo richiesto dal protocollo, è necessario aggiungere il [`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)
attributo al metodo. Questa operazione forzerà l'utente dell'implementazione per fornire effettivamente un corpo del metodo.

In generale, i protocolli vengono usati nelle classi che rispondono ai messaggi. Questa operazione viene in genere eseguita in Objective-C tramite l'assegnazione alla proprietà "delegate" di un'istanza di un oggetto che risponde ai metodi nel protocollo.

La convenzione in xamarin. IOS deve supportare sia Objective-C-regime di controllo stile quale ogni istanza di un `NSObject` può essere assegnato al delegato e per esporre anche una versione fortemente tipizzata. Per questo motivo, è in genere forniscono entrambi una `Delegate` proprietà fortemente tipizzato e una `WeakDelegate` debolmente tipizzata. In genere un'associazione di versione non fortemente tipizzate con [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute), e si usa il [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) attributo per fornire la versione fortemente tipizzata.

Viene illustrato il modo in cui sono associati i `UIAccelerometer` classe:

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

**Novità in MonoTouch 7.0**

A partire da 7.0 MonoTouch un protocollo di nuovo e migliorato funzionalità di associazione è stata incorporata.  Questo nuovo supporto rende più semplice usare idiomi di Objective-C per l'adozione di uno o più protocolli in una determinata classe.

Per ogni definizione del protocollo `MyProtocol` in Objective-C, è ora disponibile un `IMyProtocol` interfaccia in cui sono elencati tutti i metodi richiesti dal protocollo, nonché una classe di estensione che fornisce tutti i metodi facoltativi.  Quanto sopra, combinato con il nuovo supporto in Xamarin Studio l'editor consente agli sviluppatori di implementare i metodi di protocollo senza la necessità di utilizzare sottoclassi separate di classi del modello astratto precedente.

Qualsiasi definizione che contiene il [ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) attributo effettivamente genera tre classi di supporto che consentono di migliorare notevolmente la modalità che tu utilizzi protocolli:

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

Il **implementazione della classe** fornisce una classe astratta completata che è possibile eseguire l'override di singoli metodi di e ottenere l'indipendenza completa.  Ma a causa dell'errore C# non supporta più ereditarietà, esistono scenari in cui potrebbe deve avere una classe di base diversi, ma si vuole comunque implementare un'interfaccia, ovvero la posizione di

Generato **definizione dell'interfaccia** è disponibile in.  È un'interfaccia che dispone di tutti i metodi richiesti dal protocollo.  In questo modo gli sviluppatori che vogliono implementare il protocollo per implementare semplicemente l'interfaccia.  Il runtime registrerà automaticamente il tipo come adottando il protocollo.

Notare che sono elencati i metodi necessari solo l'interfaccia e di esporre i metodi facoltativi.  Ciò significa che le classi che adottano il protocollo otterranno pieno controllo dei tipi per i metodi richiesti, ma saranno necessario ricorrere alla tipizzazione debole (manualmente usando [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
gli attributi e la firma di corrispondenza) per i metodi di protocollo facoltativo.

Per rendere più pratico utilizzare un'API che usa i protocolli, lo strumento di associazione inoltre genererà una classe di metodo di estensioni che espone tutti i metodi facoltativi.  Ciò significa che fino a quando si utilizza un'API, sarà in grado di trattare i protocolli come se avessero tutti i metodi.

Se si desidera usare le definizioni di protocollo nell'API, è necessario scrivere scheletro interfacce vuote nella definizione di API.  Se si desidera utilizzare il Protocollo1 in un'API, è necessario eseguire questa operazione:

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

Il codice precedente è necessario perché in fase di associazione di `IMyProtocol` non esiste, vale a dire il motivo per cui è necessario fornire un'interfaccia vuota.

#### <a name="adopting-protocol-generated-interfaces"></a>Adozione di interfacce generate protocollo

Ogni volta che si implementa una delle interfacce generate per i protocolli, simile al seguente:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

L'implementazione per i metodi di interfaccia automaticamente esportato con il nome appropriato, pertanto è equivalente a questa:

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

### <a name="binding-class-extensions"></a>Estensioni di classi di associazione

In Objective-C è possibile estendere le classi con nuovi metodi, simili come spirito al C#di metodi di estensione. Quando uno di questi metodi è presente, è possibile usare il [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
attributo per contrassegnare il metodo come ricevitore del messaggio di Objective-C.

Ad esempio, in xamarin. IOS sono stati associati i metodi di estensione definiti nella `NSString` quando si `UIKit` viene importato come metodi nel `NSStringDrawingExtensions`, simile al seguente:

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Associazione di elenchi di argomenti di Objective-C

Objective-C supporta argomenti variadic. Ad esempio:

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

A questo metodo viene chiamato da C# è consigliabile creare una firma simile al seguente:

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Questo codice dichiara il metodo come interni, nascondendo l'API precedente dagli utenti, ma esponendolo alla libreria. È quindi possibile scrivere un metodo simile al seguente:

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

### <a name="binding-fields"></a>Campi di associazione

Talvolta potrebbe essere accedere ai campi pubblici che sono stati dichiarati in una libreria.

In genere questi campi contengono valori di stringhe o numeri interi che è necessario fare riferimento. Vengono comunemente utilizzati come stringa che rappresenta una notifica specifica e come le chiavi nei dizionari.

Per associare un campo, aggiungere una proprietà al file di definizione di interfaccia e decorare la proprietà con il [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) attributo. Questo attributo accetta un parametro: il nome di C del simbolo da ricercare. Ad esempio:

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Se si desidera eseguire il wrapping di diversi campi in una classe statica che non deriva da `NSObject`, è possibile usare il [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
attributo della classe, simile al seguente:

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

Il codice precedente genererà un `LonelyClass` che non deriva da `NSObject` e conterrà un'associazione per il `NSSomeEventNotification` 
 `NSString` esposta come un `NSString`.

Il [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) attributo può essere applicato ai tipi di dati seguenti:

-  `NSString` riferimenti (solo proprietà in sola lettura)
-  `NSArray` riferimenti (solo proprietà in sola lettura)
-  valori integer a 32 bit (`System.Int32`)
-  valori integer a 64 bit (`System.Int64`)
-  valori a virgola mobile a 32 bit (`System.Single`)
-  valori a virgola mobile a 64 bit (`System.Double`)
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

Se si crea un collegamento in modo statico, non è Nessuna raccolta da associare, pertanto è necessario usare il `__Internal` nome:

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Enumerazioni di associazione

È possibile aggiungere `enum` direttamente nell'associazione di file da rende più semplice di utilizzarli all'interno di definizioni API - senza usare un file di origine diversa (che deve essere compilato in entrambe le associazioni e il progetto finale).

Esempio:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

È anche possibile creare il proprio enumerazioni sostituire `NSString` costanti. In questo caso sarà il generatore **automaticamente** creare i metodi per convertire i valori delle enumerazioni e NSString costanti per l'utente.

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

Nell'esempio precedente è possibile decidere di decorare `void Perform (NSString mode);` con un [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attributo. Questo verrà **nascondere** l'API basata su costante dai consumer di associazione.

Tuttavia ciò limiterebbe sottoclassi di tipo come l'API coloro alternativo Usa un' [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) attributo. Tali metodi generati non sono `virtual`, vale a dire è non sarà in grado di eseguire l'override di essi, che è possibile, o non, essere una buona scelta.

In alternativa è possibile contrassegnare l'originale `NSString`-base, definizione come `[Protected]`. Questa operazione consentirà la creazione di sottoclassi al lavoro, quando necessario e la versione wrap'ed verrà comunque funzionare e chiamare il metodo sottoposto a override.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Binding `NSValue`, `NSNumber`, e `NSString` a un tipo migliore

Il [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) attributo consente di associare `NSNumber`, `NSValue` e `NSString`(enum) in più accurate C# tipi. L'attributo può essere utilizzato per creare migliori, più accurato, l'API .NET rispetto all'API nativa.

È possibile decorare i metodi (nel valore restituito), parametri e le proprietà con [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute). L'unica restrizione è che il membro **non deve** trovarsi all'interno di un [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 
oppure [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) interfaccia.

Ad esempio:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Output sarà:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Internamente questa operazione verrà eseguita la `bool?`  <->  `NSNumber` e `CGRect`  <->  `NSValue` conversioni.

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) supporta anche le matrici di `NSNumber` `NSValue` e `NSString`(enum).

Ad esempio:

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

Output sarà:

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` è un `NSString` sottoposti a enum, si recupererà il diritto `NSString` valore e gestire la conversione del tipo.

Vedere le [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) documentazione per visualizzare i tipi di conversione supportate.

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Notifiche di associazione

Le notifiche sono messaggi che vengono registrati le `NSNotificationCenter.DefaultCenter` e vengono usati come un meccanismo per trasmettere i messaggi da una parte dell'applicazione a un altro. Gli sviluppatori di sottoscrivono le notifiche in genere usando il [NSNotificationCenter](xref:Foundation.NSNotificationCenter)del [AddObserver](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification})) (metodo). Quando un'applicazione invia un messaggio per il centro notifiche, in genere contiene un payload archiviato nel [NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo) dizionario. Questo dizionario è scarsamente tipizzato e recupero di informazioni esplicitamente è soggetto a errori, come gli utenti devono in genere letti nella documentazione relativa a quali chiavi sono disponibili nel dizionario e i tipi dei valori che possono essere archiviati nel dizionario. La presenza di chiavi a volte viene usata come anche un valore booleano.

Il generatore di binding xamarin. IOS offre supporto per gli sviluppatori associare le notifiche. A tale scopo, si imposta il [`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)
attributo su una proprietà che è stato contrassegnato con un [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)
proprietà (può essere pubblico o privato).

Questo attributo può essere usato senza argomenti per le notifiche che non trasportano alcun payload, oppure è possibile specificare un `System.Type` che fa riferimento a un'altra interfaccia nella definizione dell'API, in genere con il nome che termina con "EventArgs". Il generatore di ricerca si trasforma l'interfaccia in una classe che rappresenti le sottoclassi `EventArgs` e includerà tutte le proprietà desiderate disponibili. Il [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributo deve essere utilizzato nella classe EventArgs per elencare il nome della chiave utilizzata per cercare il dizionario di Objective-C per recuperare il valore.

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

Gli utenti del codice quindi facilmente richiedere notifiche registrate per il [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) usando codice simile al seguente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

Il valore restituito da `ObserveDidStart` consente facilmente smettere di ricevere notifiche, simile al seguente:

```csharp
token.Dispose ();
```

Oppure è possibile chiamare [NSNotification.DefaultCenter.RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) e passare il token. Se la notifica contiene parametri, è necessario specificare un helper `EventArgs` interfaccia, simile alla seguente:

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

Il codice precedente genera un `MyScreenChangedEventArgs` classe con il `ScreenX` e `ScreenY` le proprietà che recupero i dati dal [NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo) dizionario usando le chiavi "ScreenXKey" e "ScreenYKey" rispettivamente e applicare le conversioni appropriate. Il `[ProbePresence]` viene usato per il generatore per verificare se la chiave viene impostata nel `UserInfo`, anziché tentare di estrarre il valore. Viene utilizzato per i casi in cui la presenza della chiave è il valore (in genere per i valori booleani).

In questo modo è possibile scrivere codice simile al seguente:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>Categorie di associazione

Le categorie sono un meccanismo di Objective-C utilizzato per estendere il set di metodi e le proprietà disponibili in una classe.   In pratica, vengono usati per estendere le funzionalità di una classe di base (ad esempio `NSObject`) quando è collegato un framework specifico nel (ad esempio `UIKit`), rendendo i relativi metodi disponibili, ma solo se è collegato al nuovo framework.   In alcuni casi, vengono usati per organizzare le funzionalità in una classe dalla funzionalità.   Sono simili come spirito al C# metodi di estensione. Si tratta di una categoria aspetto in Objective-c:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

Nell'esempio precedente se trovato in una libreria si estende anche istanze di `UIView` con il metodo `makeBackgroundRed`.

Per associare questi, è possibile usare la [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) attributo in una definizione di interfaccia.  Quando si utilizza il [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
il significato dell'attributo di [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
attributo cambia vengano utilizzati per specificare la classe di base per estendere, sia il tipo da estendere.

Il seguente viene illustrato come la `UIView` estensioni sono associate e trasformate in C# i metodi di estensione:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Il codice precedente creerà un `MyUIViewExtension` una classe che contiene il `MakeBackgroundRed` metodo di estensione.  Ciò significa che è ora possibile chiamare "MakeBackgroundRed" in qualsiasi `UIView` sottoclasse, fornendo le stesse funzionalità si otterrebbe in Objective-C. In altri casi, le categorie vengono usate non estendere una classe di sistema, ma per organizzare le funzioni, esclusivamente per scopi di effetto.  analogamente a quanto segue:

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Sebbene sia possibile usare il [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
attributi anche per questo stile di decorazione delle dichiarazioni, si potrebbe anche sufficiente aggiungerli tutti alla definizione della classe.  Entrambi questi potrebbe ottenere lo stesso risultato:

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

È sufficiente breve in questi casi le categorie di tipo merge:

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

I blocchi sono un nuovo costrutto di introdotti da Apple per portare l'equivalente funzionale di C# metodi anonimi per Objective-C. Ad esempio, il `NSSet` classe espone ora questo metodo:

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

La descrizione precedente dichiara un metodo denominato `enumerateObjectsUsingBlock:` che accetta un argomento denominato `block`. Questo blocco è simile a un C# metodo anonimo che include il supporto per l'acquisizione dell'ambiente corrente (il puntatore "this", l'accesso ai parametri e variabili locali). Il metodo sopra indicato nel `NSSet` richiama il blocco con due parametri di un `NSObject` (le `id obj` parte) e un puntatore a un valore booleano (il `BOOL *stop`) parte.

Per associare questo tipo di API con btouch, è necessario dichiarare in primo luogo la firma del tipo di blocco come una C# delegare e quindi farvi riferimento da un punto di ingresso di API, simile al seguente:

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

E ora il codice può chiamare la funzione da C#:

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

Il generatore di binding può trasformare una determinata classe di metodi in metodi async adatto (i metodi che restituiscono un'attività o un Task&lt;T&gt;).

È possibile usare il [`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 
attributo sui metodi che restituiscono void e il cui ultimo argomento è un callback.  Quando si applica questo a un metodo, il generatore di binding genera una versione di tale metodo con il suffisso `Async`.  Se il callback non accetta parametri, il valore restituito sarà una `Task`, se il callback accetta un parametro, il risultato sarà un `Task<T>`.  Se il callback accetta più parametri, è necessario impostare il `ResultType` o `ResultTypeName` per specificare il nome del tipo generato che conterrà tutte le proprietà desiderato.

Esempio:

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

Il codice precedente genera sia il metodo LoadFile, nonché:

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Esponendo tipi sicuri per i parametri NSDictionary deboli

In molte posizioni nell'API di Objective-C, i parametri vengono passati come con tipizzazione debole `NSDictionary` API con chiavi specifiche e i valori, ma questi sono soggetto a (è possibile passare le chiavi non è valide e di non ottenere alcun avviso; è possibile passare i valori non validi e non ottenere avvisi) errori e frustranti Per utilizzare in quanto richiedono più trip la documentazione per la ricerca di possibili nomi delle chiavi e valori.

La soluzione consiste nel fornire una versione fortemente tipizzata che fornisce che la versione fortemente tipizzata dell'API e dietro le quinte viene eseguito il mapping di diversi sottostante delle chiavi e i valori.

In tal caso, ad esempio, se l'API di Objective-C accetta un `NSDictionary` ed è documentata come richiede la chiave `XyzVolumeKey` che accetta un' `NSNumber` con un valore di volume da 0,0 a 1,0 e un `XyzCaptionKey` che accetta una stringa, è necessario che gli utenti abbiano un'interessante API che aspetto simile al seguente:

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

Il `Volume` proprietà viene definita come nullable float, come la convenzione in Objective-C non richiede questi dizionari per avere il valore, quindi esistono scenari in cui il valore non può essere impostato.

A tale scopo, è necessario eseguire alcune operazioni:

* Creare una classe fortemente tipizzata, che rappresenta una sottoclasse [DictionaryContainer](xref:Foundation.DictionaryContainer) e fornisce i vari metodi get e set per ogni proprietà.
* Dichiarare gli overload per i metodi che accettano `NSDictionary` per sfruttare la nuova versione fortemente tipizzata.

È possibile creare la classe fortemente tipizzata manualmente o usare il generatore per svolgere il lavoro per l'utente.  Prima di tutto illustra come eseguire questa operazione manualmente in modo da comprendere cosa sta succedendo e quindi l'approccio automatico.

È necessario creare un file di supporto per questo oggetto, non segue il contratto API.  Questo è ciò che si sarebbe necessario scrivere per creare una classe XyzOptions:

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

È quindi necessario fornire un metodo di wrapper che espone l'API di alto livello, oltre all'API di basso livello.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Se l'API non dovrà essere sovrascritti, è possibile nascondere in modo sicuro l'API basata su NSDictionary tramite il [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
Attributo.

Come può notare, usiamo i [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)
attributo per un nuovo punto di ingresso API della superficie di attacco e si riporta usando la tipizzazione `XyzOptions` classe.  Il metodo wrapper consente inoltre di null da passare.

A questo punto, noterete che abbiamo non menzionare è dove il `XyzOptionsKeys` valori provengono.  Le chiavi che espone un'API in una classe statica, ad esempio sarebbe in genere vengono raggruppati `XyzOptionsKeys`, simile al seguente:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Verrà ora esaminato il supporto automatico per la creazione di questi dizionari fortemente tipizzato.  Ciò consente di evitare una vasta gamma del boilerplate, ed è possibile definire il dizionario direttamente nel contratto API, invece di usare un file esterno.

Per creare un dizionario fortemente tipizzato, introdurre un'interfaccia nell'API e decorarla con il [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) attributo.  In questo modo il generatore che deve creare una classe con lo stesso nome di interfaccia che deriva da `DictionaryContainer` e fornisce funzioni di accesso tipizzate sicuri appositamente.

Il [ `[StrongDictionary]` ](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) attributo accetta un parametro, ovvero il nome della classe statica che contiene le chiavi del dizionario.  Ogni proprietà dell'interfaccia diventeranno quindi una funzione di accesso fortemente tipizzato.  Per impostazione predefinita, il codice utilizzerà il nome della proprietà con il suffisso "Key" nella classe statica per creare la funzione di accesso.

Ciò significa che crea la funzione di accesso fortemente tipizzate non richiede più un file esterno, né dover creare manualmente getter e setter per ogni proprietà, né dover cercare manualmente le chiavi manualmente.

Questo è l'intera associazione aspetto:

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

Nel caso in cui è necessario fare riferimento nel `XyzOption` membri un campo diverso (vale a dire non il nome della proprietà con il suffisso `Key`), è possibile decorare la proprietà con un [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attributo con il nome che si desidera utilizzare.

<a name="Type_mappings" />

## <a name="type-mappings"></a>Mapping dei tipi

Questa sezione descrive come tipi di Objective-C vengono mappati a C# tipi.

<a name="Simple_Types" />

### <a name="simple-types"></a>Tipi semplici

La tabella seguente illustra come è necessario mapping dei tipi dal Objective-C e world cocoatouch usano al mondo in xamarin. ios:

|Nome del tipo di Objective-C|Tipo di API unificata di xamarin. IOS|
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

Il runtime di xamarin. IOS esegue automaticamente la conversione di C# le matrici a `NSArrays` e operazioni di conversione, ad esempio il metodo Objective-C immaginaria che restituisce un `NSArray` dei `UIViews`:

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

L'idea consiste nell'usare l'oggetto fortemente tipizzato C# perché in questo modo l'IDE fornire il completamento del codice corretto con il tipo effettivo senza costringerli a indovinare o cercare la documentazione per scoprire il tipo effettivo degli oggetti contenuti nella matrice di matrice.

Nei casi in cui è possibile non tenere il tipo più derivato effettivo contenuto nella matrice, è possibile usare `NSObject []` come valore restituito.

<a name="Selectors" />

### <a name="selectors"></a>Selettori

I selettori visualizzati sull'API Objective-C come il tipo speciale `SEL`. Quando si associa un selettore, mappare il tipo da `ObjCRuntime.Selector`.  In genere i selettori sono esposti in un'API con un oggetto, l'oggetto di destinazione e un selettore per richiamare l'oggetto di destinazione. Fornire entrambi questi fondamentalmente corrisponde ai C# delegare: un elemento che incapsula sia il metodo da richiamare nonché l'oggetto per richiamare il metodo in.

Si tratta di come appare l'associazione:

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

Per rendere accattivante per l'associazione C# gli sviluppatori, è in genere fornirà un metodo che accetta un `NSAction` parametro, che consente di C# i delegati e le espressioni lambda per essere usato al posto del `Target+Selector`. A tale scopo è in genere necessario nascondere il `SetTarget` metodo contrassegnandolo con un [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
attributo e quindi si esporrà un nuovo metodo helper, simile al seguente:

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

A questo punto del codice utente può essere scritto come segue:

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

Quando si associa un metodo che accetta un `NSString`, è possibile sostituire con un C# tipo, sia sui tipi restituiti e parametri di stringa.

L'unico caso in cui si potrebbe voler usare una `NSString` direttamente è quando la stringa viene utilizzata come token. Per altre informazioni sulle stringhe e `NSString`, vedere l'articolo di [progettazione delle API in NSString](~/ios/internals/api-design/nsstring.md) documento.

In rari casi, un'API potrebbe esporre una stringa di tipo C (`char *`) anziché una stringa di Objective-C (`NSString *`). In questi casi, è possibile annotare il parametro con il [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)
Attributo.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out / parametri ref

Alcune API restituiscono valori sotto i relativi parametri oppure passano i parametri per riferimento.

In genere la firma di aspetto simile al seguente:

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

Il primo esempio illustra un idioma comune di Objective-C per restituire i codici di errore, un puntatore a un `NSError` viene passato puntatore e il valore impostato al momento della restituzione.   Il secondo metodo viene illustrato un metodo di Objective-C potrebbe accettare un oggetto e modificare il relativo contenuto.   Questa operazione è un passaggio per riferimento, anziché un valore di solo output.

L'associazione si presenta come segue:

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Attributi di gestione della memoria

Quando si usa la [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attributo e si passano dati che verranno mantenuti dal metodo chiamato, è possibile specificare la semantica di argomento passandolo come secondo parametro, ad esempio:

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

Il codice precedente sarebbe contrassegnare il valore come se avessero la semantica "Mantieni". La semantica disponibile è:

-  Assegna
-  Copia
-  Mantieni

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Linee guida di stile

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>Utilizzo [interna]

È possibile usare il [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
attributo per nascondere un metodo dell'API pubblica. Si potrebbe voler eseguire questa operazione nei casi in cui l'API esposta è troppo basso livello e si desidera fornire un'implementazione di alto livello in un file separato basato su questo metodo.

È anche possibile usare questo quando si verifichino limitazioni nel generatore di associazione, ad esempio alcuni scenari avanzati potrebbero esporre tipi che non sono associati e si desidera associare nel modo preferito per racchiudere tali tipi nel modo preferito.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Callback e i gestori eventi

Classi di Objective-C in genere trasmettere le notifiche o chiedere informazioni inviando un messaggio in una classe delegata (delegato Objective-C).

Questo modello, mentre completamente supportato e viene rilevata da xamarin. IOS può talvolta risultare complicata. Xamarin. IOS espone il C# modello di eventi e un sistema di metodo callback nella classe che può essere usato in queste situazioni. Ciò consente al codice simile al seguente per eseguire:

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

Il generatore di binding è in grado di ridurre la quantità di necessarie per eseguire il mapping il modello di Objective-C in digitazione di C# modello.

A partire da xamarin. IOS 1.4, sarà possibile anche istruire il generatore per produrre le associazioni per una specifica di Objective-C delegati ed esporre il delegato come C# proprietà del tipo di host e gli eventi.

Esistono due classi coinvolte nel processo, la classe host che verrà è quella attualmente emette gli eventi e invia tali file nei `Delegate` o `WeakDelegate` e la classe delegate effettivo.

Prendere in considerazione la configurazione seguente:

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

-  Nella classe host, aggiungere il [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   dichiarazione il tipo che funge da relativo delegato e il C# nome esposta. Nell'esempio precedente sono `typeof (MyClassDelegate)` e `WeakDelegate` rispettivamente.
-  Nella classe delegato, su ogni metodo che ha più di due parametri, è necessario specificare il tipo che si desidera utilizzare per la classe EventArgs generata automaticamente.

Il generatore di binding non è limitato a disposizione solo una destinazione singolo evento, è possibile che alcune classi di Objective-C per creare i messaggi a più di un delegano, quindi sarà necessario fornire le matrici per supportare questa configurazione. La maggior parte delle installazioni non saranno necessario, ma il generatore è pronto per supportare i casi.

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

Il `EventArgs` viene usato per specificare il nome del `EventArgs` classe da generare. È consigliabile usare uno per ogni firma (in questo esempio, il `EventArgs` conterrà un `With` proprietà del tipo nint).

Con le definizioni precedenti, il generatore produce il seguente evento di MyClass generato:

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

Pertanto, è ora possibile usare il codice simile al seguente:

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

I callback sono simili a chiamate agli eventi, la differenza è che, invece di avere più sottoscrittori potenziali (ad esempio, possono connettere più metodi a un `Clicked` evento o un `DownloadFinished` evento) callback possono avere solo un singolo sottoscrittore.

Il processo è identico, l'unica differenza è che, anziché esporre il nome del `EventArgs` classe che verrà generato, EventArgs viene effettivamente usato per denominare risultante C# nome del delegato.

Se il metodo nella classe delegato restituisce un valore, il generatore di associazione verrà eseguire il mapping in un metodo delegato nella classe padre anziché un evento. In questi casi è necessario fornire il valore predefinito che deve essere restituito dal metodo se l'utente non collegherà al delegato. Eseguire questa operazione usando il [`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)
oppure [ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) attributi.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) verrà impostare come hardcoded un valore restituito, mentre [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
Consente di specificare l'argomento di input verrà restituito.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Le enumerazioni e tipi di base

È anche possibile fare riferimento a enumerazioni o tipi di base che non sono supportati direttamente dal sistema btouch interfaccia definizione. A tale scopo, inserire le enumerazioni e i tipi di base in un file separato e includere come parte di uno dei file aggiuntivi da fornire ai btouch.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>Le dipendenze di collegamento

Se si associa le API che non fanno parte dell'applicazione, è necessario assicurarsi che il file eseguibile è collegato a queste librerie.

È necessario informare come collegare le librerie di xamarin. IOS, questa operazione può essere eseguita tramite modifica la configurazione di compilazione per richiamare il `mtouch` comando con alcuni extra compilare argomenti che specifichino come collegare librerie di nuovo con la "-gcc_flags" opzione, seguita da una stringa tra virgolette contenente tutte le librerie aggiuntive necessarie per il programma, simile al seguente:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

Nell'esempio precedente viene collegata `libMyLibrary.a`, `libSystemLibrary.dylib` e il `CFNetwork` libreria framework nell'eseguibile finale.

Oppure è possibile sfruttare il livello di assembly [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), che è possibile incorporare nei file del contratto (ad esempio `AssemblyInfo.cs`).
Quando si usa la [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), devi avere la libreria nativa disponibile all'indirizzo quando si esegue l'associazione, come verrà incorporata la libreria nativa con l'applicazione. Ad esempio:

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

È lecito chiedersi, perché è necessario `-force_load` comando e il motivo è che ObjC - flag anche se compila il codice in, non consente di mantenere i metadati necessari per supportare le categorie (l'eliminazione del codice non utilizzato del linker/compilatore rimuove si) che può necessario in fase di esecuzione per xamarin. IOS.

<a name="Assisted_References" />

## <a name="assisted-references"></a>Riferimenti assistiti

Alcuni oggetti temporanei, ad esempio fogli di azione e finestre di avviso sono complesse per tenere traccia per gli sviluppatori e il generatore di associazione in grado di offrire un po'.

Ad esempio se è presente una classe che è stato illustrato un messaggio e quindi generato un `Done` evento, il metodo tradizionale di gestione di questa situazione sarebbe:

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

Nello scenario precedente, lo sviluppatore deve mantenere il riferimento all'oggetto stesso e una perdita o cancellare attivamente le informazioni di riferimento per la propria casella.  Mentre il codice di associazione, il generatore supporta tenere traccia del riferimento per l'utente e cancellare, quando viene richiamato un metodo speciale, il codice sopra riportato diventerebbe quindi:

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Si noti come non è più necessario per mantenere la variabile in un'istanza, che funziona con una variabile locale e che non è necessario cancellare il riferimento quando non è più attivo.

Per sfruttare i vantaggi di questo, la classe deve contenere una proprietà di eventi impostata [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) dichiarazione e anche il `KeepUntilRef` variabile impostato sul nome del metodo che viene richiamato quando l'oggetto è stata completata l'elaborazione, ad esempio In questo:

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Eredità di protocolli

A partire da xamarin. IOS v 3.2, Supportiamo che eredita da protocolli che sono stati contrassegnati con il [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) proprietà. Ciò è utile in alcuni modelli di API, ad esempio in `MapKit` in cui la `MKOverlay` protocollo, eredita dal `MKAnnotation` protocol e viene adottato da un numero di classi che eredita da `NSObject`.

In passato è necessaria la copia del protocollo in ogni implementazione, ma in questi casi ora possiamo il `MKShape` classe ereditare il `MKOverlay` protocollo e si dovranno generare automaticamente tutti i metodi necessari.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di associazione](https://developer.xamarin.com/samples/BindingSample/)
- [Corsi di Xamarin University: Compila una libreria di binding Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Corsi di Xamarin University: Creazione di una libreria di binding Objective-C con Sharpie obiettivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
