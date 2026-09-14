# POC-VSORA
# 🚀 VSORA AI Stack Validation - Proof of Concept (POC)

Ce projet est une démonstration technique d'un framework d'intégration et de validation de bout en bout pour une stack logicielle d'inférence IA (Edge/Accélérateurs matériels). 

Il a été conçu pour illustrer les bonnes pratiques de **QA Automation** appliquées à l'**AI Edge Engineering** : test de non-régression numérique, profilage de performances et architecture orientée objet pour le support multi-modèles.

---

🎯 Objectifs

L'objectif de ce POC est de simuler la validation d'une *release* logicielle (ex: nouveau compilateur, nouveau runtime d'inférence) en la comparant à une référence absolue (Golden Reference), tout en respectant les contraintes d'une intégration continue (CI/CD).

 Compétences techniques démontrées :
- **Architecture logicielle :** Utilisation du pattern *Adapter/Strategy* pour découpler les tests du moteur d'exécution (support multi-modèles et multi-runtimes).
- **Validation Numérique :** Comparaison de tenseurs (Similarité Cosinus, Erreur absolue maximale, Accord Top-1).
- **Performance Engineering :** Mesure de la distribution de latence (p50, p95, p99), du débit (images/sec) et test de charge par tailles de batch dynamiques.
- **Reporting & Tracking :** Génération de rapports Go/No-Go qualifiés et simulation d'un outil de suivi de tickets (Jira-like).

---

## 🛠️ Architecture du Framework

Le code est structuré autour d'une interface abstraite `InferenceBackend`. Cela permet d'isoler la logique de test de l'implémentation du moteur d'IA.

1. **`PyTorchReferenceBackend`** : Incarne la "Golden Reference" (FP32). C'est la vérité terrain.
2. **`ONNXTargetBackend`** : Simule le runtime cible optimisé (la release à tester).
3. **Suite de tests** : Exécute des inférences à travers l'interface commune et compare les sorties.

*Note : L'ajout du futur runtime matériel VSORA ne nécessiterait que la création d'une nouvelle classe héritant de `InferenceBackend`, sans modifier une seule ligne des tests.*

---

## 🚦 Comment exécuter le POC

Ce POC a été packagé pour s'exécuter de manière transparente et autonome dans **Google Colab**, sans nécessiter de configuration GPU ou d'accès matériel spécifique.

1. Téléchargez le fichier `POC_VSORA.ipynb`.
2. Rendez-vous sur [Google Colab](https://colab.research.google.com/).
3. Cliquez sur **Fichier > Importer le notebook** et uploadez le fichier.
4. Exécutez la première cellule pour installer les dépendances requises (`torch`, `onnxruntime`, `pandas`, etc.).
5. Exécutez les cellules de haut en bas ou cliquez sur **Exécution > Tout exécuter**.

---

## 📊 Métriques et Critères d'Acceptation (Go/No-Go)

Le pipeline génère un rapport final basé sur des critères de sévérité précis :
- **BLOCKER** : Désaccord de prédiction finale (Top-1) ou crash du runtime sous charge. Entraîne le rejet immédiat de la release.
- **MAJOR** : Dégradation de la précision numérique (Similarité Cosinus < 0.999) ou pics de latence (p99 > 150ms).
- **MINOR/INFO** : Comportement nominal ou avertissement sans impact sur l'expérience utilisateur.

---

## 🚧 Limites assumées et Prochaines étapes (Production)

Ce POC est une abstraction volontairement simplifiée pour être exécutable en quelques minutes. Dans un environnement de production réel chez VSORA, les évolutions suivantes seraient nécessaires :

1. **Intégration Hardware :** Remplacement de ONNX Runtime par les SDK/Drivers bas niveau de l'accélérateur matériel, incluant la mesure des temps de transfert mémoire (PCIe / Host-to-Device).
2. **Datasets Réels :** Remplacement des tenseurs aléatoires par des générateurs de données injectant des sous-ensembles de validation calibrés (COCO, ImageNet) pour calculer des mAP ou Accuracy réelles.
3. **Stress Testing Asynchrone :** Injection de requêtes concurrentes via gRPC/REST pour saturer le moteur de serving, au-delà de simples boucles synchrones.
4. **CI/CD :** Conteneurisation (Docker) et déclenchement *nightly* via GitHub Actions ou GitLab CI sur des *runners* hébergeant le matériel cible.

