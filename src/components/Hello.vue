<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <el-col :span="24" class="toolbar">
      <el-form :inline="true">
        <el-form-item>
          <el-input placeholder="请输入城市名称（拼音）" v-model="input_city" @keydown.native.enter.prevent ="greet"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="greet">查询</el-button>
        </el-form-item>
      </el-form>
    </el-col>
    <el-col :span="12" :offset="6" v-if="show">
      <el-tag>{{city}} ({{country}}) 天气</el-tag>
    </el-col>
    <el-col :span="24" v-if="show">
      <el-table
        :data="gridData"
        stripe
        fit
        style="width:80%; text-align:center;">
        <el-table-column
          align="center"
          label="日期">
          <template slot-scope="tbl_data">
            {{tbl_data.row.dt|toDate}}
          </template>
        </el-table-column>
        <el-table-column
          align="center"
          label="天气">
          <template slot-scope="tbl_data">
            <img :src="tbl_data.row.weather[0].icon | toUrl" />
          </template>
        </el-table-column>
        <el-table-column
          align="center"
          prop="temp.max"
          label="最高温度">
        </el-table-column>
        <el-table-column
          align="center"
          prop="temp.min"
          label="最低温度">
        </el-table-column>
        <el-table-column
          align="center"
          prop="pressure"
          label="气压值">
        </el-table-column>
      </el-table>
    </el-col>
  </div>
</template>

<script>
export default {
  name: 'hello',
  data () {
    return {
      msg: 'VUE天气示例',
      input_city: null,
      show: false,
      gridData: [],
      city: null,
      country: null
    }
  },
  filters: {
    // 把编码转换成天气图标网址
    toUrl: function (value) {
      if (!value) return ''
      return "http://openweathermap.org/img/w/" + value + ".png"
    },
    // 把整数值转换成日期
    toDate: function (value) {
      var curdate = new Date(value*1000);
      return `${curdate.getMonth()+1}月${curdate.getDate()}日`;
    }
  },
  methods: {
    greet: function() {
      console.log("greet called.");
      this.$http.get(`http://api.openweathermap.org/data/2.5/forecast/daily?q=${this.input_city}&mode=json&units=metric&cnt=7&appid=f12159c1f548ea9ab7b5ff1907b1df50`)
        .then((response) => {
          // response.data 对象中包括了返回的全部天气预报数据，包括城市、国家等
          this.city=response.data.city.name;
          this.country=response.data.city.country;
          // data.list 中保存了未来7天的天气数据，包括clouds, temp等
          this.gridData=response.data.list;
          console.log("enter remote call.");
          this.show = true;
          // console.log(response)
        })
        .catch(function(response) {
          console.log(response)
        })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.bounce-enter-active {
  animation:bounce-in .5s;
}
.bounce-leave-active{
  animation:bounce-out .5s;
}

@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.05);
  }
  100% {
    transform: scale(1);
  }
}
@keyframes bounce-out {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(0.95);
  }
  100% {
    transform: scale(0);
  }
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.el-table{
  margin:5% 10%;
}

.el-row {
    margin-bottom: 20px;

}

.el-row:last-child {
  margin-bottom: 0;
}

.el-col {
    border-radius: 4px;
}
.bg-purple-dark {
  background: #99a9bf;
}
.bg-purple {
  background: #d3dce6;
}
.bg-purple-light {
  background: #e5e9f2;
}
.grid-content {
  border-radius: 4px;
  min-height: 36px;
}
.row-bg {
  padding: 10px 0;
  background-color: #f9fafc;
}
</style>
