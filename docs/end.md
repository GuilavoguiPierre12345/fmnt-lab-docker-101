# Et ensuite ? 
Bien que nous ayons terminé notre Lab, il reste encore BEAUCOUP à apprendre sur les conteneurs ! Nous n'allons pas approfondir le sujet ici, mais voici quelques autres domaines à examiner ensuite !

### Container Orchestration
L'exécution de conteneurs en production est difficile. Vous ne voulez pas vous connecter à une machine et simplement exécuter une ```docker run``` ou une ```docker compose up```. Pourquoi pas ? Eh bien, que se passe-t-il si les conteneurs meurent ? Comment évoluer sur plusieurs machines ? L'orchestration de conteneurs résout ce problème. Des outils comme **```Kubernetes```**, **```Swarm```**, **```Nomad```** et **```ECS```** aident tous à résoudre ce problème, tous de manière légèrement différente.

L'idée générale est que vous avez des « gestionnaires » qui reçoivent l'état attendu. Cet état peut être « Je veux exécuter deux instances de mon application Web et exposer le port 80 ». Les gestionnaires examinent ensuite toutes les machines du **cluster** et délèguent le travail aux nœuds « travailleurs ». Les gestionnaires surveillent les changements (comme la fermeture d'un conteneur) puis s'efforcent de faire en sorte que l'état réel reflète l'état attendu.

### Cloud Native Computing Foundation Projects (Projets de base sur le Cloud Native Computing)

Le CNCF est un foyer indépendant des fournisseurs pour divers projets open source, notamment **Kubernetes**, **Prometheus**, **Envoy**, **Linkerd**, **NATS**, et bien d'autres ! Vous pouvez consulter les projets diplômés et incubés ici et l'ensemble du paysage de la CNCF ici. Il existe de nombreux projets pour aider à résoudre les problèmes liés à la surveillance, à la journalisation, à la sécurité, aux registres d'images, à la messagerie, et bien plus encore !

Alors, si vous êtes nouveau dans le domaine des conteneurs et du développement d'applications cloud natives, bienvenue ! Connectez-vous à la communauté, posez des questions et continuez à apprendre ! Nous sommes ravis de vous compter parmi nous !

#
### 📞 Contacts
* Etat civil : 👤 Foromo Pierre Guilavogui : [Yaboigui]
* 🔗 LinkedIn : linkedin.com/in/ton-profil
* 🐙 GitHub : github.com/ton-profil
* 📱 WhatsApp : +224 625506385