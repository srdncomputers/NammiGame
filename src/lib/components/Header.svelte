<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  let scrolled = false;
  let menuOpen = false;

  if (typeof window !== 'undefined') {
    window.addEventListener('scroll', () => {
      scrolled = window.scrollY > 20;
    });
  }
</script>

<header class:scrolled>
  <div class="inner">
    <a href="/" class="logo">
      <span class="logo-icon">🕹️</span>
      <span class="logo-text">Game<em>Zone</em></span>
      <span class="logo-badge">Kids</span>
    </a>

    <nav class="desktop-nav">
      <a href="#games">Games</a>
      <a href="#friends">Friends</a>
      <a href="#leaderboard">Ranks</a>
      <a href="#safe">Safe Play</a>
    </nav>

    <div class="header-right">
      <div class="online-pill">
        <span class="pulse-dot"></span>
        <span>4,821 online</span>
      </div>
      <button class="btn-avatar" aria-label="Profile">
        <span class="av">🦊</span>
        <span class="av-name">FoxKid99</span>
        <span class="av-xp">⭐ 1,240</span>
      </button>
      <button class="btn-invite" on:click={() => dispatch('invite')}>
        + Invite
      </button>
      <button
        class="hamburger"
        on:click={() => menuOpen = !menuOpen}
        aria-label="Menu"
      >
        <span></span><span></span><span></span>
      </button>
    </div>
  </div>

  {#if menuOpen}
    <div class="mobile-menu">
      <a href="#games"       on:click={() => menuOpen=false}>🎮 Games</a>
      <a href="#friends"     on:click={() => menuOpen=false}>👥 Friends</a>
      <a href="#leaderboard" on:click={() => menuOpen=false}>🏆 Ranks</a>
      <a href="#safe"        on:click={() => menuOpen=false}>🛡️ Safe Play</a>
    </div>
  {/if}
</header>

<style>
  header {
    position: fixed;
    top: 0; left: 0; right: 0;
    z-index: 100;
    padding: 14px 32px;
    transition: background 0.3s, border-color 0.3s, backdrop-filter 0.3s;
    border-bottom: 1px solid transparent;
  }

  header.scrolled {
    background: rgba(8,8,18,0.85);
    backdrop-filter: blur(20px) saturate(1.5);
    border-color: var(--border);
  }

  .inner {
    max-width: 1280px;
    margin: 0 auto;
    display: flex;
    align-items: center;
    gap: 32px;
  }

  /* Logo */
  .logo {
    display: flex;
    align-items: center;
    gap: 8px;
    text-decoration: none;
    flex-shrink: 0;
  }
  .logo-icon { font-size: 1.5rem; }
  .logo-text {
    font-family: 'Clash Display', sans-serif;
    font-size: 1.25rem;
    font-weight: 700;
    color: var(--text);
    letter-spacing: -0.03em;
  }
  .logo-text em {
    font-style: normal;
    background: linear-gradient(90deg, var(--neon-1), var(--neon-2));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }
  .logo-badge {
    background: rgba(125,249,255,0.1);
    border: 1px solid rgba(125,249,255,0.25);
    color: var(--neon-1);
    font-size: 0.65rem;
    font-weight: 700;
    padding: 2px 8px;
    border-radius: 50px;
    letter-spacing: 1px;
    text-transform: uppercase;
  }

  /* Nav */
  .desktop-nav {
    display: flex;
    gap: 6px;
    flex: 1;
  }
  .desktop-nav a {
    padding: 7px 14px;
    border-radius: 10px;
    font-size: 0.85rem;
    font-weight: 600;
    color: var(--muted);
    transition: color 0.2s, background 0.2s;
  }
  .desktop-nav a:hover {
    color: var(--text);
    background: var(--glass-b);
  }

  /* Right */
  .header-right {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-left: auto;
  }

  .online-pill {
    display: flex;
    align-items: center;
    gap: 7px;
    padding: 6px 12px;
    background: rgba(48,209,88,0.08);
    border: 1px solid rgba(48,209,88,0.2);
    border-radius: 50px;
    font-size: 0.78rem;
    font-weight: 700;
    color: var(--neon-5);
  }

  .btn-avatar {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 6px 14px 6px 8px;
    background: var(--glass-b);
    border: 1px solid var(--border-h);
    border-radius: 50px;
    color: var(--text);
    font-size: 0.82rem;
    font-weight: 700;
    transition: background 0.2s;
  }
  .btn-avatar:hover { background: rgba(255,255,255,0.12); }

  .av {
    width: 28px; height: 28px;
    background: linear-gradient(135deg, var(--neon-1), var(--neon-2));
    border-radius: 50%;
    display: flex; align-items: center; justify-content: center;
    font-size: 0.9rem;
  }

  .av-name { color: var(--text); }

  .av-xp {
    color: var(--neon-4);
    font-size: 0.75rem;
  }

  .btn-invite {
    padding: 8px 20px;
    background: linear-gradient(135deg, var(--neon-1) 0%, var(--neon-2) 100%);
    color: #050510;
    border: none;
    border-radius: 50px;
    font-weight: 800;
    font-size: 0.82rem;
    letter-spacing: 0.3px;
    transition: opacity 0.2s, transform 0.2s;
  }
  .btn-invite:hover { opacity: 0.85; transform: scale(1.03); }

  .hamburger {
    display: none;
    flex-direction: column;
    gap: 4px;
    padding: 8px;
    background: var(--glass-b);
    border: 1px solid var(--border);
    border-radius: 10px;
    transition: background 0.2s;
  }
  .hamburger span {
    display: block;
    width: 18px; height: 2px;
    background: var(--text);
    border-radius: 1px;
  }
  .hamburger:hover { background: rgba(255,255,255,0.1); }

  /* Mobile menu */
  .mobile-menu {
    display: flex;
    flex-direction: column;
    padding: 12px 20px 20px;
    gap: 4px;
    animation: fadeUp 0.2s ease;
  }
  .mobile-menu a {
    padding: 12px 16px;
    border-radius: 12px;
    font-weight: 700;
    font-size: 0.95rem;
    transition: background 0.2s;
  }
  .mobile-menu a:hover { background: var(--glass-b); }

  @media (max-width: 768px) {
    header { padding: 12px 20px; }
    .desktop-nav, .online-pill, .av-name, .av-xp { display: none; }
    .hamburger { display: flex; }
  }
</style>
