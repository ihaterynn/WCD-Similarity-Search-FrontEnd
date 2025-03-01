<template>
  <div>
    <button
      @click="exportToPDF"
      class="bg-blue-500 text-white px-4 py-2 rounded-md mt-4 fixed bottom-4 right-4"
      :disabled="isGenerating"
    >
      {{ isGenerating ? 'Generating PDF...' : 'Export to PDF' }}
    </button>
  </div>
</template>
 
<script>
import html2pdf from 'html2pdf.js';
 
export default {
  props: {
    recommendations: Array,
  },
  data() {
    return {
      isGenerating: false
    };
  },
  methods: {
    exportToPDF() {
      this.isGenerating = true;
      
      // Critical setting: Wait for images to load
      const element = document.getElementById('content-to-export');
      
      // Ensure proper height for the container
      const tempHeight = element.style.height;
      element.style.height = 'auto';
      
      const opt = {
        margin: 0.5,
        filename: 'Similar Recommendations.pdf',
        image: { type: 'jpeg', quality: 0.98 },
        html2canvas: { 
          scale: 2, 
          useCORS: true,
          allowTaint: true,
          scrollY: 0,
          windowHeight: element.scrollHeight
        },
        jsPDF: { unit: 'in', format: 'letter', orientation: 'landscape' }
      };
      
      // Make sure to wait for processing to complete
      html2pdf().from(element).set(opt).save().then(() => {
        this.isGenerating = false;
        element.style.height = tempHeight;
      }).catch(err => {
        console.error('PDF generation error:', err);
        this.isGenerating = false;
        element.style.height = tempHeight;
        alert('Error generating PDF. Please try again.');
      });
    }
  }
};
</script>
 
<style scoped>
.fixed {
  position: fixed;
  bottom: 4rem;
  right: 1rem;
}
</style>