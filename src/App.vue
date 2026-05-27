<template>
  <nav class="main-nav">
    <span class="nav-brand">TAUX DE CHANGE</span>
    <div class="nav-links">
      <router-link to="/" class="nav-link">Options API</router-link>
      <span class="nav-sep">|</span>
      <router-link to="/composition" class="nav-link">Composition API</router-link>
    </div>
  </nav>
  <router-view />
</template>

<style>
@import './assets/styles/nav.css';
</style>
