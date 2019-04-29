<template>
  <div class="hello">
   <div class="echartsbox">
     <echartday/>
   </div>
  </div>
</template>

<script>
import echartday from '@/components/echartday'
import Bus from "@/bus.js";
export default {
  name: 'HelloWorld',
    components:{
    echartday
  },
  data () {
    return {
      msg: ''
    }
  },
  mounted(){
    this.areaFlow()
  },
  methods:{
     /* areaFlow流量曲线数据 */
    areaFlow() {
      var _this = this;
      _this.$axios
        .post( "https://www.easy-mock.com/mock/5ca1a886c035de157baf7fe2/areaflow1/areaflow1")
        .then(res => {
          var _this = this;
          console.log("%c接口返回值：","font-size:16px;color:red",res)
          _this.mrareaflowechartsresdata = res.data.RData;
          var Flowstarttime = "2019-03-20 10:09:00";
          var Flowendtime = "2019-03-20 19:14:00";
          let RData = res.data.RData;
          var flowobjectmr = { RData, Flowstarttime, Flowendtime };
          Bus.$emit("areaflowresdata", flowobjectmr);
        })
        .catch(error => {
          /*  console.log(error); */
        });
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello{
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 100vh;
}
.echartsbox{
 width: 60vw;
  height:40vh;
  margin-top: -400px;
}
</style>
