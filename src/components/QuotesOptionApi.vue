<template>
  <div class="min-h-screen bg-gray-50 py-8">
    <div class="container mx-auto px-4">
      <!-- Header -->
      <div class="text-center mb-8">
        <h1 class="text-4xl font-bold text-gray-800 mb-2">Inspiring Quotes</h1>
        <p class="text-gray-600">Built with Options API</p>
      </div>

      <!-- Controls -->
      <div class="flex flex-col sm:flex-row gap-4 justify-center items-center mb-8">
        <div class="flex gap-2">
          <button
            @click="fetchRandomQuote"
            :disabled="loading"
            class="bg-blue-600 hover:bg-blue-700 disabled:bg-gray-400 text-white px-6 py-2 rounded-lg font-medium transition-colors duration-200 flex items-center gap-2"
          >
            <svg v-if="loading" class="animate-spin h-4 w-4" fill="none" viewBox="0 0 24 24">
              <circle
                class="opacity-25"
                cx="12"
                cy="12"
                r="10"
                stroke="currentColor"
                stroke-width="4"
              ></circle>
              <path
                class="opacity-75"
                fill="currentColor"
                d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
              ></path>
            </svg>
            {{ loading ? "Loading..." : "New Quote" }}
          </button>

          <button
            @click="fetchMultipleQuotes"
            :disabled="loading"
            class="bg-purple-600 hover:bg-purple-700 disabled:bg-gray-400 text-white px-6 py-2 rounded-lg font-medium transition-colors duration-200"
          >
            Get 5 Quotes
          </button>
        </div>

        <!-- Category Filter -->
        <select
          v-model="selectedCategory"
          @change="fetchQuotesByCategory"
          class="px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
        >
          <option value="">All Categories</option>
          <option value="motivational">Motivational</option>
          <option value="inspirational">Inspirational</option>
          <option value="life">Life</option>
          <option value="success">Success</option>
        </select>
      </div>

      <!-- Error State -->
      <div
        v-if="error"
        class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded mb-6 max-w-2xl mx-auto"
      >
        <strong>Error:</strong> {{ error }}
      </div>

      <!-- Single Quote Display -->
      <div v-if="currentQuote && !showMultiple" class="max-w-4xl mx-auto mb-8">
        <div
          class="bg-white rounded-xl shadow-lg p-8 text-center transform hover:scale-105 transition-transform duration-300"
        >
          <svg class="w-12 h-12 text-blue-600 mx-auto mb-4" fill="currentColor" viewBox="0 0 20 20">
            <path
              fill-rule="evenodd"
              d="M3 17a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zm3.293-7.707a1 1 0 011.414 0L9 10.586V3a1 1 0 112 0v7.586l1.293-1.293a1 1 0 111.414 1.414l-3 3a1 1 0 01-1.414 0l-3-3a1 1 0 010-1.414z"
              clip-rule="evenodd"
            />
          </svg>

          <blockquote
            class="text-2xl md:text-3xl text-gray-800 font-medium italic mb-6 leading-relaxed"
          >
            "{{ currentQuote.content }}"
          </blockquote>

          <div class="flex flex-col sm:flex-row justify-center items-center gap-4 text-gray-600">
            <p class="text-lg font-semibold">— {{ currentQuote.author }}</p>
            <div class="flex gap-2">
              <span
                v-for="tag in currentQuote.tags"
                :key="tag"
                class="bg-blue-100 text-blue-800 px-2 py-1 rounded-full text-sm"
              >
                #{{ tag }}
              </span>
            </div>
          </div>

          <div class="mt-6 flex justify-center gap-4">
            <button
              @click="copyQuote"
              class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg text-sm font-medium transition-colors duration-200"
            >
              {{ copied ? "Copied!" : "Copy Quote" }}
            </button>
            <button
              @click="shareQuote"
              class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-lg text-sm font-medium transition-colors duration-200"
            >
              Share
            </button>
          </div>
        </div>
      </div>

      <!-- Multiple Quotes Display -->
      <div
        v-if="quotes.length > 0 && showMultiple"
        class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6"
      >
        <div
          v-for="quote in quotes"
          :key="quote._id"
          class="bg-white rounded-lg shadow-md hover:shadow-lg transition-shadow duration-300 p-6"
        >
          <blockquote class="text-lg text-gray-800 mb-4 italic">"{{ quote.content }}"</blockquote>

          <div class="text-right">
            <p class="text-gray-600 font-medium">— {{ quote.author }}</p>
            <div class="mt-2 flex flex-wrap gap-1 justify-end">
              <span
                v-for="tag in quote.tags"
                :key="tag"
                class="bg-gray-100 text-gray-700 px-2 py-1 rounded-full text-xs"
              >
                #{{ tag }}
              </span>
            </div>
          </div>
        </div>
      </div>

      <!-- Stats -->
      <div v-if="totalQuotesFetched > 0" class="text-center mt-8 text-gray-600">
        <p>Total quotes loaded: {{ totalQuotesFetched }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "QuotesOptionsAPI",
  data() {
    return {
      currentQuote: null,
      quotes: [],
      loading: false,
      error: null,
      selectedCategory: "",
      showMultiple: false,
      copied: false,
      totalQuotesFetched: 0,
    };
  },
  methods: {
    async fetchRandomQuote() {
      this.loading = true;
      this.error = null;
      this.showMultiple = false;

      try {
        const response = await fetch("https://api.quotable.io/random");

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const quote = await response.json();
        this.currentQuote = quote;
        this.totalQuotesFetched++;
      } catch (err) {
        this.error = "Failed to fetch quote: " + err.message;
        console.error("Error fetching quote:", err);
      } finally {
        this.loading = false;
      }
    },

    async fetchMultipleQuotes() {
      this.loading = true;
      this.error = null;
      this.showMultiple = true;

      try {
        const response = await fetch("https://api.quotable.io/quotes?limit=5");

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const data = await response.json();
        this.quotes = data.results;
        this.totalQuotesFetched += data.results.length;
      } catch (err) {
        this.error = "Failed to fetch quotes: " + err.message;
        console.error("Error fetching quotes:", err);
      } finally {
        this.loading = false;
      }
    },

    async fetchQuotesByCategory() {
      if (!this.selectedCategory) {
        this.fetchRandomQuote();
        return;
      }

      this.loading = true;
      this.error = null;
      this.showMultiple = false;

      try {
        const response = await fetch(`https://api.quotable.io/random?tags=${this.selectedCategory}`);

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const quote = await response.json();
        this.currentQuote = quote;
        this.totalQuotesFetched++;
      } catch (err) {
        this.error = "Failed to fetch quote by category: " + err.message;
        console.error("Error fetching quote by category:", err);
      } finally {
        this.loading = false;
      }
    },

    async copyQuote() {
      if (!this.currentQuote) return;

      const text = `"${this.currentQuote.content}" — ${this.currentQuote.author}`;

      try {
        await navigator.clipboard.writeText(text);
        this.copied = true;
        setTimeout(() => {
          this.copied = false;
        }, 2000);
      } catch (err) {
        console.error("Failed to copy quote:", err);
      }
    },

    shareQuote() {
      if (!this.currentQuote) return;

      const text = `"${this.currentQuote.content}" — ${this.currentQuote.author}`;
      const url = window.location.href;

      if (navigator.share) {
        navigator.share({
          title: "Inspiring Quote",
          text: text,
          url: url,
        });
      } else {
        // Fallback: copy to clipboard
        this.copyQuote();
      }
    },
  },

  mounted() {
    // Fetch a random quote when component is mounted
    this.fetchRandomQuote();
  },
};
</script>
