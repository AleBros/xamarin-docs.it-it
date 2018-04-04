---
title: Introduzione ai comportamenti
description: I comportamenti consentono di aggiungere funzionalità ai controlli dell'interfaccia utente senza dover sottoclasse li. Al contrario, la funzionalità è implementata in una classe di comportamento e associata al controllo come se fosse parte del controllo stesso. In questo articolo viene fornita un'introduzione ai comportamenti.
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: b5aa0d3de7092ac87d511ab8d59c329471fa6a28
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-behaviors"></a>Introduzione ai comportamenti

_I comportamenti consentono di aggiungere funzionalità ai controlli dell'interfaccia utente senza dover sottoclasse li. Al contrario, la funzionalità è implementata in una classe di comportamento e associata al controllo come se fosse parte del controllo stesso. In questo articolo viene fornita un'introduzione ai comportamenti._

I comportamenti consentono di implementare il codice che in genere è necessario scrivere come code-behind, poiché interagisce direttamente con l'API del controllo in modo tale che può essere conciso associato al controllo e incluso nel pacchetto per il riutilizzo in più di un'applicazione. Possono essere utilizzati per fornire un'ampia gamma di funzionalità di controlli, ad esempio:

- Aggiunta di un validator di posta elettronica da un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/).
- Creare un controllo di classificazione mediante un riconoscimento di movimento tap.
- Controllo di un'animazione.
- Aggiunta di un effetto a un controllo.

I comportamenti abilita anche scenari più avanzati. Nel contesto di *comandi*, i comportamenti sono un approccio utile per la connessione di un controllo a un comando. Inoltre, possono essere utilizzati per associare i controlli che non sono stati progettati per interagire con i comandi di comandi. Ad esempio, possono essere utilizzati per richiamare un comando in risposta a un generazione di eventi.

Xamarin. Forms supporta due diversi stili di comportamenti:

- **I comportamenti di xamarin. Forms** : le classi che derivano dal [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) (classe), in cui `T` è il tipo del controllo a cui il comportamento devono essere applicate. Per ulteriori informazioni sui comportamenti di xamarin. Forms, vedere [xamarin. Forms comportamenti](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [riutilizzabile comportamenti](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).
- **Collegato comportamenti** – `static` classi con uno o più proprietà associate. Per ulteriori informazioni sui comportamenti associati, vedere [collegato comportamenti](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Questa guida è incentrata sui comportamenti di xamarin. Forms perché sono l'approccio consigliato per la costruzione di comportamento.



## <a name="related-links"></a>Collegamenti correlati

- [Comportamento](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Comportamento<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
