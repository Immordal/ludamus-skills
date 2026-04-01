cat > README.md << 'EOF'
# ludamus-skills

Claude skill for managing RPG game sessions in [Ludamus](https://github.com/fancysnake/ludamus) (zagrajmy.net).

## Install
```
npx skills add https://github.com/Immordal/ludamus-skills
```

## What it does

- Browse sessions at events
- Sign up / drop out of sessions
- Find games from your local PDF collection
- Help write session pitches
- Submit session proposals
- Manage encounters (personal meetups)
- Edit profile

## Status

Browser automation (Playwright / agent-browser) — no API yet. Will be updated when JSON API becomes available.
EOF