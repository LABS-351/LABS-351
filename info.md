# LABS 351 [SN] v3.51RC - MANUEL DE RÉFÉRENCE & ARCHITECTURE

**Core Platform:** AETHER Deterministic Infrastructure (Slab-based)  
**Target Architectures:** Multi-Platform System (ARM64 / x86_64 / RISC-V Bare Metal)  
**Compliance:** Low-latency Industrial Standards & Memory Sovereignty  
**Deployment:** Portable / Nomadic (Zero Installation)  

---

## Sommaire
1. Introduction Technique
2. Modèle de Distribution & Soutien LABS 351
3. Infrastructure de Calcul AETHER Privée
4. Architecture Découplée & Catalogue de Services
5. Le Trampoline : Surveillance & Diagnostic
6. Architecture VFS & Gestion des Sources
7. Noyau d'Introspection Binaire & Empreinte Machine
8. Pilotage Multi-Toolchain Asynchrone
9. Cycle de Développement & Types de Projets
10. Variables de Substitution Dynamiques
11. Téléchargements & Mode Nomade

---

## 1. Introduction Technique
LABS 351 [SN] est un contrôleur de flux binaire dynamique et évolutif, ainsi qu'un environnement de prototypage haute performance pour l'assembleur bas niveau (ARM64, x86_64 et RISC-V). Conçu pour la recherche microarchitecturale, les industries de haute technologie et le secteur de l'IoT, LABS 351 [SN] automatise la complexité des chaînes d'outils de compilation croisée. Il permet une transition fluide de la conception algorithmique en assembleur vers les environnements de production industriels sans nécessiter de configuration logicielle lourde.

---

## 2. Modèle de Distribution & Soutien LABS 351
LABS 351 [SN] adopte une philosophie de distribution hybride pour protéger ses innovations tout en servant la communauté :

* **Licence Community (SDK Public)** : Version d'accès gratuite destinée à l'apprentissage, aux étudiants et aux ingénieurs indépendants. Elle expose les en-têtes d'interface (MIT) et permet de manipuler les fonctionnalités essentielles du contrôleur de flux et du système de fichiers virtuel (VFS).
* **Soutien Financement Participatif (1 € / mois)** : Si LABS 351 vous évite l'enfer des Makefiles et accélère vos maquettages, vous pouvez soutenir le laboratoire via le bouton **Sponsor** en haut du dépôt. 100% de ce micro-don est reversé à la R&D du projet (0% de commission GitHub).
* **Licences Professionnelles (AETHER Engine)** : Versions étendues pour l'industrie, incluant nos moteurs de performance propriétaires et nos outils d'analyse critique sous contrat de licence utilisateur final (EULA).

---

## 3. Infrastructure de Calcul AETHER Privée
> [!NOTE]
> Technologie disponible exclusivement dans le framework interne et les modules professionnels haut de gamme.

Le cœur de calcul de LABS 351 [SN] est propulsé par l'infrastructure AETHER, une architecture propriétaire conçue pour l'exécution massivement parallèle et la suppression des goulots d'étranglement mémoire traditionnels :

* **Souveraineté Mémoire ($O(1)$)** : Contrairement aux gestions de tas (Heap) standards qui provoquent des allocations dynamiques et de la fragmentation, AETHER s'appuie sur un modèle de dalles contiguës (Slabs). Chaque objet bénéficie d'un accès instantané et déterministe à la ressource.
* **Déterminisme Temporel** : Le cycle de vie complet d'une session de calcul (Initialisation, allocation de 100 structures de désassemblage alignées, traitement et destruction) est stabilisé à une moyenne de 11,7 microsecondes, garantissant une réactivité prédictible pour l'analyse des flux d'instructions.
* **Optimisation de la Barrière Silicium** : Maximisation de la localité spatiale en alignant dynamiquement les structures sur les lignes de cache L1/L2 du CPU (structures mesurant exactement 64 octets). Cette architecture élimine efficacement les *Cache Miss* lors du flux d'exécution en maintenant les instructions physiquement contiguës en mémoire.

---

## 4. Architecture Découplée & Catalogue de Services
L'intelligence de LABS 351 [SN] repose sur un bus de communication asynchrone (Stateless Pipeline) où le Core ne cherche pas un binaire spécifique, mais consomme un service abstrait disponible dans le catalogue :

* **Moteurs d'Analyse Distribués** : Le désassemblage (Capstone), le Highlighting et les suggestions opèrent sous forme de boîtes noires interchangeables définies par leur clé de routage (tag).
* **Substitution Transparente (Hot-Swapping)** : L'hôte est totalement agnostique. Vous pouvez modifier un champ dans votre fichier de configuration `custom.json` pour remplacer instantanément un plugin C++ natif du Core par un script Lua remplissant le même contrat d'interface (Entrée/Sortie via conteneurs `QVariant`).
* **Modèle MIT** : Le plugin de référence `org.labs351.engine.capstone` est fourni en open source pour servir de modèle d'intégration de service.

---

## 5. Le Trampoline : Surveillance & Diagnostic
L’exécution du code assemblé est encapsulée dans une couche d'abstraction propriétaire baptisée Trampoline, offrant un débogage chirurgical et une sécurité maximale :

* **Analyseur de Conformité ABI** : Surveillance active et stricte des conventions d'appel selon la plateforme cible. Toute violation ou altération des registres non-volatils déclenche une alerte immédiate.
* **Introspection Vectorielle** : Affichage en temps réel et au cycle près de l'état des registres SSE/AVX (x86) et NEON (ARM).
* **Réconciliation de Crash** : En cas d'erreur fatale ou de faute de page dans le bac à sable, le moteur calcule l'offset statique exact et pointe la ligne source fautive directement dans l'éditeur de l'IHM.

