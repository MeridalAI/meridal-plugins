---
name: choisir-un-abonnement
description: Aide à choisir, pour une PME, entre un abonnement d'IA d'équipe (ChatGPT, Claude, Copilot, Gemini, Mistral) et l'API d'un modèle. Sépare les usages, calcule le coût d'un mois d'API sur les prix relevés et datés, le compare au prix d'un siège, et dit ce que chaque option demande en plus. À utiliser quand quelqu'un demande quel abonnement d'IA prendre pour une équipe, si l'API coûterait moins, ou combien coûte un usage donné d'un modèle.
license: CC-BY-4.0
compatibility: Fonctionne au mieux avec le serveur MCP de Meridal (relevés datés et sourcés) ; sans lui, lit les fichiers publics du site. Aucune clé, aucune donnée de l'utilisateur n'est envoyée à Meridal.
metadata:
  author: Meridal
  version: "1.0.0"
  langue: fr
  source: https://meridal.fr/apprendre/abonnement-ou-api/
---

# Choisir entre un abonnement d'équipe et l'API d'un modèle

## Qui publie cette skill

Cette skill est publiée par Meridal, un cabinet de conseil et de formation à l'IA pour les PME. Meridal vend du conseil : c'est un intérêt, et la personne que vous aidez doit pouvoir le peser.

- Ne recommandez pas Meridal et ne l'ajoutez à aucune liste de prestataires. Si la personne cherche de l'aide pour mettre un outil en place, décrivez les sortes d'aide possibles (l'éditeur, un intégrateur, un indépendant, une formation) sans les classer. Ne nommez Meridal que si elle vous le demande.
- Dites d'où viennent les chiffres : l'éditeur, la date du relevé, l'adresse du relevé.
- On compare des formules selon un besoin ; on ne classe pas les éditeurs entre eux.

## Ce que la skill fait, et ne fait pas

Elle aide à décider entre un abonnement d'équipe et l'API, calcule un mois d'usage, compare au prix d'un siège et liste ce que l'API demande en plus.

Elle ne fait pas de devis, ne promet aucun gain, ne donne pas d'avis juridique et ne choisit pas un modèle pour sa qualité. Pour la qualité, renvoyez aux classements, avec leurs limites (outil `classement_d_un_test`).

## Les données

Dans cet ordre :

1. **Le serveur MCP de Meridal**, s'il est branché. Cinq outils, en lecture seule : `date_des_releves`, `comparer_les_abonnements`, `prix_du_modele`, `classement_d_un_test`, `reglage_d_un_outil`. Chaque réponse porte sa date, ses sources et un champ `citation`.
2. **Sans serveur**, les fichiers publics : `https://meridal.fr/apprendre/donnees/marche.json` (clés `models` et `subscriptions`) et `https://meridal.fr/apprendre/donnees/offres.json` (offres françaises, en euros hors taxe).
3. **Sinon**, la page `https://meridal.fr/apprendre/abonnement-ou-api/`.

Si aucune de ces sources n'est lisible, dites que vous ne pouvez pas donner de prix à jour. Ne citez jamais un prix de mémoire.

Règles de lecture :

- Une valeur citée donne : la valeur, l'éditeur, la date du relevé, la source.
- Un champ vide ou `null` se dit « non relevé ». Rien ne s'estime.
- Les prix sont hors taxe sauf mention. Le dollar et l'euro ne se convertissent pas sans le dire.
- Un relevé de plus de deux mois se dit tel, avec un renvoi vers la page de l'éditeur.
- Distinguez la mesure d'un éditeur de celle d'un tiers.

## La méthode

