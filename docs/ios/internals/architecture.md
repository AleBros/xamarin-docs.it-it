---
title: Architettura app iOS
description: Questo documento descrive Novell. iOS a un livello basso, illustrando il modo in cui il codice nativo e gestito interagiscono, la compilazione AOT, i selettori, i registrar, l'avvio dell'applicazione e il generatore.
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 7d59a295961c25ecfcc99bb54fdc188c957cf3ee
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291949"
---
# <a name="ios-app-architecture"></a>Architettura app iOS

Le applicazioni Novell. iOS vengono eseguite nell'ambiente di esecuzione mono e usano la compilazione AOT (Full ahead of Time) C# per compilare il codice nel linguaggio assembly ARM. Questa operazione viene eseguita side-by-side con il [runtime di Objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Entrambi gli ambienti di runtime vengono eseguiti su un kernel di tipo UNIX, in particolare [XNU](https://en.wikipedia.org/wiki/XNU), ed espongono diverse API al codice utente, consentendo agli sviluppatori di accedere al sistema nativo o gestito sottostante.

Il diagramma seguente mostra una panoramica di base di questa architettura:

[![](architecture-images/ios-arch-small.png "Questo diagramma mostra una panoramica di base dell'architettura di compilazione ahead of Time (AOT)")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>Codice nativo e gestito: Una spiegazione

Quando si sviluppa per Novell, vengono spesso usati i termini *nativi e codice gestito* . Il [codice gestito](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/) è codice in cui l'esecuzione viene gestita dal [.NET Framework Common Language Runtime](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx)o nel caso di Novell: il runtime di mono. Questo è il cosiddetto linguaggio intermedio.

Il codice nativo è codice che verrà eseguito in modalità nativa sulla piattaforma specifica, ad esempio Objective-C o anche codice compilato AOT, su un chip ARM. Questa guida illustra il modo in cui AOT compila il codice gestito in codice nativo e illustra il funzionamento di un'applicazione Novell. iOS, sfruttando al meglio le API iOS di Apple tramite l'uso di binding, pur avendo accesso a. BCL di NET e un linguaggio sofisticato, C#ad esempio.

## <a name="aot"></a>AOT

Quando si compila un'applicazione della piattaforma Novell, il C# compilatore mono F#(o) verrà eseguito e compilerà F# il codice e il C# codice in MSIL (Microsoft Intermediate Language). Se si esegue un Novell. Android, un'applicazione Novell. Mac o anche un'applicazione Novell. iOS nel simulatore, [Common Language Runtime (CLR) di .NET](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx) compila il codice MSIL usando un compilatore just-in-time (JIT). In fase di esecuzione viene compilato in un codice nativo, che può essere eseguito nell'architettura corretta per l'applicazione.

Tuttavia, esiste una restrizione di sicurezza in iOS, impostata da Apple, che impedisce l'esecuzione di codice generato dinamicamente in un dispositivo.
Per assicurarsi di rispettare questi protocolli di sicurezza, Novell. iOS usa invece un compilatore AOT (Ahead of Time) per compilare il codice gestito. Questo produce un file binario iOS nativo, ottimizzato facoltativamente con LLVM per i dispositivi, che può essere distribuito nel processore basato su ARM di Apple. Di seguito è illustrato un diagramma di approssimazione del modo in cui questo si integra.

[![](architecture-images/aot.png "Un diagramma di approssimazione del modo in cui si integra")](architecture-images/aot-large.png#lightbox)

L'utilizzo di AOT presenta diverse limitazioni, descritte in dettaglio nella Guida alle [limitazioni](~/ios/internals/limitations.md) . Fornisce inoltre una serie di miglioramenti rispetto a JIT attraverso una riduzione del tempo di avvio e diverse ottimizzazioni delle prestazioni

Ora che è stata esaminata la modalità di compilazione del codice dal codice sorgente al codice nativo, è possibile osservare in che modo Novell. iOS ci consente di scrivere applicazioni iOS completamente native

## <a name="selectors"></a>Selettori

Con Novell sono disponibili due ecosistemi distinti, ovvero .NET e Apple, che è necessario riunire per sembrare più semplici, per garantire che l'obiettivo finale sia un'esperienza utente uniforme. Nella sezione precedente è stato illustrato il modo in cui i due runtime comunicano ed è possibile che sia stato ascoltato il termine "Bindings", che consente di usare le API iOS native in Novell. Le associazioni sono illustrate in modo dettagliato nella documentazione di [binding di Objective-C](~/cross-platform/macios/binding/overview.md) , quindi per ora si analizzerà il funzionamento di iOS dietro le quinte.

In primo luogo, è necessario un modo per esporre Objective-C a C#, operazione eseguita tramite selettori. Un selettore è un messaggio inviato a un oggetto o a una classe. Con Objective-C questa operazione viene eseguita tramite le funzioni [objc_msgSend](~/cross-platform/macios/binding/overview.md) .
Per ulteriori informazioni sull'utilizzo dei selettori, vedere la guida ai [selettori Objective-C](~/ios/internals/objective-c-selectors.md) . Deve inoltre essere disponibile un modo per esporre il codice gestito a Objective-C, che è più complicato a causa del fatto che Objective-C non conosce nulla sul codice gestito. Per aggirare questo problema, vengono usati i *registrar*. Queste informazioni sono descritte più dettagliatamente nella sezione successiva.

## <a name="registrars"></a>Registrar

Come indicato in precedenza, il registrar è codice che espone il codice gestito a Objective-C. Questa operazione viene eseguita creando un elenco di tutte le classi gestite che derivano da NSObject:

- Per tutte le classi che non eseguono il wrapping di una classe Objective-c esistente, viene creata una nuova classe Objective-c con membri Objective-c che eseguono il mirroring di tutti`Export`i membri gestiti che dispongono di un attributo [].

- Nelle implementazioni per ogni membro Objective-C, il codice viene aggiunto automaticamente per chiamare il membro gestito con mirroring.

Lo pseudo-codice seguente mostra un esempio di come eseguire questa operazione:

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

Il codice gestito può contenere gli attributi, `[Register]` e `[Export]`, utilizzati dal registrar per tenere presente che l'oggetto deve essere esposto a Objective-C.
L' `[Register]` attributo viene utilizzato per specificare il nome della classe Objective-C generata nel caso in cui il nome generato predefinito non sia appropriato. Tutte le classi derivate da NSObject vengono registrate automaticamente con Objective-C.
L'attributo `[Export]` required contiene una stringa, ovvero il selettore usato nella classe Objective-C generata.

Esistono due tipi di registrar usati in Novell. iOS, dinamici e statici:


- **Registrar dinamici** : il registrar dinamico esegue la registrazione di tutti i tipi nell'assembly in fase di esecuzione. Questa operazione viene eseguita usando le funzioni fornite dall' [API di runtime di Objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Il registrar dinamico ha pertanto un avvio più lento, ma un tempo di compilazione più rapido. Questo è il valore predefinito per il simulatore iOS. Le funzioni native, in genere in C, denominate trampolini, vengono usate come implementazioni del metodo quando si usano i registrar dinamici. Variano a seconda delle diverse architetture.

- **Registrar statici** : il registrar statico genera codice Objective-C durante la compilazione, che viene quindi compilato in una libreria statica e collegato all'eseguibile. Questo consente un avvio più rapido, ma richiede più tempo durante la fase di compilazione. Viene usato per impostazione predefinita per le compilazioni di dispositivi. Il registrar statico può essere usato anche con il simulatore iOS `--registrar:static` passando `mtouch` come attributo nelle opzioni di compilazione del progetto, come illustrato di seguito:

    [![](architecture-images/image1.png "Impostazione di argomenti mTouch aggiuntivi")](architecture-images/image1.png#lightbox)

Per ulteriori informazioni sulle specifiche del sistema di registrazione dei tipi iOS utilizzato da Novell. iOS, vedere la guida relativa ai [tipi registrar](~/ios/internals/registrar.md) .

## <a name="application-launch"></a>Avvio dell'applicazione

Il punto di ingresso di tutti i file eseguibili Novell. iOS viene fornito da `xamarin_main`una funzione chiamata che Inizializza mono.

A seconda del tipo di progetto, vengono eseguite le operazioni seguenti:

- Per le normali applicazioni iOS e tvOS, viene chiamato il metodo Main gestito, fornito dall'app Novell. Questo metodo Main gestito chiama `UIApplication.Main`quindi, che è il punto di ingresso per Objective-C. UIApplication. Main è l'associazione per il `UIApplicationMain` metodo di Objective-C.
- Per le estensioni, viene chiamata la `NSExtensionMain` funzione nativa`NSExtensionmain` , o (per le estensioni watchos), fornita dalle librerie Apple. Poiché si tratta di progetti di librerie di classi e non eseguibili, non sono disponibili metodi Main gestiti da eseguire.

Tutta questa sequenza di avvio viene compilata in una libreria statica, che viene quindi collegata all'eseguibile finale in modo che l'app sappia come uscire da zero.

A questo punto l'app è stata avviata, mono è in esecuzione, il codice è gestito ed è noto come chiamare il codice nativo e richiamarlo. L'operazione successiva consiste nell'iniziare ad aggiungere controlli e rendere l'app interattiva.


## <a name="generator"></a>Generator

Novell. iOS contiene le definizioni per ogni singola API iOS. È possibile esplorare uno di questi nel [repository GitHub MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Queste definizioni contengono interfacce con attributi, nonché eventuali metodi e proprietà necessari. Il codice seguente, ad esempio, viene usato per definire un UIToolbar nello [spazio dei nomi](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327)UIKit. Si noti che si tratta di un'interfaccia con diversi metodi e proprietà:

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

Il generatore, chiamato [`btouch`](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) in Novell. iOS, accetta questi file di definizione e usa gli strumenti .NET per [compilarli in un assembly temporaneo](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318). Tuttavia, questo assembly temporaneo non è utilizzabile per chiamare il codice Objective-C. Il generatore legge quindi l'assembly temporaneo e genera C# il codice che può essere utilizzato in fase di esecuzione.
Questo è il motivo per cui, ad esempio, se si aggiunge un attributo casuale al file Definition. cs, questo non verrà visualizzato nel codice output. Il generatore non ne è a conoscenza e `btouch` pertanto non è in grado di cercarlo nell'assembly temporaneo per l'output.

Dopo la creazione di Novell. iOS. dll, mTouch raggruppa tutti i componenti.

A un livello elevato, ottiene questo risultato eseguendo le attività seguenti:

- Creare una struttura del bundle dell'app.
- Copiare gli assembly gestiti.
- Se è abilitato il collegamento, eseguire il linker gestito per ottimizzare gli assembly mediante l'estrazione di parti inutilizzate.
- Compilazione AOT.
- Creare un eseguibile nativo, che restituisce una serie di librerie statiche, una per ogni assembly, collegate all'eseguibile nativo, in modo che il file eseguibile nativo sia costituito dal codice dell'utilità di avvio, dal codice del registrar (se statico) e da tutti gli output dell'AOT compilatore


Per informazioni più dettagliate sul linker e su come viene usato, vedere la guida del [linker](~/ios/deploy-test/linker.md) .

## <a name="summary"></a>Riepilogo

Questa guida ha esaminato la compilazione AOT delle app Novell. iOS ed Esplora Novell. iOS e la relativa relazione con Objective-C in modo approfondito.

## <a name="related-links"></a>Collegamenti correlati

- [Limitazioni](~/ios/internals/limitations.md)
- [Binding di Objective-C](~/cross-platform/macios/binding/overview.md)
- [Selettori Objective-C](~/ios/internals/objective-c-selectors.md)
- [Registrar di tipo](~/ios/internals/registrar.md)
- [Linker](~/ios/deploy-test/linker.md)
