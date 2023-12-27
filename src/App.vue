<template>
    <div v-if = 'sender'>
      <SideBar></SideBar>
      <HeaderPage></HeaderPage>
      <ReceivedMails></ReceivedMails>
      <ComposeMails></ComposeMails>
      <DraftMails></DraftMails>
      <SentMails></SentMails>
      <TrashMails></TrashMails>
      <FilterComponent></FilterComponent>
      <ContactsList  @deleteContact="deleteContact" @updateContactFn="updateContactFn" @searchContact="searchContact" @changeSortMethod="changeSortMethod" :contacts="contacts"></ContactsList>
    </div>
 
    <div v-if = '!sender'>
      <SignUp></SignUp>
      <SignIn></SignIn>
    </div>  
</template>

<script>
  import SideBar from "./components/SideBar.vue";
  import HeaderPage from "./components/HeaderPage.vue" ;
  import ReceivedMails from "./components/ReceivedMails.vue";
  import ComposeMails from "./components/ComposeMails.vue";
  import DraftMails from "./components/DraftMails.vue";
  import SentMails from "./components/SentMails.vue";
  import TrashMails from "./components/TrashMails.vue";
  import FilterComponent from "./components/FilterComponent.vue";
  import SignUp from "./components/SignUp.vue";
  import SignIn from './components/SignIn.vue';
  import ContactsList from "./components/ContactsList.vue";

  export default {
    name: 'App',
    components: {
      SideBar,
      ReceivedMails,
      HeaderPage,
      ComposeMails,
      DraftMails,
      SentMails,
      TrashMails,
      FilterComponent,
      SignUp,
      SignIn,
      ContactsList,
  },
  data() {
    return {
      contacts: []
    }
  },
  created() {
    this.contacts = [
        {
          name: "ali",
          email: [
            "ali@mail.com",
            "fry@ok.com",
            "hacker@mail.com"
          ]
        },
        {
          name: "samer",
          email: [
            "samer@mail.com"
          ]
        },
        {
          name: "ahmed",
          email: [
            "hacker@mail.com"
          ]
        },
      ],
    this.folders = [
      "parent",
      "safety"
    ],
    this.mails = [
      "mail 1",
      "mail 2",
    ]
  },
  methods: {
    async getContacts() {
      
      const reuslt = await fetch('http://localhost:8080/contacts/')

      return await reuslt.json()

    },
    async searchContact(newContact) {
      
      const reuslt = await fetch('http://localhost:8080/contact/search', {
        method: 'post',
        headers: {
              'Content-type': "application/json; charset=UTF-8"
            },
        body: JSON.stringify(newContact),
      })

      this.contacts = await reuslt.json()

    },
    async changeSortMethod() {
      
      const reuslt = await fetch('http://localhost:8080/contact/sort')

      this.contacts = await reuslt.json()

      this.contacts.sort()

    },
    async updateContactFn(curContact) {
      // PUT can be used with POST
      await fetch('http://localhost:8080/contact/add', {
        method: 'post',
        headers: {
              'Content-type': "application/json; charset=UTF-8"
            },
        body: JSON.stringify(curContact),
      })
      console.log(curContact)

      const index = this.contacts.findIndex(item => item.name === curContact.name);
      if (index !== -1) {
        this.contacts[index] = curContact
      } else {
        this.contacts = [ ...this.contacts,  curContact]
      }



    },
    async deleteContact(newContact) {
      await fetch('http://localhost:8080/contact/delete', {
        method: 'delete',
        headers: {
              'Content-type': "application/json; charset=UTF-8"
            },
        body: JSON.stringify(newContact),
      })
      console.log(newContact)
      this.contacts = this.contacts.filter(item => item.name.toLowerCase() !== newContact.name.toLowerCase());
      console.log(this.contacts)
    },
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
