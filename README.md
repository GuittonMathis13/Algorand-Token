# Algorand-Token — Tax Token (PyTeal) + Admin UI (React)

Projet blockchain **end-to-end** sur Algorand :  
- **Smart contract (PyTeal)** qui applique une **taxe** lors d’une vente/transfert “sell” et envoie automatiquement la part taxée vers une **adresse de trésorerie**.  
- **Interface admin (React)** pour piloter des actions **manuelles** de redistribution (Burn / LP / Rewards) depuis la trésorerie.

> Objectif portfolio : démontrer ma capacité à concevoir une logique métier on-chain simple, testable et maintenable, avec une UI d’admin claire.

---

## 1) Logique métier (ce que fait le projet)

### Taxe & trésorerie
- Le token est un **ASA** (Algorand Standard Asset).
- À chaque opération considérée comme une **vente / sell** (selon la règle implémentée dans le contrat), une **taxe** est prélevée.
- La taxe est envoyée automatiquement vers une **adresse de trésorerie** (wallet fixe / contrôlé par l’admin).
- Le reste est transféré au destinataire de la vente.

### Admin & redistribution (choix assumé : manuel)
- La trésorerie **n’auto-redistribue pas**.
- L’admin déclenche manuellement des transactions de redistribution via l’UI :
  - **Burn** : destruction (ou envoi vers une adresse de burn) selon la stratégie retenue
  - **LP** : allocation liquidité (exécution manuelle)
  - **Rewards** : envoi vers des wallets de récompense (exécution manuelle)

### Pourquoi “manuel” (et pas automatique)
- **Plus de contrôle** : l’admin décide quand/à qui/combien redistribuer.
- **Auditabilité** : chaque action admin = une transaction explicite, traçable.

---

## 2) Choix techniques & sécurité (ce que je veux montrer)

### Smart contract (PyTeal)
- Logique métier **volontairement simple** : taxer et envoyer vers trésorerie.
- Code pensé pour être :
  - **Lisible** (règles explicites)
  - **Testable** (tests unitaires Python)
  - **Déployable** (scripts CLI dédiés)

### Admin UI (React)
- UI minimale : affichage wallet + actions admin (Burn/LP/Rewards).
- **Sécurité opérationnelle** : les transactions sont **signées par le wallet connecté** (pas de clé privée côté serveur).

### Séparation des responsabilités (design)
- Le contrat applique la règle **taxe → trésorerie**.
- La redistribution reste **off-chain pilotée** (UI admin), pour limiter la complexité du protocole.

> Si tu veux, je peux ajouter ensuite une section “Threat model (simple)” (sans changer ton code), pour encore mieux parler recruteur.

---

## 3) Architecture du repo
Algorand-Token/
├── contracts/ # backend Python (PyTeal + scripts + tests)
│ ├── scripts/ # deploy, create_asa, opt-in, distribute, check_balance
│ ├── tests/ # tests unitaires (pytest)
│ ├── service.py # helpers : compilation, déploiement, tx
│ └── tax_token.py # logique du smart contract (PyTeal)
└── frontend/
└── admin/ # React admin UI
├── public/
└── src/

---

## 4) Prérequis

- Python 3.x
- Node.js 18+
- Accès à un noeud Algorand (TestNet / Sandbox / provider)

---

## 5) Installation & exécution


Backend (smart contract + scripts)
```bash
cd contracts

python3 -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

pip install py-algorand-sdk pyteal python-dotenv pytest

Frontend (admin UI)
cd frontend/admin
npm install
npm start

6) Workflow (démo locale)

Déployer l’application (smart contract)
cd contracts
source .venv/bin/activate
python scripts/deploy.py

Créer l’ASA (token)
python scripts/create_asa.py

Faire opt-in les comptes cibles
python scripts/optin_targets.py

Distribuer des tokens
python scripts/distribute.py

Vérifier les balances
python scripts/check_balance.py

Lancer l’UI admin
cd frontend/admin
npm start


