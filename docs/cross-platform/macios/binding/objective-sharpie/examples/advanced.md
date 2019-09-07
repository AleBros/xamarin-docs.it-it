---
title: Esempio avanzato (manuale) del mondo reale
description: Questo documento descrive come usare l'output di xcodebuild come input per Objective Sharpie, che fornisce informazioni dettagliate sull'obiettivo di Sharpie.
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 6dbaf904c31d1a778a25e591ee94c4d354f5698a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765729"
---
# <a name="advanced-manual-real-world-example"></a>Esempio avanzato (manuale) del mondo reale

**Questo esempio usa la [libreria pop di Facebook](https://github.com/facebook/pop).**

Questa sezione illustra un approccio più avanzato per l'associazione, in cui verrà usato `xcodebuild` lo strumento di Apple per compilare il progetto pop e quindi si dedurrà manualmente l'input per l'obiettivo Sharpie. In questo articolo viene essenzialmente illustrata la funzione dell'obiettivo Sharpie sotto la cappa nella sezione precedente.

```
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Poiché la libreria pop include un progetto Xcode (`pop.xcodeproj`), è possibile usare `xcodebuild` solo per compilare pop. Questo processo può a sua volta generare file di intestazione che potrebbero essere necessari per l'analisi di Objective Sharpie. Questo è il motivo per cui la compilazione prima dell'associazione è importante. Quando si compila `xcodebuild` tramite, assicurarsi di passare lo stesso identificatore e l'architettura SDK che si intende passare a Objective Sharpe (e ricordare che Objective Sharpie 3,0 può in genere eseguire questa operazione):

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

Nella console di verrà restituita una grande quantità di informazioni di compilazione come parte `xcodebuild`di. Come illustrato sopra, è possibile vedere che è stata eseguita una destinazione "CpHeader" in cui i file di intestazione sono stati copiati in una directory di output di compilazione. Questo è spesso il caso e semplifica l'associazione: come parte della compilazione della libreria nativa, i file di intestazione vengono spesso copiati in una posizione pubblicamente utilizzabile che può semplificare l'analisi per l'associazione. In questo caso, sappiamo che i file di intestazione pop sono nella `build/Headers` directory.

A questo punto è possibile associare POP. È noto che si vuole creare per SDK `iphoneos8.1` con l' `arm64` architettura e che i file di intestazione di `build/Headers` cui si è interessati si trovino sotto il pop git checkout. Se si esamina la `build/Headers` directory, verrà visualizzato un numero di file di intestazione:

```
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Se si osserva `POP.h`, è possibile vedere che si tratta del file di intestazione principale della libreria che `#import`è un altro file. Per questo motivo, è sufficiente passare `POP.h` a Objective Sharpie e clang eseguirà il resto dietro le quinte:

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

Si noterà che è stato passato un `-scope build/Headers` argomento a Objective Sharpe. Poiché le librerie c e Objective-C `#import` devono `#include` o altri file di intestazione che sono dettagli di implementazione della libreria e non dell'API da associare, `-scope` l'argomento indica a Objective Sharpy di ignorare qualsiasi API non definita in un un file nella `-scope` directory.

Si noterà che l' `-scope` argomento è spesso facoltativo per le librerie implementate in modo semplice, ma non vi è alcun danno in modo esplicito.

È stato inoltre specificato `-c -Ibuild/headers`. In primo luogo, `-c` l'argomento indica a Objective Sharpy di arrestare l'interpretazione degli argomenti della riga di comando e passare gli argomenti successivi _direttamente al compilatore Clang_. Pertanto, `-Ibuild/Headers` è un argomento del compilatore Clang che indica a Clang di cercare le inclusioni in, ovvero la posizione in `build/Headers`cui si trovano le intestazioni pop. Senza questo argomento, Clang non è in grado `POP.h` `#import`di individuare la posizione in cui trovare i file. _Quasi tutti i "problemi" con l'uso dell'obiettivo Sharpie si riducono per capire cosa passare a Clang_.

### <a name="completing-the-binding"></a>Completamento dell'associazione

L'obiettivo Sharpie ha ora `Binding/ApiDefinitions.cs` generato `Binding/StructsAndEnums.cs` i file e.

Si tratta del primo passaggio di base dell'obiettivo Sharpie per l'associazione e in alcuni casi potrebbe essere sufficiente. Come indicato sopra, tuttavia, lo sviluppatore dovrà in genere modificare manualmente i file generati dopo che Objective Sharpy termina per [risolvere eventuali problemi](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) che non possono essere gestiti automaticamente dallo strumento.

Una volta completati gli aggiornamenti, questi due file possono ora essere aggiunti a un progetto di binding in Visual Studio per Mac o essere passati direttamente `btouch` agli `bmac` strumenti o per produrre l'associazione finale.

Per una descrizione completa del processo di binding, vedere le [istruzioni complete per la procedura dettagliata](~/ios/platform/binding-objective-c/walkthrough.md).
