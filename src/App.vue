<template>
  <div id="app">
    <div>
      总页码{{totalPage}}
      <input type="file" accept=".pdf" :multiple="false" @input="fileChange"/>
    </div>
    <virtual-scroll-pdf class="viewer"
                        :fileSrc="fileSrc"
                        @onLoad="loadEnd"></virtual-scroll-pdf>
  </div>
</template>

<script>
import VirtualScrollPdf from './components/virtual-scroll-pdf'

export default {
  name: 'App',
  components: {
    VirtualScrollPdf
  },
  data(){
    return{
      fileSrc:'/test.pdf',
      totalPage:0,
      currentPage:1//当前页码
    }
  },
  methods:{
    fileChange(event){
      let file = event.target.files[0]
      let url = window.URL.createObjectURL(file)
      this.fileSrc = url
    },
    loadEnd(totalPage){
      this.totalPage = totalPage
    }
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
  display: flex;
  width: 100%;
  height: 100%;
  flex-direction: column;
}
.viewer{
  height: 90vh;

}
</style>
