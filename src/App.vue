<template>
  <div class="w-screen h-screen bg-gray-800 flex flex-row text-white items-start justify-center">
    <app-sidebar v-if="haveInvoice" :invoices="invoices" :deleteInvoice="deleteInvoice" :editInvoice="editInvoice"/>
    <invoice-content :onSave="onSave" :activeInvoice="state.activeInvoice" :updateInvoice="updateInvoice" :deleteInvoice="deleteInvoice"/>
  </div>
</template>
<script setup>
import appSidebar from './components/appSidebar.vue'
import invoiceContent from './components/invoiceContent.vue'
import axios from 'axios'
import {computed, reactive, ref} from 'vue'

const invoices = ref([])
axios.get("//localhost:3001/invoices").then(get_invoice_response => {
  // console.log('get_invoice_response :>> ', get_invoice_response);
  invoices.value = get_invoice_response.data || []
  // console.log('invoices :>> ', invoices);
}) 
const haveInvoice = computed(() => invoices.value.length > 0 )

const onSave = (invoice) => {
  const newInvoice = {...invoice, id: new Date().getTime(), created_at: new Date().getTime()}
  invoices.value.push(newInvoice)
  axios.post('//localhost:3001/invoices',newInvoice).then((save_invoice_response) => {
      console.log(save_invoice_response);
  })
}

const deleteInvoice = (invoice) => {
  console.log(invoice);
  invoices.value = invoices.value.filter(i => i.id != invoice.id )
  axios.delete("//localhost:3001/invoices/"+invoice.id).then((delete_invoice_response) => {
    console.log('delete_invoice_response :>> ', delete_invoice_response);
  })

  setTimeout(() => {
    state.activeInvoice = null;
  }, 300);
}

const state = reactive({ activeInvoice: null})

const editInvoice = (invoice) => {
  // console.log(invoice);
  state.activeInvoice = invoice;
}
const updateInvoice = (invoice) => {
  const index = invoices.value.findIndex(i => i.id === invoice.id)
  invoices.value[index] = invoice

  axios.patch("//localhost:3001/invoices/"+state.activeInvoice.id, invoice).then((update_invoice_response) =>{
    console.log('update_invoice_response :>> ', update_invoice_response);}
  )
  setTimeout(() => {
    state.activeInvoice = null;
  }, 300);
  // state.activeInvoice = [];
}
</script>