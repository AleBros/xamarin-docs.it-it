---
title: Avanzate (manuale) esempio reale
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 82bca525433e5c8fea3a29250afb83962f2e64fc
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="advanced-manual-real-world-example"></a>Avanzate (manuale) esempio reale


**Questo esempio viene utilizzato il [libreria POP da Facebook](https://github.com/facebook/pop).**


Questa sezione descrive un approccio più avanzato all'associazione, in cui verrà utilizzato Apple `xcodebuild` strumento per generare il progetto POP prima e quindi dedurre manualmente l'input per Sharpie obiettivo. Questo articolo descrive essenzialmente svolte da obiettivo Sharpie dietro le quinte nella sezione precedente.

```csharp
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Poiché la libreria POP dispone di un progetto Xcode (`pop.xcodeproj`), è possibile utilizzare solo `xcodebuild` per compilare POP. Questo processo può a sua volta generare file di intestazione Sharpie obiettivo potrebbe essere necessario analizzare. Ecco perché la compilazione prima di associazione è importante. Quando si compila tramite `xcodebuild` verificare passare lo stesso identificatore SDK e architettura che si intende passare a Sharpie obiettivo (e ricordare, obiettivo Sharpie 3.0 in genere per questo scopo si):

```csharp
$ xcodebuild -sdk iphoneos9.0 -arch arm64

Build settings from command line:
    ARCHS = arm64
    SDKROOT = iphoneos8.1
 
=== BUILD TARGET pop OF PROJECT pop WITH THE DEFAULT CONFIGURATION (Release) ===
 
...
 
CpHeader pop/POPAnimationTracer.h build/Headers/POP/POPAnimationTracer.h
    cd /Users/aaron/src/sharpie/pop
    export PATH="/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:/Users/aaron/bin::/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/aaron/.rvm/bin"
    builtin-copy -exclude .DS_Store -exclude CVS -exclude .svn -exclude .git -exclude .hg -strip-debug-symbols -strip-tool /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/strip -resolve-src-symlinks /Users/aaron/src/sharpie/pop/pop/POPAnimationTracer.h /Users/aaron/src/sharpie/pop/build/Headers/POP
 
...
 
** BUILD SUCCEEDED **
```

Esistere molte dell'output di compilazione le informazioni nella console come parte di `xcodebuild`. Come visualizzato di sopra, possiamo vedere che è stata eseguita una destinazione "CpHeader" in cui sono stati copiati i file di intestazione in una directory di output di compilazione. Questo accade spesso e semplifica l'associazione: come parte della compilazione della libreria nativa, è spesso vengono copiati i file di intestazione in un percorso utilizzabile "pubblico" che può semplificare l'analisi più semplice per l'associazione. In questo caso, si sa che il file di intestazione del POP siano presenti i `build/Headers` directory.

Si sono ora pronte per l'associazione POP. Sappiamo che si desidera compilare per SDK `iphoneos8.1` con il `arm64` architettura e i file di intestazione, Microsoft si impegna a siano `build/Headers` sotto l'estrazione di git POP. Se si osserva il `build/Headers` directory, vedremo un numero di file di intestazione:

```csharp
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Se si esamina `POP.h`, possiamo vedere file di intestazione di primo livello principale della libreria è `#import`s altri file. Per questo motivo, è necessario solo passare `POP.h` a Sharpie obiettivo, eseguire il resto dietro le quinte clang:

```csharp
$ sharpie bind -output Binding -sdk iphoneos8.1 \
    -scope build/Headers build/Headers/POP/POP.h \
    -c -Ibuild/Headers -arch arm64

Parsing Native Code...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Binding Analysis:
  Automated binding is complete, but there are a few APIs which have
  been flagged with [Verify] attributes. While the entire binding
  should be audited for best API design practices, look more closely
  at APIs with the following Verify attribute hints:

  ConstantsInterfaceAssociation (1 instance):
    There's no fool-proof way to determine with which Objective-C
    interface an extern variable declaration may be associated.
    Instances of these are bound as [Field] properties in a partial
    interface into a near-by concrete interface to produce a more
    intuitive API, possibly eliminating the 'Constants' interface
    altogether.

  StronglyTypedNSArray (4 instances):
    A native NSArray* was bound as NSObject[]. It might be possible
    to more strongly type the array in the binding based on
    expectations set through API documentation (e.g. comments in the
    header file) or by examining the array contents through testing.
    For example, an NSArray* containing only NSNumber* instances can
    be bound as NSNumber[] instead of NSObject[].

  MethodToProperty (2 instances):
    An Objective-C method was bound as a C# property due to
    convention such as taking no parameters and returning a value (
    non-void return). Often methods like these should be bound as
    properties to surface a nicer API, but sometimes false-positives
    can occur and the binding should actually be a method.

  Once you have verified a Verify attribute, you should remove it
  from the binding source code. The presence of Verify attributes
  intentionally cause build failures.

  For more information about the Verify attribute hints above,
  consult the Objective Sharpie documentation by running 'sharpie
  docs' or visiting the following URL:

    http://xmn.io/sharpie-docs

Submitting usage data to Xamarin...
  Submitted - thank you for helping to improve Objective Sharpie!

Done.
```

Si noterà che è stato passato un `-scope build/Headers` argomento Sharpie obiettivo. Poiché le librerie C e Objective-C devono `#import` o `#include` altri file di intestazione che i dettagli di implementazione della libreria e non si desidera eseguire il binding, come API di `-scope` argomento indica Sharpie obiettivo per ignorare qualsiasi API che non è definito in un file in un punto qualsiasi all'interno di `-scope` directory.

Si noterà la `-scope` argomento è spesso facoltativo per le librerie implementate correttamente, tuttavia, non è in modo esplicito fornendo non causa problemi.

Inoltre, è specificato `-c -Ibuild/headers`. Prima di tutto il `-c` argomento indica Sharpie obiettivo per arrestare l'interpretazione degli argomenti della riga di comando e passare argomenti successivi _direttamente al compilatore clang_. Pertanto, `-Ibuild/Headers` è include un argomento del compilatore clang indica clang per la ricerca in `build/Headers`, ovvero in cui le intestazioni POP in tempo reale. Senza questo argomento, clang non sarà possibile sapere dove posizionare i file che `POP.h` è `#import`sta eseguendo un'operazione. _Quasi tutti i "problemi" con l'obiettivo Sharpie riducono a capire cosa da passare per clang_.

### <a name="completing-the-binding"></a>L'associazione di completamento

Obiettivo Sharpie ora ha generato `Binding/ApiDefinitions.cs` e `Binding/StructsAndEnums.cs` file.

Si tratta base primo passaggio dell'obiettivo Sharpie della associazione, e in alcuni casi potrebbe essere è sufficiente. Come descritto in precedenza, tuttavia, lo sviluppatore in genere è necessario modificare manualmente i file generati al termine Sharpie obiettivo per [risolvere eventuali problemi](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) che potrebbe non essere automaticamente gestita dallo strumento.

Dopo aver completato l'aggiornamento, questi due file può ora essere aggiunto a un progetto di associazione in Visual Studio per Mac o essere passati direttamente il `btouch` o `bmac` strumenti per produrre l'associazione finale.

Per una descrizione dettagliata del processo di associazione, vedere il nostro [istruzioni della procedura dettagliata completa](~/ios/platform/binding-objective-c/walkthrough.md).

