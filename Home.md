# Objectifs
Nous souhaitons construire un **gestionnaire de confiance** qui s'assure qu'un certain **niveau de sécurité et de confiance** est maintenu dans un système **multi-parties** formé par plusieurs **composants matériels ou logiciels**.

A terme, ce gestionnaire de confiance sera une VSF qui gèrera des composants logiciels et/ou matériels (objets connectés, VNF, host de VNF, PNF). Différentes cibles pourront être choisies parmi celles décrites dans [Cibles](../Cibles).

# Périmètre du stage "Modélisation objets connectés" 2020
* Le gestionnaire de confiance ne gère que des objets connectés et des PNFs.
* Le développement se fera en java.
* Le gestionnaire prendra la forme d'une application dockerisée.
* Les user stories à développer sont, par ordre de priorité, [User Story 1](../User-Stories), [User Story 2](../User-Stories), [User Story 3](../User-Stories), [User Story 4](../User-Stories)

# Structure du wiki
* [User stories](../User-Stories)
* [Architecture du TIM et contraintes de développement](../Architecture-TIM)


# Notions clés
* Niveau de Sécurité : Capacité d'un composant ou le système à résister à un certain nombre de menaces prédéfinies.
* Niveau de Confiance : Assurance qu'un composant ou le système respecte les engagements décrits soit dans un manifest, soit dans un SLA. 
* SLA : qualité de service, prestation prescrite entre un fournisseur de service et un client. Autrement dit, il s'agit de clauses basées sur un contrat définissant les objectifs précis attendus et le niveau de service que souhaite obtenir un client de la part du prestataire et fixe les responsabilités (source : https://fr.wikipedia.org/wiki/Service-level_agreement )
* TLA : Trust Level Agreement. Indicateur à définir relatif à une *Qualité de Sécurité - QoSec"
* QOS : indicateurs permettant de mesurer une qualité de service. Cela peut être un mean up time (temps moyen en service), MTBF (Mean Time Between Failures, temps moyen entre pannes), nombre de requêtes traitées par seconde,...
* QoSec : indicateurs permettant de mesurer une qualité de sécurité. A définir
* VNF : Virtual Network Function. Composant logiciel virtualisé. Cette fonction est orientée réseau. Par exemple, il peut s'agir d'une fonction de modulation du signal, d'une fonction de filtrage de paquets ou d'une fonction d'authentification d'utilisateurs.
* PNF : Physical Network Function. Composant logiciel associé à un matériel, non virtualisé.
* HNF : Hybrid Network Function. Composant constitué d'éléments à la fois matériels et virtualisés.
* VSF : Virtual Security Function. Composant logiciel virtualisé. Cette fonction est orientée protection d'autres VNFs, PNFs ou  HNFs
* VIF ou PIF : Fonction physique ou virtualisée liée à un traitement réalisée par un objet/une application IoT. Il peut s'agir d'anonymisation de données, de calibrage, d'agrégation, d'authentification,...
* Host or PDU: Matériel qui peut héberger des fonctions virtualisées. PDU = Physical Deployment Unit.
*Component : Un composant d'un système est une combinaison d'1 matériel et de 0 ou + Physical Functions et de 0 ou + Virtual Functions