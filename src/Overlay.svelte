<script>
  import { fade } from 'svelte/transition';
</script>

<div class="overlay" transition:fade="{{delay: 0, duration: 300}}">
  <img width="100" src="https://skintypesolutions.com/assets/images/loader.gif" alt="spinner"/>

</div>

<style>
  .overlay {
    position: absolute;
    width: 100%;
    height: 550px;
    background-color: rgba(0,0,0,0.5);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 99;
  }
  .overlay p {
    color: white;
    font-size: 40px;
    font-weight: 700;
  }
</style>