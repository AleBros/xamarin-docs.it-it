---
title: Xamarin per sviluppatori Objective-C
description: Questo documento fornisce una descrizione di Xamarin.iOS per gli sviluppatori Objective-C. Contiene inoltre i collegamenti alle guide che descrivono come eseguire la transizione a C# da Objective-C, come associare una libreria Objective-C per l'uso in C# e come compilare un'applicazione per dispositivi mobili multipiattaforma.
ms.prod: xamarin
ms.assetid: 9F3C86A3-403E-4025-99CA-99FCA86DC828
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2017
ms.openlocfilehash: 96a7e6b9587d696479afd084613c79e2b03f956c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292008"
---
# <a name="xamarin-for-objective-c-developers"></a>Xamarin per sviluppatori Objective-C

Xamarin consente agli sviluppatori che scelgono iOS di spostare il codice non di interfaccia utente a C# indipendente dalla piattaforma in modo che possa essere usato ovunque C# sia disponibile, inclusi Android tramite Xamarin.Android e le diverse versioni di Windows. L'uso di C# con Xamarin non impedisce tuttavia di sfruttare le competenze e il codice Objective-C esistenti. Infatti gli sviluppatori Xamarin.iOS che conoscono Objective-C ottimizzano il proprio lavoro perché Xamarin espone tutte le API delle piattaforme iOS e OS X native già note e usate, ad esempio UIKit, Core Animation, Core Foundation, Core Graphics e altre ancora, e contemporaneamente sfruttano il linguaggio C#, incluse funzionalità come LINQ e Generics, oltre alle librerie di classi base .NET avanzate da usare nelle applicazioni native.

Xamarin consente anche di sfruttare le risorse Objective-C esistenti tramite una tecnologia nota come binding. È sufficiente creare una libreria statica in Objective-C ed esporla a C# tramite un binding, come illustrato nel diagramma seguente:

 [![](images/01-bindings.png "Libreria statica in Objective-C esposta a C# tramite un binding")](images/01-bindings.png#lightbox)

Questo non si limita necessariamente al codice non di interfaccia utente. I binding possono esporre anche il codice dell'interfaccia utente sviluppato in Objective-C.

## <a name="transitioning-from-objective-c"></a>Transizione da Objective-C

Nel sito della documentazione sono disponibili moltissime informazioni sulla transizione a Xamarin, che illustrano come integrare il codice C# con le funzionalità già note. Alcune informazioni importanti per iniziare includono:

- [Panoramica di C# per sviluppatori Objective-C](primer.md): breve panoramica per gli sviluppatori Objective-C che vogliono passare a Xamarin e al linguaggio C#. 
- [Procedura dettagliata: Binding an Objective-C Library](~/ios/platform/binding-objective-c/walkthrough.md) (Procedura dettagliata: Binding di una libreria Objective-C): procedura dettagliata per riutilizzare il codice Objective-C esistente in un'applicazione Xamarin.iOS. 


## <a name="binding-objective-c"></a>Binding di Objective-C

Dopo avere appreso le differenze tra C# e Objective-C e avere eseguito la procedura dettagliata precedente per il binding, si è pronti per la transizione alla piattaforma Xamarin. Per completare l'attività, nella sezione [Binding Objective-C](~/ios/platform/binding-objective-c/index.md) (Binding di Objective-C) sono disponibili informazioni più dettagliate sulle tecnologie di associazione di Xamarin.iOS.

## <a name="cross-platform-development"></a>Sviluppo di più piattaforme

Dopo essere passati a Xamarin.iOS, è infine possibile esaminare le indicazioni disponibili per le diverse piattaforme, inclusi i case study delle applicazioni di riferimento sviluppate e le procedure consigliate per creare codice multipiattaforma riutilizzabile, contenute nella [sezione Creazione di applicazioni multipiattaforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md).
