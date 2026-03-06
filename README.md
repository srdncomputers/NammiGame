# 🕹️ GameZone Kids

Safe, fun multiplayer mini-games for kids aged 8–15. Built with **SvelteKit** and deployable to **Vercel** in minutes.

## 🎮 Games Included

| Game | Players | Category | Inspired By |
|------|---------|----------|-------------|
| Battle Quiz Royale | 2–4 | Brain | Trending trivia formats |
| Island Builder Clash | 2–4 | Creative | Minecraft / Animal Crossing |
| Squid Dash | 2–6 | Action | Battle royale mini-games |
| Ink Arena | 2–4 | Team | Splatoon's ink mechanic |
| Word Warp Speed | 2–4 | Brain | Wordle / competitive spelling |
| Monster Kart Race | 2–6 | Racing | Monster Hunter Stories |
| DrawIt Together | 2–8 | Creative | Gartic Phone |
| Math Speed Duel | 2–4 | Brain | Educational gaming trend |

## 🚀 Deploy to Vercel

### Option 1 — Vercel CLI (recommended)
```bash
npm install -g vercel
npm install
vercel
```

### Option 2 — GitHub + Vercel Dashboard
1. Push this folder to a GitHub repo
2. Go to [vercel.com/new](https://vercel.com/new)
3. Import your repo
4. Framework: **SvelteKit** (auto-detected)
5. Click **Deploy** ✅

### Option 3 — Drag & Drop
1. Run `npm run build`
2. Drag the project folder to [vercel.com/new](https://vercel.com/new)

## 💻 Local Development

```bash
npm install
npm run dev
# → http://localhost:5173
```

## 🗂️ Project Structure

```
gamezone-kids/
├── src/
│   ├── app.html          # HTML template
│   ├── app.css           # Global styles + design tokens
│   ├── lib/
│   │   ├── games.js      # All game data (easy to add more!)
│   │   └── components/
│   │       ├── Header.svelte
│   │       ├── GameCard.svelte
│   │       ├── GameModal.svelte
│   │       └── Toast.svelte
│   └── routes/
│       ├── +layout.svelte
│       └── +page.svelte  # Main platform page
├── static/               # Static assets (favicon, images)
├── svelte.config.js      # Svelte + Vercel adapter config
├── vite.config.js
├── vercel.json
└── package.json
```

## ➕ Adding a New Game

Edit `src/lib/games.js` and add an entry to the `games` array:

```js
{
  id: 'my-new-game',
  title: 'My New Game',
  subtitle: 'Brief description',
  emoji: '🎯',
  gradient: ['#FF375F', '#BF5AF2'],
  tag: 'new',           // 'hot' | 'new' | 'live'
  tagLabel: '⚡ New!',
  players: '0',
  maxPlayers: 4,
  ageRange: '8–15',
  duration: '5 min',
  description: 'Full game description here...',
  features: ['Feature 1', 'Feature 2', 'Feature 3', 'Feature 4'],
  category: 'Brain',    // 'Brain' | 'Creative' | 'Action' | 'Team' | 'Racing'
  rating: 4.5,
  reviews: '0'
}
```

## 🛡️ Safety Features (UI)
- Username-only play (no personal info)
- Chat moderation section
- Parent dashboard info page
- Anti-bullying policy display

## 📦 Tech Stack
- **SvelteKit** v2 — frontend framework
- **@sveltejs/adapter-vercel** — Vercel deployment
- **Vite** v5 — build tool
- **Clash Display + Plus Jakarta Sans** — custom fonts
- CSS custom properties — design token system

## 🎨 Design System
Colors defined in `app.css` via CSS variables:
- `--neon-1` Electric Cyan `#7DF9FF`
- `--neon-2` Neon Violet `#BF5AF2`
- `--neon-3` Hot Coral `#FF375F`
- `--neon-4` Volt Yellow `#FFD60A`
- `--neon-5` Vivid Green `#30D158`
- `--neon-6` Amber `#FF9F0A`
