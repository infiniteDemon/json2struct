<template>
  <div class="fullscreen text-white text-center flex flex-center">
    <div class="flex box flex-box">
      <p class="text text-blue">json转golang结构体工具</p>
      <div style="width: 600px;max-width: 600px;">
        <q-input
          v-model="text"
          filled
          autogrow
          class="bg-amber-2"
        />
      </div>
      <p class="text text-blue">{{ msg }}</p>
      <div style="width: 600px;max-width: 600px;">
        <q-input
          v-model="transforToText"
          filled
          autogrow
          class="bg-amber-2"
        />
      </div>
      <p class="text text-red">{{ errMsg }}</p>
    </div>
  </div>
</template>

<script>
import API from './api'
import _ from 'loadsh'
export default {
  name: 'index',

  mixins: [API],

  data () {
    return {
      text: '',
      transforToText: '',
      msg: '转换结果',
      errMsg: "",
      isShowMouse: true,
      background: '/assets/joker-bg.jpeg',
      isLoadBg: false,
      officialWebsite: ""
    }
  },

  created () {
    this.preloadImg(this.$cdn + this.background).then(e => {
      this.isLoadBg = true
    })
    // setTimeout(() => {
    //   this.isShowMouse = false
    // }, 3000)
  },

  methods: {
    titleCase (str) {
      var strArr = str.split("_")
      strArr.forEach((strChild, j) => {
        // 把字符串所有的字母变为小写，并根据空格转换成字符数组
        var arr = strChild.toLowerCase().split(" ")
        // 遍历字符数组
        for (var i = 0; i < arr.length; i++) {
          // 把第一个字符变为大写
          arr[i] = arr[i][0].toUpperCase() + arr[i].substring(1, arr[i].length)
        }
        // 加上空格，返回原模式的字符串
        strArr[j] = arr.join(" ")
      })
      console.log(strArr.join(""))
      return strArr.join("")
    },
    returnType (str) {
      switch (typeof str) {
        case "undefined":
          return "NulL"
        case "boolean":
          return "bool"
        case "string":
          return "string"
        case "number":
          if (str % 1 === 0) {
            return "int"
          } else {
            return "float64"
          }
        case "object":
          return "interface{}"
      }
    },
    returnSqlType (str) {
      switch (typeof str) {
        case "undefined":
          return "NulL"
        case "boolean":
          return "tinyint(1)"
        case "string":
          return "varchar(255)"
        case "number":
          if (str % 1 === 0) {
            return "int(255)"
          } else {
            return "float(255)"
          }
        case "object":
          return "varchar(255)"
      }
    }
  },

  watch: {
    text (newVal, oldVal) {
      const _this = this
      let tempJS
      try {
        tempJS = JSON.parse(newVal)
      } catch (err) {
        this.errMsg = err
        console.log(err)
      }
      console.log(tempJS)
      let str = `type Default struct {\n`
      _.forEach(tempJS, function (value, key) {
        console.log(key, value)
        str += `    ${_this.titleCase(key)} ${_this.returnType(value)} \`gorm:"type:${_this.returnSqlType(value)};;not null;default: ${typeof value === "number" ? value : `'${value}'`}" json:"${key}" example:"${value}" validate:"required"\`\n`
      })
      str += "}"
      console.log(str)
      this.transforToText = str
    }
  }
}
</script>
<style scoped lang="stylus">
.text
  font-size 0.28rem
.flex-box
  flex-direction column
  align-items center
@media screen and (min-width: 1080px) {
 .center-box{
   bottom 0rem
   left 0rem
   width 100vw
   height 3.22rem
   background linear-gradient(180deg, rgba(0, 0, 0, 0) 0%, rgba(0, 0, 0, 0.5) 100%)
 }
}
</style>
