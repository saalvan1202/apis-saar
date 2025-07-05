<template>
  <div> 
     <VRow>
        <VCol cols="12" md="6" sm="12">
          <h2 style="display: flex;align-items: center;gap: 5px;"><i class="ri-whatsapp-line" style="color: #64CD8A;font-size: 30px;"></i>Envio masivo por WhatsApp</h2>
        </VCol>
         <VCol cols="6">
            <VBtn @click="isDialogVisible = true">Ver registrados</VBtn>
        </VCol>
      </VRow>
      <VRow>
        <!-- Username -->
        <VCol cols="12" md="4" sm="12">
            <VTextField
                v-model="nuevoIntegrante.nombre"
                label="Nombre"
                placeholder="Juan Perez"
                required
              />
            </VCol>
          </VRow>
        <VRow>
          <VCol cols="12" md="4" sm="12">
            <VTextField
              v-model="nuevoIntegrante.numero"
              label="Número"
              placeholder="Número (ej. 51999999999)"
              required
            />
          </VCol>
        </VRow>
        
        <VRow>
          <VCol md="2" sm="6">
            <VBtn type="button" color="success" @click="agregarIntegrante" width="100%"> <VIcon start icon="ri-user-5-line"/>Agregar integrante</VBtn>
          </VCol>
          <VCol md="2" sm="6">
            <VBtn type="button" color="error" @click="borrarIntegrantes" width="100%"> <VIcon start icon="ri-delete-bin-5-line"/>Borrar todos</VBtn>
          </VCol>
        </VRow>  
 <VRow>
     <VCol cols="12">
          <VTextarea v-model="mensajeBase" rows="5" cols="50" placeholder="Escribe tu mensaje aquí. Usa {nombre} para personalizar."></VTextarea>
    </VCol>
     <VCol cols="12">
      <VBtn  type="button" @click="enviarMensajes">
        <VIcon start icon="ri-send-plane-line"/>
        Enviar mensajes
     </VBtn>
    </VCol>
 </VRow>
      <VSnackbar
      v-model="isFlatSnackbarVisible"
      location="top end"
      variant="flat"
      color="error"
    >
      Por favor completa los campos nombre y número.
    </VSnackbar>
          <VSnackbar
      v-model="personOne"
      location="top end"
      variant="flat"
      color="error"
    >
      Debe haber al menos un integrante y un mensaje base.
    </VSnackbar>  
          <VSnackbar
      v-model="deletePerson"
      location="top end"
      variant="flat"
      color="error"
    >
      Por favor completa los campos nombre y número.
    </VSnackbar>    
  </div>
  <VDialog
    v-model="isDialogVisible"
    scrollable
    max-width="450"
  >
    <!-- Dialog Content -->
    <VCard>
      <DialogCloseBtn
        variant="text"
        size="default"
        @click="isDialogVisible = false"
      />

      <VCardItem class="pb-3">
        <VCardTitle>Resgistrados</VCardTitle>
      </VCardItem>

      <VDivider />
      <VCardText style="block-size: 300px;">
        <div v-if="integrantes.length === 0" style="display: flex;justify-content: center;align-items: center;height: 100%;width: 100%;">No hay integrantes registrados.</div>
        <VTable v-else>
        <thead>
        <tr>
            <th class="text-uppercase">
            Nombre
            </th>
            <th class="text-uppercase">
            Telefono
            </th>
            <th class="text-uppercase">
            Opciones
            </th>
        </tr>
        </thead>

        <tbody>
        <tr
            v-for="item in integrantes"
            :key="item.nombre"
        >
            <td>
            {{ item.nombre }}
            </td>
            <td>
            {{ item.numero }}
            </td>
            <td>
            <VBtn
              variant="text"
              color="error"
              @click="eliminarIntegrante(integrantes.indexOf(item))"
            >
              Eliminar
            </VBtn>
            </td>
        </tr>
        </tbody>
  </VTable>
      </VCardText>

      <VDivider />

      <VCardText class="pt-5 text-end overflow-visible">
        <VSpacer />
        <VBtn
          variant="outlined"
          color="error"
          class="me-4"
          @click="isDialogVisible = false"
        >
          Cerrar
        </VBtn>
      </VCardText>
    </VCard>
  </VDialog>
  <VDialog
    v-model="isDialogVisibleWpp"
    width="300"
  >
    <VCard
      color="primary"
      width="300"
    >
      <VCardText class="pt-3 text-white">
        Espere un momento, estamos enviando los mensajes...
        <VProgressLinear
          indeterminate
          class="mt-4"
          color="#fff"
        />
      </VCardText>
    </VCard>
  </VDialog>
</template>

<script>
import { VIcon, VSnackbar, VTable, VTextarea, VTextField } from 'vuetify/components';
import { VBtn } from 'vuetify/components/VBtn';

