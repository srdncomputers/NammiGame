<script>
  import { createEventDispatcher } from 'svelte';
  export let game;

  const dispatch = createEventDispatcher();

  const tagClasses = {
    hot: 'tag-hot',
    new: 'tag-new',
    live: 'tag-live'
  };

  function getRatingStars(r) {
    return '★'.repeat(Math.floor(r)) + (r % 1 ? '½' : '');
  }
</script>

<article
  class="card"
  on:click={() => dispatch('open', game)}
  on:keydown={(e) => e.key === 'Enter' && dispatch('open', game)}
  tabindex="0"
  role="button"
  aria-label="Play {game.title}"
>
  <!-- Gradient thumb -->
  <div
    class="thumb"
    style="--g1:{game.gradient[0]};--g2:{game.gradient[1]}"
  >
    <div class="thumb-glow"></div>
    <span class="game-emoji">{game.emoji}</span>
    <div class="thumb-labels">
      <span class="tag {tagClasses[game.tag]}">{game.tagLabel}</span>
    </div>
    <div class="online-badge">
      <span class="pulse-dot"></span>
      <span>{game.players} playing</span>
    </div>
  </div>

  <!-- Info -->
  <div class="info">
    <div class="info-top">
      <div>
        <h3>{game.title}</h3>
        <p class="sub">{game.subtitle}</p>
      </div>
      <div class="rating">
        <span class="stars">{getRatingStars(game.rating)}</span>
        <span class="rev">{game.reviews}</span>
      </div>
    </div>

    <p class="desc">{game.description}</p>

    <div class="chips">
      <span class="chip">👥 {game.maxPlayers} Players</span>
      <span class="chip">🕐 {game.duration}</span>
      <span class="chip">🧒 {game.ageRange}</span>
      <span class="chip cat">{game.category}</span>
    </div>

    <button class="play-btn" on:click|stopPropagation={() => dispatch('open', game)}>
      <span>▶ Play Now</span>
      <span class="arrow">→</span>
    </button>
  </div>
</article>

<style>
  .card {
    background: var(--glass);
    border: 1px solid var(--border);
    border-radius: var(--radius-lg);
    overflow: hidden;
    cursor: pointer;
    transition: transform 0.35s cubic-bezier(0.34,1.4,0.64,1),
                border-color 0.25s,
                box-shadow 0.35s;
    position: relative;
    display: flex;
    flex-direction: column;
    animation: fadeUp 0.5s ease both;
    outline: none;
  }

  .card:hover, .card:focus-visible {
    transform: translateY(-8px) scale(1.015);
    border-color: var(--border-h);
    box-shadow: 0 24px 60px rgba(0,0,0,0.5),
                0 0 0 1px rgba(255,255,255,0.06);
  }

  /* ── Thumb ── */
  .thumb {
    position: relative;
    height: 150px;
    background: linear-gradient(135deg, var(--g1), var(--g2));
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
  }

  .thumb-glow {
    position: absolute;
    inset: 0;
    background: radial-gradient(circle at 50% 60%, rgba(255,255,255,0.12) 0%, transparent 70%);
  }

  .game-emoji {
    font-size: 4rem;
    z-index: 1;
    filter: drop-shadow(0 4px 16px rgba(0,0,0,0.35));
    transition: transform 0.3s cubic-bezier(0.34,1.5,0.64,1);
    animation: float 4s ease-in-out infinite;
  }
  .card:hover .game-emoji { transform: scale(1.2) rotate(-6deg); }

  .thumb-labels {
    position: absolute;
    top: 12px;
    left: 12px;
    z-index: 2;
  }

  .online-badge {
    position: absolute;
    bottom: 12px; right: 12px;
    display: flex; align-items: center; gap: 6px;
    background: rgba(0,0,0,0.45);
    backdrop-filter: blur(8px);
    border: 1px solid rgba(255,255,255,0.12);
    border-radius: 50px;
    padding: 4px 10px;
    font-size: 0.7rem;
    font-weight: 700;
    color: var(--neon-5);
    z-index: 2;
  }

  /* ── Info ── */
  .info {
    padding: 20px;
    display: flex;
    flex-direction: column;
    gap: 12px;
    flex: 1;
  }

  .info-top {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 10px;
  }

  h3 {
    font-size: 1.05rem;
    font-weight: 600;
    margin-bottom: 2px;
    color: var(--text);
  }

  .sub {
    font-size: 0.72rem;
    color: var(--muted);
    font-weight: 500;
  }

  .rating {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    gap: 2px;
    flex-shrink: 0;
  }
  .stars { color: var(--neon-4); font-size: 0.72rem; }
  .rev   { font-size: 0.65rem; color: var(--muted); }

  .desc {
    font-size: 0.8rem;
    color: var(--muted);
    line-height: 1.55;
    font-weight: 500;
  }

  .chips {
    display: flex;
    flex-wrap: wrap;
    gap: 6px;
  }

  .chip {
    padding: 4px 10px;
    background: rgba(255,255,255,0.05);
    border: 1px solid var(--border);
    border-radius: 50px;
    font-size: 0.7rem;
    font-weight: 700;
    color: var(--muted);
  }

  .chip.cat {
    background: rgba(125,249,255,0.06);
    border-color: rgba(125,249,255,0.18);
    color: var(--neon-1);
  }

  /* ── Play btn ── */
  .play-btn {
    width: 100%;
    padding: 12px 20px;
    background: linear-gradient(135deg, rgba(125,249,255,0.12), rgba(191,90,242,0.12));
    border: 1px solid rgba(125,249,255,0.2);
    border-radius: 14px;
    color: var(--text);
    font-weight: 700;
    font-size: 0.88rem;
    letter-spacing: 0.3px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    transition: all 0.2s;
    margin-top: auto;
  }

  .play-btn:hover {
    background: linear-gradient(135deg, rgba(125,249,255,0.22), rgba(191,90,242,0.22));
    border-color: rgba(125,249,255,0.4);
    transform: translateX(2px);
  }

  .arrow {
    font-size: 1rem;
    transition: transform 0.2s;
  }
  .play-btn:hover .arrow { transform: translateX(4px); }
</style>
