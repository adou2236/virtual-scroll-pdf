<template>
  <div class="virtual-scroll" @scroll="hanldeScroll" ref="virtual-scroll">
      <div :style="{height:totalHeight+'px',overflow:'hidden'}">
        <pdf v-for="item in filterNum"
             :style="`height:${itemH}px;transform:translateY(${offSetY}px); `"
             :key="item"
             :src="sourceUrl"
             :page="item+1"
             ></pdf>
      </div>
  </div>
</template>

<script>
import vuePdf from 'vue-pdf'
import CMapReaderFactory from 'vue-pdf/src/CMapReaderFactory.js'
const pdf = { ...vuePdf, destroyed: undefined };

export default {
  name: 'HelloWorld',
  components:{pdf},
  props:{
    fileSrc:{
      type:String
    },
    loadNum:{
      type:Number,
      default: 10
    },
    scrollLoad:{
      type:Boolean,
      default: false
    }
  },
  data(){
    return{
      scale: 1,
      numPages: 0,
      itemH:0,//每一页的高度
      lastTime:'', // 上次执行滚动事件的时刻
      scorllH: "", // 列表总高度
      list: 0,    // 展示列表
      offSetY: "", // 动态偏移量
      pdfSource: null,
      sourceUrl:''
    }
  },
  computed:{
    totalHeight(){
      return this.itemH*this.numPages
    },
    //展示的pdf页数组
    filterNum(){
      let length = this.numPages
      if(this.scrollLoad){
        length = this.list+this.loadNum
      }
      let arr = []
      for(let i = this.list;i<length;i++){
        arr.push(i)
      }
      return arr

    }
  },
  watch:{
    fileSrc(value){
      if(value){
        this.startLoading()
        this.$refs['virtual-scroll'].scrollTop = 0
        this.list=0
      }
    }
  },
  created() {
    this.startLoading()
    this.lastTime = new Date().getTime();
    // 默认展示，从第一页开始
    this.list = 0
  },
  mounted() {
    window.addEventListener('resize',this.throttle(this.handleResize,1000))
  },
  beforeDestroy() {
    window.removeEventListener('resize',this.throttle(this.handleResize,1000))
  },
  methods:{
     throttle(fn,wait){
      let pre = Date.now();
      return function(){
        let context = this;
        let args = arguments;
        let now = Date.now();
        if( now - pre >= wait){
          fn.apply(context,args);
          pre = Date.now();
        }
      }
    },
    handleResize(){
      this.culctuHeight(this.pdfSource,this.numPages)
    },
    hanldeScroll(e) {
      //   节流滚动计算
      if ( new Date().getTime()-this.lastTime > 20 && this.scrollLoad) {
        //设置动态偏移量模拟滚动
        this.offSetY = e.target.scrollTop - (e.target.scrollTop % this.itemH);
        this.list =  Math.floor(e.target.scrollTop / this.itemH)
        this.lastTime = new Date().getTime();
      }
    },
    startLoading(){
      //防止乱码
      let loadingTask = pdf.createLoadingTask({url:this.fileSrc,CMapReaderFactory})
      this.sourceUrl = loadingTask
      loadingTask.promise.then(pdf => {
        this.pdfSource = pdf
        this.numPages = pdf.numPages;
        this.culctuHeight(pdf, pdf.numPages)
        this.$emit('onLoad',pdf.numPages)
      }).catch(error=>{
        console.log(error)
      });
    },
    culctuHeight(pdf,numPages){
      let parent = this.$refs['virtual-scroll']
      let parentWidth = parent.clientWidth
      for(let i = 0;i<numPages;i++){
        pdf.getPage(i+1).then((page) => {
          let viewport = page.getViewport(1)
          //计算实际高度
          let height = (parentWidth/viewport.viewBox[2])*viewport.viewBox[3]
          //每一页的高度，防止每一页pdf高低不同导致的偏移,去最大值
          this.itemH = Number(height.toFixed(2))
        })
      }
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.virtual-scroll {
  width: 100%;
  overflow-y:scroll
}




</style>
