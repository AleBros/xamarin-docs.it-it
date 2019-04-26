---
title: Avanzate (manuali) esempio reale
description: Questo documento descrive come usare l'output di xcodebuild come input per Sharpie obiettivo, che fornisce informazioni approfondite Sharpie obiettivo del funzionamento dietro le quinte.
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: c4f7f1e9702fb2ee0f5525343a52e3aacd85d68c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200282"
---
# <a name="advanced-manual-real-world-example"></a>Avanzate (manuali) esempio reale

**Questo esempio Usa la [libreria POP proveniente da Facebook](https://github.com/facebook/pop).**

Questa sezione viene illustrato un approccio più avanzato di associazione, in cui si userà di Apple `xcodebuild` dello strumento per compilare innanzitutto il progetto POP e quindi dedurre manualmente input per Sharpie obiettivo. La descrizione riguarda essenzialmente Sharpie obiettivo operazioni svolte dietro le quinte nella sezione precedente.

```
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Poiché la libreria POP dispone di un progetto Xcode (`pop.xcodeproj`), è possibile usare semplicemente `xcodebuild` compilare POP. Questo processo, a sua volta può generare file di intestazione Objective Sharpie potrebbe essere necessario analizzare. Ecco perché la compilazione prima dell'associazione è importante. Durante la compilazione tramite `xcodebuild` verificare di passare lo stesso identificatore SDK e architettura che si prevede di passare all'obiettivo Sharpie (e ricordare, obiettivo Sharpie 3.0 in genere per questo scopo è):

```
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

Vi saranno molte dell'output di informazioni di compilazione nella console di come parte di `xcodebuild`. Come visualizzato in precedenza, possiamo vedere che è stata eseguita una destinazione di "CpHeader" in cui sono stati copiati i file di intestazione in una directory di output di compilazione. Questo accade spesso e semplifica l'associazione: come parte della compilazione della libreria nativa, in un percorso utilizzabile "pubblico" che può rendere più semplice di analisi per l'associazione spesso vengono copiati i file di intestazione. In questo caso, sappiamo che siano file di intestazione del POP il `build/Headers` directory.

A questo punto siamo pronti eseguire l'associazione POP. Sappiamo che si desidera creare per SDK `iphoneos8.1` con il `arm64` architettura e che i file di intestazione ci preoccupiamo siano `build/Headers` sotto il checkout di git POP. Se si osserva il `build/Headers` directory, si vedrà un numero di file di intestazione:

```
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Se osserviamo `POP.h`, possiamo vedere si tratta di file di intestazione di primo livello principale della libreria `#import`s altri file. Per questo motivo, è sufficiente passare `POP.h` a Sharpie obiettivo, e clang farà il resto dietro le quinte:

```
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

Si noterà che viene passato un `-scope build/Headers` argomento Sharpie obiettivo. Poiché le librerie C e Objective-C devono `#import` o `#include` altri file di intestazione sono dettagli di implementazione della libreria e non si vuole eseguire il binding, come API di `-scope` argomento indica Sharpie obiettivo per ignorare qualsiasi API che non è definito in un file in un punto qualsiasi all'interno di `-scope` directory.

Si noterà il `-scope` argomento spesso è facoltativo per le librerie implementate correttamente, tuttavia non crea alcun problema in modo esplicito fornendo.

Inoltre, abbiamo specificato `-c -Ibuild/headers`. In primo luogo, il `-c` argomento indica Sharpie descrive come arrestare interpreta gli argomenti della riga di comando e passare argomenti successivi _direttamente al compilatore clang_. Pertanto `-Ibuild/Headers` è un argomento del compilatore clang che istruisce clang per la ricerca include sotto `build/Headers`, ovvero dove si trovano le intestazioni POP. Senza questo argomento, clang non saprebbe dove posizionare i file che `POP.h` è `#import`ing. _Quasi tutti i "problemi" con obiettivo Sharpie riducono a capire cosa da passare per clang_.

### <a name="completing-the-binding"></a>Il completamento dell'associazione

Obiettivo Sharpie ora ha generato `Binding/ApiDefinitions.cs` e `Binding/StructsAndEnums.cs` file.

Si tratta base prima passata dell'obiettivo Sharpie all'associazione e in alcuni casi potrebbe essere tutto quello che serve. Come indicato in precedenza, tuttavia, lo sviluppatore in genere necessario modificare manualmente i file generati al termine obiettivo Sharpie al [risolvere i problemi](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) che potrebbe non essere automaticamente gestita dallo strumento.

Dopo aver completati gli aggiornamenti, questi due file può ora essere aggiunto a un progetto di associazione in Visual Studio per Mac o essere passati direttamente al `btouch` o `bmac` strumenti per produrre l'associazione finale.

Per una descrizione dettagliata del processo di associazione, vedere la [istruzioni della procedura dettagliata completa](~/ios/platform/binding-objective-c/walkthrough.md).

## <a name="related-links"></a>Collegamenti correlati

- [Corsi di Xamarin University: Compila una libreria di binding Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Corsi di Xamarin University: Creazione di una libreria di binding Objective-C con Sharpie obiettivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)