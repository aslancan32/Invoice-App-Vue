<template>
    <section class="bg-gray-900 w-1/3 mx-auto mt-10 p-2 px-5 rounded-md shadow-2xl">
    <h1 v-if="activeInvoice" class="text-2xl text-gray-200 font-bold">Fatura Guncelle</h1>
    <h1 v-else class="text-2xl text-gray-200 font-bold	">Yeni Fatura Ekle</h1>
    <hr class="bg-gradient-to-r h-[1px] border-none from-gray-700 mb-2" />

    <!-- FAtura Bilgileri -->
    <!-- <heading title="Fatura Bilgileri"/>  //! This component imported globally -->
    <contact-section :contact="state.contact"/>

    <div class="mt-5">
        <app-heading title="Fatura Kalemleri"></app-heading> <!-- //! This componen imported on same page -->
        <invoice-items  :items="state.items" :addInvoiceItem="addInvoiceItem"/>
    </div>
    <!-- Summary -->
    <invoice-summary :items='state.items'/>

    <hr class="bg-gradient-to-r h-[1px] border-none from-gray-700 mt-5" />
    <div class="actionbar text-right my-5">
        <span v-if="activeInvoice">
            <button  @click="onSubmit"  class="green-button">Guncelle</button>
            <button  @click="deleteInvoice(props.activeInvoice)"  class="danger-button ml-2">Sil</button>
        </span>
        <button v-else @click="onSubmit" class="purple-button">Kaydet</button>
    </div>
    </section>
</template>

<script setup>
import appHeading from './ui/appHeading.vue'
import invoiceItems from './invoiceItems.vue'
import invoiceSummary from './invoiceSummary.vue'
import contactSection from './contactSection.vue'
import {reactive, provide, watch, computed} from 'vue'
const props = defineProps({onSave: Function, activeInvoice:[Object, null], updateInvoice: Function, deleteInvoice:Function})

const state = reactive({
    contact: {
        contact_name: null,
        email: null,
        city: null,
        country: null,
        zipcode: null,
    },
    items: [],
})
const addInvoiceItem = () => {
    state.items.push({
        id: new Date().getTime() ,
        name:null,
        qty:1,
        unit_price:0.0,
        total_price:0.0,
    })
    // console.log('state.items :>> ', state.items);
};
const deleteInvoiceItem = (deletedItem) => {
    console.log('deleted :>> ', deletedItem);
    state.items = state.items.filter(i => i.id != deletedItem.id)
};
provide('deleteInvoiceItem',deleteInvoiceItem)
const onSubmit = () => {
    (props.activeInvoice) ? props.updateInvoice({...state, id:props.activeInvoice.id, created_at:props.activeInvoice.created_at}) : props.onSave(state)
    
    state.contact = {
        contact_name: null,
        email: null,
        city: null,
        country: null,
        zipcode: null,
    }
    state.items = []
}

const clearInput = () => {}

watch(
    () => props.activeInvoice, 
    (activeInvoice) => {
        if(activeInvoice){
            state.contact = {
                ...activeInvoice.contact
            }
            state.items = [...activeInvoice.items]
        }
    
        // console.log("activeInvoice",activeInvoice);
})
</script>