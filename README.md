<p align="center"><img src="docs/icon.png" width="120" alt="Icône de La Map des Gueux : une vieille carte au trésor"></p>

<h1 align="center">La Map des Gueux · Android</h1>

<p align="center">
Coque Android minimale autour de l'appli web <b>La Map des Gueux</b>,<br>
la carte collaborative des manifestations en direct, sans compte.
</p>

<p align="center">
<img alt="licence AGPL-3.0" src="https://img.shields.io/badge/licence-AGPL--3.0-blue">
<img alt="Android 7.0+" src="https://img.shields.io/badge/Android-7.0%2B-3ddc84">
<img alt="aucune dépendance tierce" src="https://img.shields.io/badge/d%C3%A9pendances-aucune-lightgrey">
</p>

> ⚠️ **Projet non compilé par son auteur.** Il a été écrit sans Android SDK sous la main. Le code est court (un seul fichier Java) et suit l'API standard de la WebView, mais le premier build dans Android Studio est le vrai test. Si une erreur apparaît, ouvrez une *issue* avec le message complet.

## En deux mots

Cette appli **n'embarque aucune logique** : elle affiche votre serveur La Map des Gueux dans une WebView verrouillée sur son adresse. Les signalements, les photos et la carte restent sur votre serveur.

- Aucune bibliothèque tierce, aucun pont JavaScript, aucune sauvegarde cloud (`allowBackup=false`).
- Écran protégé par défaut : pas de capture d'écran, pas d'aperçu dans les applis récentes.
- GPS et caméra autorisés **uniquement pour l'adresse de votre serveur** (jamais le micro).

## Ce qu'il vous faut

| Quoi | Pourquoi |
|---|---|
| Le serveur [La Map des Gueux](https://github.com/VOTRE-COMPTE/map-des-gueux) lancé (`node server.js`, Node 22.13+) | c'est lui qui fait tout le travail |
| **HTTPS** pour un usage réel | sans HTTPS, Android bloque la caméra et le GPS (`localhost` fait exception, voir plus bas) |
| [Android Studio](https://developer.android.com/studio) | Gradle 8.7 et le plugin Android 8.5.2 se téléchargent tout seuls |

## Démarrage rapide (test sur votre ordinateur)

1. Lancez le serveur :
   ```bash
   cd map-des-gueux
   node server.js                       # http://localhost:8080
   ```
2. Branchez le téléphone (débogage USB activé) ou lancez un émulateur, puis :
   ```bash
   adb reverse tcp:8080 tcp:8080        # le téléphone voit « localhost:8080 »
   ```
3. Ouvrez ce dossier dans Android Studio (*File → Open*), attendez la synchronisation Gradle, puis ▶ **Run** (variante `debug`).

`localhost` est traité comme une adresse sûre par Android : la caméra et le GPS fonctionnent sans HTTPS.

## Configuration

Tout se règle dans [`gradle.properties`](gradle.properties) :

| Propriété | Rôle |
|---|---|
| `vigieUrl` | adresse publique HTTPS de votre serveur (version *release*) |
| `vigieDebugUrl` | `http://localhost:8080` par défaut (version *debug*) |

Le nom affiché est dans `app/src/main/res/values/strings.xml` et l'identifiant de l'appli (`org.mapdesgueux.app`) dans `app/build.gradle.kts`.

## Fabriquer un APK installable

*Build → Generate Signed App Bundle / APK → APK*.

- Gardez votre fichier de signature (`*.keystore`) **hors du dépôt** : il est déjà dans `.gitignore`. Sans lui, vous ne pourrez plus publier de mise à jour.
- Pour installer sans passer par le Play Store : copiez l'APK sur le téléphone et autorisez les « sources inconnues » pour votre gestionnaire de fichiers.

## Comportement de la coque

- Les liens vers d'autres sites s'ouvrent dans le navigateur.
- `tel:`, `sms:` et `mailto:` (bouton SOS) ouvrent les applis du téléphone.
- `javascript:`, `intent:` et `file:` sont refusés.
- « Choisir un fichier » ouvre le sélecteur Android.
- Une page « Pas de réseau » propose un bouton Réessayer.
- Pour désactiver la protection d'écran, mettez `secure_screen` à `false` dans `res/values/bools.xml`.

## Limites à connaître

- **Pas de notifications push** : la WebView Android ne gère pas Web Push. Pour les alertes appli fermée, installez plutôt la version web depuis Chrome (« Installer l'application »). Le reste (carte, alertes de proximité appli ouverte, photos) fonctionne.
- Le cache de carte hors ligne dépend de la WebView, moins fiable que Chrome sur un réseau saturé.
- **Play Store** : ce type d'applis peut en être retiré. Un APK distribué directement ou la version web installable évitent cette dépendance.
- Cette appli ne remplace ni un conseil juridique ni les précautions de sécurité du serveur : voir le `README` et `docs/` du serveur.

## Structure

```
app/src/main/java/org/mapdesgueux/app/MainActivity.java   toute la logique
app/src/main/AndroidManifest.xml                          permissions (réseau, caméra, localisation)
app/src/main/res/                                         icônes, textes, configuration réseau
gradle.properties                                         adresse du serveur
```

## Licence

[AGPL-3.0](https://www.gnu.org/licenses/agpl-3.0.html). Si vous publiez une version modifiée (APK compris), publiez aussi son code source.
La Map des Gueux est un fork de [Cortège](https://github.com/n-e-r-u/cortege) (n-e-r-u) ; ce travail repose sur le sien.
