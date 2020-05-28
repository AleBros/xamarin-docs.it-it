---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d5bee3baa08387a1ed67384ba4089d63aad08d2c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135772"
---
# <a name="introduction-to-behaviors"></a>Introduzione ai comportamenti

_I comportamenti consentono di aggiungere funzionalità ai controlli dell'interfaccia utente senza dover sottometterle in sottoclasse. Al contrario, la funzionalità viene implementata in una classe di comportamento e collegata al controllo come se facesse parte del controllo stesso. Questo articolo fornisce un'introduzione ai comportamenti._

I comportamenti consentono di implementare il codice che normalmente dovrebbe essere scritto come code-behind, poiché interagisce direttamente con l'API del controllo in modo tale da poter essere associato concisamente al controllo e inserito nel pacchetto per essere riutilizzato in più di un'applicazione. Possono essere usati per offrire un'ampia gamma di funzionalità ai controlli, ad esempio:

- Aggiunta di un validator di posta elettronica a un [`Entry`](xref:Xamarin.Forms.Entry) .
- Creazione di un controllo classificazione tramite un riconoscimento del movimento di tocco.
- Controllo di un'animazione.
- Aggiunta di un effetto a un controllo.

I comportamenti consentono anche scenari più avanzati. Nel contesto dell'*esecuzione di comandi*, i comportamenti sono un approccio utile per la connessione di un controllo a un comando. Possono anche essere usati per associare comandi a controlli che non sono stati progettati per interagire con i comandi. Si possono usare ad esempio per richiamare un comando in risposta all'attivazione di un evento.

Xamarin.Formssupporta due stili diversi di comportamento:

- ** Xamarin.Forms comportamenti** : classi che derivano dalla [`Behavior`](xref:Xamarin.Forms.Behavior) classe o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) , dove `T` è il tipo del controllo a cui deve essere applicato il comportamento. Per ulteriori informazioni sui Xamarin.Forms comportamenti, vedere comportamenti [ Xamarin.Forms ](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [comportamenti riutilizzabili](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).
- **Comportamenti collegati** : `static` classi con una o più proprietà associate. Per altre informazioni sui comportamenti associati, vedere [Comportamenti associati](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Questa guida è incentrata sui Xamarin.Forms comportamenti perché rappresentano l'approccio preferito alla costruzione del comportamento.

## <a name="related-links"></a>Collegamenti correlati

- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento &lt; T&gt;](xref:Xamarin.Forms.Behavior`1)
