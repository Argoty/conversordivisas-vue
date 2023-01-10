<template>
  <v-container>
    <v-form @submit.prevent="convertir">
      <v-row>
        <v-col>
          <v-text-field
            label="Cantidad"
            type="number"
            v-model="cantidad"
            clearable
            color="deep-purple darken-3"
            outlined
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-autocomplete
            label="De"
            v-model="divisaOrigen"
            :items="divisas"
            color="deep-purple darken-3"
          />
        </v-col>
        <v-col>
          <v-autocomplete
            label="A"
            v-model="divisaDestino"
            :items="divisas"
            color="deep-purple darken-3"
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-btn type="submit" color="deep-purple darken-3" class="white--text"
            >Convertir<v-icon>mdi-currency-usd</v-icon></v-btn
          >
        </v-col>
      </v-row>
    </v-form>

    <SpinnerLoader v-if="loader" class="mt-3 mx-auto"/>
    <v-row v-if="resultado.vlrDestino" class="mt-4">
      <v-col>
        <v-divider class="deep-purple darken-3"></v-divider>
        <p class="mt-3">
          {{ resultado.cantidad }}
          <span class="font-weight-bold">{{ resultado.divisaOrigen }}</span> son
          {{ resultado.vlrDestino }}
          <span class="font-weight-bold">{{ resultado.divisaDestino }}</span>
        </p>
        <v-divider class="deep-purple darken-3"></v-divider>
        <p class="text-caption mt-4">
          <v-icon>mdi-sort-calendar-ascending</v-icon>Ultima actualización:
          <span class="font-weight-medium text-body-2">{{
            resultado.fecha_ultima
          }}</span>
        </p>
        <p class="text-caption">
          <v-icon>mdi-sort-calendar-descending</v-icon>
          Siguiente actualización:
          <span class="font-weight-medium text-body-2">{{
            resultado.fecha_siguiente
          }}</span>
        </p>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import SpinnerLoader from "./SpinnerLoader.vue";

export default {
  name: "ConversorApp",
  components: {
    SpinnerLoader
  },

  data() {
    return {
      cantidad: 0,
      divisaOrigen: "USD",
      divisaDestino: "COP",

      resultado: {},
      divisas: [],

      loader: false,
    };
  },
  created() {
    this.obtenerDivisas();
  },
  methods: {
    async obtenerDivisas() {
      try {
        let response = await fetch(
          "https://v6.exchangerate-api.com/v6/6fce4086ee60d9b9ee1ee4d0/latest/USD"
        );
        let data = await response.json();

        this.divisas = Object.keys(data.conversion_rates);
      } catch (error) {
        console.error(error);
      }
    },

    async convertir() {
      this.resultado = {};
      this.loader = true;
      try {
        let response = await fetch(
          `https://v6.exchangerate-api.com/v6/6fce4086ee60d9b9ee1ee4d0/latest/${this.divisaOrigen}`
        );
        let data = await response.json();

        this.loader = false;

        let tasa = data.conversion_rates[this.divisaDestino];
        this.resultado = {
          divisaOrigen: data.base_code,
          cantidad: this.agregarSeparadorMiles(this.cantidad),
          divisaDestino: this.divisaDestino,
          vlrDestino: this.agregarSeparadorMiles(
            (this.cantidad * tasa).toFixed(2)
          ),
          fecha_ultima: this.formatFecha(data.time_last_update_unix),
          fecha_siguiente: this.formatFecha(data.time_next_update_unix),
        };
      } catch (error) {
        this.loader = false;
        console.error(error);
      }
    },

    formatFecha(fecha_divisa) {
      let fecha = new Date(fecha_divisa * 1000);
      let formateador = new Intl.DateTimeFormat("es-ES", {
        year: "numeric",
        month: "long",
        day: "numeric",
      });

      let fechaFormateada = formateador.format(fecha);

      return fechaFormateada;
    },

    agregarSeparadorMiles(numero) {
      let partesNumero = numero.toString().split(".");
      partesNumero[0] = partesNumero[0].replace(/\B(?=(\d{3})+(?!\d))/g, ",");
      return partesNumero.join(".");
    },
  },
};
</script>