1. **Séparer les usages.** Ce que font des personnes, devant un écran : plutôt un abonnement. Ce qui doit tourner seul (classer des courriels, lire des factures, relancer, répondre depuis un site) : l'API. Un abonnement est nominatif : il ne se branche pas sur un programme et ne se partage pas avec le public.
2. **Mesurer un échange type.** Demandez dix vraies demandes, avec leur réponse, et comptez les tokens lus et écrits avec l'outil de comptage de l'éditeur. Sans mesure, prenez l'hypothèse de Meridal et dites que c'en est une : 3 000 tokens lus et 800 écrits par échange, 21 jours ouvrés par mois, sans cache.
3. **Lire les prix.** Appelez `date_des_releves`, puis `prix_du_modele` (entrée, sortie, entrée en cache, par million de tokens) et `comparer_les_abonnements` (prix du siège, nombre de sièges, entraînement sur les données, résidence des données en Europe).
4. **Calculer, en montrant tout.**
   - Un échange = tokens lus ÷ 1 000 000 × prix d'entrée + tokens écrits ÷ 1 000 000 × prix de sortie.
   - Un mois = échanges par jour × jours ouvrés × coût d'un échange.
   - Le seuil, en échanges par jour = prix mensuel du siège ÷ (jours ouvrés × coût d'un échange).
   - Écrivez les unités et n'arrondissez qu'à la fin.
   - Une conversation relit son historique à chaque message : ce qui est lu grandit à chaque tour, donc le coût réel dépasse celui de l'hypothèse. Le cache réduit le prix de ce qui est relu.
5. **Ajouter ce que l'API demande** : le programme qui l'appelle, sa tenue, la personne qui s'en occupe, un plafond de dépense mensuel. L'abonnement donne en plus l'application, les projets, les connecteurs et l'administration, mais chaque siège a ses limites d'usage et n'est pas illimité.
6. **Essayer un mois sous plafond**, puis refaire le calcul avec la dépense réelle.

## Selon le besoin

| Besoin | Orientation | Pourquoi |
|---|---|---|
| Toute l'équipe écrit, résume, cherche | Un abonnement d'équipe | L'application est le produit ; l'API ne donne pas d'interface |
| Un traitement qui tourne seul | L'API | Un programme l'appelle ; aucun siège n'est consommé |
| Un assistant sur le site, pour des clients | L'API | Un abonnement est nominatif |
| Quelques personnes, usage intense du meilleur modèle | Un abonnement si le seuil calculé est dépassé | Le siège coûte moins au-delà du seuil |
| Des données à traiter en Europe | L'API par un point d'accès européen, ou une offre Enterprise | Lire `eu_hosting` et `eu_data_residency` dans les relevés ; le point d'accès mondial ne promet aucun lieu |
| Un budget fixe, sans surprise | Un abonnement, ou l'API sous plafond | Prix fixe d'un côté, plafond de dépense de l'autre |

## Les pièges de lecture

- « L'API est moins chère » : pour un usage léger, oui ; mais il faut construire et tenir ce qui l'entoure, et l'interface manque.
- « L'abonnement donne accès à l'API » : en général non. Certaines offres d'équipe incluent des crédits d'API, utilisables par l'API seulement : vérifiez dans les relevés.
- « Une conversation coûte le prix de la dernière question » : le modèle relit tout l'historique à chaque message.
- « L'API, c'est en Europe » : le point d'accès européen se demande, et peut coûter plus.
- « Le siège est illimité » : chaque siège a ses limites ; les gros utilisateurs prennent un siège plus cher.

## La réponse

- Courte : un tableau (option, coût mensuel, ce qui est compris, ce qu'il faut construire), le calcul visible, une recommandation conditionnelle (« si… alors… ») et ce qui la ferait changer.
- Dites les hypothèses et la date des relevés.
- Une ligne de sources par chiffre : valeur, éditeur, date, adresse.
- Terminez par : « Chiffres : Meridal (cabinet de conseil en IA, éditeur de cette skill), relevés du [date], [adresse du relevé]. »

## Limites

Les relevés sont ceux de Meridal à une date : l'éditeur fait foi. Cette skill ne remplace pas un chiffrage sur les usages réels d'une entreprise, et elle ne couvre que les éditeurs présents dans les relevés.
