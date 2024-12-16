<template>
  <div class="logo-and-time-container">
    <h1 class="logo-name">Cloudy</h1>
    <div class="date-time-wrapper">
      <h3 class="time">{{ currentTime }}</h3>
      <h2 class="date">{{ date }}</h2>
    </div>
  </div>
</template>

<script>
export default {
  name: "Header",
  data() {
    return {
      currentTime: "", // Store the current time
    };
  },
  mounted() {
    this.updateTime(); // Call immediately to set the initial time
    setInterval(this.updateTime, 1000); // Update the time every second
  },
  methods: {
    updateTime() {
      this.currentTime = new Date().toLocaleTimeString("en-US", {
        hour: "2-digit",
        minute: "2-digit",
        second: "2-digit",
      });
    },
  },
  computed: {
    date() {
      const date = new Date();
      return date.toLocaleDateString("en-GB", {
        weekday: "long", // Full weekday name (e.g., "Friday")
        day: "numeric", // Day of the month (e.g., "13")
        month: "short", // Full month name (e.g., "Dec")
        year: "2-digit", // Full year (e.g., "24")
      });
    },
  },
};
</script>

<style>
.logo-and-time-container {
  display: flex;
  justify-content: space-between;
  align-items: center;

  .logo-name {
    font-size: clamp(0.9375rem, 0.8378rem + 0.3191vw, 1.5625rem);
  }

  .date-time-wrapper {
    display: flex;
    flex-direction: column;
    .date {
      font-size: clamp(0.9375rem, 0.8378rem + 0.3191vw, 1.125rem);
      color: rgb(92, 92, 228);
      text-shadow: 1px 3px 4px #000;
    }
    .time {
      font-size: clamp(0.9375rem, 0.8378rem + 0.3191vw, 1.125rem);
    }
  }
}
</style>
