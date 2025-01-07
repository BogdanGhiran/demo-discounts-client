<template>
  <div>
    <h1>Discount Hub</h1>

    <button @click="generateCodes">Generate 5 Codes</button>

    <ul>
      <li v-for="(code, index) in generatedCodes" :key="index">
        {{ code }}
        <button @click="useCode(code)">Use This Code</button>
      </li>
    </ul>

    <div v-if="codeUsageResult">
      <strong>Code: {{ codeUsageResult.code }}</strong>
      <p>Success: {{ codeUsageResult.success }}</p>
    </div>
  </div>
</template>

<script>
import { HubConnectionBuilder } from '@microsoft/signalr';

export default {
  name: "DiscountHub",

  data() {
    return {
      connection: null,
      generatedCodes: [],
      codeUsageResult: null
    };
  },

  methods: {
    async generateCodes() {
      try {
        // Make sure connection is started
        await this.connection.invoke('GenerateCodes', 5, 7);
      } catch (e) {
        console.error('GenerateCodes error:', e);
      }
    },

    async useCode(code) {
      try {
        await this.connection.invoke('UseCode', code);
      } catch (e) {
        console.error('UseCode error:', e);
      }
    }
  },

  mounted() {
    // Build and start the connection once the component is mounted
    this.connection = new HubConnectionBuilder()
      .withUrl('https://demo-discounts.gab16.com/discountHub')
      .build();

    // Listen for server callbacks
    this.connection.on('CodesGenerated', (codes) => {
      console.log('Received codes', codes);
      // Replace your local data with new codes
      // If "codes" is an array, do something like:
      this.generatedCodes = codes;
    });

    this.connection.on('CodeUsed', (info) => {
      console.log('Code usage:', info);
      // Store or display usage info
      this.codeUsageResult = info;
    });

    // Actually start the connection
    this.connection
      .start()
      .then(() => {
        console.log('SignalR connected!');
      })
      .catch(err => {
        console.error('SignalR connection error:', err);
      });
  }
};
</script>

<style scoped>
/* Styles, if any */
</style>
