<template>
    <Navbar @find="search = $event" :lang='lang' @changeLang="changeLang"/>
    <Notes 
    :search='search'
    :lang='lang'
    :notes="filterNotes"
    @remove="removeNote"
    @changeNote="changeNote"
    />
    <Modal
     :lang='lang'
     v-show="showModal"
     @showOrClose="showModal = false"
     @addNote="addNote"
     :edit="edit"
     :editNote="editNote"
     :currentId="currentId"
     @editedNote="editedNote"
     />
    <AddButton @click="showModal = !showModal, edit = false"/>
</template>
<script>
import AddButton from './components/AddButton.vue'
import Modal from './components/Modal.vue'
import Navbar from './components/Navbar.vue'
import Notes from './components/Notes.vue'
// import { v4 as uuidv4 } from 'uuid';
import langs from './lang'

export default {
  components: { Navbar, Notes, Modal, AddButton },
  data() {
    return {
      notes: [],
      showModal: false,
      edit: false,
      editNote: {},
      currentId: localStorage.id ? localStorage.id : localStorage.id =1,
      lang: 'ru',
      langWords: {},
      search:''
    }
  },
  methods: {
    addNote(obj){
      const note = {...obj}
      note.date = new Date().toLocaleString()
      note.id = this.currentId++
      this.notes.push(note)
    },
    removeNote(id){
      const idx = this.notes.findIndex(c => c.id == id)
      this.notes.splice(idx, 1)
    },
    getNotes(){
      this.notes = JSON.parse(localStorage.notes)
    },
    changeNote(id){
      this.edit = this.showModal = true
      let pickedNote = this.notes.find(note => note.id == id)
      this.editNote = pickedNote
    },
    editedNote(noteEdited){
        this.notes.forEach(note => {
            if(note.id == noteEdited.id){
              note.title = noteEdited.title
              note.text = noteEdited.text
              note.date = noteEdited.date
            }
        })
        this.showModal = false 
    },
    changeLang(val){
      this.lang = localStorage.lang = val
    }
  },
  watch: {
    notes: {
      handler(){
         localStorage.notes = JSON.stringify(this.notes)
      },
      deep: true
    }
  },
  computed:{
    filterNotes(){
      if(this.search){
        let se = this.search.toLowerCase()
        const filterNotes = this.notes.filter(item => {
          const title = item.title.toLowerCase()
          if(title.includes(se)) return item
        })
        return filterNotes
      }
      else return this.notes
    }
  },
  created(){
    if(localStorage.notes){
      this.getNotes()
    }
      localStorage.lang = localStorage.lang || 'ru'
      this.lang = localStorage.lang
      this.langWords = langs
      localStorage.words = JSON.stringify(this.langWords)
  },
  provide(){
    return {
      words: localStorage.words ? JSON.parse(localStorage.words) : location.reload()
    }
  }
}
</script>
<style lang="scss">
    
</style>