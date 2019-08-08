---
title: about
date: 2018-12-12 22:14:36
keywords: 关于
description: 
comments: false
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/banner/about.jpg
---
{% raw %}
<div class="moe-mashiro" style="text-align:center; font-size: 50px; margin-bottom: 20px;">[最菜のlouhc]</div>
<div id="hello-mashiro" class="popcontainer" style="min-height: 300px; padding: 2px 6px 4px; background-color: rgba(242, 242, 242, 0.5); border-radius: 10px;">
  <center>
  <p>
  </p>
  <h4>
  与&nbsp;<ruby>
  最菜のlouhc&nbsp;<rp>
  （</rp>
  <rt>
  真（ま）白（しろ）</rt>
  <rp>
  ）</rp>
  </ruby>
  对话中...</h4>
  <p>
  </p>
  </center>
  <bot-ui></botui>
</div>
<script src="https://cdn.jsdelivr.net/vue/latest/vue.min.js"></script>
<script src="https://unpkg.com/botui/build/botui.min.js"></script>
<script>
function bot_ui_ini() {
    var botui = new BotUI("hello-mashiro");
    botui.message.add({
        delay: 800, content: "Hi👋"
    }).then(function () {
        botui.message.add({
            delay: 1100, content: "这里是 louhc"
        }).then(function () {
            botui.message.add({
                delay: 1100, content: "一个不会修电脑,不会做PPT的OIer"
            }).then(function () {
                botui.action.button({
                    delay: 1600,
                    action: [{
                        text: "然后呢？ ", value: "sure"
                    }, {
                        text: "少废话！ ", value: "skip"
                    }]
                }).then(function (a) {
                    "sure" == a.value && sure();
                    "skip" == a.value && end()
                })
            })
        })
    });
    var sure = function () {
            botui.message.add({
                delay: 600, content: "😘"
            }).then(function () {
                secondpart()
            })
        },
        end = function () {
            botui.message.add({
                delay: 600,
                content: "![...](https://view.moezx.cc/images/2018/05/06/a1c4cd0452528b572af37952489372b6.md.jpg)"
            })
        },
        secondpart = function () {
            botui.message.add({
                delay: 1500,
                content: "目前就读于YWHS"
            }).then(function () {
                botui.message.add({
                    delay: 1500,
                    content: "全机房OIer大家好"
                }).then(function () {
                    botui.message.add({
                        delay: 1200,
                        content: "我是练习OI时长两年半的个人练习生"
                    }).then(function () {
                        botui.message.add({
                            delay: 1500,
                            content: "最菜のlouhc"
                        }).then(function () {
                            botui.message.add({
                                delay: 1500,
                                content: "喜欢爆零,滚粗,打代码"
                            }).then(function () {
                                botui.message.add({
                                    delay: 1800,
                                    content: "music 三っ•̀.̫•́)っ"
                                })
                            })
                        })
                    })
                })
            })
        }
}
bot_ui_ini()
</script>
{% endraw %}