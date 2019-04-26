---
title: Architettura di App iOS
description: Questo documento descrive xamarin. IOS in un codice nativo e gestito basso livello, che pubblica interagire, la compilazione AOT, i selettori, Registrar, avvio dell'applicazione e il generatore.
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: d08f7e3a35cd82f4262ef1f145d4b4648f7baef8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036130"
---
# <a name="ios-app-architecture"></a>Architettura di App iOS

Le applicazioni xamarin. IOS eseguire all'interno dell'ambiente di esecuzione Mono e utilizzare la compilazione Ahead of Time (AOT) completo per compilare C# esecuzione di codice in linguaggio assembly ARM. Ciò comporta l'esecuzione side-by-side con la [Runtime Objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Entrambi gli ambienti di runtime eseguiti su un kernel simili a UNIX, in particolare [XNU](https://en.wikipedia.org/wiki/XNU)ed esporre diverse API per il codice utente che consente agli sviluppatori di accedere al sistema nativo o gestito sottostante.

Il diagramma seguente mostra una panoramica di base di questa architettura:

[ ![](architecture-images/ios-arch-small.png "Questo diagramma mostra una panoramica di base dell'architettura la compilazione Ahead of Time (AOT)")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>Nativo e codice gestito: Una spiegazione

Durante lo sviluppo per Xamarin i termini *nativi e gestiti* codice vengono spesso usati. [Codice gestito](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/) è il codice che include l'esecuzione gestita dal [Common Language Runtime di .NET Framework](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx), o, in caso di Xamarin: il Runtime di Mono. Questo è ciò che chiamiamo un linguaggio intermedio.

Codice nativo è codice che verrà eseguito in modo nativo nella piattaforma specifica (ad esempio, Objective-C o anche il codice di compilazione AOT, in un chip ARM). Questa Guida Esplora come AOT compila il codice gestito a codice nativo e spiega come funziona un'applicazione xamarin. IOS, basate sull'uso delle API di iOS di Apple tramite l'uso di associazioni, ma anche di accedere a. Libreria di classi base della rete e un linguaggio sofisticato, ad esempio C#.

## <a name="aot"></a>AOT

Quando si esegue la compilazione di qualsiasi applicazione della piattaforma Xamarin, di Mono C# (o F#) del compilatore verrà eseguito e verrà compilato il C# e F# codice in Microsoft Intermediate Language (MSIL). Se si esegue un xamarin. Android, un'applicazione xamarin. Mac o persino un'applicazione xamarin. IOS nel simulatore, il [.NET Common Language Runtime (CLR)](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx) compila il codice MSIL con un solo nel compilatore Time (JIT). In fase di esecuzione in questo viene compilato in codice nativo, che può eseguire sull'architettura corretta per l'applicazione.

Tuttavia, vi è una restrizione di sicurezza in iOS, configurate da Apple, che non consente l'esecuzione del codice generato dinamicamente in un dispositivo.
Per garantire che sono conformi a questi protocolli di sicurezza, xamarin. IOS Usa invece un compilatore Ahead of Time (AOT) per compilare il codice gestito. Questa operazione produce una binaria, native per iOS, facoltativamente, ottimizzata con LLVM per i dispositivi, che possono essere distribuiti nel processore basato su ARM di Apple. Di seguito è illustrato un diagramma di base di questo adattamento insieme:

[![](architecture-images/aot.png "Un diagramma approssimativo del modo in cui questo di integrazione")](architecture-images/aot-large.png#lightbox)

Uso di AOT presenta alcune limitazioni, illustrate in dettaglio nella [limitazioni](~/ios/internals/limitations.md) Guida. Fornisce inoltre una serie di miglioramenti tramite JIT attraverso una riduzione del tempo di avvio e varie ottimizzazioni delle prestazioni

Ora che è stato esaminato come il codice viene compilato dall'origine al codice nativo, diamo uno sguardo dietro le quinte per vedere come xamarin. IOS consente di scrivere applicazioni completamente native per iOS

## <a name="selectors"></a>Selettori

Con Xamarin, abbiamo due ecosistemi separati, .NET e Apple, che è necessario visualizzare insieme a sembrare come semplice possibile garantire che l'obiettivo finale è un'esperienza utente uniforme. Abbiamo visto nella sezione precedente modalità di comunicazione tra i due runtime, e si è già molto bene del termine 'binding' che consente l'API da usare in Xamarin native per iOS. Le associazioni sono illustrate in dettaglio nel nostro [binding Objective-C](~/cross-platform/macios/binding/overview.md) documentazione, pertanto, per Ora esaminiamo funzionamento iOS dietro le quinte.

In primo luogo, deve esistere un modo per esporre Objective-C in C#, che viene eseguita tramite i selettori. Un selettore è un messaggio che viene inviato a un oggetto o classe. Con Objective-C questa operazione viene eseguita tramite il [objc_msgSend](~/cross-platform/macios/binding/overview.md) funzioni.
Per altre informazioni sull'uso dei selettori, consultare il [selettori Objective-C](~/ios/internals/objective-c-selectors.md) Guida. È anche con un metodo per esporre il codice gestito a Objective-C, che è più complicato dovuto al fatto che Objective-C non conosce il codice gestito. Per evitare questo problema, usiamo *Registrar*. Questi sono spiegati più dettagliatamente nella sezione successiva.

## <a name="registrars"></a>Registrar

Come indicato in precedenza, il programma di registrazione è codice che espone il codice gestito per Objective-C. A tale scopo, si crea un elenco di ogni classe gestita che deriva da NSObject:

- Per tutte le classi che non esegue il wrapping di una classe di Objective-C esistente, creata una nuova classe di Objective-C con i membri di Objective-C mirroring tutti i membri gestiti con un [`Export`] attributo.

- Nelle implementazioni per ogni membro di Objective-C, viene aggiunto automaticamente codice per chiamare il membro gestito con mirroring.

Il pseudo-codice seguente viene illustrato un esempio di questa procedura:

**C#(Codice gestito)**

```csharp
 class MyViewController : UIViewController{
     [Export ("myFunc")]
     public void MyFunc ()
     {
     }
 }
```

**Objective-C:**

```objectivec
@interface MyViewController : UIViewController { }

    -(void)myFunc;
@end

@implementation MyViewController {}

    -(void) myFunc
    {
        /* code to call the managed MyViewController.MyFunc method */
    }
@end

```

Il codice gestito può contenere gli attributi `[Register]` e `[Export]`, che usa le registrar per sapere che l'oggetto deve essere esposto per Objective-C.
Il `[Register]` attributo viene usato per specificare il nome della classe Objective-C generata nel caso in cui il nome generato predefinito non è adatto. Tutte le classi derivate da NSObject vengono registrate automaticamente in Objective-C.
Obbligatorio `[Export]` attributo contiene una stringa che rappresenta il selettore usato nella classe generata Objective-C.

Esistono due tipi di registri usati in xamarin. ios: statiche e dinamiche:


- **Registrar dinamica** – programma di registrazione dinamica viene la registrazione di tutti i tipi nell'assembly in fase di esecuzione. A tale scopo, utilizzo delle funzioni fornite da [runtime dell'Objective-C API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Il programma di registrazione dinamica ha pertanto un avvio più lento, ma un veloce fase di compilazione. Questa è predefinita per il simulatore iOS. Le funzioni native (in genere in C), chiamate trampolines, vengono utilizzate come implementazioni del metodo quando si usano le Registrar dinamico. Variano tra architetture diverse.

- **Registrar statico** – programma di registrazione statico genera il codice Objective-C durante la compilazione, che viene quindi compilata in una libreria statica e collegata nel file eseguibile. Questo consente un avvio più veloce, ma richiede più tempo durante la fase di compilazione. Per impostazione predefinita viene utilizzato per le compilazioni di dispositivo. Il programma di registrazione statico è anche utilizzabile con il simulatore iOS passando `--registrar:static` come un `mtouch` attributo nelle opzioni di compilazione del progetto, come illustrato di seguito:

    [![](architecture-images/image1.png "Impostazione degli argomenti aggiuntivi di mtouch")](architecture-images/image1.png#lightbox)

Per altre informazioni sulle specifiche di iOS sistema di registrazione del tipo usato da xamarin. IOS, vedere la [Registrar tipo](~/ios/internals/registrar.md) Guida.

## <a name="application-launch"></a>Avvio dell'applicazione

Il punto di ingresso di tutti gli eseguibili di xamarin. IOS viene fornito da una funzione denominata `xamarin_main`, che consente di inizializzare mono.

A seconda del tipo di progetto, viene eseguita la seguente:

- Per regolare iOS e tvOS applicazioni, viene chiamato il metodo Main gestito, fornito dall'app per Xamarin. Questa operazione gestita metodo Main chiama quindi `UIApplication.Main`, ovvero il punto di ingresso per Objective-C. UIApplication.Main riguarda il binding di Objective-C `UIApplicationMain` (metodo).
- Le estensioni, la funzione nativa – `NSExtensionMain` o (`NSExtensionmain` per le estensioni WatchOS), fornito da Apple librerie viene chiamato. Poiché questi progetti sono librerie di classi e i progetti non eseguibili, non sono metodi Main gestiti per l'esecuzione.

Tutta questa sequenza di avvio viene compilato in una libreria statica, che viene quindi collegata nell'eseguibile finale in modo che l'app sa come cominciare.

A questo punto è stata avviata l'App nell'app, Mono è in esecuzione, siamo in codice gestito e abbiamo imparato a chiamare codice nativo e richiamato. La prossima cosa che dobbiamo fare è avviare l'aggiunta di controlli e rendere l'app interattive effettivamente.


## <a name="generator"></a>Generator

Xamarin. IOS contiene definizioni per ogni singola API di iOS. È possibile esplorare tramite uno di questi sul [repository github MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Queste definizioni contengono si interfaccia con attributi, nonché qualsiasi proprietà e metodi necessari. Ad esempio, il codice seguente consente di definire un UIToolbar nel UIKit [dello spazio dei nomi](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327). Si noti che è un'interfaccia con un numero di metodi e proprietà:

```csharp
[BaseType (typeof (UIView))]
public interface UIToolbar : UIBarPositioning {
    [Export ("initWithFrame:")]
    IntPtr Constructor (CGRect frame);

    [Export ("barStyle")]
    UIBarStyle BarStyle { get; set; }

    [Export ("items", ArgumentSemantic.Copy)][NullAllowed]
    UIBarButtonItem [] Items { get; set; }

    [Export ("translucent", ArgumentSemantic.Assign)]
    bool Translucent { [Bind ("isTranslucent")] get; set; }

    // done manually so we can keep this "in sync" with 'Items' property
    //[Export ("setItems:animated:")][PostGet ("Items")]
    //void SetItems (UIBarButtonItem [] items, bool animated);

    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage ([NullAllowed] UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);

    ...
}
```

Il generatore, chiamato [ `btouch` ](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) in xamarin. IOS, accetta questi file di definizione e Usa gli strumenti di .NET per [compilarli in un assembly temporaneo](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318). Tuttavia, questo assembly temporaneo non è facile da usare per chiamare codice Objective-C. Il generatore, quindi legge l'assembly temporaneo e viene generato l'errore C# codice che può essere usato in fase di esecuzione.
Questo è il motivo per cui, ad esempio, se si aggiunge un attributo casuale per il file con estensione cs della definizione, non verrà visualizzati nel codice di output. Il generatore non sa su di esso e pertanto `btouch` all'oscuro da cercare nell'assembly temporaneo può eseguirne l'output.

Dopo aver creato la DLL, mtouch verrà riunire tutti i componenti di elementi.

A livello generale, raggiunge questo eseguendo le attività seguenti:

-   Creare una struttura di bundle dell'app.
-   Copiare negli assembly gestiti.
-   Se il collegamento è abilitato, eseguire il linker gestito per ottimizzare gli assembly mediante copia da parti non utilizzate out.
-   Compilazione AOT.
-   Creare un file eseguibile nativo, che invia l'output di una serie di librerie statiche (uno per ogni assembly) collegate nel file eseguibile nativo, in modo che il file eseguibile nativo è costituito da tutti gli output da AOT il codice dell'utilità di avvio e il codice di registrar (Se static) compilatore


Per altre informazioni sul linker e su come utilizzarlo, vedere la [Linker](~/ios/deploy-test/linker.md) Guida.

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato la compilazione AOT di App xamarin. IOS, xamarin. IOS esplorati e relativa relazione con Objective-C in modo approfondito.

## <a name="related-links"></a>Collegamenti correlati

- [Limitazioni](~/ios/internals/limitations.md)
- [Binding di Objective-C](~/cross-platform/macios/binding/overview.md)
- [Selettori Objective-C](~/ios/internals/objective-c-selectors.md)
- [Tipo Registrar](~/ios/internals/registrar.md)
- [Linker](~/ios/deploy-test/linker.md)
