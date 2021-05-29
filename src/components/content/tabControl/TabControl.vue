<template>
  <div class="tabControl">
    <div v-for="(item,index) in titles"
         class="tab-control-item" :class="{active:index === currentIndex}" @click="tabclick(index)">
      <span>{{item}}</span>
    </div>
  </div>
</template>

<script>
  export default {
    name: "TabControl",
    props:{
      titles:{
        type:Array,
        default(){
          return []
        }
      }
    },
    data(){
      return{
        currentIndex:0
      }
    },
    methods:{
      tabclick(index){
        this.currentIndex=index;
        this.$emit('tabclick',index)
      }
    }
  }
</script>

<style scoped>
  .tabControl{
    display: flex;
    text-align: center;
    font-size: 15px;
    height: 40px;
    line-height: 40px;
    background: #fff;
    z-index: 9;
  }
  .tab-control-item{
    flex: 1;
  }
  .tab-control-item span{
    padding: 5px;
  }
  .active{
    color:var(--color-high-text) ;

  }
  .active span{
    border-bottom: 3px solid var(--color-high-text);
  }
</style>