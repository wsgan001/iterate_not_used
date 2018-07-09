
# 对于bootstrap 的选项卡还是有些东西不是很清楚的：
- 怎么选项卡套选项卡？
- 选项卡的css 怎么设置？
- 有没有选项卡套选项卡是在是什么情况下使用的？我的这种情况适不适合使用选项卡套选项卡？还是有别的更好的设计？


# 临时的一些代码：


```html
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>




      <div class='container-fluid'>
                <h2 class='page-header'>Bootstrap 选项卡</h2>

                <div class='tabbable tabs-left'>

                    <!--
                        选项卡：通过BS的类来控制选项卡的样式
                    -->
                    <ul class='nav nav-tabs'>
                        <li class='active'><a href='#tab1' data-toggle='tab'>选项一</a></li>
                        <li><a href='#tab2' data-toggle='tab'>选项二</a></li>
                        <li><a href='#tab3' data-toggle='tab'>选项三</a></li>
                        <li><a href='#tab4' data-toggle='tab'>选项四</a></li>
                        <li><a href='#tab5' data-toggle='tab'>选项五</a></li>
                    </ul>

                    <!--
                        选项卡的内容定义
                    -->
                    <div class='tab-content'>
                        <div class='tab-pane active' id='tab1'>我是选项卡一的内容</div>
                        <div class='tab-pane' id='tab2'>我是选项卡二的内容</div>
                        <div class='tab-pane' id='tab3'>我是选项卡三的内容</div>
                        <div class='tab-pane' id='tab4'>我是选项卡四的内容</div>
                        <div class='tab-pane' id='tab5'>我是选项卡五的内容</div>
                    </div>
                </div>

            </div>
```