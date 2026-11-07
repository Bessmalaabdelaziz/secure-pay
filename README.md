# SecurePay

Application mobile de suivi et de vérification des paiements TPE pour les commerçants
algériens. Interface fintech sombre, français par défaut, arabe avec mise en page RTL,
montants en dinars algériens (DZD).

> **Prototype.** Les confirmations bancaires proviennent d'un **registre simulé local**.
> Un reçu importé n'est jamais une preuve d'encaissement : seule une API officielle de
> banque ou de fournisseur de paiement (SATIM / GIE Monétique) peut confirmer un règlement.

## Démarrage

```bash
npm install
npm run dev      # http://localhost:3000
npm run build    # vérification TypeScript + build de production
```

Application web mobile installable (PWA). Sur Android : ouvrir l'URL puis
« Ajouter à l'écran d'accueil ».

## Comptes de démonstration

| Rôle | E-mail | Mot de passe |
| --- | --- | --- |
| Propriétaire (accès complet) | `demo@securepay.dz` | `demo1234` |
| Employé (accès limité) | `karim@securepay.dz` | `demo1234` |
| Employée (transactions + vérification) | `nadia@securepay.dz` | `demo1234` |

La double authentification est active : le code à 6 chiffres est affiché à l'écran
(environnement de démonstration).

## Écrans

Splash, connexion / inscription, tableau de bord, transactions, nouveau paiement,
détail d'une transaction, vérification bancaire (simulée), analyse de reçu,
détection de fraude, notifications, rapports & statistiques, équipe, profil & réglages,
journal d'audit.

## Rôles et permissions

- **Propriétaire** : tout, y compris rapports, fraude, gestion de l'équipe et journal d'audit.
- **Employé** : enregistrement des ventes, reçus et transactions autorisées. Les écrans
  sensibles affichent un refus d'accès et l'action est tracée dans le journal d'audit.

## Données de démonstration

136 transactions réparties sur 45 jours, dont des cas scriptés pour chaque règle :
doublon de référence, écart attendu/confirmé, montant inhabituel, encaissement nocturne,
rafale de paiements, reçu suspect, paiement manquant, retardé et échoué.

## Architecture

- `src/store.tsx` — état applicatif persistant (localStorage), authentification,
  permissions, journal d'audit, vérification simulée.
- `src/lib/fraud.ts` — moteur de règles pondérées (score de risque, alertes).
- `src/lib/receipt.ts` — analyse documentaire simulée (contrôles de cohérence).
- `src/lib/stats.ts` — agrégats, séries temporelles, export CSV.
- `src/data/seed.ts` — génération déterministe des données de démonstration et du
  registre bancaire simulé.
- `src/i18n.ts` — dictionnaires français et arabe.
- `src/screens/*` — un écran par route, `src/components/*` — design system.

Les identifiants sont hachés (SHA-256 + sel) côté client ; les données restent sur
l'appareil. Ce prototype ne remplace pas un backend sécurisé pour la production.

## Application Android (Capacitor)

Le projet Android natif est généré avec Capacitor 8 et vit dans `android/`.
Application `com.securepay.dz`, nom affiché **SecurePay**, portrait, RTL activé,
icône adaptative et écran de démarrage générés par `@capacitor/assets`.

```bash
npm run android:sync   # build web + copie dans le projet Android
npm run android:apk    # APK release signé -> android/app/build/outputs/apk/release/
npm run android:open   # ouvre le projet dans Android Studio
```

Le build nécessite **JDK 21** et le **SDK Android 36**. Les chemins utilisés dans
cet environnement sont notés dans `android/local.properties` (à adapter, ou à
supprimer pour laisser Android Studio le régénérer).

Signature: `android/securepay-release.keystore` + `android/keystore.properties`.
Cette clé autosignée sert au partage direct de l'APK ; pour Google Play, générez
une clé de production et ne la versionnez pas.

L'APK signé est copié à la racine du projet sous le nom `SecurePay.apk`
(6,9 Mo, `com.securepay.dz`, Android 7.0+, cible Android 16).

Les polices Inter et Noto Kufi Arabic sont auto-hébergées (`public/fonts/`) :
l'application garde sa typographie sans connexion internet.

Guide d'installation en arabe : [INSTALL-AR.md](INSTALL-AR.md).
