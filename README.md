# Apex Echoes

RPG textuel solo procédural, façon Monster Hunter, développé en Flutter (Android + iOS, 100% hors-ligne).

## Contenu implémenté (version complète)

- **Modèles de données** : joueur, monstres, équipement (avec rareté et tranchant), zones, compagnon (Tamagotchi), bestiaire, lore.
- **Moteur de combat** textuel au tour par tour : dégâts physiques/élémentaires, faiblesses, critiques, endurance, aiguisage.
- **Génération procédurale** : zones avec rapport d'expédition (20 monstres, cap de 3 exemplaires identiques, 1-2 boss Apex, 10% de corruption "Aura Violette"), monstres mis à l'échelle par danger de zone, équipements avec préfixes/suffixes et rareté (Commun → Maudit).
- **Système d'œuf & compagnon** : drop selon la difficulté du boss (0.5% à 7%), soins façon Tamagotchi (nourrir/baigner/entraîner), croissance sur plusieurs jours réels, 3 stades, bonus passifs de combat.
- **Codex / Bestiaire** : fiche par espèce (élément, faiblesse, taux de drop), fragments de lore débloqués progressivement selon le nombre de victoires contre chaque monstre, plus un onglet de lore mondial (trame façon corruption du Gore Magala) débloqué selon le total de victoires cumulées.
- **Campement** regroupant :
  - **Sanctuaire / Repos** : restaure PV et Endurance gratuitement.
  - **Marchand** : potions de soin, rations d'endurance, pierres à aiguiser, rations pour le compagnon.
  - **Forgeron** : améliore l'arme équipée (ATQ/DEF) contre des Orins, coût croissant.
  - **Expédition du compagnon (AFK)** : envoyez votre compagnon chasser seul pour 1h/4h/8h ; le butin se calcule sur la différence de temps réel écoulé entre le départ et le moment où vous revenez consulter le résultat — **ça fonctionne même si l'application est complètement fermée entre-temps**, sans nécessiter de service natif en arrière-plan.
- **Sauvegarde locale chiffrée AES-256** avec signature HMAC-SHA256 anti-triche (empêche la modification manuelle du fichier de sauvegarde : Orins, stats, inventaire, bestiaire, expédition en cours), sauvegarde auto chaque seconde.
- **UI Dark Mode** minimaliste avec navigation par onglets (Chasse / Compagnon / Inventaire / Camp / Codex).

## Pistes d'extension possibles (non incluses, pour aller plus loin)

- Plus de zones, de monstres et de fragments de lore écrits (la base de données `lore_database.dart` et `zone_generator.dart` sont conçues pour être étendues facilement en ajoutant des entrées).
- Notifications push locales quand une expédition du compagnon est terminée.
- Combats AFK du joueur lui-même (pas seulement du compagnon).

## Comment obtenir un .apk depuis un téléphone Android (sans PC)

Ce dossier contient tout le code source Flutter, prêt à compiler. Comme il n'existe aucun moyen de compiler un `.apk` directement depuis un tchat, voici la méthode la plus simple **entièrement gratuite** :

### Option recommandée : GitHub + Codemagic (ou GitHub Actions)

1. Crée un compte gratuit sur [github.com](https://github.com) depuis ton téléphone.
2. Crée un nouveau dépôt (ex : `apex-echoes`) et importe-y tous les fichiers de ce dossier (tu peux uploader le `.zip` fourni puis le laisser GitHub l'extraire, ou glisser les fichiers un par un depuis l'appli GitHub).
3. Va sur [codemagic.io](https://codemagic.io), connecte-toi avec GitHub, sélectionne le dépôt `apex-echoes`.
4. Choisis le workflow **Flutter App** par défaut, type de build **Android APK**.
5. Lance le build : Codemagic compile dans le cloud et te donne un lien de téléchargement du `.apk` directement utilisable sur ton téléphone (autoriser "sources inconnues" dans les paramètres Android pour l'installer).

### Alternative : FlutLab.io ou Google IDX (dans le navigateur mobile)

1. Ouvre [flutlab.io](https://flutlab.io) ou [idx.google.com](https://idx.google.com) dans Chrome sur ton téléphone.
2. Crée un nouveau projet Flutter.
3. Remplace/ajoute les fichiers un par un en respectant exactement l'arborescence de ce dossier (`lib/models/...`, `lib/engine/...`, etc.) et copie le contenu de `pubspec.yaml`.
4. Clique sur "Build APK" et télécharge le fichier généré.

## Structure du projet

```
apex_echoes/
├── pubspec.yaml
└── lib/
    ├── main.dart
    ├── core/
    │   ├── security/encryption_service.dart   (chiffrement AES-256 + HMAC anti-triche)
    │   └── database/save_service.dart         (sauvegarde locale chiffrée)
    ├── models/
    │   ├── element_type.dart
    │   ├── player_model.dart
    │   ├── monster_model.dart
    │   ├── equipment_model.dart
    │   ├── companion_model.dart
    │   ├── zone_model.dart
    │   ├── bestiary_model.dart
    │   ├── lore_model.dart
    │   └── combat_log.dart
    ├── engine/
    │   ├── combat_engine.dart          (formules de dégâts, session de combat)
    │   ├── zone_generator.dart         (rapports d'expédition procéduraux)
    │   ├── monster_generator.dart      (instanciation de monstres mis à l'échelle)
    │   ├── equipment_generator.dart    (loot procédural, rareté)
    │   └── lore_database.dart          (fragments de lore par monstre + lore mondial)
    └── screens/
        ├── game_home_screen.dart  (hub principal : chasse, sauvegarde, navigation)
        ├── combat_screen.dart     (combat textuel au tour par tour)
        ├── companion_screen.dart  (soin du compagnon / Tamagotchi)
        ├── inventory_screen.dart  (équiper / vendre)
        ├── camp_screen.dart       (Sanctuaire, Marchand, Forgeron, expédition AFK)
        └── codex_screen.dart      (Bestiaire + Lore du monde)
```
