---
title: Architettura di iOS
description: Esplorazione di un livello basso di xamarin. IOS
ms.topic: article
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: c54ea9a2f63853066413f2db9713882441399744
ms.sourcegitcommit: cc38757f56aab53bce200e40f873eb8d0e5393c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2018
---
# <a name="ios-architecture"></a>Architettura di iOS

Xamarin. IOS applicazioni eseguite all'interno dell'ambiente di esecuzione Mono e utilizzano compilazione completa di anticipo del tempo AOT () per compilare il codice c# in linguaggio assembly ARM. Ciò comporta l'esecuzione side-by-side con la [Objective-C Runtime](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Entrambi gli ambienti di runtime sono eseguiti su un kernel simili a UNIX, in particolare [XNU](https://en.wikipedia.org/wiki/XNU)ed esporre diverse API per il codice utente consentendo agli sviluppatori di accedere al sistema nativo o gestito sottostante.

Il diagramma seguente mostra una panoramica dell'architettura di base:

[ ![](architecture-images/ios-arch-small.png "Questo diagramma mostra una panoramica di base dell'architettura di compilazione di anticipo del tempo AOT)")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>Nativo e codice gestito: una spiegazione

Durante lo sviluppo di Xamarin le condizioni di *nativo e gestito* codice vengono spesso utilizzati. [Codice gestito](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/) è codice che l'esecuzione gestita dal [Common Language Runtime di .NET Framework](https://msdn.microsoft.com/en-us/library/8bs2ecf4(v=vs.110).aspx), o in caso di Xamarin: il Runtime Mono. Questo è ciò che viene definito un linguaggio intermedio.

Codice nativo è codice che verrà eseguito in modo nativo nella piattaforma specifica (ad esempio, Objective-C o persino codice AOT compilati, in un chip ARM). Questa guida viene descritto come AOT compila il codice gestito al codice nativo e illustra la modalità di funzionamento di xamarin. IOS dell'applicazione, rendendo uso completo dell'API di iOS di Apple tramite l'utilizzo di associazioni, ma anche l'accesso a. Libreria di classi base della rete e un linguaggio di sofisticate, ad esempio c#.


## <a name="aot"></a>AOT

Quando si compila tutte le applicazioni di piattaforma Xamarin, il compilatore c# Mono (o F #) verrà eseguito e verrà compilato il codice c# e F # in Microsoft Intermediate Language (MSIL). Se si esegue un xamarin, un'applicazione Xamarin.Mac o un'applicazione di xamarin. IOS in un simulatore di [.NET Common Language Runtime (CLR)](https://msdn.microsoft.com/en-us/library/8bs2ecf4(v=vs.110).aspx) compila il codice MSIL con un solo nel compilatore JIT di tempo. In fase di esecuzione in questo viene compilato in codice nativo, che possono eseguire nella corretta architettura per l'applicazione.

Tuttavia, è una restrizione di sicurezza in iOS, l'impostazione di Apple, che non consente l'esecuzione del codice generato in modo dinamico in un dispositivo.
Per assicurarsi che ci sono conformi a questi protocolli di sicurezza, xamarin. IOS utilizza invece un compilatore di anticipo del tempo AOT () per compilare il codice gestito. Ciò produce un iOS native binario, facoltativamente ottimizzata con LLVM per i dispositivi, che possono essere distribuiti nel processore basato su ARM di Apple. Un diagramma di base del funzionamento questo complessivo è illustrato di seguito:

[![](architecture-images/aot.png "Un diagramma di base di questo adattamento insieme")](architecture-images/aot-large.png#lightbox)

Utilizzo AOT ha un numero di limiti che vengono descritti in dettaglio il [limitazioni](~/ios/internals/limitations.md) Guida. Fornisce inoltre una serie di miglioramenti tramite JIT tramite una riduzione del tempo di avvio e varie ottimizzazioni delle prestazioni

Ora che è stato esaminato come il codice viene compilato dall'origine al codice nativo, diamo un'occhiata dietro le quinte per vedere come xamarin consente di scrivere applicazioni completamente native per iOS

## <a name="selectors"></a>Selettori

Con Xamarin, sono disponibili due ecosistemi separati, .NET e Apple, che è necessario introdurre insieme a sembrare come semplice possibile, in modo da garantire che l'obiettivo finale è un'esperienza utente uniforme. Abbiamo visto nella sezione precedente la comunicazione tra i due runtime, e può anche molto sentito il termine 'binding', che consente l'API da utilizzare in Xamarin native per iOS. Le associazioni sono illustrate in dettaglio in nostro [associazione Objective-C](~/cross-platform/macios/binding/overview.md) documentazione, pertanto, per Ora esaminiamo funzionamento iOS dietro le quinte.

In primo luogo, deve esistere un modo per esporre Objective-C per c#, che viene eseguita tramite i selettori. Un selettore è un messaggio che viene inviato a un oggetto o una classe. Con Objective-C questa operazione viene eseguita tramite il [objc_msgSend](~/cross-platform/macios/binding/overview.md) funzioni.
Per ulteriori informazioni sull'uso dei selettori, consultare il [selettori Objective-C](~/ios/internals/objective-c-selectors.md) Guida. Sono inoltre deve essere un modo per esporre il codice gestito e Objective-C, che è più complicato che Objective-C non conosce assolutamente il codice gestito. Per evitare questo problema, utilizziamo *Registrar*. Questi sono spiegati in dettaglio nella sezione successiva.

## <a name="registrars"></a>Registrar

Come indicato in precedenza, il programma di registrazione è codice che espone il codice gestito per Objective-C. A tale scopo, la creazione di un elenco di tutte le classi gestite che deriva da NSObject:

- Per tutte le classi che non esegue il wrapping di una classe Objective-C esistente, crea una nuova classe Objective-C con membri Objective-C mirroring tutti i membri gestiti che hanno un [`Export`] attributo.

- Nelle implementazioni per ogni membro Objective-C, viene aggiunto automaticamente codice per chiamare il membro gestito con mirroring.

Lo pseudocodice seguente viene illustrato un esempio di questa procedura:

**C# (codice gestito)**

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

Il codice gestito può contenere gli attributi, `[Register]` e `[Export]`, che utilizza il programma di registrazione di sapere che l'oggetto deve essere esposto a Objective-C.
Il `[Register]` attributo viene utilizzato per specificare il nome della classe Objective-C generata nel caso in cui il nome predefinito generato non è adatto. Tutte le classi derivate da NSObject vengono registrate automaticamente in Objective-C.
Obbligatorio `[Export]` attributo contiene una stringa, che è il selettore utilizzato nella classe generata Objective-C.

Esistono due tipi di registri usati in xamarin. ios: dinamico e statico:


- **Registrar dinamica** : il programma di registrazione dinamica la registrazione di tutti i tipi nell'assembly in fase di esecuzione. A tale scopo, utilizzando le funzioni fornite da [runtime dell'Objective-C API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Il programma di registrazione dinamica dispone pertanto di un avvio più lento, ma una più rapida la compilazione. Questa è l'impostazione predefinita per il simulatore iOS. Le funzioni native (in genere in C), chiamate trampolines, vengono utilizzate come implementazioni del metodo quando si utilizza il Registrar dinamico. Variano tra le architetture diverse.

- **Registrar statico** : il programma di registrazione statico genera codice Objective-C durante la compilazione, che viene quindi compilata in una libreria statica e il file eseguibile collegata. In questo modo un avvio più rapido, ma richiede più tempo durante la fase di compilazione. Per impostazione predefinita viene utilizzato per le build di dispositivo. Il programma di registrazione statico utilizzabile anche con il simulatore iOS passando `--registrar:static` come un `mtouch` attributo nelle opzioni di compilazione del progetto, come illustrato di seguito:

    [![](architecture-images/image1.png "Impostazione mtouch ulteriori argomenti")](architecture-images/image1.png#lightbox)

Per ulteriori informazioni sulle specifiche del sistema di registrazione del tipo usato da xamarin iOS, vedere il [tipo Registrar](~/ios/internals/registrar.md) Guida.

## <a name="application-launch"></a>Avvio dell'applicazione

Il punto di ingresso di tutti i file eseguibili di xamarin è fornito da una funzione denominata `xamarin_main`, che inizializza mono.

A seconda del tipo di progetto, viene eseguita la seguente:

- Per regolare iOS e applicazioni tvOS, viene chiamato il metodo Main gestito, fornito dall'app Xamarin. Questa operazione gestita metodo Main chiama quindi `UIApplication.Main`, ovvero il punto di ingresso per Objective-C. UIApplication.Main è l'associazione per Objective-C `UIApplicationMain` metodo.
- Per le estensioni, la funzione nativa: `NSExtensionMain` o (`NSExtensionmain` per le estensioni WatchOS): fornite da Apple librerie viene chiamato. Poiché questi progetti sono librerie di classi e i progetti non eseguibili, non sono gestiti metodi principali per l'esecuzione.

Tutta questa sequenza di avvio viene compilato in una libreria statica, che viene quindi collegata nell'eseguibile finale, in modo l'app sia in grado di ottenere disattivare completamente riprogettato.

A questo punto è stata avviata l'app, Mono è in esecuzione, siamo nel codice gestito e si sa come chiamare codice nativo e sia chiamato di nuovo. La successiva cosa che è necessario eseguire è effettivamente avviare l'aggiunta di controlli e rendere l'app interattive.


## <a name="generator"></a>Generator

Xamarin contiene le definizioni per ogni singola API di iOS. È possibile esplorare uno di questi sul [repository github MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Queste definizioni di contengono interfacce con gli attributi, nonché qualsiasi proprietà e metodi necessari. Ad esempio, il codice seguente viene utilizzato per definire un UIToolbar nel UIKit [dello spazio dei nomi](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327). Si noti che è un'interfaccia con un numero di metodi e proprietà:

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

Il generatore, chiamato [ `btouch` ](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) in xamarin. IOS, accetta questi file di definizione e utilizza gli strumenti di .NET per [compilarle in un assembly temporaneo](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318). Tuttavia, l'assembly temporaneo non è utilizzabile per chiamare codice Objective-C. Il generatore quindi legge l'assembly temporaneo e genera codice c# che può essere utilizzato in fase di esecuzione.
Si tratta, ad esempio, se si aggiunge un attributo casuale per il file di definizione. cs, non verrà visualizzati nel codice di output. Il generatore non sa su di esso e pertanto `btouch` non riconosce da cercare nell'assembly temporaneo per eseguirne l'output.

Dopo aver creato il Xamarin.iOS.dll, mtouch verrà raggruppare tutti i componenti.

In generale, raggiunge questo eseguendo le attività seguenti:

-   Creare una struttura di bundle di app.
-   Copiare gli assembly gestiti.
-   Se il collegamento è abilitato, eseguire il linker gestito per ottimizzare gli assembly tramite la copia di pezzi inutilizzati.
-   Compilazione AOT.
-   Creare un file eseguibile nativo, che restituisce una serie di librerie statiche (uno per ogni assembly) che sono collegati nell'eseguibile nativo, in modo che l'eseguibile nativo è costituito il codice di avvio, il codice di registrazione (se statico) e tutti gli output da AOT compilatore


Per ulteriori informazioni sul linker e su come utilizzarlo, vedere il [Linker](~/ios/deploy-test/linker.md) Guida.

## <a name="summary"></a>Riepilogo

In questa guida viene esaminato da compilazione AOT su app xamarin. IOS e consentirne xamarin. IOS e la relazione per Objective-C in modo approfondito.

## <a name="related-links"></a>Collegamenti correlati

- [Limitazioni](~/ios/internals/limitations.md)
- [Binding di Objective-C](~/cross-platform/macios/binding/overview.md)
- [Selettori Objective-C](~/ios/internals/objective-c-selectors.md)
- [Registrazione di tipo](~/ios/internals/registrar.md)
- [Linker](~/ios/deploy-test/linker.md)