---

## 6. Architecture VFS & Gestion des Sources
LABS 351 [SN] repose sur un système de fichiers virtuel (VFS) étanche qui isole vos actifs numériques de bout en bout :

* **Importation Sécurisée & Redirection** : Le Core ne travaille jamais directement sur vos fichiers originaux. Lors de l'ouverture d'un projet, le VFS duplique les ressources et crée automatiquement les répertoires obligatoires requis par l'application. Le dossier masqué `Work/` redirige de manière transparente toutes les opérations d'écriture relatives.
* **Étanchéité (Sandbox)** : Vous pouvez modifier, compiler et crash-tester votre assembleur sans jamais altérer vos sources de référence sur le disque dur.
* **Structure Normalisée du Projet** :
  * `src/` : Dossier racine virtualisé pour vos fichiers sources (.s, .asm).
  * `headers/` : Centralise les fichiers d'en-tête (.h, .inc) pour la résolution automatique des dépendances.
  * `build/` : Zone de sortie volatile vidée à chaque début de build contenant vos fichiers objets et binaires (.o, .exe, .dll, .a, .dylib).
  * `Work/` : (Masqué) Couche d'exécution interne sandboxée.

---

## 7. Noyau d'Introspection Binaire & Recherche
LABS 351 [SN] déconstruit le code produit pour identifier les structures machine réelles avec une autonomie absolue :

* **Parsing Haute Fidélité Indépendant** : Analyse native des formats PE, COFF et ELF. Le logiciel cartographie les segments directement depuis le binaire assemblé via ses propres parseurs autonomes. LABS 351 [SN] ne dépend d'aucune API système hôte, restant insensible aux mises à jour ou modifications des bibliothèques de l'OS.
* **Scoring Intelligence** : Détection automatique et transparente du point d’entrée, garantissant un linkage correct sans exiger de symboles explicites de la part de l'utilisateur.
* **Innovation Windows** : Outrepasse les limites natives en résolvant les liaisons complexes et l'extraction des symboles sans usage de fichiers de définitions (.def) ou de directives de compilation tierces.
* **Recherche Universelle** : Analyse active post-exécution sur l'ensemble du projet avec détection des conflits d'étiquettes (Duplicate Labels) et Quick-Jump instantané vers l'instruction coupable.

---

## 8. Pilotage Multi-Toolchain Asynchrone
Une couche d'abstraction intelligente s'interpose entre l'utilisateur et les chaînes de compilation :

* **Agnosticisme des Toolchains** : Intégration de drivers pour NASM, FASM, GNU AS et CLANG. Le passage d'un moteur d’assemblage à un autre se fait à la volée dans l’IHM sans modifier le code source original.
* **Scan Automatique & Forçage Manuel** : Un plugin natif recherche de manière asynchrone à chaque démarrage l'emplacement des outils système ou installés (LLVM, Clang, Make, Git, Windows SDK). L'utilisateur peut à tout moment forcer l'usage d'un répertoire spécifique dans l'IHM pour y injecter une toolchain croisée isolée.
* **Driver de Liaison Hybride** : Sélection automatique entre les linkers bas niveau (ld, lld) et les drivers complexes (Clang) pour l'automatisation de l'accès aux SDK système (UCRT, libSystem).

---

## 9. Cycle de Développement & Types de Projets
Trois types de profils configurables permettent de segmenter vos objectifs de build :

* **PROJET : Fonction Isolée (PROJ_ISO / Mode PIC)** : Conçu pour la validation d'une routine de calcul isolée. Le système génère du code indépendant de la position (Position Independent Code), prêt pour l'injection dynamique, avec vérification stricte de l'ABI.
* **PROJET : Application Exécutable (PROJ_EXE)** : Finalisation complète d'un logiciel pour une exécution directe par l'OS hôte avec résolution des dépendances.
* **PROJET : IoT & Embarqué (PROJ_BARE)** : Développement de micro-kernels et de firmwares pour l'embarqué. Active le mode sans bibliothèque standard (`-nostdlib`) pour un contrôle matériel total (ex: cibles RISC-V ou ARM Bare Metal).

---

## 10. Variables de Substitution Dynamiques
Balises d'injection utilisées pour piloter et forger vos lignes de commande d'outils personnalisés :

* `$SRCPATH` : Chemin absolu vers le fichier source (.s).
* `$OUTPATH` : Destination pour le fichier objet généré (.o).
* `$INCDIR` : Chemin vers le dossier miroir des en-têtes (.h / .inc).
* `$OUT` : Chemin complet du binaire final généré (EXE, DLL, A, DYLIB).
* `$OBJS` : Liste consolidée de tous les fichiers objets actifs du projet.
* `$LIBS` : Liste des chemins vers les bibliothèques externes configurées dans l'onglet Liaison.

---

## 11. Téléchargements & Mode Nomade
LABS 351 [SN] est une application rigoureusement **portable et nomade**. Elle ne requiert aucune procédure d'installation ni de modification des registres de votre système. Il vous suffit d'extraire l'archive pour exécuter le cockpit de développement.

* **macOS (Apple Silicon / Intel)** : `LABS351_Suite_Mac.zip` *(Disponible dès validation du contrat EULA)*
* **Windows (x64 / LLVM)** : `LABS351_Suite_Win.zip` *(Disponible dès validation du contrat EULA)*

---
© 2026 LABS 351 [SN] – All rights reserved. Proprietary Technology.
