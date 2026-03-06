<script>
  import { createEventDispatcher } from 'svelte';
  export let game = null;
  export let open = false;

  const dispatch = createEventDispatcher();

  function close() { dispatch('close'); }
  function handleKey(e) { if (e.key === 'Escape') close(); }
  function handleLaunch() {
    dispatch('launch', game);
    close();
  }
</script>

<svelte:window on:keydown={handleKey} />

{#if open && game}
  <!-- svelte-ignore a11y-click-events-have-key-events -->
  <div class="overlay" on:click={close} role="dialog" aria-modal="true" aria-label="Game details">
    <div class="modal" on:click|stopPropagation role="document">
      <button class="close" on:click={close} aria-label="Close">✕</button>

      <div class="modal-thumb" style="--g1:{game.gradient[0]};--g2:{game.gradient[1]}">
        <div class="glow-bg"></div>
        <span class="big-emoji">{game.emoji}</span>
      </div>

      <div class="modal-body">
        <div class="modal-header">
          <div>
            <h2>{game.title}</h2>
            <p class="sub">{game.subtitle}</p>
          </div>
          <div class="stars">
            <span style="color:var(--neon-4)">{'★'.repeat(Math.floor(game.rating))}</span>
            <span style="font-size:0.8rem;color:var(--muted)"> {game.rating} · {game.reviews} reviews</span>
          </div>
        </div>

        <p class="modal-desc">{game.description}</p>

        <div class="stat-row">
          <div class="stat">
            <span class="sv">{game.players}</span>
            <span class="sl">Playing Now</span>
          </div>
          <div class="stat">
            <span class="sv">2–{game.maxPlayers}</span>
            <span class="sl">Players</span>
          </div>
          <div class="stat">
            <span class="sv">{game.duration}</span>
            <span class="sl">Game Time</span>
          </div>
          <div class="stat">
            <span class="sv">{game.ageRange}</span>
            <span class="sl">Ages</span>
          </div>
        </div>

        <div class="features">
          <p class="feat-title">Features</p>
          <ul>
            {#each game.features as f}
              <li>✓ {f}</li>
            {/each}
          </ul>
        </div>

        <div class="modal-actions">
          <button class="btn-play" on:click={handleLaunch}>
            🚀 Start Game — Find Players
          </button>
          <button class="btn-invite" on:click={() => { dispatch('invite', game); close(); }}>
            👥 Invite Friends
          </button>
        </div>
      </div>
    </div>
  </div>
{/if}

<style>
  .overlay {
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.75);
    backdrop-filter: blur(12px) saturate(1.4);
    z-index: 200;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 20px;
    animation: fadeUp 0.2s ease;
  }

  .modal {
    background: #0D0D1F;
    border: 1px solid var(--border-h);
    border-radius: var(--radius-lg);
    width: 100%;
    max-width: 500px;
    overflow: hidden;
    position: relative;
    animation: scaleIn 0.3s cubic-bezier(0.34,1.4,0.64,1);
  }

  .close {
    position: absolute;
    top: 14px; right: 14px;
    z-index: 10;
    width: 34px; height: 34px;
    background: rgba(255,255,255,0.1);
    border: 1px solid var(--border);
    border-radius: 50%;
    color: var(--text);
    font-weight: 700;
    font-size: 0.85rem;
    display: flex; align-items: center; justify-content: center;
    transition: background 0.2s;
  }
  .close:hover { background: rgba(255,255,255,0.2); }

  .modal-thumb {
    height: 130px;
    background: linear-gradient(135deg, var(--g1), var(--g2));
    display: flex; align-items: center; justify-content: center;
    position: relative; overflow: hidden;
  }

  .glow-bg {
    position: absolute; inset: 0;
    background: radial-gradient(circle at 50% 80%, rgba(255,255,255,0.15), transparent 60%);
  }

  .big-emoji {
    font-size: 4.5rem;
    filter: drop-shadow(0 6px 20px rgba(0,0,0,0.4));
    animation: float 3s ease-in-out infinite;
    z-index: 1;
  }

  .modal-body { padding: 24px; }

  .modal-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 12px;
    gap: 10px;
  }

  h2 {
    font-size: 1.4rem;
    margin-bottom: 3px;
  }

  .sub { font-size: 0.75rem; color: var(--muted); font-weight: 500; }

  .modal-desc {
    font-size: 0.85rem;
    color: var(--muted);
    line-height: 1.6;
    margin-bottom: 20px;
  }

  .stat-row {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
    margin-bottom: 20px;
  }

  .stat {
    background: var(--glass-b);
    border: 1px solid var(--border);
    border-radius: 14px;
    padding: 12px 8px;
    text-align: center;
  }
  .sv { display: block; font-family: 'Clash Display', sans-serif; font-size: 1.1rem; color: var(--neon-4); }
  .sl { display: block; font-size: 0.65rem; color: var(--muted); font-weight: 700; margin-top: 3px; }

  .features { margin-bottom: 22px; }
  .feat-title { font-size: 0.72rem; font-weight: 700; color: var(--muted); text-transform: uppercase; letter-spacing: 1px; margin-bottom: 10px; }
  ul { list-style: none; display: grid; grid-template-columns: 1fr 1fr; gap: 6px; }
  li { font-size: 0.8rem; font-weight: 600; color: var(--neon-5); }

  .modal-actions { display: flex; gap: 10px; }

  .btn-play {
    flex: 2;
    padding: 14px;
    background: linear-gradient(135deg, var(--neon-1), var(--neon-2));
    border: none;
    border-radius: 14px;
    color: #050510;
    font-weight: 800;
    font-size: 0.9rem;
    transition: opacity 0.2s, transform 0.2s;
  }
  .btn-play:hover { opacity: 0.88; transform: scale(0.98); }

  .btn-invite {
    flex: 1;
    padding: 14px;
    background: var(--glass-b);
    border: 1px solid var(--border-h);
    border-radius: 14px;
    color: var(--text);
    font-weight: 700;
    font-size: 0.88rem;
    transition: background 0.2s;
  }
  .btn-invite:hover { background: rgba(255,255,255,0.1); }

  @media (max-width: 480px) {
    .stat-row { grid-template-columns: repeat(2, 1fr); }
    ul { grid-template-columns: 1fr; }
    .modal-actions { flex-direction: column; }
  }
</style>
