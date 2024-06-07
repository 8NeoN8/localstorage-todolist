
<template>
  <div class="container">
    <div class="notes-and-options">
      <h1 class="header">LOCAL TODO LIST</h1>

      <ul class="note-list">
        <template v-for="(note, index) in notes" :key="index">

          <div class="listed-note" @click="openNote(index)">
            {{ note.id }}
            <br>
            {{ index }}
            <br>
            {{ note.title }}
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
  </div>
</template>

<script>
export default{
  data() {
    return {
      notes:[],
      noteData:{
        id: null,
        exists: false,
        title: null,
        content: null,
        tags: null,
      },
      globalTags: null,
      isOpenNote: false
    }
  },
  computed:{

  },
  created() {

  },
  mounted() {
    this.getNotes()
    console.log(this.notes);
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
    },
    saveNote(){
      
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
      

      this.setAndGet()
    },
    deleteNote(note){
      this.notes.splice(this.notes.indexOf(note), 1)

      this.setAndGet()
    },
    openNote(index){
      this.noteData = this.notes[index]
    },
    log(note, saveNote = false, deleteNote = false){

    }
  },

}
</script>
<style>
</style>