<template>
  <v-container>
    <v-form @submit.prevent="convertir">
      <v-row>
        <v-col>
          <v-text-field label="Cantidad" type="number" v-model="cantidad" clearable/>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-autocomplete label="De" v-model="divisaOrigen" :items="divisas" />
        </v-col>
        <v-col>
          <v-autocomplete label="A" v-model="divisaDestino" :items="divisas" />
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-btn type="submit" color="primary">Convertir</v-btn>
        </v-col>
      </v-row>
    </v-form>
    <v-row v-if="resultado.vlrDestino">
      <v-col>
        <p>
          {{ resultado.cantidad }} {{ resultado.divisaOrigen }} son {{ resultado.vlrDestino.toFixed(2) }}
          {{ resultado.divisaDestino }}
        </p>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "ConversorApp",
  data() {
    return {
      cantidad: 0,
      divisaOrigen: "USD",
      divisaDestino: "COP",

      vlrOrigen: 0,
      resultado: {},

      divisas: [],
    };
  },
  created() {
    this.obtenerDivisas();
  },  
  methods: {
    async obtenerDivisas() {
      try {
        let response = await fetch('https://v6.exchangerate-api.com/v6/6fce4086ee60d9b9ee1ee4d0/latest/USD');
        let data = await response.json();

        this.divisas = Object.keys(data.conversion_rates);
      } catch (error) {
        console.error(error);
      }
    },

    async convertir() {
      try {
        let response = await fetch(
          `https://v6.exchangerate-api.com/v6/6fce4086ee60d9b9ee1ee4d0/latest/${this.divisaOrigen}`
        );
        let data = await response.json();

        console.log(data);

        let tasa = data.conversion_rates[this.divisaDestino];
        this.resultado = {
          divisaOrigen: data.base_code,
          cantidad: this.cantidad,
          divisaDestino: this.divisaDestino,
          vlrDestino: this.cantidad * tasa
        };
      } catch (error) {
        console.error(error);
      }
    },
  },
};
</script>
