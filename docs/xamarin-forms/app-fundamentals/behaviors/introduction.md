---
title: Introduzione ai comportamenti
description: I comportamenti consentono di aggiungere funzionalità ai controlli dell'interfaccia utente senza sottoclassarli. La funzionalità viene invece implementata in una classe di comportamento e associata al controllo come se fosse parte del controllo stesso. Questo articolo offre un'introduzione ai comportamenti.
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: d62ba6b025b2fe9865df8279a5e98eba254bb5a2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70772056"
---
# <a name="introduction-to-behaviors"></a>Introduzione ai comportamenti

_I comportamenti consentono di aggiungere funzionalità ai controlli dell'interfaccia utente senza doverli sottoclassare. Al contrario, la funzionalità viene implementata in una classe di comportamento e associata al controllo come se fosse parte del controllo stesso. In questo articolo viene fornita un'introduzione ai comportamenti._

I comportamenti consentono di implementare il codice che normalmente dovrebbe essere scritto come code-behind, poiché interagisce direttamente con l'API del controllo in modo tale da poter essere associato concisamente al controllo e inserito nel pacchetto per essere riutilizzato in più di un'applicazione. Possono essere usati per offrire un'ampia gamma di funzionalità ai controlli, ad esempio:

- Aggiunta di un [`Entry`](xref:Xamarin.Forms.Entry)validatore di posta elettronica a un file .
- Creazione di un controllo classificazione tramite un riconoscimento del movimento di tocco.
- Controllo di un'animazione.
- Aggiunta di un effetto a un controllo.

I comportamenti consentono anche scenari più avanzati. Nel contesto dell'*esecuzione di comandi*, i comportamenti sono un approccio utile per la connessione di un controllo a un comando. Possono anche essere usati per associare comandi a controlli che non sono stati progettati per interagire con i comandi. Si possono usare ad esempio per richiamare un comando in risposta all'attivazione di un evento.

Xamarin.Forms supporta due stili diversi comportamenti:

- **Comportamenti di Xamarin.Forms**: classi che derivano dalla classe [`Behavior`](xref:Xamarin.Forms.Behavior) o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), dove `T` è il tipo del controllo al quale deve essere applicato il comportamento. Per altre informazioni sui comportamenti di Xamarin.Forms, vedere [Comportamenti di Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [Comportamenti riutilizzabili](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).
- **Comportamenti associati:** `static` classi con una o più proprietà associate. Per altre informazioni sui comportamenti associati, vedere [Comportamenti associati](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Questa guida è incentrata sui comportamenti di Xamarin.Forms perché sono l'approccio consigliato per la costruzione di comportamento.

## <a name="related-links"></a>Collegamenti correlati

- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
