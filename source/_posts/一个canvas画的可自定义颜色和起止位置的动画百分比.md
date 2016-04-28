title: 自定义起止位置的canvas动画百分比
tags: [canvas,百分比]
---



## 说明
一个简单的用canvas画的可自定义颜色和起止位置的动画百分比

<b class="clickme" style="cursor: pointer;vertical-align: top;color: blue;">-->先看效果<--</b><canvas width="190" height="170" class="aaa"></canvas><canvas width="190" height="170" class="bbb"></canvas><canvas width="190" height="170" class="ccc"></canvas>
<script src="/vendors/jquery/index.js"></script>
<script>
    
  // call function
    $('.clickme').click(function(){
        function PercentageAnimation(element, options) {
    var canvas = $(element),
        cWidth = canvas[0].width,
        cHeight = canvas[0].height;
    this.options = $.extend({}, $.fn.percentageAnimation.defaults, options);
    this.canvas = canvas;
    this.cWidth = canvas[0].width;
    this.cHeight = canvas[0].height;
    this.init();
}
PercentageAnimation.prototype = {
    init: function() {
        var drawingStaff,
            num,
            endArc,
            arcIncrements = 0,
            difference = this.options.roundStartDegree - this.options.roundEndDegree,
            actureDegree = difference > 0 ? 360 - difference : Math.abs(difference),
            that = this,
            cxt = this.canvas[0].getContext('2d');
        endArc = this.options.percentage * actureDegree * Math.PI / 180;
        drawingStaff = setInterval(function() {
            arcIncrements += Math.PI / 180;
            that.drawCanvasStaff(cxt, arcIncrements, actureDegree);
            if (arcIncrements > endArc) {
                clearInterval(drawingStaff);
            };
        }, this.options.speed)
    },
    drawCanvasStaff: function(cxt, arcEndStaff, actureDegree) {
        var text,
            textWidth;
        this.drawCanvasRound(cxt, this.options.baseColor, this.options.roundStartDegree * Math.PI / 180, this.options.roundEndDegree * Math.PI / 180);

        // draw cover round
        cxt.beginPath();
        cxt.strokeStyle = this.options.coverColor;
        cxt.lineWidth = this.options.lineWidth;
        cxt.lineCap = this.options.shape;
        cxt.arc(this.cWidth / 2, this.cHeight / 2, this.options.radius, this.options.coverStartDegree * Math.PI / 180, arcEndStaff - (actureDegree - this.options.roundEndDegree) * Math.PI / 180, false);
        cxt.stroke();

        // draw text
        cxt.fillStyle = this.options.coverColor;
        cxt.font = this.options.numberFont;
        text = Math.floor(arcEndStaff / (actureDegree * Math.PI / 180) * 100);
        textWidth = cxt.measureText(text).width;
        cxt.fillText(text, this.cWidth / 2 - textWidth / 2, this.cHeight / 2 + 20);
        cxt.font = this.options.subFont;
        cxt.fillStyle = '#ccc';
        cxt.fillText(this.options.subtitle, this.cWidth / 2 + textWidth / 2, this.cHeight / 2 + 20);

    },
    // draw basic round
    drawCanvasRound: function(cxt, color, sAngle, eAngle) {
        cxt.clearRect(0, 0, this.cWidth, this.cHeight);

        cxt.beginPath();
        cxt.strokeStyle = color;
        cxt.lineWidth = this.options.lineWidth;
        cxt.arc(this.cWidth / 2, this.cHeight / 2, this.options.radius, sAngle, eAngle, false);
        cxt.stroke();
    }
}
$.fn.percentageAnimation = function(options) {
    return this.each(function() {
        new PercentageAnimation(this, options)
    });
}
$.fn.percentageAnimation.defaults = {
    baseColor: '#e1e1e1',
    coverColor: '#e45050',
    lineWidth: 6,
    percentage: 0.8,
    roundStartDegree: 0,
    roundEndDegree: 360,
    coverStartDegree: 0,
    radius: 80,
    speed: 10, //the more the slower
    shape: 'round', //square\butt\round
    subtitle: '分',
    numberFont: '60px Microsoft YaHei',
    subFont: '18px PT Sans'
}
        $('.aaa').percentageAnimation();
        $('.bbb').percentageAnimation({
            percentage: 0.5,
            roundStartDegree : 135,
            roundEndDegree : 45,
            coverStartDegree : 135,
            numberFont : '54px songti',
            coverColor : 'green'
        })
        $('.ccc').percentageAnimation({
            percentage: 0.2,
            subtitle : '%',
            shape : 'square',
            roundStartDegree : 90,
            roundEndDegree : 270,
            coverStartDegree : 90,
            coverColor : 'blue',
            speed: 50,
            lineWidth: 10
        })
    })
</script>


## 配置
| 参数        | 默认值   |  说明  |
| --------   | -----:  | ----:  |
| baseColor     | #e1e1e1 |   底部圆颜色     |
| coverColor        |   #45050   |   动画圆颜色   |
| lineWidth        |    6    |  圆宽  |
| percentage        |    0.8    |  百分比  |
| roundStartDegree        |    0    |  底部圆结束度数  |
| roundEndDegree        |    360    |  底部圆结束度数  |
| coverStartDegree        |    0    |  动画圆开始度数  |
| radius        |    80    |  半径  |
| speed        |    10    |  动画速度  |
| shape        |    round    |  动画圆边角形状  |
| subtitle        |    分    |  辅助文字  |
| numberFont        |    60px Microsoft YaHei    |  数字字体  |
| subFont        |    18px PT Sans    |  辅助字体  |

## 计算度数和弧度
知道起止度数，计算实际静止圆的度数：
```
difference = this.options.roundStartDegree - this.options.roundEndDegree,
actureDegree = difference > 0 ? 360 - difference : Math.abs(difference),
```
知道实际静止圆的度数和百分比算出动画圆的结束弧度
```
endArc = this.options.percentage * actureDegree * Math.PI / 180;
```

## 依赖  
jQuery

## 调用
例如：
```
$('.aaa').percentageAnimation({  
    speed: 20  
});
```

## Git地址
[percentageAnimation项目地址](https://github.com/nameit/percentageAnimation)