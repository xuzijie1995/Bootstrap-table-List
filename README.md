# Bootstrap-table-List For Distribution template <br/>基于bootstrap-table的配送模板表格列

The bootstrap-table list which I have designed is Based on bootstrap-table.(Your Product Manager:'I want it.')<br/>
我设计了一个基于bootstrap-table的表格列，在特定场合会用到它,尤其是产品经理坚持的时候，这次是一个配送模板

# For you valuable time <br/> 这不是所有人的菜，把你的时间用到需要的地方去吧

This is the special advanced use of Bootstrap-table(It's more complicated than usual, at least I think :P) .The Bootstrap-table basic source code changes are not involved. As long as you use Bootstrap, it is not difficult. And you do not need it for most of your time until your product manager thinks you need it.<br/> 
这只是我对于Bootstrap-table的特殊使用方法，不涉及源码的改动,只要你用过Bootstrap-table，这肯定不难，只是你未必会想到用它做个我这样的配送模板出来，一般的使用就能够满足绝大部分场景了，像我这么大度的人，既然产品想要，我当然得给了。

# What does it look like <br/> 来看看它到底长啥样

Please click the picture. 
点击看大图会好点。
![图片名称](https://raw.githubusercontent.com/xuzijie1995/Bootstrap-table-List/master/images/bootstrap-table-list.png)

+ That's it.Just a little bit changes when you set your bootstrap-table.
+ You may ask 'Why don't you set the detailView true'.Of course, the detailView can handle it,but my PM wants that the details should be more intuitive.So I design this.
+ 就是这样，在你设置表格的时候来点小操作
+ 父子表是我首先想到的，只不过，这不能更加直观的看到里面的数据，即使你可以做到，但我宁可不那么做，我的产品也不会允许那样子的。

# The setting code <br/> 代码

## First <br/> 首先

All of the following must be relied upon. Also, you can search 'bootstrap-table' to find js & css files you need <br/>
确保你有以下引用,或者你可以搜索'bootstrap-table'文档来明确你所需要的的js与css文件以及如何去使用

```jsx
+ <script src="jquery.min.js"></script>
+ <script src="bootstrap.min.js"></script>
+ <script src="bootstrap-table.js"></script>
+ <-- put your locale files after bootstrap-table.js -->
+ <script src="bootstrap-table-zh-CN.js"></script>
+ <link rel="stylesheet" href="bootstrap.min.css">
+ <link rel="stylesheet" href="bootstrap-table.css">
```

## My setting code <br/> 设置代码


```jsx
<html>
  ...
  <div class="form-group traTablelist">
			
	</div> 
  ...
</html>
```

```jsx
js/jq in script
 
function addTra($data){
  for(var i=0;i<$data.length;i++){
    var $n=$data[i]['shipping_name'];
    var $d=[];
    var $id=$data[i]['shipping_id'];
    var $m=$data[i]['methods'];
    for(var j=0;j<$m.length;j++){
      var temp={
        shipping_area:$m[j]['shipping_area'],
        shipping_area_name:$m[j]['shipping_area_name'],
        is_default:$m[j]['is_default'],
        firstNum:$m[j]['config']['firstNum'],
        firstFee:$m[j]['config']['firstFee'],
        conNum:$m[j]['config']['conNum'],
        conFee:$m[j]['config']['conFee'],
      }
      $d.push(temp);
    }
    var $e = "<table class='traTable'></table>";
    $(".traTablelist").append($e);
    var oTraTable = new TraTableInit($n,$d,$id);
    oTraTable.Init();
  }
  var oTraButtonInit = new TraButtonInit();
  oTraButtonInit.Init();
}

$(function(){
  $.ajax({
    type: "POST",
    url: YourUrl,
    contentType: 'application/x-www-form-urlencoded; charset=UTF-8',
    dataType: "json",
    success: function(data){
        ...
        addTra(data.data);
        ...
    },
    error:function(data){
        ...
    }
  });
})

var TraTableInit = function ($t,$d,$id) {
  var oTraTableInit = new Object();
  $("table.traTable").data("name",$t);
  oTraTableInit.Init = function () {
    $('table.traTable').bootstrapTable({
      url: '',   
      method: 'post',
      contentType: 'application/x-www-form-urlencoded; charset=UTF-8',
      striped: true,      
      cache: false,      
      pagination: false,     
      sortable: false,      
      sortOrder: "asc",     
      queryParams: '',
      sidePagination: "client", 
      pageNumber:1,
      pageSize: 100,
      pageList: [],
      search: false, 
      strictSearch: false,
      showColumns: false,
      showRefresh: false,
      minimumCountColumns: 2,
      clickToSelect: false,
      singleSelect: false,
      height: '', 
      uniqueId: '',
      showToggle:false,
      cardView: false,
      detailView: false,
      showHeader:true,
      columns: [
      [{
        field: '',
        title: '模块名称:'+$t,
        align: 'left'
      },{
        field: '',
        title: '<a href="javascript:void(0)" data-id="'+$id+'" data-url="distributionEdit.html" data-index="1400">编辑</a><span style="margin-right:15px"></span>',
        align: 'right',
        colspan: 4,
      }],[{
        field: 'shipping_area_name',
        title: '配送区域',
        formatter:function(value,row,index){
          if(empty(value)){
            return '默认';
          }else{
            return value;
          }
        }
      },{
        field: 'firstNum',
        title: '首件',
        width:100
      },{
        field: 'firstFee',
        title: '首费',
        width:100
      },{
        field: 'conNum',
        title: '续件',
        width:100
      },{
        field: 'conFee',
        title: '续费',
        width:100
      }]],
      data: $d
    });
  };
return oTraTableInit;
};
  
var TraButtonInit = function () {
  var oTraButtonInit = new Object();
  var postdata = {};
  oTraButtonInit.Init = function () {
    $("table.traTable").removeClass("traTable");
    $("div.traTablelist").on("click","a",function(e){
      if(e.target.nodeName.toLowerCase()=='a'){
        edit($(this));
      }
    });
  };
  return oTraButtonInit;
};
 
 
```
To be continue
