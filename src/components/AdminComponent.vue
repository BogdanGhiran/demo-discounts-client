<template>
  <div>
    <h1>Discount Hub Admin</h1>

    <!-- Inputs for length and count -->
    <div>
      <label class="form-label">Length:</label>
      <input class="form-control" type="number" v-model="length" />
      <label class="form-label">Count:</label>
      <input class="form-control" type="number" v-model="count" />
    </div>

    <!-- Two separate generate buttons -->
    <button class="btn btn-primary m-3" @click="generateCodes">Generate Codes</button>
    <button class="btn btn-secondary m-3" @click="generateAndDisplayCodes">Generate &amp; Display Codes</button>

    <!-- Show success/failure result from the first button -->
    <div v-if="generateSuccess !== null">
      <p>Generate Result:
        <strong v-if="generateSuccess">Success!</strong>
        <strong v-else>Failed!</strong>
      </p>
    </div>

    <!-- List out any codes from the second button -->
    <ul>
      <li v-for="(code, index) in generatedCodes" :key="index">
        {{ code }}
        <button class="btn btn-secondary btn-sm m-1" @click="useCode(code)">Use This Code</button>
      </li>
    </ul>

    <!-- Show the result of a code usage (just like before) -->
    <div v-if="codeUsageResult">
      <strong>Code: {{ codeUsageResult.code }}</strong>
      <p>Success: {{ codeUsageResult.success }}</p>
    </div>
  </div>
</template>

<script>
import { HubConnectionBuilder } from '@microsoft/signalr';

export default {
  name: "DiscountHubAdmin",

  data() {
    return {
      connection: null,
      length: 7,         // default length
      count: 10,         // default count
      generateSuccess: null,   // boolean or null
      generatedCodes: [],
      codeUsageResult: null
    };
  },

  methods: {
    async generateCodes() {
      try {
        // Reset previous results
        this.generateSuccess = null;
        this.generatedCodes = [];
        // Invoke the hub method that returns a boolean success
        await this.connection.invoke('GenerateCodes', this.count, this.length);
        // We'll get a callback in "CodesGenerated" event
      } catch (e) {
        console.error('GenerateCodes error:', e);
        this.generateSuccess = false;
      }
    },

    async generateAndDisplayCodes() {
      try {
        this.generateSuccess = null;
        this.generatedCodes = [];
        // Invoke the hub method that returns actual codes
        await this.connection.invoke('GenerateCodesWithResponse', this.count, this.length);
        // We'll get a callback in "CodesGeneratedWithResponse" event
      } catch (e) {
        console.error('GenerateCodesWithResponse error:', e);
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
    const hubUrl = import.meta.env.VITE_API_URL; // read the variable
    // Create and start the SignalR connection
    this.connection = new HubConnectionBuilder()
      .withUrl(hubUrl) // now it picks the correct URL based on env
      .build();

    // Listen for "CodesGenerated" event -> returns a boolean
    this.connection.on('CodesGenerated', (success) => {
      console.log('CodesGenerated (boolean):', success);
      this.generateSuccess = success;
    });

    // Listen for "CodesGeneratedWithResponse" event -> returns an array of codes
    this.connection.on('CodesGeneratedWithResponse', (codes) => {
      console.log('CodesGeneratedWithResponse (array):', codes);
      this.generatedCodes = codes;
    });

    // Listen for code usage response
    this.connection.on('CodeUsed', (info) => {
      console.log('Code usage:', info);
      this.codeUsageResult = info;
    });

    // Start the connection
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
/* Your component-level styles go here */
</style>
