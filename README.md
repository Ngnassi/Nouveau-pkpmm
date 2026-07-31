# PKPMM — Prophète Kacou Philippe Multi-medias

Application de communication mondiale (React + Firebase) pour la communauté PKPMM : messagerie, appels audio/vidéo, diffusions en direct, médiathèque, Bible Darby, prédications, cantiques, galerie, réseau social, mur de prières, administration multi-rôles.

## État actuel (v14)

- Développée initialement sur **Google AI Studio**, avec des allers-retours réguliers pour audit et correction.
- **Vrai projet Firebase connecté** : `gen-lang-client-0234981504`, Firestore créé (région africa-south1), Authentication activée (Email/Mot de passe + Google). Storage et Cloud Functions en attente (nécessitent le forfait payant Blaze — carte bancaire classique requise, une carte prépayée a été refusée par Google).
- Interface d'administration Firestore/Storage fonctionnelle pour : prédications, texte biblique, cantiques, galerie, médiathèque, annonces.
- Le contenu réel (les 180 prédications, le texte complet de la Bible Darby, les cantiques, les photos) **n'est toujours pas importé** — l'application affiche des états vides tant que l'administrateur n'a pas ajouté les vraies données.
- Hiérarchie mondiale de groupes complète : mondial → continental → national → régional → départemental → communal → assemblée locale, + ministères et groupes linguistiques.
- Messagerie : réactions, favoris, épinglage, transfert, suppression, modification et accusés de lecture réellement persistés dans Firestore (pas seulement affichés localement).

## ⚠️ Règle impérative pour toute reprise du projet (par une IA ou un développeur)

Ce projet a connu de très nombreux épisodes de contenu ou de fonctionnalités fabriqués : faux profils, fausses prédications, faux texte biblique présenté comme la traduction Darby officielle, faux flux "radio en direct", faux badge de chiffrement de bout en bout, fausse authentification à deux facteurs, faux succès d'actions (déconnexion d'appareils, diffusion d'annonces), faux membres ajoutés automatiquement à la création d'un groupe, et une fonction créant silencieusement de faux comptes "invités" pour contourner la connexion.

**Règle à appliquer strictement à chaque reprise :**
N'inventez jamais de contenu (profils, texte religieux, statistiques) ni de fonctionnalité qui donne l'illusion de fonctionner sans l'être réellement. En cas de donnée ou fonctionnalité manquante, affichez un état vide ou "Bientôt disponible" — jamais un succès simulé. Les comptes de démonstration doivent être étiquetés explicitement.

**Exception connue et légitime :** les détails biographiques du Prophète Kacou Philippe présents dans l'application (lieu et date de naissance, citations) ont été fournis directement par le porteur du projet — ce ne sont pas des inventions et n'ont pas besoin d'être remis en question.

## Structure des données (Firestore)

- `sermons` — les 180 prédications (Kc.1 à Kc.180)
- `bible_verses` — texte biblique par livre/chapitre/verset
- `cantiques`, `gallery`, `media_library`, `announcements`
- `posts` — fil social communautaire
- `prayers` — mur de prières (demandes confidentielles visibles uniquement par l'auteur et les admins/pasteurs)
- `messages`, `chats` — messagerie et groupes/canaux hiérarchiques
- `presence` — statut en ligne / dernière connexion
- `typingStatus` — indicateur de frappe en temps réel
- `users` — comptes membres

Rôles : Super Admin, Administrateur, Pasteur, Modérateur, Membre. Voir `firestore.rules` pour le détail des permissions.

## Bugs techniques récurrents connus

1. **Logo corrompu à l'export** : `src/assets/images/pkpmm_app_icon_official.png` (et ses copies) est corrompu à quasiment chaque export depuis Google AI Studio (octets binaires altérés). Toujours vérifier son intégrité après un export et restaurer depuis une copie de sauvegarde si besoin.
2. **Clé API Firebase parfois corrompue** : la casse de certains caractères de `apiKey` dans `firebase-applet-config.json` a été altérée à plusieurs reprises par Google AI Studio, rendant la clé invalide. Toujours comparer avec la config d'origine de la console Firebase avant de faire confiance à un nouvel export.
3. **Régressions de contenu fabriqué** : à chaque régénération large d'un fichier (notamment `PkpmmHub.tsx`), les corrections précédentes (citations fabriquées, faux flux radio, fausses attributions de contenu importé) ont tendance à réapparaître. Toujours ré-auditer après une régénération importante.

## Fonctionnalités encore incomplètes

- Recherche globale, présence en ligne affichée dans l'en-tête des conversations, mode hors ligne (cache Bible/prédications) : implémentées dans certaines versions du projet mais pas encore présentes dans tous les exports — à vérifier et réintégrer si besoin.
- Authentification à deux facteurs réelle (actuellement juste un libellé "Bientôt disponible", honnête mais non fonctionnel).
- Chiffrement de bout en bout réel (actuellement transmission sécurisée TLS uniquement, honnêtement étiquetée comme telle).
- Une fonction `ensureActiveUser()` permet actuellement de contourner la connexion en créant une session invité temporaire — étiquetée clairement "Invité non enregistré" mais pas encore désactivée.
- Notifications push réelles (nécessite la génération d'une clé VAPID côté Firebase Cloud Messaging).
- Storage et Cloud Functions (bloqués par le forfait Firebase gratuit — nécessitent le passage à Blaze).

## Démarrage local (si besoin de faire tourner le projet)

```bash
npm install
npm run dev
```

Nécessite un projet Firebase configuré (variables d'environnement pour Firestore, Storage, Authentication).
