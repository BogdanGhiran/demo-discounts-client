<template>
  <div>
    <h1>Discount Hub Client</h1>

    <!-- Mocked "shopping cart" items -->
    <div>
      <p>Item1 - $5</p>
      <p>Item2 - $26</p>
      <p>Item3 - $0.5</p>
      <hr />
      <!-- Display the current total -->
      <p>Total: ${{ total }}</p>
    </div>

    <!-- Coupon code input + button -->
    <div class="mt-3">
      <label for="couponInput" class="form-label">Apply Coupon:</label>
      <input id="couponInput" class="form-control" type="text" v-model="code" placeholder="Enter discount code" />
      <button class="btn btn-primary mt-2" @click="useCode">Use Code</button>
    </div>

    <!-- Show success/failure after using the code -->
    <div v-if="useSuccess !== null" class="mt-2">
      <p>Code usage:
        <strong v-if="useSuccess">Success!</strong>
        <strong v-else>Failed!</strong>
      </p>
    </div>
  </div>
</template>

<script>
import { HubConnectionBuilder } from '@microsoft/signalr';

export default {
  name: 'DiscountHubClient',

  data() {
    return {
      connection: null,
      code: '',
      baseTotal: 31.5,      // The initial total (sum of $5 + $26 + $0.5)
      total: 31.5,          // The total displayed to the user, can be changed
      useSuccess: null      // null = haven't used code yet, true/false = success/fail
    };
  },

  methods: {
    async useCode() {
      try {
        // Reset any previous result
        this.useSuccess = null;

        // Call the hub method "UseCode" with the current "code" input
        await this.connection.invoke('UseCode', this.code);
        // The hub will respond with "CodeUsed" event
      } catch (e) {
        console.error('UseCode error:', e);
        // In case of an error at the network/call level
        this.useSuccess = false;
      }
    }
  },

  mounted() {
    const hubUrl = import.meta.env.VITE_API_URL; // read the variable
    // Build the SignalR connection
    this.connection = new HubConnectionBuilder()
      .withUrl(hubUrl)
      .build();

    // Listen for the server's "CodeUsed" callback
    // This should be triggered by the Hub method: await Clients.Caller.SendAsync("CodeUsed", ...)
    this.connection.on('CodeUsed', (info) => {
      console.log('Code usage:', info); // { Code: '...', Success: true/false }

      if (info.success) {
        // Code is valid and now used
        this.useSuccess = true;
        // Halve the total as per your requirement
        this.total = this.baseTotal / 2;
      } else {
        // Code is invalid or already used
        this.useSuccess = false;
        // Revert to the full price (no discount)
        this.total = this.baseTotal;
      }
    });

    // Start the SignalR connection
    this.connection
      .start()
      .then(() => {
        console.log('SignalR connected!');
      })
      .catch((err) => {
        console.error('SignalR connection error:', err);
      });
  }
};
</script>

<style scoped>
/* Optional: some spacing or Bootstrap classes */
.mt-2 {
  margin-top: 0.5rem;
}

.mt-3 {
  margin-top: 1rem;
}
</style>
