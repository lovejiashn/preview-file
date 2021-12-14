<template>
  <div class="home">
        <el-button type="primary" @click="showOnlineDoc">生成文档</el-button>
  </div>
  <div id="showExcel" style="margin:0px;padding:0px;position:absolute;width:100%;left: 0px;top: 30px;bottom:0px;"></div>
</template>
<script>
import Axios from "axios";
import LuckyExcel from 'luckyexcel'
export default {
  name: 'Home',
  data(){
    return {
      workbook: {},
      fileContent: ""
    }
  },
  created(){
    this.showOnlineDoc();
  },
  methods:{
    showOnlineDoc(){
      const url = "minio/getFileInputStream.do/项目查询 (3).xlsx";
      debugger;
      Axios.get(url,{
        responseType: "blob",
      }).then(({data})=> {
        debugger;
        let file = new window.File([data], '项目查询 (3).xlsx', {type: 'xlsx'});
        this.uploadExcel(file);
      });
    },
    uploadExcel(file){
      /*const files = evt.target.files;
      if(files==null || files.length==0){
        alert("No files wait for import");
        return;
      }

      let name = files[0].name;
      let suffixArr = name.split("."), suffix = suffixArr[suffixArr.length-1];
      if(suffix != "xlsx"){
        alert("Currently only supports the import of xlsx files");
        return;
      }*/
      LuckyExcel.transformExcelToLucky(file, function(exportJson){

        if(exportJson.sheets==null || exportJson.sheets.length==0){
          alert("Failed to read the content of the excel file, currently does not support xls files!");
          return;
        }
        window.luckysheet.destroy();

        window.luckysheet.create({
          container: 'showExcel', //luckysheet is the container id
          lang: "zh",
          showinfobar:false,
          enableAddBackTop: false,//返回头部按钮
          allowCopy: false, // 是否允许拷贝
          showtoolbar: false, // 是否显示工具栏
          allowEdit: false, // 是否允许前台编辑
          enableAddRow: false, // 允许增加行
          enableAddCol: false, // 允许增加列
          sheetFormulaBar: false, // 是否显示公式栏
          showRowBar: false, // 是否显示行号区域
          showColumnBar: false, // 是否显示列号区域
          showstatisticBarConfig: {
            count:false,
            view:false,
            zoom:false,
          },
          data:exportJson.sheets,
          title:exportJson.info.name,
          userInfo:exportJson.info.name.creator
        });
      });
    },
  }
}
</script>

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
