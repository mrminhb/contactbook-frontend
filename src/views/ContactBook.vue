<script>
    import ContactCard from '@/components/ContactCard.vue';
    import InputSearch from '@/components/InputSearch.vue';
    import ContactList from '@/components/ContactList.vue'; 
    import { contactService } from '@/services/contact.service'


    export default {
        components: {
            ContactCard,
            InputSearch,
            ContactList,
        },
        data() {
            return {
                contacts: [],
                activeIndex: -1,
                searchText: '',
            };
        },
        watch: {
            searchText() {
                this.activeIndex = -1
            },
        },
        computed: {
            contactAsString() {
                return this.contacts.map((contact) => {
                    const { name, email, address, phone } = contact;
                    return { name, email, address, phone }.join('')
                });
            },
            filteredContacts() { 
                if (!this.searchText) return this.contacts; 
                return this.contacts.filter((contact, index) => 
                    this.contactsAsStrings[index].includes(this.searchText)
                ); 
            },

            activeContact() { 
                if (this.activeIndex < 0) return null; 
                return this.filteredContacts[this.activeIndex]; 
            }, 
                
            filteredContactsCount() { 
                return this.filteredContacts.length; 
            },
        },
        methods: {
            async retriveContacts() {
                try {
                    const contactsList = await contactService.getMany();
                    this.contacts =  contactsList.sort((current, next) =>
                        current.name.localeCompare(next.name)
                    )
                } catch (error) {
                    console.log(error)
                }
            },
            refreshList() {
                this.retriveContacts();
                this.activeIndex = -1
            },
            async onDeleteContact() {
                if(confirm("B???n mu???n x??a t???t c??? li??n h????")) {
                    try {
                        await contactService.deleteMany()
                    } catch (error) {
                        console.log(error)
                    }
                }
            },
            goToAddContact() {
                this.$router.push({ name: 'contact.add' });
            },
        },
        mounted() {
            this.refreshList();
        }
    }; 
</script>
<template>    
    <div class="page-row">
        <div class="col-md-10">
            <InputSearch v-model="searchText" />
        </div>
        <div class="mt-3 col-md-6">
            <h4>
                Danh b??? <i class="fas fa-addess-book" />
            </h4>
            <ContactList 
                v-if="filteredContactsCount > 0"
                :contacts="filteredContacts"
                v-model:activeIndex="activeIndex"
            />
            <p v-else>
                Kh??ng c?? li??n h??? n??o
            </p>
            <div class="mt-3 row justify-content-around align-items-center">
                <button 
                    class="btn btn-sm btn-primary"
                    @click="refreshList()"
                >
                    <i class="fas fa-redo" /> L??m m???i
                </button>
                <button 
                    class="btn btn-sm btn-primary"
                    @click="goToAddContact"
                >
                    <i class="fas fa-plus" /> Th??m m???i
                </button>
                <button 
                    class="btn btn-sm btn-danger" 
                    @click="onDeleteContact"
                >
                    <i class="fas fa-trash" /> X??a t???t c???
                </button>
            </div>
        </div>
        <div class="mt-3 col-md-6">
            <div v-if="activeContact">
                <h4>
                    Chi ti???t Li??n h???
                    <i class="fa fa-address-card"></i>
                </h4>
                <ContactCard :contact="activeContact" />
                <router-link
                    :to="{
                        name: 'contact.edit',
                        params: { id: activeContact.id },
                    }"
                >
                    <span class="mt-2 badge badge-warning">
                        <i class="fas fa-edit" /> Hi???u ch???nh
                    </span>
                </router-link>
            </div>
        </div>
    </div>
</template>

<style scoped>
.page {
     text-align: left;
     max-width: 750px;
 }
</style>