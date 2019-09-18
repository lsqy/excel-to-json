<template>
  <div>
   <input
      class="importModel"
      ref="uploadFile"
      type="file"
      accept=".xlsx,.xls"
      @change="readWorkbookFromExcel"
    />
  </div>
</template>

<script>
import XLSX from 'xlsx'

export default {
  name: 'ExportJson',
  props: {
    msg: String
  },
  data() {
    return {
      keyArr: [],
      valueArr: [],
      repeateIndexArr: [],
      exportJson: {},
      hashMap: {}
    }
  },
  methods: {
    // 1. load本地excel
    readWorkbookFromExcel(file) {
      const reader = new FileReader()
      reader.readAsBinaryString(file.target.files[0])
      reader.onload = e => {
        const data = e.target.result
        try {
          const workbook = XLSX.read(data, { type: 'binary' })
          this.outputWorkbook(workbook)
        } catch (err) {
          console.log('err', err)
          console.error('请检查上传文件的格式与内容是否正确')
        }
      }
    },
    // 2. 解析xls
    outputWorkbook(workbook) {
      const sheetNames = workbook.SheetNames // 工作表名称集合
      const keyArr = []
      const valueArr = []
      let reg = /[^\u4e00-\u9fa50-9A-Za-z]/ig
      sheetNames.forEach(name => {
        const worksheet = workbook.Sheets[name] // 只能通过工作表名称来获取指定工作表
        console.log('worksheet', worksheet)
        for (let key in worksheet) {
          // console.log('key', key)
          // v是读取单元格的原始值
          let _val = key[0] === '!' ? worksheet[key] : worksheet[key].v
          // console.log('_val', _val)
          if (worksheet[key].v && key.indexOf('A') == 0 && key !== 'A1') {
            _val = _val.replace(reg, '_')
            keyArr.push(_val)
          }
          if (worksheet[key].v && key.indexOf('B') == 0 && key !== 'B1') {
            valueArr.push(_val)
          }
        }
      })
      this.keyArr = keyArr
      this.valueArr = valueArr
      this.getExportJson(keyArr, valueArr)
    },
    /* getRemoveDuplicateArr(arr) {
      const repeateIndexArr = this.repeateIndexArr
      const tempArr = JSON.parse(JSON.stringify(arr))
      for (let i = repeateIndexArr.length; i > 0; i--) {
        tempArr.splice(repeateIndexArr[i], 1)
      }
      return tempArr
    },
    getRepeateIndex(arr) {
      let hashMap = {}
      let repeateIndexArr = []
      for (let i = 0; i < arr.length; i++) {
        if (hashMap[arr[i]]) {
          console.log('重复元素为', arr[i])
          repeateIndexArr.push(i)
        } else {
          hashMap[arr[i]] = true
        }
      }
      // this.downloadFile(
      //   JSON.stringify(hashMap, null, 4),
      //   // 'en.json'
      //   'hashMap.json'
      // )
      console.log('hashMap', hashMap)
      console.log('repeateIndexArr', repeateIndexArr)
      this.hashMap = hashMap
      return repeateIndexArr
    }, */
    getExportJson(keyArr, valueArr) {
      keyArr.forEach((item, index) => {
        console.log('index', index)
        console.log('item', item)
        this.exportJson[item] = valueArr[index]
      })
      // for (let key in this.hashMap) {
      //   this.exportJson[]
      // }
      console.log('this.exportJson', this.exportJson)
      this.downloadFile(
        JSON.stringify(this.exportJson, null, 4),
        'en.json'
        // 'cn.json'
      )
    },
    downloadFile(content, filename) {
      const eleLink = document.createElement('a')
      eleLink.download = filename
      eleLink.style.display = 'none'
      // 字符内容转变成blob地址
      const blob = new Blob([content])
      console.log('blob =>', blob)
      eleLink.href = window.URL.createObjectURL(blob)
      console.log(
        'window.URL.createObjectURL(blob)=>',
        window.URL.createObjectURL(blob)
      )
      // 触发点击
      document.body.appendChild(eleLink)
      eleLink.click()
      // 然后移除
      document.body.removeChild(eleLink)
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
</style>
