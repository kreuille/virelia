# Claude Telegram Bridge

Projet : intégration entre Claude Code et Telegram pour recevoir des alertes et envoyer des instructions à distance depuis un téléphone.

## Structure

```
C:\claude-telegram\
├── config.json        ← Configuration (token, chat_id, options)
├── utils.py           ← Fonctions partagées (send_message, get_updates)
├── notify.py          ← Déclenché par les hooks Stop / Notification / PostToolUse
├── approval.py        ← Déclenché par PreToolUse, attend approbation synchrone
├── bot_listener.py    ← Tourne en arrière-plan, reçoit les messages Telegram
└── start_bot.bat      ← Lance bot_listener.py
```

Hooks Claude Code configurés dans : `%USERPROFILE%\.claude\settings.json`

## Config actuelle

```json
{
  "bot_token": "8338819786:AAEGVbufMJ3DKa5lC1JTWV5p_5ijwPVESi4",
  "chat_id": "5003668982",
  "approval_timeout_seconds": 120,
  "notify_on_stop": true,
  "notify_on_notification": true,
  "notify_on_post_tool": true,
  "approval_on_pre_tool": false,
  "approval_tools": ["Bash", "Write", "Edit"]
}
```

## Problème en cours à résoudre

Le bot ne reçoit rien sur Telegram. Tester dans l'ordre :

1. Vérifier que Python est installé : `python --version`
2. Vérifier que `requests` est installé : `python -c "import requests; print('OK')"`
3. Tester l'envoi direct à l'API Telegram :
```
python -c "import requests; r = requests.post('https://api.telegram.org/bot8338819786:AAEGVbufMJ3DKa5lC1JTWV5p_5ijwPVESi4/sendMessage', json={'chat_id': '5003668982', 'text': 'Test'}); print(r.json())"
```
4. Si erreur réseau → vérifier proxy/firewall Windows
5. Si `{"ok": false}` → le token ou chat_id est invalide
6. Si `{"ok": true}` → le problème vient de bot_listener.py, pas de l'API

## Commandes utiles

```bash
# Lancer le bot listener
cd C:\claude-telegram
python bot_listener.py

# Tester notify.py manuellement
echo '{"reason": "test"}' | python C:\claude-telegram\notify.py stop

# Vérifier les hooks Claude Code
type %USERPROFILE%\.claude\settings.json
```

## Comportement attendu

- `bot_listener.py` démarre → message Telegram "Bot démarré"
- Claude Code s'arrête → alerte Telegram "Claude Code s'est arrêté"
- `/send mon texte` depuis Telegram → texte copié dans presse-papiers Windows
- `/status` → confirmation que le bot tourne
- `/help` → liste des commandes

## OS

Windows 11, utilisateur : Arnaud, profil : `C:\Users\Arnau`
Bureau : `C:\Users\Arnau\OneDrive\Bureau`
