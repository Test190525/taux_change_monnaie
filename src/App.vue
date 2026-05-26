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
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  background: #08142e;
  overflow-x: hidden;
}

#app {
  min-height: 100vh;
  font-family: 'Segoe UI', 'Helvetica Neue', Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
}

.main-nav {
  background: rgba(8, 20, 46, 0.97);
  border-bottom: 1px solid rgba(201, 154, 10, 0.25);
  padding: 0.6rem 1.5rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}

.nav-brand {
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.2em;
  color: #c99a0a;
  text-transform: uppercase;
}

.nav-links {
  display: flex;
  align-items: center;
  gap: 0.6rem;
}

.nav-link {
  color: #7a9bbf;
  text-decoration: none;
  font-size: 0.78rem;
  letter-spacing: 0.06em;
  padding: 0.2rem 0.5rem;
  border-radius: 4px;
  transition: color 0.2s, background 0.2s;
}

.nav-link:hover {
  color: #fff;
}

.nav-link.router-link-active {
  color: #c99a0a;
  background: rgba(201, 154, 10, 0.12);
}

.nav-sep {
  color: rgba(255, 255, 255, 0.15);
  font-size: 0.75rem;
}
</style>
