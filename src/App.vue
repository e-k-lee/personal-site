<template>
  <div class="app-wrapper">
    <div class="glow-orb glow-top-left"></div>
    <div class="glow-orb glow-mid-right"></div>

    <!-- 💡 Vue dynamically injects the 'scrolled' class here on scroll -->
    <header class="sticky-nav" :class="{ 'scrolled': isScrolled }">
      <div class="nav-container">
        <div class="logo">eric</div>

        <button
          class="menu-toggle"
          @click="toggleMenu"
          :aria-expanded="isMenuOpen"
          aria-label="Toggle navigation"
        >
          <span></span>
          <span></span>
          <span></span>
        </button>

        <nav class="nav-links" :class="{ open: isMenuOpen }">
          <a @click.prevent="handleNavClick('home')">Home</a>
          <a @click.prevent="handleNavClick('game')">Lumines Demo</a>
          <a @click.prevent="handleNavClick('about')">About Me</a>
        </nav>
      </div>
    </header>

    <main class="content-container">
      <section id="home" class="hero-section">
        <h1>Innovative Software Solutions</h1>
        <!-- Extra placeholder spacing so you can test scrolling down -->
        <div style="height: 150vh;"></div> 
      </section>
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

// Reactive state tracking whether the user has scrolled down the page
const isScrolled = ref(false)
const isMenuOpen = ref(false)

const handleScroll = () => {
  // If the window is scrolled past 10 pixels, flip the state to true
  isScrolled.value = window.scrollY > 10
}

// Hook into browser lifecycle events safely
onMounted(() => {
  window.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})

const toggleMenu = () => {
  isMenuOpen.value = !isMenuOpen.value
}

const closeMenu = () => {
  isMenuOpen.value = false
}

const handleNavClick = (id) => {
  closeMenu()
  scrollToSection(id)
}

const scrollToSection = (id) => {
  const element = document.getElementById(id)
  if (element) {
    // Align the section with the sticky header height so scrolling feels precise
    const offset = 96
    const bodyRect = document.body.getBoundingClientRect().top
    const elementRect = element.getBoundingClientRect().top
    const elementPosition = elementRect - bodyRect
    const offsetPosition = elementPosition - offset

    window.scrollTo({
      top: offsetPosition,
      behavior: 'smooth' // 🔥 Forces a smooth programmatical scroll tracking loop
    })
  }
}
</script>

<style scoped>
.app-wrapper {
  position: relative;
  width: 100%;
  min-height: 100vh;
}

/* Base Navbar State (Pinned to the viewport) */
.sticky-nav {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
  width: 100%;
  border-bottom: 1px solid transparent; /* No border visible initially */
  background-color: transparent;         /* Invisible base background */
  backdrop-filter: blur(0px);            /* No blur effect at top */
  -webkit-backdrop-filter: blur(0px);
  transition: background-color 0.3s ease, border-color 0.3s ease, backdrop-filter 0.3s ease; /* 💡 Explicit transitions, no layout changes */
}

/* 🚀 Scrolled Navbar State (Injected automatically on-scroll) */
.sticky-nav.scrolled {
  border-color: var(--border-subtle);      /* Fades in the subtle border line */
  backdrop-filter: blur(40px);              /* Fades in the glass blur */
  -webkit-backdrop-filter: blur(40px);
  /* ❌ Removed the padding property entirely to lock its size */
}

/* Maintain container rules - this dictates the persistent size of your navbar */
.nav-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 1.25rem 2rem; /* Kept completely locked in place */
  display: flex;
  justify-content: space-between;
  align-items: center;
  position: relative;
  min-height: 72px;
}

.menu-toggle {
  display: none;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 4px;
  width: 44px;
  min-height: 44px;
  background: transparent;
  border: none;
  padding: 0.5rem;
  cursor: pointer;
  position: relative;
  z-index: 110;
}

.menu-toggle span {
  display: block;
  width: 24px;
  height: 2px;
  background: var(--text-primary);
  border-radius: 999px;
}

.logo {
  font-weight: 700;
  font-size: 1.25rem;
  letter-spacing: -0.02em;
}

.nav-links {
  display: flex;
  gap: 2.5rem;
  user-select: none;
  -webkit-user-select: none;
  align-items: center;
}

.nav-links a {
  color: var(--text-secondary);
  text-decoration: none;
  font-size: 0.9rem;
  font-weight: 500;
  transition: color 0.2s ease;
  cursor: pointer;
  user-select: none;
  -webkit-user-select: none;
}

.nav-links a:hover, .nav-links a.active {
  color: var(--text-primary);
}

.btn-pill {
  background: var(--gradient-brand);
  color: #fff;
  border: none;
  padding: 0.5rem 1.25rem;
  border-radius: 9999px;
  font-weight: 600;
  font-size: 0.85rem;
  cursor: pointer;
  transition: opacity 0.2s ease;
}
.btn-pill:hover {
  opacity: 0.9;
}

@media (max-width: 768px) {
  .nav-container {
    padding: 1rem 1.25rem;
    min-height: 64px;
  }

  .menu-toggle {
    display: flex;
  }

  .nav-links {
    position: fixed;
    top: 64px;
    left: 0;
    right: 0;
    z-index: 90;
    flex-direction: column;
    align-items: flex-start;
    gap: 0.75rem;
    padding: 1rem 1.25rem 1.25rem;
    background: rgba(11, 15, 25, 0.98);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    opacity: 0;
    visibility: hidden;
    pointer-events: none;
    transform: translateY(-4px);
    transition: opacity 0.2s ease, visibility 0.2s ease, transform 0.2s ease;
  }

  .nav-links.open {
    opacity: 1;
    visibility: visible;
    pointer-events: auto;
    transform: translateY(0);
  }

  .nav-links a {
    font-size: 1.05rem;
    display: block;
    width: 100%;
    padding: 0.75rem 0;
    color: var(--text-secondary);
    text-align: left;
  }
}

/* ✨ Ambient Background Glow System */
.glow-orb {
  position: absolute;
  width: 500px;
  height: 500px;
  border-radius: 50%;
  pointer-events: none; /* Prevents selecting or blocking clicks */
  filter: blur(130px);   /* Defuses the colors completely */
  opacity: 0.15;
  z-index: 0;
}
.glow-top-left {
  top: -10%;
  left: -10%;
  background: var(--accent-blue);
}
.glow-mid-right {
  top: 40%;
  right: -10%;
  background: var(--accent-indigo);
}

.content-container {
  position: relative;
  z-index: 10;
  max-width: 1200px;
  margin: 0 auto;
  padding: 6rem 2rem 4rem;
}

.hero-section {
  min-height: 80vh;
  scroll-margin-top: 96px;
}
</style>
