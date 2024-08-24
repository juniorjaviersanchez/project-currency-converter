<template>
  <v-container>
    <v-card
    class="mx-auto mt-10"
    width="500"
    >
      <template v-slot:title>
        <v-col cols="12">
          <v-img
            :src="logo"
            class="my-3"
            contain
            height="100"
          />
        </v-col>
        <span class="font-weight-black">Conversor de moneda</span>
      </template>

      <v-card-text>
        <div class="text-medium-emphasis text-justify">
          Proyecto N1: Este proyecto utiliza Vue.js para construir la interfaz de usuario, Vuetify para los componentes estilizados, y Axios para realizar peticiones a una API de tasas de cambio. Además, SweetAlert2 se emplea para mostrar alertas personalizadas.
        </div>
      </v-card-text>

      <v-card-text class="bg-surface-light pt-4 text-center">
        <v-form ref="form">
          <v-col cols="12" md="12">
            <v-text-field
              v-model="amount"
              label="Cantidad"
              type="number"
              min="0.01"
              :rules="amountRules"
            ></v-text-field>
          </v-col>
  
          <v-col cols="12" md="12">
            <v-select
              v-model="fromCurrency"
              :items="currencies"
              label="De"
            ></v-select>
          </v-col>
          <v-col cols="12" md="12">
            <v-select
              v-model="toCurrency"
              :items="currencies"
              label="A"
            ></v-select>
          </v-col>
          <v-col cols="12" md="12">
            <v-btn color="#622ec3" @click="submit">
              Convertir
            </v-btn>
          </v-col>
        </v-form>
        <v-col cols="12">
          <v-alert v-if="result" type="success">
            {{ result }} {{ toCurrency }}
          </v-alert>
        </v-col>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script>
import axios from 'axios';
import Swal from 'sweetalert2';
import logo from '../assets/javier-logo-color.png'

export default {
  name: 'HelloWorld',

  data: () => ({
    logo,
    amount: 1,
    fromCurrency: 'PEN',
    toCurrency: 'USD',
    currencies: ['PEN', 'USD', 'EUR', 'GBP', 'JPY', 'CLP', 'COP', 'BRL'], //Peso Chileno (CLP) Real Brasileño (BRL) Libra esterlina(GBP)
    result: null,
    amountRules: [
        v => !!v || 'El campo es obligatorio.',
        v => (parseFloat(v) > 0) || 'Debe ser un número positivo mayor que cero.'
      ]
  }),
  methods: {
    async submit() {
      let {valid} = await this.$refs.form.validate();
      if (valid) this.convertCurrency();
    },
    async convertCurrency() {
      if (this.amount && this.fromCurrency && this.toCurrency) {
        try {
          const response = await axios.get(`https://api.exchangerate-api.com/v4/latest/${this.fromCurrency}`);
          const rate = response.data.rates[this.toCurrency];
          this.result = (this.amount * rate).toFixed(2);
          this.showSuccessAlert();
        } catch (error) {
          this.showErrorAlert();
        }
      }else{
        this.showValidationAlert();
      }
    },
    showSuccessAlert() {
      Swal.fire({
        title: 'Conversión Exitosa!',
        text: `${this.amount} ${this.fromCurrency} = ${this.result} ${this.toCurrency}`,
        icon: 'success',
        background: '#f9f9f9',
        showConfirmButton: false,
        toast: true,
        timer: 4000,
        timerProgressBar: true,
        position: 'top-end',
      });
    },
    showErrorAlert() {
      Swal.fire({
        title: '¡Error!',
        text: 'Ocurrió un error al obtener la tasa de cambio.',
        icon: 'error',
      });
    },
    showValidationAlert() {
      Swal.fire({
        title: '¡Campos Incompletos!',
        text: 'Por favor, completar todos los campos.',
        icon: 'warning'
      });
    }
  },
}
</script>
