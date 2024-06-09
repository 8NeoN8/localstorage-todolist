
<template>
  <div class="container">
    <div class="notes-and-options">
      <dialog class="confirm-delete-dialog" :open="showConfirm">

        <div class="confirm-text">
          Are you sure you want to delete this note?
        </div>

        <div class="confirm-button">
          <button class="button-yes" @click="deleteNote(); showConfirm = false">Yes</button>
          <button class="button-no" @click="noteToDelete = null, showConfirm = false">No</button>
        </div>

      </dialog>
      <h1 class="header">LOCAL TODO LIST</h1>
      <select name="tag-select" class="tag-select" @change="test()">
        <option value="" selected></option>
        <template v-for="(tag, index) in globalTags" :key="index">
          <option :value="tag"> {{tag}}</option>
        </template>
      </select>
      <ul class="note-list">
        <template v-for="(note, index) in notes" :key="index">

          <div class="listed-note">
            <div class="note-title" @click="openNote(index)">
              {{ note.title }}
            </div>
            <button class="delete-button" @click="confirmNoteDelete(note)">
              &#x1F5D1;
            </button>
          </div>

        </template>
      </ul>
    </div>
    <div class="note-expanded">
      <input type="text" class="title-input" placeholder="NewNote" v-model="noteData.title">

      <div class="tags">
        <input type="text" class="tags-input" placeholder="E.x. #homework #today #friends #food" v-model="noteData.tags">
        <div class="tags-text">
          Tags:
        </div>
      </div>

      <textarea name="content-input" class="content-input" cols="30" rows="10" v-model="noteData.content"></textarea>
      
      <div class="save-button" @click="saveNote()">
        &#128190;
      </div>

    </div>

    <div v-if="showNotifs" class="notifications" :class="{'error': hasError, 'safe': isGood}">
      <div class="notif-text">
        {{ notifMsg }}
      </div>
      <div class="notif-close">
        &#x274c;
      </div>
    </div>
  </div>
</template>

<script>
export default{
  data() {
    return {
      notes:[],
      filteredNotes: [],
      noteData:{
        id: null,
        exists: false,
        title: null,
        content: null,
        tags: null,
      },
      noteToDelete: null,
      globalTags: [],
      isOpenNote: false,
      showNotifs: false,
      notifMsg: 'Note Saved!',
      hasError: false,
      isGood: true,
      showConfirm: false
    }
  },
  computed:{

  },
  created() {

  },
  mounted() {
    this.getNotes()
  },
  methods: {
    setAndGet(){
      localStorage.setItem('noteList', JSON.stringify(this.notes))

      this.getNotes()
    },
    getNotes(){
      this.notes = localStorage.getItem('noteList')

      if(!this.notes) localStorage.setItem('noteList', [])

      if(this.notes) this.notes = JSON.parse(this.notes)

      this.globalTags = []

      this.notes.forEach(note => {

        if(note.tags == null || note.tags == '') return

        let noteTags = note.tags
        noteTags = noteTags.split(' ')
        for (let i = 0; i < noteTags.length; i++) {
          this.globalTags.push(noteTags[i])  
        }
      });

      this.globalTags = this.globalTags.filter((value, index) => this.globalTags.indexOf(value) === index)
    },
    saveNote(){
      
      if(
        this.noteData.title == null || this.noteData.title == ''
      ){
        this.fillNotif(true)
        return
      }

      if(!this.noteData.exists){
        this.noteData = {
          ...this.noteData, 
          creationDateTime: new Date(),
          id: "noteid_" + Math.random().toString(16).slice(2),
          exists: true
        }
        this.notes.push(this.noteData)
      }

      if(this.noteData.exists){
        this.noteData.updateDateTime = new Date()
        this.notes[this.notes.indexOf(this.noteData)] = this.noteData
      }
      
      this.fillNotif()

      this.setAndGet()
    },
    fillNotif(error = false, ok = true){

      this.notifMsg = 'Nota Guardada!'
      this.hasError = false
      this.isGood = true

      if(error){
        this.notifMsg = 'Titulo de Nota Requerido!'
        this.hasError = true
        this.isGood = false
      }


      this.showNotifs = true
      setTimeout(() => {
        this.showNotifs = false
      }, 3000);
    },    
    confirmNoteDelete(note){
      this.showConfirm = true
      this.noteToDelete = note
    },
    deleteNote(){
      this.notes.splice(this.notes.indexOf(this.noteToDelete), 1)
      this.notes.filter((note) => {
        return note.id != this.noteToDelete.id
      })

      this.setAndGet()
    },
    openNote(index){
      this.noteData = this.notes[index]
    },
    test(){
      console.log('a');
    }
  },

}
</script>
<style>
</style>