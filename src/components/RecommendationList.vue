<template>
  <div v-if="recommendations.length" class="mt-6">
    <!-- Grid Layout -->
    <div class="max-w-7xl mx-auto grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-6">
      <div
        v-for="(rec, index) in recommendations"
        :key="rec.filename"
        class="bg-white rounded-lg shadow-md hover:shadow-lg p-4 h-100 flex flex-col"
      >
        <!-- Image Container with fixed height -->
        <a :href="rec.url" target="_blank" class="block h-60">
          <img
            :src="processImageUrl(rec.image_url)"
            alt="Recommended Image"
            class="w-full h-full object-cover rounded-md"
            crossorigin="anonymous"
          />
        </a>
        <!-- Metadata displayed in the desired order -->
        <div class="mt-2 text-center flex-grow flex flex-col justify-center">
          <p class="font-semibold text-gray-800">{{ rec.filename }}</p>
          <p class="text-sm text-gray-600">Product Type: {{ rec.Product_Type }}</p>
          <p class="text-sm text-gray-600">UOM: {{ rec.UOM }}</p>
          <p class="text-sm text-gray-600">Inventory: {{ rec.inventory_count }}</p>
          <p class="text-sm text-gray-600">Similarity: {{ rec.similarity.toFixed(4) }}%</p>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    recommendations: {
      type: Array,
      default: () => [],
    },
    backendUrl: {
      type: String,
      default: () => 'http://127.0.0.1:8080'
    }
  },
  methods: {
    processImageUrl(url) {
      if (!url) return '';
      
      // Skip if already proxied
      if (url.includes('/proxy-image')) return url;
      
      // Skip local URLs
      if (url.includes('127.0.0.1') || url.includes('localhost')) return url;
      
      // Proxy external URLs
      if (url.startsWith('http')) {
        return `${this.backendUrl}/proxy-image?url=${encodeURIComponent(url)}`;
      }
      
      return url;
    }
  }
};
</script>