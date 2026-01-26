# Algorand-Token — Tax Token (PyTeal) + Admin UI (React)

Projet blockchain **end-to-end** sur Algorand :

* **Smart contract (PyTeal)** qui applique une **taxe** lors d’une vente/transfert "sell" et envoie automatiquement la part taxée vers une **adresse de trésorerie**.
* **Interface admin (React)** pour piloter des actions **manuelles** de redistribution (Burn / LP / Rewards) depuis la trésorerie.

> Objectif portfolio : démontrer ma capacité à concevoir une logique métier on-chain simple, testable et maintenable, avec une UI d’admin claire.

---

## 1) Logique métier (ce que fait le projet)

### Taxe & trésorerie

* Le token est un **ASA** (Algorand Standard Asset).
* À chaque opération considérée comme une **vente / sell** (selon la règle implémentée dans le contrat), une **taxe** est prélevée.
* La taxe est envoyée automatiquement vers une **adresse de trésorerie** (wallet fixe / contrôlé par l’admin).
* Le reste est transféré au destinataire de la vente.

### Admin & redistribution (choix assumé : manuel)

* La trésorerie **n’auto-redistribue pas**.
* L’admin déclenche manuellement des transactions de redistribution via l’UI :

  * **Burn** : destruction (ou envoi vers une adresse de burn) selon la stratégie retenue
  * **LP** : allocation liquidité (exécution manuelle)
  * **Rewards** : envoi vers des wallets de récompense (exécution manuelle)

### Pourquoi “manuel” (et pas automatique)

* **Plus de contrôle** : l’admin décide quand/à qui/combien redistribuer.
* **Auditabilité** : chaque action admin correspond à une transaction explicite et traçable.

---

## 2) Choix techniques & sécurité (ce que je veux montrer)

### Smart contract (PyTeal)

* Logique métier **volontairement simple** : taxer et envoyer vers trésorerie.
* Code pensé pour être :

  * **Lisible** (règles explicites)
  * **Testable** (tests unitaires Python)
  * **Déployable** (scripts CLI dédiés)

### Admin UI (React)

* UI minimale : affichage du wallet connecté + actions admin (Burn / LP / Rewards).
* **Sécurité opérationnelle** : toutes les transactions sont **signées par le wallet connecté** (aucune clé privée côté frontend).

### Séparation des responsabilités (design)

* Le smart contract applique la règle **taxe → trésorerie**.
* La redistribution reste **off-chain pilotée** via l’UI admin, afin de limiter la complexité du protocole.

> Une section “Threat model” pourra être ajoutée ultérieurement sans modifier la logique métier.

---

## 3) Architecture du repository

```
Algorand-Token/
├── contracts/                 # Backend Python (PyTeal + scripts + tests)
│   ├── scripts/               # deploy, create_asa, opt-in, distribute, check_balance
│   ├── tests/                 # Tests unitaires (pytest)
│   ├── service.py             # Helpers : compilation, déploiement, transactions
│   └── tax_token.py           # Logique du smart contract (PyTeal)
└── frontend/
    └── admin/                 # Interface admin React
        ├── public/
        └── src/
```

---

## 4) Prérequis

* Python 3.x
* Node.js 18+
* Accès à un nœud Algorand (TestNet / Sandbox / provider)

---

## 5) Installation & exécution

### Backend (smart contract + scripts)

> ⚠️ Notes importantes
>
> * Le SDK Algorand Python s’installe via **`py-algorand-sdk`** (et non `algosdk`)
> * **PyTeal nécessite `setuptools`** (sinon erreur `pkg_resources` sous Python ≥ 3.12)

```bash
cd contracts

python3 -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

python -m pip install --upgrade pip setuptools wheel
pip install py-algorand-sdk pyteal python-dotenv pytest
```

### Frontend (admin UI)

```bash
cd frontend/admin
npm install
npm start
```

---

## 6) Workflow (démo locale)

### 1. Déployer l’application (smart contract)

```bash
cd contracts
source .venv/bin/activate
python scripts/deploy.py
```

### 2. Créer l’ASA (token)

```bash
python scripts/create_asa.py
```

### 3. Faire opt-in les comptes cibles

```bash
python scripts/optin_targets.py
```

### 4. Distribuer des tokens

```bash
python scripts/distribute.py
```

### 5. Vérifier les balances

```bash
python scripts/check_balance.py
```

### 6. Lancer l’UI admin

```bash
cd frontend/admin
npm start
```
