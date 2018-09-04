<template>
  <div class="dateDemo">
  	
		<calendar 
      :calendar="calendar"
      :ckey="calendar.key"
      @setIptDate="setIptDate"
    >
    </calendar>
    
    <div class="setDateWrap">
      <div>
        <el-date-picker
          v-model="defDatePicker"
          @change="changeDefDatePicker"
          value-format="yyyy-MM-dd"
          type="date"
          placeholder="选择默认日期">
        </el-date-picker>
      </div>
      <br>
      <br>
      <el-date-picker
        v-model="datePicker"
        type="daterange"
        value-format="yyyy-MM-dd"
        @change="changePicker"
        range-separator="至"
        start-placeholder="设置范围开始日期"
        end-placeholder="设置范围结束日期">
      </el-date-picker>
      <br>
      <br>
      <br>
      <div class="ipt">
        <el-input v-model="price" placeholder="请输入价格"></el-input>
      </div>
      <br>
      <br>
      <el-date-picker
        v-model="priceDatePicker"
        type="daterange"
        :picker-options="pickerDateLimit"
        range-separator="至"
        start-placeholder="设置价格开始日期"
        end-placeholder="设置价格结束日期">
      </el-date-picker>

      <br>
      <br>
      <br>

      <div class="week">
        <p>选择设置价格的星期</p>
        <p>
          <label>星期1<input type="checkbox" value="1" v-model="pickWeek"></label>
          <label>星期2<input type="checkbox" value="2" v-model="pickWeek"></label>
          <label>星期3<input type="checkbox" value="3" v-model="pickWeek"></label>
          <label>星期4<input type="checkbox" value="4" v-model="pickWeek"></label>
          <label>星期5<input type="checkbox" value="5" v-model="pickWeek"></label>
          <label>星期6<input type="checkbox" value="6" v-model="pickWeek"></label>
          <label>星期日<input type="checkbox" value="0" v-model="pickWeek"></label>
        </p>
        <br>
        <p>
          <el-button size="mini" @click="setWeekDay(['1','2','3','4','5'])">工作日</el-button>
          <el-button size="mini" @click="setWeekDay(['0','6'])">周末</el-button>
        </p>
        <br>
        <p>{{pickWeek}}</p>

      </div>
      <br>
      <br>
      <br>
      <p>选择设置价格的日期</p>
      
      <div class="daylist">
        <label v-for="item in dayList">{{item}}<input type="checkbox" :value="item" v-model="pickDay"></label>
      </div>
      <br>
      <p>
        <el-button size="mini" @click="setAllDay()">全选</el-button>
        <el-button size="mini" @click="setInvertDay()">反选</el-button>
      </p>
      <br>
        <br>

      <p>
        <el-button @click="setPrice">设置价格</el-button>
        <el-button @click="cleanPrice">清空</el-button>
      </p>

    </div>
  </div>
</template>

