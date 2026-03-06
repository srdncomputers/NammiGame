<script>
  export let message = '';
  export let visible = false;
</script>

<div class="toast" class:visible>
  {message}
</div>

<style>
  .toast {
    position: fixed;
    bottom: 32px;
    left: 50%;
    transform: translateX(-50%) translateY(80px);
    background: linear-gradient(135deg, var(--neon-5), var(--neon-1));
    color: #030f08;
    padding: 12px 28px;
    border-radius: 50px;
    font-weight: 800;
    font-size: 0.88rem;
    z-index: 500;
    pointer-events: none;
    transition: transform 0.4s cubic-bezier(0.34,1.5,0.64,1), opacity 0.3s;
    opacity: 0;
    white-space: nowrap;
  }
  .toast.visible {
    transform: translateX(-50%) translateY(0);
    opacity: 1;
  }
</style>
