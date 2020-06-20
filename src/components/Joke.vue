<template>
  <div>
    <h1>Here's a random joke for you:</h1>
    <button v-on:click="updateJoke">new</button>
    <section v-if="joke" class="joke">
      <p>{{ lines.firstLine }}</p>
      <p>{{ lines.secondLine }}</p>
    </section>
    <section v-else class="loading">
      Loading...
    </section>
    <section class="credit">
      jokes from
      <a
        href="
      https://icanhazdadjoke.com/
      "
        target="_blank"
        rel="noopener noreferrer"
        >https://icanhazdadjoke.com/</a
      >
    </section>
  </div>
</template>

<script>
export default {
  name: "Joke",
  data() {
    return {
      joke: "",
    };
  },
  methods: {
    async fetchOneJoke() {
      const res = await fetch("https://icanhazdadjoke.com/", {
        headers: {
          Accept: "application/json",
        },
      });
      const data = await res.json();
      return data.joke;
    },
    async updateJoke() {
      this.joke = "";
      this.joke = await this.fetchOneJoke();
    },
  },
  computed: {
    lines() {
      if (!this.joke) return "";
      try {
        const [firstLine, secondLine] = this.joke
          .replace(/([.?!])\s*(?=[A-Z])/g, "$1|")
          .split("|");
        return { firstLine, secondLine };
      } catch {
        this.fetchOneJoke();
        return "";
      }
    },
  },
  async created() {
    await this.updateJoke();
  },
};
</script>

<style scoped>
div {
  --vue-green: rgb(65, 184, 131);
  --vue-darkgreen: #2c3e50;
}

div > :not(:last-child) {
  margin-bottom: 1.5rem;
}

button {
  padding: 5px 10px;
  border: none;
  background-color: var(--vue-green);
  text-transform: capitalize;
  transition: background 250ms;
}

button:hover {
  background-color: var(--vue-darkgreen);
  color: white;
}

button:active {
  background-color: var(--vue-green);
  color: var(--vue-darkgreen);
}

.joke,
.loading {
  width: max-content;
  margin: 0 auto;
  padding: 1rem 2rem;
  background-color: rgba(0, 0, 0, 0.1);
  border-radius: 8px;
}

.joke > :last-child {
  font-style: italic;
  font-weight: bold;
}

.credit {
  width: 100%;
  position: fixed;
  bottom: 5px;
  font-size: small;
  text-align: center;
}
</style>
