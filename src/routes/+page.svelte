<script>
  import '../app.css';
  import Header from '$lib/components/Header.svelte';
  import GameCard from '$lib/components/GameCard.svelte';
  import GameModal from '$lib/components/GameModal.svelte';
  import Toast from '$lib/components/Toast.svelte';
  import { games, categories, stats } from '$lib/games.js';

  // State
  let activeCategory = 'All';
  let selectedGame = null;
  let modalOpen = false;
  let toastMsg = '';
  let toastVisible = false;
  let searchQuery = '';

  // Filter
  $: filtered = games.filter(g => {
    const inCat = activeCategory === 'All' || g.category === activeCategory;
    const inSearch = !searchQuery || g.title.toLowerCase().includes(searchQuery.toLowerCase());
    return inCat && inSearch;
  });

  function openGame(game) {
    selectedGame = game;
    modalOpen = true;
  }

  function closeModal() { modalOpen = false; }

  let toastTimer;
  function showToast(msg) {
    clearTimeout(toastTimer);
    toastMsg = msg;
    toastVisible = true;
    toastTimer = setTimeout(() => toastVisible = false, 2800);
  }

  function handleLaunch(e) {
    showToast(`🎮 Finding players for ${e.detail.title}...`);
  }

  function handleInvite() {
    const code = 'GZKD-' + Math.random().toString(36).substring(2,6).toUpperCase();
    if (typeof navigator !== 'undefined' && navigator.clipboard) {
      navigator.clipboard.writeText(code);
    }
    showToast(`✅ Room code ${code} copied!`);
  }

  // Leaderboard mock data
  const leaders = [
    { rank: 1, emoji: '🐱', name: 'StarCat42',   pts: '24,450', badge: '🥇' },
    { rank: 2, emoji: '🐶', name: 'DogWizard',   pts: '21,200', badge: '🥈' },
    { rank: 3, emoji: '🦊', name: 'FoxKid99',    pts: '19,840', badge: '🥉' },
    { rank: 4, emoji: '🐸', name: 'FrogJumper',  pts: '18,760', badge: '4'  },
    { rank: 5, emoji: '🐼', name: 'PandaPower',  pts: '17,990', badge: '5'  },
  ];

  const friends = [
    { emoji: '🐱', name: 'StarCat42',  status: 'In Quiz Battle', online: true },
    { emoji: '🐶', name: 'DogWizard',  status: 'In Ink Arena',   online: true },
    { emoji: '🐸', name: 'FrogJumper', status: 'Idle',           online: true },
    { emoji: '🐧', name: 'IcePenguin', status: 'Offline',        online: false },
  ];

  let roomCode = 'FOXK-7821';
  function copyCode() {
    if (typeof navigator !== 'undefined' && navigator.clipboard) {
      navigator.clipboard.writeText(roomCode);
    }
    showToast('✅ Room code copied to clipboard!');
  }
</script>

<svelte:head>
  <title>GameZone Kids — Play With Friends!</title>
  <meta name="description" content="Safe multiplayer mini-games for kids aged 8–15. Play Quiz Battle, Island Builder, Ink Arena & more with friends!" />
</svelte:head>

<Header on:invite={handleInvite} />

