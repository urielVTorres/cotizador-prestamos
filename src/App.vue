<script setup>
  import { ref, computed, watch } from 'vue'
  import Header from './components/Header.vue'
  import Button from './components/Button.vue'
  import {calcularTotalPagar} from './helpers'
  
  const cantidad = ref(0);
  const meses = ref(6);
  const total = ref(0);
  const MIN = 0;
  const MAX = 20000;
  const STEP = 100;

  //función sustituida por v-model.number
  // const handleChange = e =>{
  //   cantidad.value = Number(e.target.value);

  // }

  const formatearDinero = (valor)=>{
    const formatter = new Intl.NumberFormat('en-US', {
      style: 'currency',
      currency: 'USD'
    });

    return formatter.format(valor)
  }

  watch([cantidad, meses], ()=>{
    total.value = calcularTotalPagar(cantidad.value, meses.value);
  });

  const pagoMensual = computed(()=>{
    return total.value / meses.value
  })
  const handleChangeDecremento = () => {
    if(cantidad.value === MIN) return;
    cantidad.value -= STEP;
  }

  const handleChangeIncremento = () => {
    if(cantidad.value === MAX) return;
    cantidad.value += STEP;
  }
</script>

<template>
  <div class="my-20 max-w-lg mx-auto shadow-lg p-10 bg-white">
    <Header />
    <div class="flex justify-between mt-100">
      <Button 
        :operador="'-'"
        @fn="handleChangeDecremento"
      />
      <Button 
        :operador="'+'"
        @fn="handleChangeIncremento"
      />
    </div>


    <div class="my-5">
      <input
        type="range"
        class="w-full bg-gray-200 accent-lime-500 hover:accent-lime-600"
        :min= "MIN"
        :max= "MAX"
        :step= "STEP"
        v-model.number="cantidad"
      />

      <p class="text-center text-5xl text-indigo-700 font-bold">{{ formatearDinero(cantidad) }}</p>
      
      <h2 class="text-2xl font-extrabold text-gray-500 text-center mt-3">
        Elige un <span>plazo</span> a pagar
      </h2>

      <select
        class="w-full p-2 bg-white border border-gray.300 rounded-lg text-center text-xl font-bold text-gray-500 mt-5"
        :value="meses"
        v-model.number="meses"
      >
        <option value="6">6 Meses</option>
        <option value="12">12 Meses</option>
        <option value="24">24 Meses</option>
      </select>
    </div>

    <div v-if="total>0" class="my-5 space-y-3 bg-gray-50 p-5 text-gray-500 text-center font-bold text-xl">
      <h2 class="text-2xl font-extrabold ">
        Resumen <span class="text-indigo-600">de pagos</span>
      </h2>
      <p>{{meses}} Meses</p>
      <p>Total a pagar: {{ formatearDinero(total) }}</p>
      <p>Mensualidades de {{ formatearDinero(pagoMensual) }} </p>
    </div>
    <p v-else class="text-center">Añade una cantidad y un plazo a pagar</p>
  </div>
</template>

