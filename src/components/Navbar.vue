<template>
  <nav class="bg-gradient-to-r from-blue-600 to-purple-600 shadow-lg">
    <div class="max-w-7xl mx-auto px-4">
      <div class="flex justify-between items-center h-16">
        <!-- Logo/Brand -->
        <div class="flex items-center">
          <h1 class="text-white text-xl font-bold">Bahara Quotes App</h1>
        </div>

        <!-- Desktop Menu -->
        <div class="hidden md:block">
          <div class="ml-10 flex items-baseline space-x-4">
            <button
              @click="setActiveComponent('options')"
              :class="[
                'px-3 py-2 cursor-pointer rounded-md text-sm font-medium transition-colors duration-200',
                activeComponent === 'options'
                  ? 'bg-blue-700 text-white'
                  : 'text-blue-100 hover:bg-blue-500 hover:text-white',
              ]"
            >
              Options API
            </button>
          </div>
        </div>

        <!-- Mobile menu button -->
        <div class="md:hidden">
          <button
            @click="toggleMobileMenu"
            class="text-blue-100 hover:text-white hover:bg-blue-500 px-3 py-2 rounded-md"
          >
            <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path
                v-if="!mobileMenuOpen"
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M4 6h16M4 12h16M4 18h16"
              />
              <path
                v-else
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M6 18L18 6M6 6l12 12"
              />
            </svg>
          </button>
        </div>
      </div>

      <!-- Mobile Menu -->
      <div v-if="mobileMenuOpen" class="md:hidden">
        <div class="px-2 pt-2 pb-3 space-y-1 sm:px-3 bg-blue-700 rounded-md mt-2">
          <button
            @click="setActiveComponent('options')"
            :class="[
              'block px-3 py-2 rounded-md text-base font-medium w-full text-left transition-colors duration-200',
              activeComponent === 'options'
                ? 'bg-blue-800 text-white'
                : 'text-blue-100 hover:bg-blue-600 hover:text-white',
            ]"
          >
            Options API
          </button>
        </div>
      </div>
    </div>
  </nav>
</template>

<script>
export default {
  name: "Navbar",
  props: {
    activeComponent: {
      type: String,
      default: "options",
    },
  },
  data() {
    return {
      mobileMenuOpen: false,
    };
  },
  methods: {
    setActiveComponent(component) {
      this.$emit("update:activeComponent", component);
      this.mobileMenuOpen = false;
    },
    toggleMobileMenu() {
      this.mobileMenuOpen = !this.mobileMenuOpen;
    },
  },
};
</script>
