<template>
<div>
  <button @click="renderHtml">click me</button>
  <div ref="ed" class="editor" contenteditable="true" v-html="html" @focusout="input"></div>
</div>
</template>

<script>
export default {
  name: 'ContentEditor',
  data(){
    return {
      html: '',
    }
  },
  methods:{
    input(e){
      this.html = e.target.innerHTML;
    },
    renderHtml(){
      this.html += "<img src='https://cdn-images-1.medium.com/max/853/1*FH12a2fX61aHOn39pff9vA.jpeg' alt='someimage' width=200px dir='rt'>";
    }
  }
}
</script>

<style>
  .editor{
    min-height: 20px;
    white-space: pre;
    background-color:rgb(250, 28, 65);
  }
 
</style>