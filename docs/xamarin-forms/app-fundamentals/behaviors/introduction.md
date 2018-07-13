---
title: Introduzione ai comportamenti
description: I comportamenti consentono di aggiungere funzionalità a controlli dell'interfaccia utente senza la necessità di sottoclasse li. Al contrario, la funzionalità è implementata in una classe di comportamento e associata al controllo come se fosse parte del controllo stesso. Questo articolo fornisce un'introduzione ai comportamenti.
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 176f41d4b7349af2cf7cc49de8ba0789ad2f8c11
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995814"
---
# <a name="introduction-to-behaviors"></a>Introduzione ai comportamenti

_I comportamenti consentono di aggiungere funzionalità a controlli dell'interfaccia utente senza la necessità di sottoclasse li. Al contrario, la funzionalità è implementata in una classe di comportamento e associata al controllo come se fosse parte del controllo stesso. Questo articolo fornisce un'introduzione ai comportamenti._

I comportamenti consentono di implementare il codice che sarebbe normalmente necessario per scrivere come code-behind, in quanto interagisce direttamente con l'API del controllo in modo che possa essere conciso associato al controllo o incluso nel pacchetto per il riutilizzo attraverso più di un'applicazione. Possono essere utilizzati per fornire un'ampia gamma di funzionalità a controlli, ad esempio:

- Aggiunta di un validator del messaggio di posta elettronica da un [ `Entry` ](xref:Xamarin.Forms.Entry).
- Creazione di un controllo rating utilizzando un riconoscimento di movimento tap.
- Controllo di un'animazione.
- Aggiunta di un effetto a un controllo.

I comportamenti abilitano anche scenari più avanzati. Nel contesto di *esecuzione di comandi*, i comportamenti sono un approccio utile per la connessione di un controllo a un comando. Inoltre, possono essere utilizzati per associare i comandi con i controlli che non sono stati progettati per interagire con i comandi. Ad esempio, possono essere utilizzati per richiamare un comando in risposta all'attivazione di un evento.

Xamarin. Forms supporta due stili diversi comportamenti:

- **I comportamenti di xamarin. Forms** : classi che derivano dal [ `Behavior` ](xref:Xamarin.Forms.Behavior) oppure [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) (classe), in cui `T` è il tipo di controllo su cui il comportamento devono essere applicate. Per altre informazioni sui comportamenti di xamarin. Forms, vedere [comportamenti di xamarin. Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [comportamenti riutilizzabili](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).
- **Comportamenti collegati** – `static` classi con uno o più proprietà associate. Per altre informazioni sui comportamenti associati, vedere [comportamenti collegati](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Questa guida è incentrata sui comportamenti di xamarin. Forms perché sono l'approccio consigliato per la costruzione di comportamento.



## <a name="related-links"></a>Collegamenti correlati

- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
