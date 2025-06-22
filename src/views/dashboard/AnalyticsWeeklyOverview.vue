<script setup>
import { hexToRgb } from '@layouts/utils'
import { ref } from 'vue'
import { useTheme } from 'vuetify'
const vuetifyTheme = useTheme()

const options = computed(() => {
  const currentTheme = ref(vuetifyTheme.current.value.colors)
  const variableTheme = ref(vuetifyTheme.current.value.variables)
  const disabledColor = `rgba(${hexToRgb(currentTheme.value['on-surface'])},${variableTheme.value['disabled-opacity']})`
  const borderColor = `rgba(${hexToRgb(String(variableTheme.value['border-color']))},${
    variableTheme.value['border-opacity']
  })`

  return {
    chart: {
      offsetY: -10,
      offsetX: -15,
      parentHeightOffset: 0,
      toolbar: { show: false },
    },
    plotOptions: {
      bar: {
        borderRadius: 6,
        distributed: true,
        columnWidth: '30%',
      },
    },
    stroke: {
      width: 2,
      colors: [currentTheme.value.surface],
    },
    legend: { show: false },
    grid: {
      borderColor,
      strokeDashArray: 7,
      xaxis: { lines: { show: false } },
    },
    dataLabels: { enabled: false },
    colors: [
      currentTheme.value['track-bg'],
      currentTheme.value['track-bg'],
      currentTheme.value['track-bg'],
      'rgba(var(--v-theme-primary),1)',
      currentTheme.value['track-bg'],
      currentTheme.value['track-bg'],
    ],
    states: {
      hover: { filter: { type: 'none' } },
      active: { filter: { type: 'none' } },
    },
    xaxis: {
      categories: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'],
      tickPlacement: 'on',
      labels: { show: false },
      crosshairs: { opacity: 0 },
      axisTicks: { show: false },
      axisBorder: { show: false },
    },
    yaxis: {
      show: true,
      tickAmount: 4,
      labels: {
        style: {
          colors: disabledColor,
          fontSize: '13px',
        },
        formatter: value => `${value > 999 ? `${(value / 1000).toFixed(0)}` : value}k`,
      },
    },
    responsive: [
      {
        breakpoint: 1560,
        options: { plotOptions: { bar: { columnWidth: '35%' } } },
      },
      {
        breakpoint: 1380,
        options: { plotOptions: { bar: { columnWidth: '45%' } } },
      },
    ],
  }
})

const series = [
  {
    data: [37, 57, 45, 75, 57, 40, 65],
  },
]

const moreList = [
  {
    title: 'Share',
    value: 'Share',
  },
  {
    title: 'Refresh',
    value: 'Refresh',
  },
  {
    title: 'Update',
    value: 'Update',
  },
]

const formattedResponse = ref('Cargando datos...')
const tipoConsulta = ref('TODOS')
async function getDate(){
  formattedResponse.value='Cargando datos...' 
try {
    const res = await fetch(url.value)
    if (!res.ok) throw new Error('Error al obtener datos')

    const data = await res.json()
    formattedResponse.value = JSON.stringify(data, null, 2)
  } catch (err) {
    formattedResponse.value = `Error: ${err.message}`
  }
}
const url=ref('https://apis-salvan-production.up.railway.app/paises/')
onMounted(async () => {
 getDate() 
})

watch(tipoConsulta, (nuevoValor) => {
    console.log(nuevoValor)
  url.value="https://apis-salvan-production.up.railway.app/paises/"
  if(nuevoValor=='LIMITE'){
    url.value="https://apis-salvan-production.up.railway.app/paises?limit=10"
  }
})
</script>

<template>
  <VCard>
    <VCardItem>
      <VCardTitle>Entorno de Prueba</VCardTitle>

      <template #append>
        <div class="me-n3">
          <MoreBtn :menu-list="moreList" />
        </div>
      </template>
    </VCardItem>

    <VCardText>
      <!-- <VueApexCharts
        type="bar"
        :options="options"
        :series="series"
        :height="200"
      /> -->
      <VRow class="match-height">
        <VCol
          cols="12"
          md="5"
        >
          <VChip color="primary"> {{url}} </VChip>
        </VCol>
        <VCol
          cols="12"
          md="4"
        >
          <VSelect
            :items="['TODOS', 'LIMITE']"
            label="Tipo de Consulta"
            placeholder="Select Item"
            v-model="tipoConsulta"
            eager
          />
        </VCol>
        <VCol
          cols="12"
          md="2"
        >
         <VBtn @click="getDate"> Consultar </VBtn>
        </VCol>
      </VRow>
        <v-card class="pa-4" elevation="3">
    <v-card-title>API Response</v-card-title>
    
    <v-textarea
      v-model="formattedResponse"
      label="API Output"
      readonly
      auto-grow
      rows="5"
      class="monospace fixed-height"
      variant="outlined"
      color="primary"
    ></v-textarea>
  </v-card>

<!-- 
      <div class="d-flex align-center mb-5 gap-x-4">
        <h4 class="text-h4">45%</h4>
        <p class="mb-0">
          Your sales performance is 45% <span class="text-high-emphasis">😎</span> better compared to last month
        </p>
      </div> -->

      
    </VCardText>
  </VCard>
</template>
<style>
.monospace textarea {
  font-family: 'Fira Code', 'Courier New', monospace;
  border-radius: 8px;
  padding: 10px;
  overflow: auto;
  min-height: 200px !important;
}

.fixed-height textarea {
  max-height: 100px;
  min-height: 200px !important;
  overflow-y: auto;
  resize: none;
}
</style>
