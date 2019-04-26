---
ms.openlocfilehash: d46968f9c53314abe561e7f4871cfbf6e07b7002
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61047857"
---
## <a name="firewall-configuration"></a>Configurazione del firewall

Affinché i test da inviare a Xamarin Test Cloud, il computer inviando i test deve essere in grado di comunicare con i server di Test Cloud. I firewall devono essere configurati per consentire il traffico di rete da e verso i server che si trova in **testcloud.xamarin.com** sulle porte 80 e 443. Questo endpoint viene gestito dal DNS e l'indirizzo IP è soggetta a modifiche. 

In alcuni casi, un test (o un dispositivo che esegue il test) deve comunicare con i server web protetti da firewall. In questo scenario il firewall deve essere configurato per consentire il traffico dagli indirizzi IP seguenti:

* **195.249.159.238**
* **195.249.159.239**