<script>
import Calendar from '@/components/Calendar.vue'
export default {
	data(){
		return {
			calendar:{
        defDate : '',//初始时间，默认今天
        beginDate : '2018-1-10',
        endDate : '2018-11-15',
        tag : null,//目标item的key
        item : {
          date1 : {
            value : '',
          },
        },
        key : 0,
        priceDataJSON : {},
      },
      price : '',//设置价格
      dayList : [],
      datePicker : '',
      defDatePicker : '',
      priceDatePicker : '',
      pickWeek : ["0","1","2","3","4","5","6"],
      limitWeek : [],
      pickDay : [],
      limitDay : [],
      pickerDateLimit:{
        disabledDate: (time) => {
          let beginDateVal = Date.parse(this.datePicker[0]+' 00:00:00')
          let endDateVal = Date.parse(this.datePicker[1]+' 00:00:00')
          if (beginDateVal&&endDateVal) {
            return time.getTime() < beginDateVal||time.getTime() > endDateVal
          }
            
        }
      },
		}
	},
	components : {
    Calendar,
  },
  mounted(){
    this.initDayList()
  	this.calendarShow({tag:'date1'})
  },
  methods:{
		calendarShow(opt) {
      let item = {}
      if(opt&&opt.tag){
        this.calendar.tag = opt.tag
        item = this.calendar.item[opt.tag]
      }
      this.calendar.show = true

    },
    initDayList(){
      this.dayList = []
      for(let i=0;i<31;i++){
        this.dayList.push(i+1)
        this.pickDay.push(i+1)
      }
    },
    setIptDate(opt){
      console.log(opt)
      this.calendar.item[opt.tag].value = opt.value
    },
    changeDefDatePicker(){
      this.calendar.defDate = this.defDatePicker
      this.calendar.key++
    },
    changePicker(){
      this.calendar.beginDate = this.datePicker[0]
      this.calendar.endDate = this.datePicker[1]
      this.calendar.key++
    },
    changeLimitWeek(opt){
      let tmpJSON = {}
      for (var i = 0; i < 7; i++) {
        tmpJSON[''+i] = false
      }
      this.pickWeek.map((item,i)=>{
        tmpJSON[''+item] = true
      })
      this.limitWeek = []
      for(let attr in tmpJSON){
        if(!tmpJSON[attr]){
          this.limitWeek.push(parseInt(attr))
        }
      }
      console.log(this.limitWeek)
    },
    changeLimitDay(opt){
      let tmpJSON = {}
      for (var i = 1; i < 32; i++) {
        tmpJSON[''+i] = false
      }
      this.pickDay.map((item,i)=>{
        tmpJSON[''+item] = true
      })
      this.limitDay = []
      for(let attr in tmpJSON){
        if(!tmpJSON[attr]){
          this.limitDay.push(parseInt(attr))
        }
      }
      console.log(this.limitDay)
    },
    setPrice(){
      if(!this.price){
        return
      }
      this.changeLimitWeek()
      this.changeLimitDay()
      for(var i=Date.parse(this.priceDatePicker[0]);i<=Date.parse(this.priceDatePicker[1]);i+=86400000){
        let d = new Date(i)

        let dis = false
        this.limitWeek.map((item,i)=>{
          if(d.getDay()===parseInt(item)){
            dis = true
          }
        })
        this.limitDay.map((item,i)=>{
          if(d.getDate()===parseInt(item)){
            dis = true
          }
        })
        if(!this.calendar.priceDataJSON[d.getFullYear()]){
          this.calendar.priceDataJSON[d.getFullYear()] = {}
        }
        if(!this.calendar.priceDataJSON[d.getFullYear()][d.getMonth()]){
          this.calendar.priceDataJSON[d.getFullYear()][d.getMonth()] = {}
        }
        if(!this.calendar.priceDataJSON[d.getFullYear()][d.getMonth()][d.getDate()]){
          this.calendar.priceDataJSON[d.getFullYear()][d.getMonth()][d.getDate()] = {}
        }
        if(!dis){
          this.calendar.priceDataJSON[d.getFullYear()][d.getMonth()][d.getDate()]['price'] = '￥'+this.price||''
        }

      }
      console.log(this.calendar.priceDataJSON)

      this.calendar.key++
    },
    cleanPrice(){
      this.calendar.priceDataJSON = {}
      this.calendar.key++
    },
    setWeekDay(arr){
      this.pickWeek = arr
    },
    setAllDay(){
      this.pickDay = this.dayList
    },
    setInvertDay(){
      let tmpJSON = {}
      for (var i = 1; i < 32; i++) {
        tmpJSON[''+i] = false
      }
      this.pickDay.map((item,i)=>{
        tmpJSON[''+item] = true
      })
      this.pickDay = []
      for(let attr in tmpJSON){
        if(!tmpJSON[attr]){
          this.pickDay.push(parseInt(attr))
        }
      }
    },
  },
}

</script>
<style>
.setDateWrap{
  margin:0 0 0 500px;
}
.week{
  font-size:14px;
}
.daylist{
  font-size:14px;
}
.ipt{
  width:200px;
}
label{
  margin:0 15px 0 0;
  display:inline-block;
}
</style>
