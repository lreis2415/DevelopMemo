# jsPanel 使用

https://jspanel.de/

https://jspanel.de/api.html#options/overview

## 官方示例

```javascript
jsPanel.create({
    theme: {
        bgPanel: 'url("img/trianglify-primary.svg") right bottom',
        bgContent: '#fff',
        colorHeader: '#fff',
        colorContent: '#000'
    },
    headerToolbar: '<span style="font-size:.85rem;">Just some text in optional header toolbar ...</span>',
    footerToolbar: '<span style="flex:1 1 auto">You can have a footer toolbar too</span>'+
                   '<i class="fal fa-clock"></i><span class="clock">loading ...</span>',
    contentSize: {
        width: function() { return Math.min(730, window.innerWidth*0.9);},
        height: function() { return Math.min(400, window.innerHeight*0.5);}
    },
    position: 'center-top 0 100',
    animateIn: 'jsPanelFadeIn',
    headerTitle: 'I\'m a jsPanel',
    contentAjax: {
        url: 'docs/sample-content/sampleContentHome.html',
        evalscripttags: true,
        done: function (panel) {
            panel.content.innerHTML = this.responseText;
            $(panel.content).mCustomScrollbar({
                scrollButtons: {enable: true},
                theme: 'dark-thin'
            });
            Prism.highlightAll();
        }
    },
    onwindowresize: true,
    callback: function (panel) {
        jsPanel.setStyles(panel.content, {
            padding: '10px',
            fontSize: '1rem'
        });
        function clock() {
            var time = new Date(),
                hours = time.getHours(),
                minutes = time.getMinutes(),
                seconds = time.getSeconds();
            panel.footer.querySelectorAll('.clock') [0].innerHTML = harold(hours) + ':' + harold(minutes) + ':' + harold(seconds);
            function harold(standIn) {
                if (standIn < 10) {standIn = '0' + standIn;}
                return standIn;
            }
        }
        setInterval(clock, 1000);
    }
});

```

## Options 使用

```
theme:'default',// 
'secondary','primary','info','success','warning','danger','light','dark','none'
// 可以在后面加  'filled', 'filledlight' and 'filleddark' ，或者加前缀'bootstrap-'，‘mdb-’
// 头部按钮控制
headerControls: {
  minimize: 'disable', //禁用
  smallify: 'remove', // 移除
  normalize:'hide', // 隐藏，可通过 setControlStatus() 再次显示
  maximize:'remove', 
  close:'disable'
},
//headerControls: 'closeonly xs', // shorthand
closeOnEscape: true, // 按 ESC 键关闭
dragit: {
    axis: 'x', //只能在 x 轴方向拖动
    disable, //禁止拖动
    containment: [60, 20, 20, 20], // 限制离边框的距离 containment: 40 表示 [40, 40, 40, 40]
    opacity: 0.4, // 拖动时的透明度
  },
// 改变大小
resizeit: {
   aspectRatio: 'content', //content 部分按比例缩放
   disable: true,//
   containment: [20], // 离边框的保留距离
   minWidth: 200,
   maxWidth: 500,
   minHeight: 200,
   maxHeight: 500
},
animateIn: 'jsPanelFadeIn', //内置动画
animateOut: 'jsPanelFadeOut'
```