<!-- ──────────── HERO ──────────── -->
<section class="hero">
  <div class="hero-inner">
    <div class="hero-content">
      <span class="badge-safe tag tag-live">🛡️ Safe for Ages 8–15</span>
      <h1>
        Play <em class="shimmer">Awesome</em> Games<br>
        With <em class="shimmer-2">Your Friends!</em>
      </h1>
      <p>8 trending multiplayer mini-games. Challenge friends, climb leaderboards, and have a blast — all in a safe, parent-approved space.</p>

      <div class="hero-actions">
        <button class="btn-hero-main" on:click={() => openGame(games[0])}>
          🚀 Play Now — It's Free
        </button>
        <button class="btn-hero-sec" on:click={handleInvite}>
          🔗 Invite Friends
        </button>
      </div>

      <div class="hero-avatars">
        <div class="avatar-stack">
          {#each ['🐱','🐶','🦊','🐸','🐼','🐧'] as av}
            <span class="ha">{av}</span>
          {/each}
        </div>
        <span class="ha-text">+4,821 kids playing right now</span>
      </div>
    </div>

    <div class="hero-cards">
      {#each games.slice(0,3) as game, i}
        <div
          class="hero-mini-card"
          style="--delay:{i*0.12}s; --g1:{game.gradient[0]}; --g2:{game.gradient[1]}"
          on:click={() => openGame(game)}
          on:keydown={(e) => e.key === 'Enter' && openGame(game)}
          role="button"
          tabindex="0"
        >
          <span class="hmc-emoji">{game.emoji}</span>
          <div>
            <p class="hmc-title">{game.title}</p>
            <p class="hmc-stat">
              <span class="pulse-dot" style="width:6px;height:6px"></span>
              {game.players} playing
            </p>
          </div>
        </div>
      {/each}
    </div>
  </div>

  <!-- Stats bar -->
  <div class="stats-bar">
    {#each stats as s}
      <div class="stat-item">
        <span class="stat-i">{s.icon}</span>
        <span class="stat-v">{s.value}</span>
        <span class="stat-l">{s.label}</span>
      </div>
    {/each}
  </div>
</section>

<!-- ──────────── GAMES ──────────── -->
<section class="games-section" id="games">
  <div class="section-header">
    <div>
      <h2>🎮 Trending Games</h2>
      <p>All games support live multiplayer — invite friends with a room code</p>
    </div>
    <div class="search-wrap">
      <span class="search-icon">🔍</span>
      <input
        class="search"
        type="text"
        placeholder="Search games..."
        bind:value={searchQuery}
      />
    </div>
  </div>

  <!-- Category filter -->
  <div class="categories">
    {#each categories as cat}
      <button
        class="cat-btn"
        class:active={activeCategory === cat}
        on:click={() => activeCategory = cat}
      >
        {cat}
      </button>
    {/each}
  </div>

  <!-- Grid -->
  {#if filtered.length > 0}
    <div class="games-grid">
      {#each filtered as game (game.id)}
        <GameCard
          {game}
          on:open={(e) => openGame(e.detail)}
        />
      {/each}
    </div>
  {:else}
    <div class="no-results">
      <span>🔍</span>
      <p>No games found for "{searchQuery}"</p>
    </div>
  {/if}
</section>

<!-- ──────────── BOTTOM: Leaderboard + Friends ──────────── -->
<section class="bottom-grid" id="leaderboard">

  <!-- Leaderboard -->
  <div class="panel">
    <h3>🏆 Weekly Champions</h3>
    <div class="lb-list">
      {#each leaders as l}
        <div class="lb-row">
          <span class="lb-badge">{l.badge}</span>
          <span class="lb-av">{l.emoji}</span>
          <span class="lb-name">{l.name}</span>
          <span class="lb-pts">{l.pts} pts</span>
        </div>
      {/each}
    </div>
    <button class="panel-btn" on:click={() => showToast('🏆 Full leaderboard coming soon!')}>
      See Full Leaderboard →
    </button>
  </div>

  <!-- Friends -->
  <div class="panel" id="friends">
    <h3>👥 Friends Online</h3>
    <div class="friends-list">
      {#each friends as f}
        <div class="friend-row">
          <span class="f-av">{f.emoji}</span>
          <div class="f-info">
            <span class="f-name">{f.name}</span>
            <span class="f-status" class:online={f.online}>{f.online ? '🟢' : '⚫'} {f.status}</span>
          </div>
          {#if f.online}
            <button class="f-join" on:click={() => showToast(`👋 Joining ${f.name}'s game!`)}>
              Join
            </button>
          {/if}
        </div>
      {/each}
    </div>
    <button class="panel-btn" on:click={() => showToast('👥 Friend search coming soon!')}>
      Find More Friends →
    </button>
  </div>

  <!-- Invite card -->
  <div class="invite-card">
    <div class="invite-inner">
      <div class="invite-icon">🎉</div>
      <h3>Play With Friends!</h3>
      <p>Share your room code — up to 4 friends can join instantly</p>

      <div class="code-box">
        <span class="code-val">{roomCode}</span>
        <button class="code-copy" on:click={copyCode}>Copy</button>
      </div>

      <div class="share-chips">
        <button on:click={() => showToast('💬 Sent to chat!')}>💬 Chat</button>
        <button on:click={() => showToast('📧 Email sent!')}>📧 Email</button>
        <button on:click={() => showToast('🔗 Link copied!')}>🔗 Link</button>
        <button on:click={() => showToast('📱 WhatsApp message sent!')}>📱 WhatsApp</button>
      </div>
    </div>
  </div>

</section>

<!-- ──────────── SAFE PLAY ──────────── -->
<section class="safe-section" id="safe">
  <h2>🛡️ Safe by Design</h2>
  <p>We put kids' safety first — always.</p>
  <div class="safe-grid">
    {#each [
      { icon:'🔒', title:'No Personal Info', desc:'Kids play with usernames only. No real names, emails, or locations required.' },
      { icon:'🚫', title:'Chat Moderation', desc:'AI-powered chat filter blocks inappropriate words in real-time, 24/7.' },
      { icon:'👨‍👩‍👧', title:'Parent Dashboard', desc:'Parents get weekly reports, can set time limits and review activity.' },
      { icon:'🤝', title:'Anti-Bullying', desc:'One-click report system. Bullying or toxic behavior leads to immediate bans.' },
    ] as s}
      <div class="safe-card">
        <span class="safe-icon">{s.icon}</span>
        <h4>{s.title}</h4>
        <p>{s.desc}</p>
      </div>
    {/each}
  </div>
</section>

<!-- ──────────── FOOTER ──────────── -->
<footer>
  <div class="footer-inner">
    <div class="footer-logo">🕹️ GameZone <em>Kids</em></div>
    <div class="footer-links">
      <a href="#safe">Safety</a>
      <a href="#" on:click|preventDefault={() => showToast('📧 support@gamezonekids.com')}>Support</a>
      <a href="#" on:click|preventDefault={() => showToast('📜 Privacy policy page coming soon!')}>Privacy</a>
      <a href="#" on:click|preventDefault={() => showToast('👨‍👩‍👧 Parents guide coming soon!')}>For Parents</a>
    </div>
    <p class="footer-copy">© 2026 GameZone Kids · Safe gaming for ages 8–15</p>
  </div>
</footer>

<!-- Modal -->
<GameModal
  game={selectedGame}
  open={modalOpen}
  on:close={closeModal}
  on:launch={handleLaunch}
  on:invite={handleInvite}
/>

<!-- Toast -->
<Toast message={toastMsg} visible={toastVisible} />

<style>
  /* ── Hero ── */
  .hero {
    position: relative;
    z-index: 1;
    padding: 120px 32px 60px;
    max-width: 1280px;
    margin: 0 auto;
  }

  .hero-inner {
    display: grid;
    grid-template-columns: 1fr auto;
    gap: 60px;
    align-items: center;
    margin-bottom: 50px;
  }

  .hero-content { max-width: 580px; }

  .badge-safe { margin-bottom: 20px; }

  h1 {
    font-size: clamp(2.4rem, 5vw, 4rem);
    line-height: 1.1;
    margin: 12px 0 18px;
    letter-spacing: -0.035em;
  }

  .shimmer {
    font-style: normal;
    background: linear-gradient(90deg, var(--neon-1), var(--neon-2), var(--neon-1));
    background-size: 200%;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    animation: shimmer 3s linear infinite;
  }

  .shimmer-2 {
    font-style: normal;
    background: linear-gradient(90deg, var(--neon-4), var(--neon-6), var(--neon-3), var(--neon-4));
    background-size: 200%;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    animation: shimmer 4s linear infinite;
  }

  .hero-content > p {
    font-size: 1rem;
    color: var(--muted);
    line-height: 1.65;
    margin-bottom: 28px;
    font-weight: 500;
  }

  .hero-actions { display: flex; gap: 12px; flex-wrap: wrap; margin-bottom: 28px; }

  .btn-hero-main {
    padding: 15px 32px;
    background: linear-gradient(135deg, var(--neon-1), var(--neon-2));
    border: none;
    border-radius: 14px;
    color: #050510;
    font-weight: 800;
    font-size: 1rem;
    transition: opacity 0.2s, transform 0.2s;
    box-shadow: 0 8px 32px rgba(125,249,255,0.3);
  }
  .btn-hero-main:hover { opacity: 0.85; transform: translateY(-2px); }

  .btn-hero-sec {
    padding: 15px 28px;
    background: var(--glass-b);
    border: 1px solid var(--border-h);
    border-radius: 14px;
    color: var(--text);
    font-weight: 700;
    font-size: 0.95rem;
    transition: background 0.2s, transform 0.2s;
  }
  .btn-hero-sec:hover { background: rgba(255,255,255,0.1); transform: translateY(-2px); }

  .hero-avatars {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .avatar-stack { display: flex; }
  .ha {
    width: 32px; height: 32px;
    border-radius: 50%;
    background: linear-gradient(135deg, var(--neon-1), var(--neon-2));
    border: 2px solid var(--bg);
    margin-left: -6px;
    display: flex; align-items: center; justify-content: center;
    font-size: 1rem;
    animation: float 3s ease-in-out infinite;
  }
  .ha:nth-child(1){margin-left:0; animation-delay:0s}
  .ha:nth-child(2){animation-delay:0.15s}
  .ha:nth-child(3){animation-delay:0.3s}
  .ha:nth-child(4){animation-delay:0.45s}
  .ha:nth-child(5){animation-delay:0.6s}
  .ha:nth-child(6){animation-delay:0.75s}

  .ha-text { font-size: 0.8rem; font-weight: 700; color: var(--muted); }

  /* Hero mini cards */
  .hero-cards {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .hero-mini-card {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 14px 18px;
    background: var(--glass-b);
    border: 1px solid var(--border-h);
    border-radius: 16px;
    cursor: pointer;
    transition: all 0.25s;
    animation: fadeUp 0.5s ease both;
    animation-delay: var(--delay);
    width: 240px;
    outline: none;
  }
  .hero-mini-card:hover {
    background: rgba(255,255,255,0.08);
    transform: translateX(-4px);
  }

  .hmc-emoji {
    font-size: 2rem;
    flex-shrink: 0;
    background: linear-gradient(135deg, var(--g1), var(--g2));
    width: 46px; height: 46px;
    border-radius: 12px;
    display: flex; align-items: center; justify-content: center;
  }

  .hmc-title { font-weight: 700; font-size: 0.85rem; margin-bottom: 3px; }
  .hmc-stat  { display: flex; align-items: center; gap: 6px; font-size: 0.72rem; color: var(--neon-5); font-weight: 700; }

  /* Stats bar */
  .stats-bar {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    padding: 24px 28px;
    background: var(--glass);
    border: 1px solid var(--border);
    border-radius: var(--radius-lg);
  }

  .stat-item {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 4px 8px;
  }
  .stat-i { font-size: 1.6rem; }
  .stat-v { font-family: 'Clash Display', sans-serif; font-size: 1.2rem; font-weight: 700; color: var(--neon-1); }
  .stat-l { font-size: 0.72rem; color: var(--muted); font-weight: 600; line-height: 1.2; }

  /* ── Games section ── */
  .games-section {
    position: relative;
    z-index: 1;
    padding: 60px 32px;
    max-width: 1280px;
    margin: 0 auto;
  }

  .section-header {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 20px;
    margin-bottom: 28px;
    flex-wrap: wrap;
  }

  .section-header h2 { font-size: 1.8rem; margin-bottom: 4px; }
  .section-header p  { font-size: 0.85rem; color: var(--muted); font-weight: 500; }

  .search-wrap {
    position: relative;
    display: flex;
    align-items: center;
  }
  .search-icon {
    position: absolute;
    left: 14px;
    font-size: 0.85rem;
    pointer-events: none;
  }
  .search {
    padding: 10px 16px 10px 36px;
    background: var(--glass-b);
    border: 1px solid var(--border);
    border-radius: 12px;
    color: var(--text);
    font-family: inherit;
    font-size: 0.85rem;
    width: 220px;
    transition: border-color 0.2s;
    outline: none;
  }
  .search:focus { border-color: rgba(125,249,255,0.4); }
  .search::placeholder { color: var(--muted); }

  .categories {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
    margin-bottom: 28px;
  }

  .cat-btn {
    padding: 8px 18px;
    background: var(--glass);
    border: 1px solid var(--border);
    border-radius: 50px;
    color: var(--muted);
    font-weight: 700;
    font-size: 0.82rem;
    transition: all 0.2s;
  }
  .cat-btn:hover { border-color: var(--border-h); color: var(--text); }
  .cat-btn.active {
    background: linear-gradient(135deg, rgba(125,249,255,0.15), rgba(191,90,242,0.15));
    border-color: rgba(125,249,255,0.35);
    color: var(--neon-1);
  }

  .games-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 20px;
  }

  .no-results {
    text-align: center;
    padding: 60px;
    color: var(--muted);
  }
  .no-results span { font-size: 3rem; display: block; margin-bottom: 12px; }

  /* ── Bottom grid ── */
  .bottom-grid {
    position: relative;
    z-index: 1;
    display: grid;
    grid-template-columns: 1fr 1fr 1.2fr;
    gap: 20px;
    padding: 0 32px 60px;
    max-width: 1280px;
    margin: 0 auto;
  }

  .panel {
    background: var(--glass);
    border: 1px solid var(--border);
    border-radius: var(--radius-lg);
    padding: 24px;
  }

  .panel h3 {
    font-size: 1.05rem;
    margin-bottom: 20px;
    font-family: 'Clash Display', sans-serif;
  }

  /* Leaderboard */
  .lb-list { display: flex; flex-direction: column; gap: 4px; margin-bottom: 18px; }
  .lb-row {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 10px 12px;
    border-radius: 12px;
    transition: background 0.2s;
  }
  .lb-row:hover { background: var(--glass-b); }
  .lb-badge { font-size: 1rem; width: 22px; text-align: center; }
  .lb-av {
    width: 34px; height: 34px;
    background: var(--glass-b);
    border: 1px solid var(--border);
    border-radius: 50%;
    display: flex; align-items: center; justify-content: center;
    font-size: 1rem;
  }
  .lb-name { flex: 1; font-weight: 700; font-size: 0.85rem; }
  .lb-pts  { font-weight: 800; color: var(--neon-4); font-size: 0.82rem; }

  /* Friends */
  .friends-list { display: flex; flex-direction: column; gap: 4px; margin-bottom: 18px; }
  .friend-row {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 10px 12px;
    border-radius: 12px;
    transition: background 0.2s;
  }
  .friend-row:hover { background: var(--glass-b); }
  .f-av {
    width: 34px; height: 34px;
    background: var(--glass-b);
    border: 1px solid var(--border);
    border-radius: 50%;
    display: flex; align-items: center; justify-content: center;
    font-size: 1rem;
    flex-shrink: 0;
  }
  .f-info { flex: 1; }
  .f-name   { display: block; font-weight: 700; font-size: 0.85rem; }
  .f-status { display: block; font-size: 0.7rem; color: var(--muted); font-weight: 600; }
  .f-status.online { color: var(--neon-5); }

  .f-join {
    padding: 5px 12px;
    background: rgba(48,209,88,0.1);
    border: 1px solid rgba(48,209,88,0.25);
    border-radius: 50px;
    color: var(--neon-5);
    font-size: 0.72rem;
    font-weight: 700;
    transition: background 0.2s;
  }
  .f-join:hover { background: rgba(48,209,88,0.2); }

  .panel-btn {
    width: 100%;
    padding: 11px;
    background: var(--glass-b);
    border: 1px solid var(--border);
    border-radius: 12px;
    color: var(--muted);
    font-weight: 700;
    font-size: 0.82rem;
    transition: all 0.2s;
  }
  .panel-btn:hover { color: var(--text); border-color: var(--border-h); }

  /* Invite card */
  .invite-card {
    background: linear-gradient(135deg, #160D2E, #1A0828);
    border: 1px solid rgba(191,90,242,0.25);
    border-radius: var(--radius-lg);
    overflow: hidden;
    position: relative;
  }
  .invite-card::before {
    content: '';
    position: absolute;
    top: -50%; left: -20%;
    width: 200%; height: 200%;
    background: radial-gradient(ellipse 60% 50% at 50% 30%, rgba(191,90,242,0.15), transparent 70%);
    pointer-events: none;
  }

  .invite-inner {
    padding: 28px;
    text-align: center;
    position: relative;
    z-index: 1;
  }

  .invite-icon { font-size: 2.5rem; margin-bottom: 12px; }
  .invite-inner h3 { font-size: 1.15rem; margin-bottom: 6px; }
  .invite-inner > p { font-size: 0.8rem; color: var(--muted); font-weight: 500; margin-bottom: 20px; }

  .code-box {
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: rgba(0,0,0,0.3);
    border: 1px solid rgba(191,90,242,0.25);
    border-radius: 14px;
    padding: 14px 16px;
    margin-bottom: 14px;
  }
  .code-val {
    font-family: 'Clash Display', sans-serif;
    font-size: 1.5rem;
    letter-spacing: 4px;
    color: var(--neon-2);
  }
  .code-copy {
    padding: 8px 18px;
    background: var(--neon-2);
    color: #050510;
    border: none;
    border-radius: 10px;
    font-weight: 800;
    font-size: 0.8rem;
    transition: opacity 0.2s;
  }
  .code-copy:hover { opacity: 0.85; }

  .share-chips { display: flex; gap: 8px; flex-wrap: wrap; justify-content: center; }
  .share-chips button {
    padding: 7px 14px;
    background: rgba(255,255,255,0.07);
    border: 1px solid rgba(255,255,255,0.12);
    border-radius: 50px;
    color: var(--text);
    font-size: 0.75rem;
    font-weight: 700;
    transition: background 0.2s;
  }
  .share-chips button:hover { background: rgba(255,255,255,0.14); }

  /* ── Safe section ── */
  .safe-section {
    position: relative;
    z-index: 1;
    padding: 60px 32px;
    max-width: 1280px;
    margin: 0 auto;
    text-align: center;
  }

  .safe-section h2 { font-size: 1.8rem; margin-bottom: 6px; }
  .safe-section > p { color: var(--muted); font-size: 0.95rem; margin-bottom: 36px; font-weight: 500; }

  .safe-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 18px;
  }

  .safe-card {
    background: var(--glass);
    border: 1px solid var(--border);
    border-radius: var(--radius-lg);
    padding: 28px 22px;
    text-align: center;
    transition: border-color 0.2s, transform 0.3s;
  }
  .safe-card:hover { border-color: rgba(125,249,255,0.25); transform: translateY(-4px); }
  .safe-icon { font-size: 2.2rem; display: block; margin-bottom: 14px; }
  .safe-card h4 { font-size: 0.95rem; margin-bottom: 8px; }
  .safe-card p  { font-size: 0.78rem; color: var(--muted); line-height: 1.55; font-weight: 500; }

  /* ── Footer ── */
  footer {
    position: relative;
    z-index: 1;
    border-top: 1px solid var(--border);
    padding: 32px;
  }

  .footer-inner {
    max-width: 1280px;
    margin: 0 auto;
    display: flex;
    align-items: center;
    gap: 32px;
    flex-wrap: wrap;
  }

  .footer-logo {
    font-family: 'Clash Display', sans-serif;
    font-size: 1.1rem;
    font-weight: 700;
  }
  .footer-logo em {
    font-style: normal;
    background: linear-gradient(90deg, var(--neon-1), var(--neon-2));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }

  .footer-links { display: flex; gap: 20px; }
  .footer-links a { font-size: 0.82rem; font-weight: 600; color: var(--muted); transition: color 0.2s; }
  .footer-links a:hover { color: var(--text); }

  .footer-copy { font-size: 0.75rem; color: var(--muted); margin-left: auto; }

  /* ── Responsive ── */
  @media (max-width: 1100px) {
    .bottom-grid  { grid-template-columns: 1fr 1fr; }
    .invite-card  { grid-column: 1 / -1; }
    .safe-grid    { grid-template-columns: repeat(2, 1fr); }
  }

  @media (max-width: 860px) {
    .hero-inner { grid-template-columns: 1fr; }
    .hero-cards { flex-direction: row; flex-wrap: wrap; }
    .hero-mini-card { width: auto; flex: 1; min-width: 180px; }
    .stats-bar { grid-template-columns: repeat(2, 1fr); }
  }

  @media (max-width: 640px) {
    .hero, .games-section, .bottom-grid, .safe-section, footer { padding-left: 16px; padding-right: 16px; }
    .bottom-grid  { grid-template-columns: 1fr; }
    .safe-grid    { grid-template-columns: 1fr; }
    .stats-bar    { grid-template-columns: 1fr 1fr; gap: 10px; padding: 16px; }
    .section-header { flex-direction: column; }
    .search { width: 100%; }
    .footer-inner { flex-direction: column; align-items: flex-start; gap: 16px; }
    .footer-copy  { margin-left: 0; }
  }
</style>
