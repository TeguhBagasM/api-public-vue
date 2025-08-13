<template>
  <div id="app">
    <!-- Navbar -->
    <Navbar :active-component="activeComponent" @update:active-component="activeComponent = $event" />

    <!-- Main Content -->
    <main class="transition-all duration-500 ease-in-out">
      <!-- Options API Component -->
      <QuotesOptionsAPI v-if="activeComponent === 'options'" />

      <!-- Composition API Component -->
      <QuotesCompositionAPI v-if="activeComponent === 'composition'" />
    </main>

    <!-- Footer -->
    <footer class="bg-gray-800 text-white py-8 mt-12">
      <div class="container mx-auto px-4">
        <div class="text-center">
          <h3 class="text-xl font-bold mb-4">Vue.js Quotes Application</h3>
          <div class="flex justify-center space-x-8 mb-4">
            <div class="text-center">
              <h4 class="font-semibold mb-2">Options API Features</h4>
              <ul class="text-sm text-gray-300 space-y-1">
                <li>✓ Traditional Vue.js approach</li>
                <li>✓ Separated data, methods, computed</li>
                <li>✓ Easy to understand structure</li>
                <li>✓ Copy & share functionality</li>
                <li>✓ Category filtering</li>
              </ul>
            </div>
          </div>

          <div class="text-sm text-gray-400 border-t border-gray-700 pt-4">
            <p class="mb-2">
              Data provided by
              <a href="https://quotable.io" target="_blank" class="text-blue-400 hover:text-blue-300">
                Quotable API
              </a>
            </p>
            <p>Built with Vue.js 3 & Tailwind CSS</p>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<script>
import Navbar from "./components/Navbar.vue";
import QuotesOptionsAPI from "./components/QuotesOptionsAPI.vue";
import QuotesCompositionAPI from "./components/QuotesCompositionAPI.vue";

export default {
  name: "App",
  components: {
    Navbar,
    QuotesOptionsAPI,
    QuotesCompositionAPI,
  },
  data() {
    return {
      activeComponent: "options", // default to Options API
    };
  },
};
</script>

<style>
#app {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* Custom scrollbar */
::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: #f1f5f9;
}

::-webkit-scrollbar-thumb {
  background: #cbd5e1;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #94a3b8;
}

/* Smooth transitions */
* {
  transition: color 0.2s ease, background-color 0.2s ease, transform 0.2s ease;
}

/* Loading animation enhancement */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.fade-in {
  animation: fadeIn 0.5s ease-out;
}

/* Responsive text sizing */
@media (max-width: 768px) {
  .text-4xl {
    font-size: 2.5rem;
  }

  .text-3xl {
    font-size: 2rem;
  }

  .text-2xl {
    font-size: 1.5rem;
  }
}
</style>