export default {
  name: "EnvioMasivoWhatsApp",
  data() {
    return {
      countryList : [
  {
    label: 'Bahamas, The',
    value: 'bahamas',
  },
  {
    label: 'Bahrain',
    value: 'bahrain',
  },
  {
    label: 'Bangladesh',
    value: 'bangladesh',
  },
  {
    label: 'Barbados',
    value: 'barbados',
  },
  {
    label: 'Belarus',
    value: 'belarus',
  },
  {
    label: 'Belgium',
    value: 'belgium',
  },
  {
    label: 'Belize',
    value: 'belize',
  },
  {
    label: 'Benin',
    value: 'benin',
  },
  {
    label: 'Bhutan',
    value: 'bhutan',
  },
  {
    label: 'Bolivia',
    value: 'bolivia',
  },
  {
    label: 'Bosnia and Herzegovina',
    value: 'bosnia',
  },
  {
    label: 'Botswana',
    value: 'botswana',
  },
  {
    label: 'Brazil',
    value: 'brazil',
  },
  {
    label: 'Brunei',
    value: 'brunei',
  },
  {
    label: 'Bulgaria',
    value: 'bulgaria',
  },
  {
    label: 'Burkina Faso',
    value: 'burkina',
  },
      ],
      selectedCountry:"",
      isDialogVisible: false,
      isFlatSnackbarVisible: false,
      isDialogVisibleWpp:false,
      personOne: false,
      deletePerson: false,  
      nuevoIntegrante: {
        nombre: "",
        numero: ""
      },
      integrantes: [],
      mensajeBase: localStorage.getItem("mensaje"),
    };
  },
  mounted() {
    localStorage.setItem("mensaje", `Asunto: Recordatorio de Pago – Cuota del Grupo Deportivo\n\nHola {nombre}, por parte de PERICOS FC🦜\n\nTe recordamos que la cuota correspondiente al mes de JUNIO del grupo deportivo PERICOS FC vence el 5 DE JUNIO.\n\nMonto: [S/. 30.00]\nForma de pago: [Transferencia de preferencia]\n\nTu aporte es fundamental para el mantenimiento y desarrollo de nuestras actividades. Agradecemos tu puntualidad y compromiso.💯👌🏻\n\nSi ya realizaste el pago, por favor, ignora este mensaje. Para cualquier consulta, no dudes en contactarnos.\n\nSaludos,\nFrank Gil Reátegui`);
 // Inicializa el localStorage si no existe
    this.mensajeBase = localStorage.getItem("mensaje") || `Asunto: Recordatorio de Pago – Cuota del Grupo Deportivo\n\nHola {nombre}, por parte de PERICOS FC🦜\n\nTe recordamos que la cuota correspondiente al mes de JUNIO del grupo deportivo PERICOS FC vence el 5 DE JUNIO.\n\nMonto: [S/. 30.00]\nForma de pago: [Transferencia de preferencia]\n\nTu aporte es fundamental para el mantenimiento y desarrollo de nuestras actividades. Agradecemos tu puntualidad y compromiso.💯👌🏻\n\nSi ya realizaste el pago, por favor, ignora este mensaje. Para cualquier consulta, no dudes en contactarnos.\n\nSaludos,\nFrank Gil Reátegui`;
    const guardados = localStorage.getItem("integrantes");
    const mensajeGuardado = localStorage.getItem("mensaje");
    if (guardados) {
      try {
        this.integrantes = JSON.parse(guardados);
      } catch (e) {
        this.integrantes = [];
      }
    }
  },
  methods: {
    guardarIntegrantes() {
      localStorage.setItem("integrantes", JSON.stringify(this.integrantes));
    },
    agregarIntegrante() {
      const { nombre, numero } = this.nuevoIntegrante;
      if (!nombre.trim() || !numero.trim()) {
        this.isFlatSnackbarVisible=true;
        return;
      }
      this.integrantes.push({ nombre: nombre.trim(), numero: numero.trim() });
      this.guardarIntegrantes();
      this.nuevoIntegrante = { nombre: "", numero: "" };
    },
    eliminarIntegrante(index) {
      this.integrantes.splice(index, 1);
      this.guardarIntegrantes();
    },
    borrarIntegrantes() {
      if (confirm("¿Estás seguro de que quieres borrar todos los integrantes?")) {
        this.integrantes = [];
        this.guardarIntegrantes();
      }
    },
    async enviarMensajes() {
      if (!this.mensajeBase.trim() || this.integrantes.length === 0) {
        this.personOne = true;
        return;
      }

     this.isDialogVisibleWpp = true;
     const delay = (ms) => new Promise(resolve => setTimeout(resolve, ms));

  for (let i = 0; i < this.integrantes.length; i++) {
    const integrante = this.integrantes[i];
    const mensaje = this.mensajeBase.replace("{nombre}", integrante.nombre);

    try {
      const response = await fetch('https://apiwsp.factiliza.com/v1/message/sendtext/NTE5MDE5ODExMjc=', {
        method: 'POST',
        headers: {
          Authorization: 'Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIzODU3MyIsImh0dHA6Ly9zY2hlbWFzLm1pY3Jvc29mdC5jb20vd3MvMjAwOC8wNi9pZGVudGl0eS9jbGFpbXMvcm9sZSI6ImNvbnN1bHRvciJ9.dKmKFEJ438eSF6gx4L52asNttTiVEbBd9RMxYj3GyE0', // ⚠️ Nunca lo pongas visible si puedes evitarlo
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          number: `+51${integrante.numero}`, // Asegúrate de que el número esté en el formato correcto
          text: mensaje
        })
      });

      const data = await response.json();
      console.log(`✅ Mensaje enviado a ${integrante.numero}`, data);

    } catch (error) {
      console.error(`❌ Error al enviar a ${integrante.numero}`, error);
    }

    await delay(1500); // espera 1.5s antes de continuar con el siguiente
  }
  this.isDialogVisibleWpp = false;
    }
  }
};
</script>

<style scoped>
form > div {
  margin-bottom: 10px;
}

button {
  margin-right: 10px;
}

ul li button {
  margin-left: 10px;
  background-color: red;
  color: white;
  border: none;
  padding: 2px 6px;
  border-radius: 4px;
  cursor: pointer;
}
</style>
