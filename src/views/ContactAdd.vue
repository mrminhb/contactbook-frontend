<script>
import ContactForm from '@/components/ContactForm.vue';
import { contactService } from '@/services/contact.service'

export default {
    components: {
        ContactForm,
    },
    data() {
        return {
            contact: {
                name: "",
                email: "",
                address: "",
                phone: "",
                favorite: 0,
            },
            message: '',
        };
    },
    methods: {
        async onUpdateContact(contact) {
            try {
                await contactService.create(contact);
                this.message = 'Liên hệ được thêm mới thành công.'
            } catch (error) {
                console.log(error)
            }
        },
    },
    created() {
        this.message = '';
    }
}
</script>
<template>
    <div class="page">
        <h4>Thêm liên hệ</h4>
        <ContactForm 
            :contact="contact" 
            @submit:contact="onUpdateContact" 
        />
        <p>{{ message }}</p>
    </div>
</template>