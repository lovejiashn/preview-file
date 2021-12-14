# preview-file
### 项目目录
```
│  App.vue
│  main.js
│
├─assets
│      logo.png
│
├─components
│      HelloWorld.vue
│
├─router
│      index.js
│
├─store
│      index.js
│
├─utils
│      request.js
│
└─views
        About.vue
        Home.vue
```

###项目主要实现在线Excel显示，主要是实现xlsx的在线展示。
##### 主要采用了开源的luckyexcel进行Excel展示。
luckyexcel支持在线编辑，展示等一系列Excel操作。
## 实现步骤：
1）在vuejs中public目录下的index.html引入luckyeexcel的样式，js
```
<link rel='stylesheet' href='https://cdn.jsdelivr.net/npm/luckysheet@latest/dist/plugins/css/pluginsCss.css' />
<link rel='stylesheet' href='https://cdn.jsdelivr.net/npm/luckysheet@latest/dist/plugins/plugins.css' />
<link rel='stylesheet' href='https://cdn.jsdelivr.net/npm/luckysheet@latest/dist/css/luckysheet.css' />
<link rel='stylesheet' href='https://cdn.jsdelivr.net/npm/luckysheet@latest/dist/assets/iconfont/iconfont.css' />
<script src="https://cdn.jsdelivr.net/npm/luckysheet@latest/dist/plugins/js/plugin.js"></script>
<script src="https://cdn.jsdelivr.net/npm/luckysheet@latest/dist/luckysheet.umd.js"></script>
```
2）在vue中引入luckyexcel
```
import LuckyExcel from 'luckyexcel'
```
3）使用luckyexcel实现在线浏览excel（仅支持xlsx）

3.1 通过接口获取后端返回的二进制流，并转换成文件
```
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
    }
```

3.2 luckyexcel获取文件，展示在前端
```
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
    }
```
