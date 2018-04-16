---
layout:     post
title:      "分布式框架Tudou-Dubbo(七)"
subtitle:   "分布式框架Tudou(七) 前端框架选型和介绍"
date:       2016-10-13 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Dubbo
    - Java
--- 

## 前端框架选型

[https://www.w3cschool.cn/extjs/extjs_components.html](https://www.w3cschool.cn/extjs/extjs_components.html)

[http://www.jeasyui.net/demo/380.html](http://www.jeasyui.net/demo/380.html)

[http://b-jui.com/](http://b-jui.com/)

[http://www.bootcss.com/](http://www.bootcss.com/)

[https://adminlte.io/](https://adminlte.io/)

[http://www.layui.com/](http://www.layui.com/)

[https://ant.design/](https://ant.design/)

**为了方便表格的自定义扩展 选择 layui 加 b-jui(griddata) ,adminlte 做为基础模板**

![img](/img/in-post/java_introduction/dubbo_introduction_27.png)

![img](/img/in-post/java_introduction/dubbo_introduction_28.png)


### 前端文件基础说明

![img](/img/in-post/java_introduction/dubbo_introduction_29.png)

![img](/img/in-post/java_introduction/dubbo_introduction_30.png)


### 简单代码说明

```
<script type="text/javascript">
	//layui动态加载插件
    layui.define(['jquery', 'BJUIajax', 'BJUIdatagrid', 'BJUIinitui', 'BJUIplugins'], function () {
        var $ = layui.jquery;
        var user_btn = ''
        var toolbarCustom_btn = ''
        //判断权限，显示菜单按钮
        for (var data of window.user_data) {
            var value = data.permissionValue;
            if (value == 'upms:user:organization' || value == 'upms:user:role' || value == 'upms:user:permission') {
                toolbarCustom_btn += '<button type="button" name=' + data.name + ' url=' + data.uri + ' class="layui-btn layui-btn-small user_dialog_btn"style="margin-left: 5px;"  >' ;
                if(value == 'upms:user:organization'){
                    toolbarCustom_btn+='<i class="layui-icon">&#xe7fb;</i>';
                }else if(value == 'upms:user:role'){
                    toolbarCustom_btn+='<i class="layui-icon">&#xe99f;</i>';
                }
                else if(value == 'upms:user:permission'){
                    toolbarCustom_btn+='<i class="layui-icon">&#xe8e8;</i>';
                }
                toolbarCustom_btn+= '&nbsp;' + data.name + ' </button>'
            }

            switch (value) {
                case 'upms:user:create':
                    user_btn += 'add,'
                    break;
                case 'upms:user:update':
                    user_btn += 'edit,'
                    break;
                case 'upms:user:delete':
                    user_btn += 'del,'
                    break;
            }
        }

        if (user_btn.indexOf('add') || user_btn.indexOf('edit')) {
            user_btn += 'save,cancel'
        }

        //动态表格参数-通过配置url和参数即可动态绘制表单，而且赛选条件系统自动处理
        var user_datagrid_data = {
            local: 'remote',
            dataUrl: '/manage/user/list',
            dataType: 'json',
            sortAll: false,
            columnFilter:true,
            loadType: 'get',
            height: '100%',
            columns: [
                {name: 'userId', width: '150', label: '编号', align: 'center', attrs: {'readonly': "true"}},
                {name: 'username', width: '150', label: '账号', align: 'center', rule: 'required'},
                {name: 'realname', width: '150', label: '姓名', align: 'center', rule: 'required'},
                {name: 'avatar', width: '150', align: 'center', label: '头像'},
                {name: 'phone', width: '150', align: 'center', label: '电话'},
                {name: 'email', width: '150', align: 'center', label: '邮箱'},
                {
                    name: 'sex',
                    width: '150',
                    align: 'center',
                    label: '性别',
                    type: 'select',
                    items: [{'1': '男'}, {'0': '女'}, {'': '未选择'}]
                },
                {
                    name: 'locked',
                    width: '150',
                    label: '状态',
                    rule: 'required',
                    type: 'select',
                    align: 'center',
                    items: [{'0': '正常'}, {'1': '锁定'}, {'': '未选择'}]
                },
            ],
            paging: {pageSize: 50, selectPageSize: '50,100,200', pageCurrent: 1},
            fullGrid: true,
            showToolbar: true,
            toolbarItem: user_btn,
            inlineEditMult: false,
            toolbarCustom: toolbarCustom_btn,
        }

        //配置控制按钮请求URL
        if (user_btn.indexOf('add') != -1) {
            user_datagrid_data.ifvalue = 'userId'
            user_datagrid_data.addUrl = $.cookie("tudou-upms-server") + '/manage/user/create'
        }

        if (user_btn.indexOf('edit') != -1) {
            user_datagrid_data.editUrl = $.cookie("tudou-upms-server") + '/manage/user/update'
            user_datagrid_data.editdblOnclick = true
            user_datagrid_data.editCallback = 'manage_user_back_edit'
        }

        if (user_btn.indexOf('del') != -1) {
            user_datagrid_data.delConfirm = true
            user_datagrid_data.delPK = 'userId'
            user_datagrid_data.delType = 'POST'
            user_datagrid_data.delUrl = $.cookie("tudou-upms-server") + '/manage/user/delete'
            user_datagrid_data.delCallback = 'del_user_back'
        }

        //开始绘制表单
        $('#manage_user_table').datagrid(user_datagrid_data);
        window.manage_user_back_edit = manage_user_back_edit
        //下面是JS方法
        function manage_user_back_edit(result) {
            if (result.code != 1) {
                if (result.data instanceof Array) {
                    $.each(result.data, function (index, value) {
                        layer.warn( value.errorMsg)
                    });
                } else {
                    layer.warn( result.data)
                }
            } else {
                layer.ok('操作成功')
            }
            $('#manage_user_table').datagrid('refresh_part');
        }

        function del_user_back(d) {
            if (d.code == 1) {
                layer.ok("删除成功")
            } else {
                layer.err(d.data)
            }
            $('#manage_user_table').datagrid('refresh_part');
        }
        $('body').delegate(".user_dialog_btn", 'click', function () {
            var n = this;
            var selected = $('#manage_user_table').data('selectedDatas');
            if (null == selected || selected.length == 0) {
                return;
            }
            var user_select_id = selected[0].userId;
            $("#user_select_id").val(user_select_id);
            var url = n.attributes["url"].value;
            var name = n.attributes["name"].value;
            layer.open({
                type: 5,
                title: name,
                area: ['1000px', '900px'],
                content: url,
                btn: ['保存', '取消'],
                btnAlign: 'r',
                moveType: 1,//拖拽模式，0或者1
                yes:function(){
                }
            });
        });
    });
</script>
//html 代码
<div class="bjui-pageContent">
    <input type="hidden" id="user_select_id">
    <table id="manage_user_table" class="table table-hover" data-width="100%"></table>
</div>
```

